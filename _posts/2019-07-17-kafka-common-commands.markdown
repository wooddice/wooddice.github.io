---
layout:       post
title:        "Kafka 常用命令"
subtitle:     "针对topic的创建和groupid的消费情况查看"
date:         2019-07-17 09:18:00
author:       "Shuang"
header-img:   "img/in-post/code_head.png"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - kafka
    - bash
---

> kafka使用版本：kafka_2.11-0.10.2.1



### 启动kafka
1.逐次启动三台机器的zookeeper：
{% highlight shell linenos%}
/home/hadoop/zookeeper-3.4.12/bin/zkServer.sh start

{% endhighlight %}
2.检查zookeeper 状态：
{% highlight shell linenos%}
/home/hadoop/zookeeper-3.4.12/bin/zkServer.sh status

{% endhighlight %}
3.逐次启动三台机器的kafka（启动kafka前zookeeper必须是已经启动的）
{% highlight shell linenos%}
/home/hadoop/kafka_2.11-0.10.2.1/bin/kafka-server-start.sh -daemon /home/hadoop/kafka_2.11-0.10.2.1/config/server.properties&

{% endhighlight %}

### 查看topics list
{% highlight shell linenos%}
kafka-topics.sh --list --zookeeper bdp-slave1:22181
kafka-topics.sh --describe --topic landslide_topic  --zookeeper bdp-slave1:22181
{% endhighlight %}

### 创建topics
{% highlight shell linenos %}
kafka-topics.sh --create --topic landslide_topic --partitions 5 --replication-factor 1  \
--zookeeper bdp-slave1:22181

kafka-topics.sh --create --topic tower_topic --partitions 1 --replication-factor 1  \
--zookeeper bdp-slave1:22181

kafka-topics.sh --create --topic coapserver_log_topic --partitions 1 --replication-factor 1  \
--zookeeper bdp-slave1:22181
kafka-topics.sh --create --topic pluginserver_log_topic --partitions 1 --replication-factor 1  \
--zookeeper bdp-slave1:22181
kafka-topics.sh --create --topic cigserver_log_topic --partitions 1 --replication-factor 1  \
--zookeeper bdp-slave1:22181
kafka-topics.sh --create --topic agentserver_log_topic --partitions 1 --replication-factor 1  \
--zookeeper bdp-slave1:22181
{% endhighlight %}

### 删除topic
{% highlight shell linenos%}
kafka-topics.sh --delete --zookeeper bdp-slave1:22181 --topic agentserver_log_topic
kafka-topics.sh --delete --zookeeper bdp-slave1:22181 --topic coapserver_log_topic
kafka-topics.sh --delete --zookeeper bdp-slave1:22181 --topic landslide_topic
kafka-topics.sh --delete --zookeeper bdp-slave1:22181 --topic pluginserver_log_topic
kafka-topics.sh --delete --zookeeper bdp-slave1:22181 --topic tower_topic

#还需要去zookeeper中删除，步骤如下
zkCli.sh -server bdp-slave1:22181
ls /brokers/topics
[coapserver_log_topic, tower_topic, pluginserver_log_topic, agentserver_log_topic, landslide_topic, __consumer_offsets]

rmr /brokers/topics/coapserver_log_topic
rmr /brokers/topics/tower_topic
rmr /brokers/topics/pluginserver_log_topic
rmr /brokers/topics/agentserver_log_topic
rmr /brokers/topics/landslide_topic

ls /consumers/
{% endhighlight %}


### 查看topic消费组状态
{% highlight shell linenos%}
kafka-consumer-groups.sh --bootstrap-server bdp-slave1:29092 --command-config /home/hadoop/kafka_2.11-0.10.2.1/config/consumer.properties --describe --group landslideTest
{% endhighlight %}

