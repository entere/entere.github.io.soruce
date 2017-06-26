---
title: 【每周一文】Neo4j教程二：二度人脉推荐
date: 2017-05-16 23:36:20
tags: [图形数据库, Neo4j]
---

# 业务需求

人脉分为一度人脉和二度人脉。二度人脉包括你的好友的好友、同学的好友，2nd后面的数字代表二度人脉的人数，后面的+66是最近好友的好友、同学的好友最近添加的好友或同学人数。

我们的需求就是要给用户推荐**朋友的朋友**。

比如

张三的朋友有：李四和王五，
李四的朋友有：赵六和钱七，
王五的朋友有：孙八和杨九，

需要把赵六、钱七、孙八、杨九推荐给张三。


![二度人脉](/images/20170505_git_6.png "二度人脉")

<!-- more --> 

# 技术分析

Neo4j 适合于社区关系：用户之间的关系有朋友关系，亲友关系，同事关系等，把每个人看作是一个结点，用户与用户之间的关系看作是一条边，这样整个社区就像一张大的图一样。

用图论的方法查找用户之间的关系网是很方便的，查询速度也极快，它可以查出通过朋友可能认识的人（二度人脉），甚至三度人脉及多度人脉，还可以查出两个用户间最短路径。

# 具体实现

## 1. 创建节点

```
create 
(p3:User {name:"张三"}),
(p4:User {name:"李四"}),
(p5:User {name:"王五"}),
(p6:User {name:"赵六"}),
(p7:User {name:"钱七"}),
(p8:User {name:"孙八"}),
(p9:User {name:"杨九"}),
(p10:User {name:"吴十"});

```


## 2. 创建关系

```
match (p3),(p4)
where p3.name="张三" and p4.name="李四"
create (p3)-[:HAS_FRIEND]->(p4);

match (p3),(p5)
where p3.name="张三" and p5.name="王五"
create (p3)-[:HAS_FRIEND]->(p5);


match (p4),(p6)
where p4.name="李四" and p6.name="赵六"
create (p4)-[:HAS_FRIEND]->(p6);



match (p4),(p7)
where p4.name="李四" and p7.name="钱七"
create (p4)-[:HAS_FRIEND]->(p7);


match (p5),(p8)
where p5.name="王五" and p8.name="孙八"
create (p5)-[:HAS_FRIEND]->(p8);


match (p5),(p9)
where p5.name="王五" and p9.name="杨九"
create (p5)-[:HAS_FRIEND]->(p9);


match (p6),(p10)
where p6.name="赵六" and p10.name="吴十"
create (p6)-[:HAS_FRIEND]->(p10);

```

## 3. 查询

### 3.1 查找张三朋友的朋友


```
MATCH (p3:User {name:"张三"})-[:HAS_FRIEND]->()-[:HAS_FRIEND]->(u) RETURN u;

```

查询结果如下图，我们可以把张三朋友的朋友推荐给张三，从而实现二度人脉推荐

![朋友的朋友](/images/20170505_git_8.png "朋友的朋友")


### 3.2 张三怎么样才能快速认识吴十

```
match p = shortestPath( (p3:User {name:"张三"})-[:HAS_FRIEND*..3]->(p10:User {name:"吴十"}) ) return p;

```

这个是 Neo4j 最短路径问题 Easy ~  按下图找到吴十是最快的方法

![最短路径](/images/20170505_git_7.png "最短路径")



