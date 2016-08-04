---
layout: post
title: Hibernate的持久化对象的三个状态
date: 2016-6-16
categories: blog
tags:
  - Hibernate
  - 持久化对象
description: Hibernate的持久化对象的三个状态
---

# Hibernate持久层

持久化类:就是一个Java类（咱们编写的JavaBean），这个Java类与表建立了映射关系就可以成为是持久化类。

## 持久化类的编写规则

1. 提供一个无参数 public访问控制符的构造器 -- 底层需要进行反射.
2. 提供一个标识属性，映射数据表主键字段 -- 唯一标识OID.数据库中通过主键.Java对象通过地址确定对象.持久化类通过唯一标识OID确定记录
3. 所有属性提供public访问控制符的 set或者get 方法
4. 标识属性应尽量使用基本数据类型的包装类型

## 自然主键和代理主键

1. 创建表的时候

  - 自然主键:对象本身的一个属性.创建一个人员表,每个人都有一个身份证号.(唯一的)使用身份证号作为表的主键.自然主键.（开发中不会使用这种方式）
  - 代理主键:不是对象本身的一个属性.创建一个人员表,为每个人员单独创建一个字段.用这个字段作为主键.代理主键.（开发中推荐使用这种方式）

2. 创建表的时候尽量使用代理主键创建表

## 主键的生成策略

1. increment:适用于short,int,long作为主键.不是使用的数据库自动增长机制.

  - Hibernate中提供的一种增长机制.
  - 先进行查询 :select max(id) from user;
  - 再进行插入 :获得最大值+1作为新的记录的主键.
  - 问题:不能在集群环境下或者有并发访问的情况下使用.

2. identity:适用于short,int,long作为主键。但是这个必须使用在有自动增长数据库中.采用的是数据库底层的自动增长机制.

  - 底层使用的是数据库的自动增长(auto_increment).像Oracle数据库没有自动增长.

3. sequence:适用于short,int,long作为主键.底层使用的是序列的增长方式.

  - Oracle数据库底层没有自动增长,想自动增长需要使用序列.

4. uuid:适用于char,varchar类型的作为主键.

  - 使用随机的字符串作为主键.

5. native:本地策略.根据底层的数据库不同,自动选择适用于该种数据库的生成策略.(short,int,long)

  - 如果底层使用的MySQL数据库:相当于identity.
  - 如果底层使用Oracle数据库:相当于sequence.

6. assigned:主键的生成不用Hibernate管理了.必须手动设置主键.

# Hibernate的持久化对象三个状态

- 瞬时态

  - 没有持久化标识，没有保存在session缓存中，没有被session对象管理

- 持久态

  - 有持久化标识，保存在session缓存中，被session对象管理
  - 一级缓存赋予了处于持久态的对象自动更新数据库的能力，在`transaction.commit()`

    > 通过调用JavaBean自身的变异器可以达到修改数据库的能力。 session的一级缓存是一个集合，当使用session.get(User.class,id)时先进入缓存查询若缓存中无数据，查询数据库。

    > 快照机制：session中有很多集合，功能都不一样，session中存在两个区域，快照区和缓存区。在使用session.get(User.class,id)时，同时将数据存入缓存区和快照区.

    > 使用user.setName("新换个名字")修改用户名称。 事务提交前，对比缓存区和快照区的数据，如果发现不一样，自动更新数据库中的数据和快照区的数据。

- 脱管态

  - 有持久化标识，没有保存在session缓存中，没有被session对象管理

```java
User user = new User();
//瞬时态

Serializable id = session.save(user);
//持久态

tx.commit();
session.close();

System.out.println(user.getUid);
//托管态
```

# Hibernate的持久化对象三态转化

## 瞬时态转换其他两态

获取：使用 new 关键词 `User user = new User()`

瞬时-->持久：`save() saveOrUpdate()`

瞬时-->脱管：`u.setUid(10);`不推荐

## 持久态转换其他两态

获取：`user = session.get()`

持久-->瞬时：`delete()` 存在争议

持久-->脱管：`close()`、`clear()`、`evict()`

## 脱管态转换其他两态

获取：`new User().setId()` 不推荐

脱管-->瞬时：`user.setUid(null)` 存在争议

脱管-->持久：`update()`、`saveOrUpdate()`

# 操作session的一级缓存

- clear()清除所有缓存
- evict(object)清除指定一级缓存
- flush()主动刷出缓存，在事务提交之前进行缓存区与快照区的对比操作。
