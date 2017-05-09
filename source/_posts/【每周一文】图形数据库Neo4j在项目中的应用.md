---
title: 【每周一文】图形数据库Neo4j在项目中的应用
date: 2017-05-02 12:00:24
tags: [数据库, neo4j]
---

# 前言

项目需要从零开始构建用户的关系、知识图谱。而以 MySQL 为代表的关系型数据库和以 MongoDB 为代表的 NoSQL 都不适用于有实时价值的数据关系。经过一系列调研，我们从众多图数据库中选中了 Neo4j 来构建用户图谱。

# 安装 Neo4j

下载 Neo4j  Community Edition 版，[点此下载](https://neo4j.com/download/community-edition/) 本文下载的 Mac OS 版。下好后一路安装即可。

注意：我在在安装 Neo4j 时 Mac OS系统出现  【Neo4j 已损坏您应该将它一道废纸篓】  这样的提示，遇到这种情况，你需要把你 Mac 的 安全与隐私中的  选择“任何来源”即可。


# Neo4j 基本概念

Neo4j中不存在表的概念，只有两类：节点( Node )和关联( Relation )，可以简单理解为图里面的点和边。
在数据查询中，节点一般用小括号()，关联用中括号[]。
当然也隐含路径的概念，是用节点和关联表示的，如：(a)-[r]->(b)，表示一条从节点a经关联r到节点b的路径。


# Neo4j Cypher 基本操作

## 1. 创建节点

CREATE (节点名:标签 {属性key1:属性value1,属性key2:属性value2,......})
             

```
CREATE (p1:Person { name: "entere", age: 30, pid: 1 })
CREATE (p2:Person { name: "fxd", age: 40, pid: 2 })

```

CREATE 创建数据
() 表示节点
p1:Person，p1 是节点名( node )，Person 是 节点的标签名( lable ) 
{} 是节点属性

要记住的事情 

    Neo4j的数据库服务器使用该<节点名称>以存储Database.As一个Neo4j的DBA或开发该节点的详细信息，我们不能用它来访问节点的详细信息。

    Neo4j的数据库服务器创建一个<标签名称>作为别名到内部节点name.As一个Neo4j的DBA或开发人员，我们应该利用这个标签名称访问节点的详细信息。


## 2.创建关联

```
match (p1),(p2)
where p1.pid=1 and p2.pid=2
create (p1)-[:HAS_FANS]->(p2)
```

创建 p1 节点和 p2 节点的路径，此时变量 HAS_FANS 即代表关联，它也可以有标签

注意：创建关联之前须先创建节点，如果先创建关联，在创建节点会出问题
 

## 3. 创建多个节点和关系

CREATE (节点1), (节点2), (节点3), (节点4), 
             (节点1)-[关系1]->(节点2),
             (节点2)-[关系2]->(节点3),
             ......

CREATE 
(p1:Person {name: "方兴东"}),
(p2:Person      {name: "甜夏"}),
(p3:Person {name: "开心战士"}),
(p4:Person      {name: "狐狸猫"}),
(p5:Person      {name: "唐镇宇"}),
(p6:Person      {name: "郭思思"}),
(p7:Person      {name: "庄北"}),
(p1)-[:粉丝]->(p2),
(p1)-[:粉丝]->(p3),
(p1)-[:粉丝]->(p4),
(p1)-[:粉丝]->(p5),
(p1)-[:粉丝]->(p6),
(p1)-[:粉丝]->(p7),
(p2)-[:粉丝]->(p3),
(p2)-[:粉丝]->(p4),
(p2)-[:粉丝]->(p1),
(p4)-[:粉丝]->(p3),
(p3)-[:粉丝]->(p4),
(p3)-[:粉丝]->(p7);


MATCH (p:Person) RETURN p


## 4. 查找


查询所有节点及关联
match (n) return n 

查询属性name的值是fxd的节点，及其所有关联节点
match (a)-[r]->(b) where a.name='fxd' return a,b 
match (a {name:'fxd'})-[r]->(b) return a,b

查询属性name值是Kaine的节点，及其所有距离为1到3的关联节点
match (a)-[*1..3]->(b) where a.name='fxd' return a,b 
match (a {name:'fxd'})-[*1..3]->(b) return a,b 

                    
查询属性name的值是fxd的节点，及其所有距离为2并且去除距离为1的节点。
（在计算好友的好友时会用到，即如果a、b、c三个人都认识，如果仅计算跟a距离为2的人的时候会把b、c也算上，因为a->b->c，或者a->c->b都是通路）
match (a)-[*2]->(b) where a.name='fxd' and not (a)-[*1]->(b) return a,b 
     


注：关联的中括号内数字的含义
    n 距离为n
    ..n 最大距离为n
    n.. 最小距离为n
    m..n 距离在m到n之间





## 5.清空

```
match(n)
optional match(n)-[r]-()
delete n,r
```

# Neo4j 应用

应用一：梳理用户之间的关注关系

比如：把用户关系导入到 Neo4j 中，查看一下 方兴东 用户关系


![债务关系](/images/20170505_git_5.png "债务关系")


应用二：梳理用户之间的债务关系

比如：张三借钱给李四，李四借钱给王五，王五借钱给赵六，赵六借钱给张三

```
create 
(u3:Money {name:"张三",uid:3}),
(u4:Money {name:"李四",uid:4}),
(u5:Money {name:"王五",uid:5}),
(u6:Money {name:"赵六",uid:6}),
(u3)-[:borrow_to]->(u4),
(u4)-[:borrow_to]->(u5),
(u5)-[:borrow_to]->(u6),
(u6)-[:borrow_to]->(u3);

match (n:Money) return n;

```

![债务关系](/images/20170505_git_5.png "债务关系")




