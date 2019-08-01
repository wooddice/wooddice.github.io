---
layout:       post
title:        "Cassandra的cql使用介绍"
subtitle:     "主键查询的一些限制"
date:         2019-08-01 12:52:22
author:       "Shuang"
header-img:   "img/in-post/code_head.png"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - cassandra
    - sql
---

> 使用版本：cqlsh 5.0.1,Cassandra 2.1.15.1403,DSE 4.8.9,CQL spec 3.2.1,Native protocol v3 <br/>

### Cassandra三种表达式
cassandra目前支持的表达式目前有三种：
我们先假设我们的表结构是这样的：
{% highlight sql linenos %}
cassandra@cqlsh:xxxxxxx> describe test_key_query_in_cassandra ;
CREATE TABLE test_key_query_in_cassandra (
    a INT,
    b INT,
    c INT,
    d INT,
    e INT,
    value text,
    PRIMARY KEY (a,b,c,d)
);
CREATE INDEX ON test(e);
{% endhighlight  %} 

#### 1、前缀表达式
就是查询条件必须是主键，且前面的主键是=号，只有最后一个主键是> >= < <=。
举例：
{% highlight sql linenos %} 

SELECT * FROM test_key_query_in_cassandra WHERE a=1 AND b>2;
SELECT * FROM test_key_query_in_cassandra WHERE a=1 AND b=1 AND c>2;
SELECT * FROM test_key_query_in_cassandra WHERE a=1 AND b=1 AND c=1 AND d>2;
SELECT * FROM test_key_query_in_cassandra WHERE a=1 AND b=1 AND c>2 AND c<2;

以上都是可行的。


SELECT * FROM ttest_key_query_in_cassandraest  WHERE a>1;
//不行，第一主键只能用=号
SELECT * FROM test_key_query_in_cassandra  WHERE a=1 AND c>2; 
//不行 中间不能有跳跃
SELECT * FROM test_key_query_in_cassandra  WHERE a=1 AND b>2 AND b>=2;
//不行，同一个主键不能同时用> >= = 或者< <= =
{% endhighlight  %} 

#### 2、不含第一主键的前缀表达式

这类表达式，需要加上ALLOW FILTERING来查询，这样的查询效率肯定是低一些
举例：

{% highlight sql linenos %} 
SELECT * FROM test_key_query_in_cassandra WHERE b>2 ALLOW FILTERING;
SELECT * FROM test_key_query_in_cassandra WHERE b=1 AND c>2 ALLOW FILTERING;
SELECT * FROM test_key_query_in_cassandra WHERE b=1 AND c=1 AND d>2 ALLOW FILTERING;
SELECT * FROM test_key_query_in_cassandra WHERE b=1 AND c>2 AND c<2 ALLOW FILTERING;

{% endhighlight  %} 
#### 3、包含索引列查询

举例：
{% highlight sql linenos %} 

SELECT * FROM test_key_query_in_cassandra WHERE e=1;
//只含索引
SELECT * FROM test_key_query_in_cassandra WHERE a=1 AND b=1 AND c>1 AND e=1; 
//包含索引列和前缀表达式的组合
SELECT * FROM test_key_query_in_cassandra WHERE b=1 AND c>1 AND e=1;
//包含索引列和不含第一主键的前缀表达式的组合 不需要ALLOW FILTERING
SELECT * FROM test_key_query_in_cassandra WHERE c>1 AND e=1 ALLOW FILTERING;
//除此之外和任意其它表达式的组合，都可以进行查询，
//前提是要加ALLOW FILTERING;
{% endhighlight  %} 
最后说一下，cassandra查询出来的结果，无论什么查询结果，都是先按token(a)排序，再按b排序 再按c排序，再按d排序：

### 项目实例
表结构如下：
{% highlight sql linenos %} 
cassandra@cqlsh:xxxxxxx> describe tri_client_data_recording ;

CREATE TABLE tri_client_data_recording (
    endpoint text,
    start_date timestamp,
    resource_uri text,
    subscription_id text,
    condition text,
    value text,
    PRIMARY KEY (endpoint, start_date, resource_uri, subscription_id, condition)
) WITH CLUSTERING ORDER BY (start_date ASC, resource_uri ASC, subscription_id ASC, condition ASC)
    AND bloom_filter_fp_chance = 0.01
    AND caching = '{"keys":"ALL", "rows_per_partition":"NONE"}'
    AND comment = ''
    AND compaction = {'class': 'org.apache.cassandra.db.compaction.SizeTieredCompactionStrategy'}
    AND compression = {'sstable_compression': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND dclocal_read_repair_chance = 0.1
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair_chance = 0.0
    AND speculative_retry = '99.0PERCENTILE';
CREATE INDEX client_data_recording_value ON china_gatewayiot17.tri_client_data_recording (value);

{% endhighlight  %} 
#### 查询某sn在某个时间段的信息
{% highlight sql linenos %} 
select count(*) from tri_client_data_recording where endpoint='xxxxx' and start_date >= '2019-07-31 02:33:12+0000' and start_date < '2019-07-31 16:00:00+0000' limit 10000;
{% endhighlight  %} 