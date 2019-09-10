---
layout:       post
title:        "springcloud使用websock"
subtitle:     "结合redis做权限分流"
date:         2019-09-06 15:52:22
author:       "Shuang"
header-img:   "img/in-post/code_head.png"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
    - springcloud
    - 
---

> 使用版本：springboot1.5 <br/>

因为涉及到springcloud架构，用户的信息是通过zuul转发到下游服务端</br>
所以首先要先把request中的token或者user信息截取出来和websock的httpsession做绑定放入redis中</br>
在websocke的推送类中根据redis中的权限信息做权限适配</br>
其中核心问题在于：
   * 由于HTTP协议与websocket协议的不同，导致没法直接从websocket中获取协议，需要写一个继承ServletRequestListener的监听器，把zuul传递的用户信息和当前HttpSession关联起来入redis
   * 因为WebSocket对象根本不存在与Spring容器中，所以传统的ContextLoader.getCurrentWebApplicationContext()自然也就无法使用。需要自定义ApplicationContextAware在项目加载时就获得项目的Spring容器
  
### 配置监听类
配置一个继承ServletRequestListener的监听类，把zuul传递的用户信息和当前httpsession的id关联起来
{% highlight java linenos %}
@Component
public class RequestListenerForWebSocket implements ServletRequestListener {

    public static final Logger LOG = LoggerFactory.getLogger(RequestListenerForWebSocket.class);

    @Autowired
    private RedisUtilSynchronized redisUtil;

    private final int dataNum=1;


    @Override
    public void requestInitialized(ServletRequestEvent sre)  {
        HttpSession session = ((HttpServletRequest) sre.getServletRequest()).getSession();
        LOG.info("websockserver add httpSession {} to all request",session.getId());
        HttpServletRequest hsr = (HttpServletRequest) sre.getServletRequest();
        String user = hsr.getHeader("userName");
        if(StringUtils.isNotEmpty(user)){
            //only save session-user one day
            redisUtil.hset(session.getId(),"username",user,dataNum,86400L);
        }

    }
    public RequestListenerForWebSocket() {}

    @Override
    public void requestDestroyed(ServletRequestEvent arg0)  {}
}
{% endhighlight  %} 
### websock配置类
配置WebSocketConfig，把httpsession传递到websock应用类
{% highlight java linenos %}
@Configuration
public class WebSocketConfig  extends ServerEndpointConfig.Configurator{
    private static final Logger LOG = LoggerFactory.getLogger(WebSocketConfig.class);
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }

    @Override
    public void modifyHandshake(ServerEndpointConfig sec, HandshakeRequest request, HandshakeResponse response) {
        HttpSession httpSession = (HttpSession) request.getHttpSession();
        if(httpSession!=null){
            sec.getUserProperties().put(HttpSession.class.getName(), httpSession);
        }
        super.modifyHandshake(sec, request, response);
    }


}
{% endhighlight  %} 
### 配置读取spring容器类
 * 为了解决在websocket的环境中无法使用@Autowired注解注入类
 * 目的是获取到Spring容器，进行手动注入
 * 因为新生成的WebSocket对象根本不存在与Spring容器中，所以传统的ContextLoader.getCurrentWebApplicationContext()无法使用。
 * 这个类为了项目加载时就获得项目的Spring容器
{% highlight java linenos %}
@Repository
public final class SpringUtil implements BeanFactoryPostProcessor {

