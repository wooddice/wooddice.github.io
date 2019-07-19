---
layout:       post
title:        "Java 数组和字符串互相转换"
subtitle:     "及Date格式比较大小"
date:         2019-07-19 16:42:22
author:       "Shuang"
header-img:   "img/in-post/code_head.png"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - java
---

> JAVA使用版本：jdk1.8.0_191 <br/>




### Java实现数组转字符串及字符串转数组的方法

#### 字符串转数组

使用Java split() 方法
split() 方法根据匹配给定的正则表达式来拆分字符串。
注意： . 、 | 和 * 等转义字符，必须得加 \\。多个分隔符，可以用 | 作为连字符。
{% highlight java linenos %}
// 字符串转数组 java.lang.String
String str = "0,1,2,3,4,5";
String[] arr = str.split(","); // 用,分割
System.out.println(Arrays.toString(arr)); // [0, 1, 2, 3, 4, 5]
{% endhighlight %}
#### 数组转字符串

##### 方法一: 遍历
{% highlight java linenos %}
String[] arr = { "0", "1", "2", "3", "4", "5" };
// 遍历
StringBuffer str5 = new StringBuffer();
for (String s : arr) {
  str5.append(s);
}
System.out.println(str5.toString()); // 012345
{% endhighlight %}
##### 方法二: 使用StringUtils的join方法
{% highlight java linenos %}
//数组转字符串 org.apache.commons.lang3.StringUtils
String str3 = StringUtils.join(arr); // 数组转字符串,其实使用的也是遍历
System.out.println(str3); // 012345
String str4 = StringUtils.join(arr, ","); // 数组转字符串(逗号分隔)(推荐)
System.out.println(str4); // 0,1,2,3,4,5
{% endhighlight %}
##### 方法三: 使用ArrayUtils的toString方法
{% highlight java linenos %}
// 数组转字符串 org.apache.commons.lang3.ArrayUtils
String str2 = ArrayUtils.toString(arr, ","); // 数组转字符串(逗号分隔,首尾加大括号)
System.out.println(str2); // {0,1,2,3,4,5}
{% endhighlight %}

### Java中Date日期类型的大小比较

#### 方法一:

java.util.Date类实现了Comparable接口，可以直接调用Date的compareTo()方法来比较大小
{% highlight java linenos %}
String beginTime = "2019-07-08 14:42:32";
String endTime = "2019-07-19 12:26:32";
SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
try {
	Date date1 = format.parse(beginTime);
	Date date2 = format.parse(endTime);
	
	int compareTo = date1.compareTo(date2);
	
	System.out.println(compareTo);
	
} catch (ParseException e) {
	e.printStackTrace();
}
{% endhighlight %}
compareTo()方法的返回值，date1小于date2返回-1，date1大于date2返回1，相等返回0

#### 方法二：

通过Date自带的before()或者after()方法比较
{% highlight java linenos %}
String beginTime = "2019-07-18 14:42:32";
String endTime = "2019-07-28 12:26:32";
SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
try {
	Date date1 = format.parse(beginTime);
	Date date2 = format.parse(endTime);
	
	boolean before = date1.before(date2);
	
	System.out.println(before);
	
} catch (ParseException e) {
	e.printStackTrace();
}
{% endhighlight %}
before()或者after()方法的返回值为boolean类型

#### 方法三：

通过调用Date的getTime()方法获取到毫秒数来进行比较
{% highlight java linenos %}
String beginTime = "2019-07-11 14:42:32";
String endTime = "2019-07-19 12:26:32";
 
SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
 
try {
	Date date1 = format.parse(beginTime);
	Date date2 = format.parse(endTime);
	
	long beginMillisecond = date1.getTime();
	long endMillisecond = date2.getTime();
	
	System.out.println(beginMillisecond > endMillisecond);
	
} catch (ParseException e) {
	e.printStackTrace();
}
{% endhighlight %}
