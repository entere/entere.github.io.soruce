---
title: 【每周一文】Neo4j教程三：梳理用户债务关系
date: 2016-05-22 21:34:40
tags: [图形数据库, Neo4j]
---

# 业务需求

张三借钱给李四，李四借钱给王五，王五借钱给赵六，赵六借钱给张三，分析这几个哥们之间的债务关系

![债务关系](/images/20170505_git_5.png "债务关系")
<!-- more --> 

# 实现

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

