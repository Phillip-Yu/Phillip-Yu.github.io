---
layout: post
title: MapingException的解决方案
date: 2016-6-16
categories: blog
tags:
  - Hibernate
  - Exception
  - Mapping
description: MapingException的解决方案
---

```exception
org.hibernate.MappingException: Repeated column in mapping for entity: com.hibernate.Domain.Customer column: cust_user_id (should be mapped with insert="false" update="false")
```

原因：映射文件中映射表出现异常，一般情况下是因为JavaBean的属性与映射的数据库字段出现一对多的映射问题，比如：

```xml
<property name="cust_user_id" column="cust_user_id"/>
<property name="cust_source" column="cust_user_id"/>
```

```exception
org.hibernate.MappingException: Repeated column in mapping for entity: com.hibernate.Domain.Customer column: cust_user_id (should be mapped with insert="false" update="false")
    at org.hibernate.mapping.PersistentClass.checkColumnDuplication(PersistentClass.java:764)
    at org.hibernate.mapping.PersistentClass.checkPropertyColumnDuplication(PersistentClass.java:782)
    at org.hibernate.mapping.PersistentClass.checkColumnDuplication(PersistentClass.java:804)
    at org.hibernate.mapping.PersistentClass.validate(PersistentClass.java:539)
    at org.hibernate.mapping.RootClass.validate(RootClass.java:265)
    at org.hibernate.boot.internal.MetadataImpl.validate(MetadataImpl.java:329)
    at org.hibernate.boot.internal.SessionFactoryBuilderImpl.build(SessionFactoryBuilderImpl.java:443)
    at org.hibernate.cfg.Configuration.buildSessionFactory(Configuration.java:708)
    at org.hibernate.cfg.Configuration.buildSessionFactory(Configuration.java:724)
    at com.hibernate.Utils.HibernateUtil.<clinit>(HibernateUtil.java:36)
    at com.hibernate.Dao.Impl.CustomerDaoImpl.run5(CustomerDaoImpl.java:45)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:606)
    at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:44)
    at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:15)
    at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:41)
    at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:20)
    at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:263)
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:69)
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:48)
    at org.junit.runners.ParentRunner$3.run(ParentRunner.java:231)
    at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:60)
    at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:229)
    at org.junit.runners.ParentRunner.access$000(ParentRunner.java:50)
    at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:222)
    at org.junit.runners.ParentRunner.run(ParentRunner.java:292)
    at org.junit.runner.JUnitCore.run(JUnitCore.java:157)
    at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:119)
    at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:42)
    at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:234)
    at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:74)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:606)
    at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

java.lang.ExceptionInInitializerError
    at com.hibernate.Dao.Impl.CustomerDaoImpl.run5(CustomerDaoImpl.java:45)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:606)
    at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:44)
    at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:15)
    at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:41)
    at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:20)
    at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:263)
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:69)
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:48)
    at org.junit.runners.ParentRunner$3.run(ParentRunner.java:231)
    at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:60)
    at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:229)
    at org.junit.runners.ParentRunner.access$000(ParentRunner.java:50)
    at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:222)
    at org.junit.runners.ParentRunner.run(ParentRunner.java:292)
    at org.junit.runner.JUnitCore.run(JUnitCore.java:157)
    at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:119)
    at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:42)
    at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:234)
    at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:74)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:606)
    at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)
Caused by: org.hibernate.HibernateException: 初始化会话工厂失败！
    at com.hibernate.Utils.HibernateUtil.<clinit>(HibernateUtil.java:39)
    ... 28 more
```

=========================