     /*
      *Spring应用上下文环境
      */
    private static ConfigurableListableBeanFactory beanFactory;

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        SpringUtil.beanFactory = beanFactory;
    }

    public static ConfigurableListableBeanFactory getBeanFactory() {
        return beanFactory;
    }

    /**
     * 获取对象
     *
     * @param name
     * @return Object 一个以所给名字注册的bean的实例
     * @throws org.springframework.beans.BeansException
     *
     */
    @SuppressWarnings("unchecked")
    public static <T> T getBean(String name) throws BeansException {
        return (T) getBeanFactory().getBean(name);
    }

    /**
     * 获取类型为requiredType的对象
     *
     * @param clz
     * @return
     * @throws org.springframework.beans.BeansException
     *
     */
    public static <T> T getBean(Class<T> clz) throws BeansException {
        T result = (T) getBeanFactory().getBean(clz);
        return result;
    }

    /**
     * 如果BeanFactory包含一个与所给名称匹配的bean定义，则返回true
     *
     * @param name
     * @return boolean
     */
    public static boolean containsBean(String name) {
        return getBeanFactory().containsBean(name);
    }

    /**
     * 判断以给定名字注册的bean定义是一个singleton还是一个prototype。 如果与给定名字相应的bean定义没有被找到，将会抛出一个异常（NoSuchBeanDefinitionException）
     *
     * @param name
     * @return boolean
     * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
     *
     */
    public static boolean isSingleton(String name) throws NoSuchBeanDefinitionException {
        return getBeanFactory().isSingleton(name);
    }

    /**
     * @param name
     * @return Class 注册对象的类型
     * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
     *
     */
    public static Class<?> getType(String name) throws NoSuchBeanDefinitionException {
        return getBeanFactory().getType(name);
    }

    /**
     * 如果给定的bean名字在bean定义中有别名，则返回这些别名
     *
     * @param name
     * @return
     * @throws org.springframework.beans.factory.NoSuchBeanDefinitionException
     *
     */
    public static String[] getAliases(String name) throws NoSuchBeanDefinitionException {
        return getBeanFactory().getAliases(name);
    }

}
{% endhighlight  %} 
### 配置websock应用类
实现在websock应用中使用spring容器里面的实例，完成redis应用类的注入
根据redis中的缓存内容完成传输数据和关联用户的适配，达到限制内容传输到页面端的权限限制
{% highlight java linenos %}
@Component
@ServerEndpoint(value = "/websocket/data",configurator = WebSocketConfig.class)
public class AppWebSocket {

    private static final Logger LOG = LoggerFactory.getLogger(AppWebSocket.class);

    //private volatile static int onlineCount = 0;

    private static CopyOnWriteArraySet<AppWebSocket> webSocketSet = new CopyOnWriteArraySet<AppWebSocket>();

    private Session session;

    private RedisUtilSynchronized redisUtil = SpringUtil.getBean(RedisUtilSynchronized.class);

    private HttpSession httpSession;

    private String sessionUsername;

    private final int dataNum=1;




    @OnOpen
    public  synchronized void onOpen(@PathParam("device") String device, Session session,EndpointConfig config) {
        this.session = session;
        this.httpSession = (HttpSession) config.getUserProperties().get(HttpSession.class.getName());
        String username = (String) redisUtil.hget(httpSession.getId(),"username",dataNum);
        if(StringUtils.isNotEmpty(username)){
            this.sessionUsername = username;
            LOG.info("websockserver AppWebSocket sessionUsername : {}",username);
        }
        webSocketSet.add(this);
        //addOnlineCount();
        int onlineCount = webSocketSet.size();
        LOG.info("New connection for " + device + ", total " + onlineCount  + " connections" + "session :" + session.toString() );
        try {
        	
            sendMessage(String.valueOf(onlineCount));
        } catch (IOException e) {
            LOG.error("IOException: " + e.getMessage());
        }
    }
    
    public static synchronized void sendInfoToOwer(String message, List<String> userlist) throws IOException {
        for (AppWebSocket item : webSocketSet) {
            try {
                if(item.sessionUsername!=null&&userlist.contains(item.sessionUsername)){
                    LOG.debug("sent web socket data: " + message + "to Ower :{}" + item.sessionUsername );
                    item.sendMessage(message);
                }
            } catch (IOException e) {
                LOG.error("sent web socket data IOException error: " + e.getMessage());
                continue;
            } catch (IllegalStateException e){
                LOG.error("sent web socket data IllegalStateException error: " + e.getMessage());
                continue;
            }
        }
    }
｝
{% endhighlight  %} 

