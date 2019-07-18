---
layout:       post
title:        "Spark 处理json数组"
subtitle:     "json扁平化，包含数组的1个对象转换为多个对象"
date:         2019-07-18 08:34:22
author:       "Shuang"
header-img:   "img/in-post/code_head.png"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - spark
    - java
    - dataset
---

> hadoop使用版本：hadoop-2.6.0-cdh5.11.0 <br/>
> spark使用版本：spark-2.1.1-bin-hadoop2.6 <br/>



### 需要处理的json
{% highlight javascript linenos%}
{
  "observation":
    {
      "result":
        {
          "resultCode":0
        },
      "resources":
        [
          {
            "resourcePath":"34/4/1",
            "value":"99"
          },
          {
            "resourcePath":"34/3/1",
            "value":"89"
          },
          {
            "resourcePath":"34/2/1",
            "value":"93"
          },
          {
            "resourcePath":"34/1/1",
            "value":"96"
          }
        ],
      "protocol":"LWM2M",
      "correlatorId":"b4f414f5-3567-4bbd-ab83-b30395da37c5",
      "serialNumber":"869610030001230",
      "gatewayId":"636768666911916",
      "timestamp":1557372343267
    }
}
{% endhighlight %}

### 希望处理后的结构
{% highlight javascript linenos%}
{"serialNumber":"869610030001230","resourcePath":"34/4/1","value":"99","timestamp":1557372343267}
{"serialNumber":"869610030001230","resourcePath":"34/3/1","value":"89","timestamp":1557372343267}
{"serialNumber":"869610030001230","resourcePath":"34/2/1","value":"93","timestamp":1557372343267}
{"serialNumber":"869610030001230","resourcePath":"34/1/1","value":"96","timestamp":1557372343267}
{% endhighlight %}
### 实现代码
{% highlight java linenos%}
    Dataset<Row> dataset = spark.read().json("**json files**");
//    dataset.show();
    dataset.createOrReplaceTempView("messagedset");
    Dataset<Row> deviceDataSet= spark.sql("select observation.serialNumber as serialNumber" +
        ",observation.timestamp as timestamp " +
        ",explode(observation.resources) as resources " +
        " from messagedset ")
        .withColumn("resourcePath",col("resources").getField("resourcePath"))
        .withColumn("value",col("resources").getField("value"))
        .drop("resources");
//    deviceDataSet.show();
//    deviceDataSet.printSchema();
    deviceDataSet.repartition(1)
    .write()
    .format("json")
    .mode("append")
    .save("**path of json files**");
{% endhighlight %}