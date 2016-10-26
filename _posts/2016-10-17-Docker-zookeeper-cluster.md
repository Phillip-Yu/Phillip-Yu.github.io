---
layout: post
title: Docker组建zookeeper集群
date: 2016-10-17
categories: blog
tags:
  - Docker
  - zookeeper cluster
description: Docker组建zookeeper集群
---


Dockerfile:

```ruby
FROM            docker.io/centos:latest
COPY            ./jdk/ /opt/jdk/
ENV             JAVA_HOME /opt/jdk
ENV             JRE_HOME $JAVA_HOME/jre
ENV             PATH $PATH:$JAVA_HOME/bin
ENV             CLASSPATH .:$JAVA_HOME/lib/:$JRE_HOME/lib/
COPY            zookeeper/ /opt/zookeeper/
ENTRYPOINT      ["/opt/zookeeper/start.sh"]
```

start.sh

```bash
#!/bin/sh
ZOO_CFG="/opt/zookeeper/conf/zoo.cfg"
myid="/opt/zookeeper/data/myid"
echo "server id (myid): ${SERVER_ID}"
echo "server IP : ${ADDITIONAL_ZOOKEEPER_1}"
echo "server IP : ${ADDITIONAL_ZOOKEEPER_2}"
echo "server IP : ${ADDITIONAL_ZOOKEEPER_3}"

echo "${SERVER_ID}" > ${myid}

echo "${ADDITIONAL_ZOOKEEPER_1}" >> ${ZOO_CFG}
echo "${ADDITIONAL_ZOOKEEPER_2}" >> ${ZOO_CFG}
echo "${ADDITIONAL_ZOOKEEPER_3}" >> ${ZOO_CFG}

exec /opt/zookeeper/bin/zkServer.sh start-foreground
```

command
```bash
docker run -d --name=zookeeper1 --net=express \
-e SERVER_ID=1 \
-e ADDITIONAL_ZOOKEEPER_1="server.1=zookeeper1:2881:3881" \
-e ADDITIONAL_ZOOKEEPER_2="server.2=zookeeper2:2881:3881" \
-e ADDITIONAL_ZOOKEEPER_3="server.3=zookeeper3:2881:3881" \
my/zkclusterbase:v0.1
```

### 容易出现的问题

- 检查SELinux状态
- 检查字母是否写错

### 结论
总体来说 使用docker 搭建zookeeper集群 非常容易.
本次的主要目的是练习使用bash脚本传值
 ps: 如果需要外网访问权限别忘记在command里写入端口映射 `-p ServerPort:DockerPort`
