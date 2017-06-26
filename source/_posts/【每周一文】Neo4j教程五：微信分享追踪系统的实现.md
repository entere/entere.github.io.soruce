---
title: 【每周一文】Neo4j教程五：微信分享追踪系统的实现
date: 2017-06-06 21:18:46
tags: [图形数据库, Neo4j, 微信]
---

# 一、Why

在做微信营销时，运营期望能追踪到H5海报在微信朋友圈的传播路径，即：海报->UserA->UserB->UserC……，从而筛选出传播势能较大的用户进行相应红包奖励。

实际项目中海报传播路径如下图：

![实际项目中海报传播路径](/images/20170523_git_2.png "实际项目中海报传播路径")

<!-- more --> 

# 二、What

要追踪到海报的传播路径，我们需要知道以下内容

1、需要知道分享海报用户是谁，也就是说要拿到分享海报用户的 openid。（微信公众号的静默授权可以在用户无感知的状态下拿到 openid）

2、要捕获到用户分享事件，也就是说用户触发分享事件时，系统要知道。(微信的 JS-SDK 可以捕捉到分享事件)

3、要红包奖励用户，那么需要知道根据openid 给用户下发红包。（微信有发送红包的接口，有了 openid，发红包就差钱了～～)


# 三、How

通过分析比较，我们发现图数据库 Neo4j 比较适合处理关系。我们简单分析下海报在用户之间的传播过程

节点：用户(User)、海报(Poster)
关系：转发(FORWARD)、传播(SPREAD)

即：
(:User)-[:FORWARD]->(:Poster) //用户转发海报
(u1:User)-[:SPREAD {ids:["1"]}]->(u2:User) //用户1把海报传播给用户2

1、当用户转发海报时，创建用户节点，创建海报节点，创建用户与海报之间的关系

```
merge (u1:User   {openid:"1",name:"用户1"});//创建用户节点
merge (p9:Poster {pid:"9",name:"9号海报"});//创建海报节点
match (u1),(p9) where u1.openid="1" and p9.pid="9" merge (u1)-[:FORWARD]->[p9];

```

2、当用户打开海报时(Url中会追加微信授权的openid：parent_openid和present_openid)，创建父用户节点u1，创建子用户节点u2，创建u1把9号海报传播给u2的关系

```
merge (u1:User   {openid:"1",name:"用户1"});//创建父用户节点
merge (u2:User   {openid:"2",name:"用户2"});//创建子用户节点
match (u1),(p9) where u1.openid="1" and p9.pid="9" merge (u1)-[:SPREAD {ids:["9"]}]->[p9];

```


3、查询9号海报在用户之间的传播路径

```
match path =  (u1)-[r:SPREAD]->(u2) where '9' in r.ids return path;

```

4、模拟测试数据

```
create
(p9:Poster {name:"9号海报",id:"9"}),
(p8:Poster {name:"8号海报",id:"8"}),
(u1:User {name:"用户A",openid:"1"}),
(u2:User {name:"用户B",openid:"2"}),
(u3:User {name:"用户C",openid:"3"}),
(u4:User {name:"用户D",openid:"4"}),
(u5:User {name:"用户E",openid:"5"}),
(u6:User {name:"用户F",openid:"6"}),
(u7:User {name:"用户G",openid:"7"}),
(u1)-[:FORWARD]->(p9),
(u1)-[:SPREAD {ids:["p9"]}]->(u2),
(u2)-[:FORWARD]->(p9),
(u2)-[:SPREAD {ids:["p9"]}]->(u3),
(u2)-[:SPREAD {ids:["p9"]}]->(u1),
(u3)-[:SPREAD {ids:["p9"]}]->(u4),
(u3)-[:SPREAD {ids:["p9"]}]->(u5),
(u3)-[:SPREAD {ids:["p9"]}]->(u6),


(u1)-[:FORWARD]->(p8),
(u1)-[:SPREAD {ids:["p8"]}]->(u2),
(u2)-[:FORWARD]->(p8),
(u2)-[:SPREAD {ids:["p8"]}]->(u3),
(u3)-[:SPREAD {ids:["p8"]}]->(u7);

match path =  (u1:User)-[r:SPREAD]->(u2:User) where "p9" in r.ids return path;

```

在 Neo4j 中查询出海报传播路径如下图：

![海报传播路径](/images/20170523_git_1.png "海报传播路径")


5、问题

当 `用户A` 把 `9号海报` 传播给 `用户B` 后，如果 `用户B` 不打开海报，系统并不知道 `用户A` 把 `9号海报` 传播给了 `用户B`，因为如果 `用户B` 不打开海报，就无法获取 `用户B` 的 openid。


# 其它实现方案

……


