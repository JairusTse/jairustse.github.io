---
layout: post
title: Redis在Python中的基本用法
category: python
tags: [python,redis]
---

Redis可以用来做缓存，做队列，去重，实现计分板和实现发布/订阅功能。

## 概述

`Redis`是一种键值数据库，属于非关系型数据库的一种，具有极高的读写性能，适合用于处理大量数据的高访问负载。可以用来做缓存，做队列，去重，实现计分板和实现发布/订阅功能。


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
```
# 创建（nx可省略，为True时已有key不覆盖）
client.set('key', 'value', nx=True)

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


#### 列表
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


#### 集合
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


## 高级数据结构

#### 哈希表
哈希表适合保存大量键值对，无论有多少键值对，查询时间始终不变。相同数量的键值对存储空间只需字符串的1/4。
```
# 添加
client.hset('key', '字段名', '值')
client.hmset('key', {'字段名1': '值1', ... , '字段名n': '值n'})

# 获取所有字段名
client.hkeys('key')

# 获取一个字段的值
client.hget('key', '字段名')

# 获取多个字段的值
client.hmget('key', ['字段名1', ..., '字段名n'])

# 获取所有字段名和值
client.hgetall('key')

# 判断字段是否存在
client.hexists('key', '字段名')

# 获取字段数量
client.hlen('key')

# 删除字段
client.hdel('key', '字段名')
```

#### 发布消息/订阅频道
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