### 关于highlight 显示效果查看
abap
{% highlight abap  linenos %}   
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 actionscript
{% highlight actionscript linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 apache
{% highlight apache linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 apiblueprint
{% highlight apiblueprint linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 applescript
{% highlight applescript linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 awk
{% highlight awk linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 biml
{% highlight biml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 brainfuck
{% highlight brainfuck linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 bsl
{% highlight bsl linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 c
{% highlight c linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 ceylon
{% highlight ceylon linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 cfscript
{% highlight cfscript linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 clojure
{% highlight clojure linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 cmake
{% highlight cmake linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 coffeescript
{% highlight coffeescript linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 common_lisp
{% highlight common_lisp linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 conf
{% highlight conf linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 console
{% highlight console linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 coq
{% highlight coq linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 cpp
{% highlight cpp linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 crystal
{% highlight crystal linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 csharp
{% highlight csharp linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 css
{% highlight css linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 d
{% highlight d linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 dart
{% highlight dart linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 diff
{% highlight diff linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 digdag
{% highlight digdag linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 docker
{% highlight docker linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 dot
{% highlight dot linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 eiffel
{% highlight eiffel linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 elixir
{% highlight elixir linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 elm
{% highlight elm linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 erb
{% highlight erb linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 erlang
{% highlight erlang linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 escape
{% highlight escape linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 factor
{% highlight factor linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 fortran
{% highlight fortran linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 fsharp
{% highlight fsharp linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 gherkin
{% highlight gherkin linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 glsl
{% highlight glsl linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 go
{% highlight go linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 gradle
{% highlight gradle linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 graphql
{% highlight graphql linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 groovy
{% highlight groovy linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 hack
{% highlight hack linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 haml
{% highlight haml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 handlebars
{% highlight handlebars linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 haskell
{% highlight haskell linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 hcl
{% highlight hcl linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 html
{% highlight html linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 http
{% highlight http linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 hylang
{% highlight hylang linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 idlang
{% highlight idlang linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 igorpro
{% highlight igorpro linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 ini
{% highlight ini linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 io
{% highlight io linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 irb
{% highlight irb linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 java
{% highlight java linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 javascript
{% highlight javascript linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 jinja
{% highlight jinja linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 json
{% highlight json linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 json-doc
{% highlight json-doc linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 jsonnet
{% highlight jsonnet linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 jsp
{% highlight jsp linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 jsx
{% highlight jsx linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 julia
{% highlight julia linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 kotlin
{% highlight kotlin linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 lasso
{% highlight lasso linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 liquid
{% highlight liquid linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 literate_coffeescript
{% highlight literate_c linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} offeescript
 literate_haskell
{% highlight literate_haske linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} ll
 llvm
{% highlight llvm linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 lua
{% highlight lua linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 m68k
{% highlight m68k linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 magik
{% highlight magik linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 make
{% highlight make linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 markdown
{% highlight markdown linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 mathematica
{% highlight mathematica linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 matlab
{% highlight matlab linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 moonscript
{% highlight moonscript linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 mosel
{% highlight mosel linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 mxml
{% highlight mxml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 nasm
{% highlight nasm linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 nginx
{% highlight nginx linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 nim
{% highlight nim linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 nix
{% highlight nix linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 objective_c
{% highlight objective_c linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 ocaml
{% highlight ocaml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 pascal
{% highlight pascal linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 perl
{% highlight perl linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 php
{% highlight php linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 plaintext
{% highlight plaintext linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 plist
{% highlight plist linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 powershell
{% highlight powershell linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 praat
{% highlight praat linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 prolog
{% highlight prolog linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 prometheus
{% highlight prometheus linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 properties
{% highlight properties linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 protobuf
{% highlight protobuf linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 puppet
{% highlight puppet linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 python
{% highlight python linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 q
{% highlight q linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 qml
{% highlight qml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 r
{% highlight r linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 racket
{% highlight racket linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 ruby
{% highlight ruby linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 rust
{% highlight rust linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 sass
{% highlight sass linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 scala
{% highlight scala linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 scheme
{% highlight scheme linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 scss
{% highlight scss linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 sed
{% highlight sed linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 shell
{% highlight shell linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 sieve
{% highlight sieve linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 slim
{% highlight slim linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 smalltalk
{% highlight smalltalk linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 smarty
{% highlight smarty linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 sml
{% highlight sml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 sqf
{% highlight sqf linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 sql
{% highlight sql linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 supercollider
{% highlight supercollider linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 swift
{% highlight swift linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 tap
{% highlight tap linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 tcl
{% highlight tcl linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 terraform
{% highlight terraform linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 tex
{% highlight tex linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 toml
{% highlight toml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 tsx
{% highlight tsx linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 tulip
{% highlight tulip linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 turtle
{% highlight turtle linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 twig
{% highlight twig linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 typescript
{% highlight typescript linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 vala
{% highlight vala linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 vb
{% highlight vb linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 verilog
{% highlight verilog linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 vhdl
{% highlight vhdl linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 viml
{% highlight viml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 vue
{% highlight vue linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 wollok
{% highlight wollok linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 xml
{% highlight xml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 xojo
{% highlight xojo linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 
 yaml
{% highlight yaml linenos %} 
/home/hadoop/testhighlinght.sh start &
{% endhighlight  %} 

