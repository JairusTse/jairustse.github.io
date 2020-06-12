---
layout: post
title: Redis在Python中的基本用法
category: python
tags: [python,redis]
---

Redis可以用来做缓存，做队列，去重，实现计分板和实现发布/订阅功能。

## 概述

`Redis`是一种键值数据库，属于非关系型数据库的一种，具有极高的读写性能，适合用于处理大量数据的高访问负载。
`Redis`是一个单线程，单进程的数据库。n台服务器同时操作公共的Redis数据库，Redis也会让它们自动排队。


## 安装
#### 在MacOs安装Redis
```
$ brew install redis
```

#### 启动
```
$ redis-server /usr/local/etc/redis.conf
```

#### Python安装Redis库
```
$ pip3 install redis
```


## Python创建Redis连接
```
import redis

client = redis.Redis()
```
Python中对Redis所有的操作都是使用`client`对象来进行。

## 基础数据结构
#### 字符串
字符串（String）是Redis的基本数据结构之一，由`key`和`value`两部分组成。
```
# 创建
client.set('key', 'value', nx=True, ex=120)

# 获取所有key
client.keys()

# 查询
client.get('key')

# 拼接字符串
client.append('key', 'append_value')

# 增加（n省略时默认为1）
client.incr('key', n)

# 减小（n省略时默认为1）
client.dect('key', n)

```
`client.set('key', 'value', nx=True, ex=120)`：nx和ex可省略，nx为True时已有key不覆盖；ex设置过期时间，过期后Redis自动删除这个key。

#### 列表
列表（Lists）好像一根放平的水管，可以从左边或者右边塞入和弹出数据。列表一般用来作为一个`队列`，存放一批可以使用相同逻辑处理的数据。
```
# 左右侧添加
client.lpush('key', 'value1', ... , 'valueN')
client.lpush('key', *datas)

client.rpush('key', 'value1', ... , 'valueN')
client.rpush('key', *datas)

# 查看列表长度
client.llen('key')

# 查询列表一定索引范围的数据：(0, -1)为全部数据
client.lrange('key', 开始索引, 结束索引)

# 左右弹出数据（弹出的数据会被删除）
client.lpop('key')
client.rpop('key')

# 修改数据
client.lset('key', index, value)
```

`client.lpush('key', *datas)`：添加是无序的


#### 集合
集合（Sets）跟列表一样可以存放很多数据，但是数据不能重复，也没有顺序。集合可以用来`去重`，计算多个集合的`交集`，`并集`和`差集`。
```
# 添加数据
client.sadd('key', 'value1', ... , 'valueN')
client.sadd('key', *datas)

# 查看数据条数
client.scard('key')

# 随机获取一条数据并删除
client.spop('key')

# 获取全部数据
client.smembers('key')

# 删除数据
client.srem('key', 'value')

# 求交集（得到key1到keyN集合的交集）
client.sinter('key1', ... , 'keyN')

# 求并集（得到key1到keyN集合的并集）
client.sunion('key1', ... , 'keyN')

# 求差集（得到key1和后面集合的差集）
client.sdiff('key1', ... , 'keyN')

```

`client.smembers('key')` ：返回的数据是无序的

## 高级数据结构

#### 哈希表
哈希表适合保存大量键值对（2^23-1，大约43亿），无论有多少键值对，查询时间始终不变。相同数量的键值对存储空间只需字符串的1/4。哈希表可以用来保存用户登录状态和积分信息（避免有太多的key和key重复的问题）。
```
# 添加
client.hset('哈希表名', '字段名', '值')
client.hmset('哈希表名', {'字段名1': '值1', ... , '字段名n': '值n'})

# 获取所有字段名
client.hkeys('哈希表名')

# 获取一个字段的值
client.hget('哈希表名', '字段名')

# 获取多个字段的值
client.hmget('哈希表名', ['字段名1', ..., '字段名n'])

# 获取所有字段名和值
client.hgetall('哈希表名')

# 判断字段是否存在
client.hexists('哈希表名', '字段名')

# 获取字段数量
client.hlen('哈希表名')

# 删除字段
client.hdel('哈希表名', '字段名')
```

#### 发布消息/订阅频道
`发布/订阅`是一种消息通信模式，可以实现一对多的消息实时发布功能。
```
# 发布消息
client.publish('频道名', '消息')

# 订阅频道
listener = client.pubsub()
listener.subscribe('频道名1', ..., '频道名n')
for message in listener.listen():
    channel = message['channel'].decode()
    data = message['data'].decode()

```

#### 有序集合
有序集合跟集合一样，数据不能重复，但是是有序的。通过score来进行排序。有序集合可以用来实现`排行榜`功能。
```
# 添加
client.zadd('key', 值1, 评分1, ..., 值n, 评分n)
client.zadd('key', 值1=评分1, ..., 值n=评分n)

# 修改评分（改变量格式为3、-3）
client.zincrby('key', 值, 改变量)

# 根据评分范围排序(小-大)
client.zrangebyscore('key', 上限, 下限, 开始位置, 数量, withscores=True)

# 根据评分范围排序(大-小)
client.zrevrangebyscore('key', 上限, 下限, 开始位置, 数量, withscores=True)

# 根据位置排序(小-大)
client.zrange('key', 开始位置, 结束位置, withscores=True)

# 根据位置排序(大-小)
client.zrevrange('key', 开始位置, 结束位置, withscores=True)

# 查询排名（顺序/倒序）
client.zrank('key', '值')
client.zrevrank('key', '值')

# 查询评分
client.zscore('key', '值')

# 查询评分范围内值的数量
client.zcount('key', 评分下限, 评分上限)

```




