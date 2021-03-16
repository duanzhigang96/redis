# redis

## nosql概述

缓存使用

主要用于解决读的问题。

发展过程：优化数据结构和索引---->文件缓存---->Memcached

1. 分库分表+水平拆分+mysql集群

数据库的本质是解决读写的问题

使用分库分表来解决写的压力。（按业务进行分库分表，可以做成单个的微服务）

## ![image-20210314215841740](/Users/duanzhigang/Library/Application Support/typora-user-images/image-20210314215841740.png)

### 为什么使用nosql

1.用户的个人信息，社交网络，地理位置，用户产生的数据，用户日志，等等 爆发型的数据 需要使用nosql。

### 什么是nosql

关系型数据库：行，列，表格记录数据

nosql = not only sql （不仅仅是sql）泛指非关系型数据库，传统的关系数据库无法对付web2.0时代，尤其是超大规模的高并发社区！

数据类型没有固定的格式。

### nosql的特点

1. 方便拓展（数据之间没有关系，很好拓展）
2. 大数据量高性能（redis 一秒可以写8万次，读取11万次，nosql的缓存记录级，是一种细粒度的缓存）
3. 数据类型是多样型的（不需要事先设计数据库，随取随用！如果是数据库十分大的表）
4. 传统rdbms和nosql

```bash
传统的 rdbms
	- 结构化组织
	- sql
	- 数据和关系都存在单独的表中
	- 数据结构化语言
	- 严格的一致性
	- ....
```

```
nosql
	- 不仅仅是数据
	- 没有固定的查询语言
	- 键值对存储，列存储，文档存储，图形数据库
	- 最终一致性
	- cap定理和base   （初级架构师）
	- 高性能，高可拓，高可用
	- ....
```

### 了解：3V+3高

#### 大数据时代的3V：主要是描述问题的

1. 海量Volume
2. 多样Variety
3. 实时Velocity

#### 大数据时代的3高：主要是对程序的要求

1. 高并发
2. 高可扩
3. 高性能

### nosql的四大分类

**kv键值对：**

新浪：redis

美团：redis+tair

阿里：redis+memecache

**文档型数据库（bson，json格式）**

- MongoDB(基于分布式文件存储的数据库，c++编写，主要处理大量的文件) 

  ​	MongoDB 是一个介于关系型数据库和非关系型数据库中中间的产品。

- ConthDB

**列存储数据库**

- Hbase
- 分布式文件系统

**图关系型数据库（朋友圈社交网络）**

- Neo4j. infoGrid

## Redis 入门

### 概述

Redis（Remote Dictionary Server )，即远程字典服务

是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

> Redis 能干吗？

1. 内存存储，持久化，内存中是断电即失，所以说持久化很重要（rdb，aof）
2. 效率高，可以用来告诉缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器，（微信微博浏览量）
6. .........

> 特性

1. 多样的数据类型
2. 持久化
3. 集群
4. 事务

> 官网，GitHub 学习

### 安装，部署 使用redis客户端连接redis

```bash
docker container run --restart=always \
-p 6379:6379 \
-v /home/redis/data:/data \
-v /home/redis/conf/redis.conf:/etc/redis/redis.conf \
--privileged=``true` `\
--name myredis \
-d redis redis-server /etc/redis/redis.conf --appendonly yes

docker run -it --link myredis:redis --rm redis redis-cli -h redis -p 6379
```

> 测试连接  ping pong

Docker 默认没有redis.conf配置文件

> 基础命令

1. 查看redis的进程是否开启

```bash
ps -ef|grep redis
```

2. 关闭redis服务

```bash
shutdown
```

### 性能测试工具(官方自带)

```bash
redis-benchmark

docker exec -it 323bbce01b72 redis-benchmark -h 127.0.0.1 -p 6379 -c 100 -n 100000

```

> 参数

| 1    | **-h**    | 指定服务器主机名                           | 127.0.0.1 |
| ---- | --------- | ------------------------------------------ | --------- |
| 2    | **-p**    | 指定服务器端口                             | 6379      |
| 3    | **-s**    | 指定服务器 socket                          |           |
| 4    | **-c**    | 指定并发连接数                             | 50        |
| 5    | **-n**    | 指定请求数                                 | 10000     |
| 6    | **-d**    | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**    | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**    | 通过管道传输 <numreq> 请求                 | 1         |
| 10   | **-q**    | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv** | 以 CSV 格式输出                            |           |
| 12   | **-l**    | 生成循环，永久执行测试                     |           |
| 13   | **-t**    | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | **-I**    | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |

查看分析

```bash
====== SET ======                                                   
  100000 requests completed in 2.02 seconds		#10000个请求写入测试
  100 parallel clients	#100并发客户端
  3 bytes payload #每次写入3个字节
  keep alive: 1	#1台服务器
  host configuration "save": 3600 1 300 100 60 10000
  host configuration "appendonly": yes
  multi-thread: no

Latency by percentile distribution:
0.000% <= 0.679 milliseconds (cumulative count 9)
50.000% <= 1.415 milliseconds (cumulative count 50349)  # 1.4毫秒处理 50%
75.000% <= 1.703 milliseconds (cumulative count 75223)
87.500% <= 1.919 milliseconds (cumulative count 87688)
93.750% <= 2.183 milliseconds (cumulative count 93750)
```

### 基础知识

redis 默认有16个数据库

可以使用select进行切换数据库

```bash
select 3 #切换数据库

dbsize #查看数据库容量

keys * #查看所有key

flushdb #清空当前数据库

flushall #清空所有数据库
```

> redis是单线程的

redis 的性能瓶颈是内存和网络带宽而不是cup

redis 是C语言写的，官方提供的数据是100000+的QPS

**redis 为什么单线程还这么快？**

1. 高性能的服务器不一定是多线程
2. 多线程（cup上下文切换）不一定比单线程效率高

redis 是将所有的数据全部放在内存中的，对于内存系统中没有上下文切换效率就是最高的！ 