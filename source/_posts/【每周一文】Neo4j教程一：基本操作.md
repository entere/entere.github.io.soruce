---
title: 【每周一文】Neo4j教程一：基本操作
date: 2017-05-09 22:00:24
tags: [图形数据库, Neo4j]
---


# Neo4j 基本概念

Neo4j中不存在表的概念，只有两类：节点( Node )和关联( Relation )，可以简单理解为图里面的点和边。
在数据查询中，节点一般用小括号()，关联用中括号[]。
当然也隐含路径的概念，是用节点和关联表示的，如：(a)-[r]->(b)，表示一条从节点a经关联r到节点b的路径。

节点：
图中的对象
可带若干名-值属性
可带标签
例： (m:Movie {title:"The Matrix"})


关系：
连接节点（有类型、带方向）
可带若干名-值属性

例：(a)-[:ACTED_IN {roles:["Neo"]}]->(m)


<!-- more --> 



# 安装 Neo4j

下载 Neo4j  Community Edition 版，[点此下载](https://neo4j.com/download/community-edition/) 本文下载的 Mac OS 版。下好后一路安装即可。

注意：我在在安装 Neo4j 时 Mac OS系统出现  【Neo4j 已损坏您应该将它移到废纸篓】  这样的提示，遇到这种情况，你需要把你 Mac 的 安全与隐私中的  选择“任何来源”即可。





# Neo4j Cypher 基本操作

## 1. 创建节点

CREATE (节点名:标签 {属性key1:属性value1,属性key2:属性value2,......})
             

```shell

CREATE (p1:Person { name: "entere", age: 30, pid: 1 })
CREATE (p2:Person { name: "fxd", age: 40, pid: 2 })

```

CREATE 创建数据
() 表示节点
p1:Person，p1 是节点名( node )，Person 是 节点的标签名( lable ) 
{} 是节点属性

要记住的事情: 

Neo4j的数据库服务器使用该<节点名称>以存储Database.As一个Neo4j的DBA或开发该节点的详细信息，我们不能用它来访问节点的详细信息。

Neo4j的数据库服务器创建一个<标签名称>作为别名到内部节点name.As一个Neo4j的DBA或开发人员，我们应该利用这个标签名称访问节点的详细信息。


## 2.创建关联



```shell

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




## 4. 查找案例一

```shell

CREATE 
(p1:Person {name: "方兴东",uid:1}),
(p2:Person {name: "甜夏",uid:2}),
(p3:Person {name: "开心战士",uid:3}),
(p4:Person {name: "狐狸猫",uid:4}),
(p5:Person {name: "唐镇宇",uid:5}),
(p6:Person {name: "郭思思",uid:6}),
(p7:Person {name: "庄北",uid:7}),
(p1)-[:HAS_FANS]->(p2),
(p1)-[:HAS_FANS]->(p3),
(p1)-[:HAS_FANS]->(p4),
(p1)-[:HAS_FANS]->(p5),
(p1)-[:HAS_FANS]->(p6),
(p2)-[:HAS_FANS]->(p3),
(p2)-[:HAS_FANS]->(p4),
(p2)-[:HAS_FANS]->(p1),
(p4)-[:HAS_FANS]->(p3),
(p3)-[:HAS_FANS]->(p4),
(p3)-[:HAS_FANS]->(p7);

MATCH (p:Person) RETURN p

```

### 查询所有节点

match (n) return n

### 查询某个节点

match (a:Person {name:"方兴东"})-[:HAS_FANS]->(b) return a,b

match p = (a:Person {name:"方兴东"})-[:HAS_FANS]->(b) return p;
match p = (a:Person {name:"方兴东"})-[:HAS_FANS]->(b) return nodes(p);
match p = (a:Person {name:"方兴东"})-[:HAS_FANS]->(b) return rels(p)



### 统计节点个数

match (a:Person {name:"方兴东"})-[:HAS_FANS]->(b) return count(b)

### 查询任意关系

match (a:Person {name:"方兴东"})-[*]->(b) return a,b

### 按照属性值排序

match (a:Person {name:"方兴东"})-[:HAS_FANS]->(b) return a,b order by b.uid desc

### 查询属性name的值是方兴东的节点，及其所有关联节点


match (a)-[r]->(b) where a.name='方兴东' return a,b 
match (a {name:'方兴东'})-[r]->(b) return a,b


### 查询属性name值是Kaine的节点，及其所有距离为1到3的关联节点



match (a)-[*1..3]->(b) where a.name='方兴东' return a,b 
match (a {name:'方兴东'})-[*1..3]->(b) return a,b 

                    
### 查询属性name的值是方兴东的节点，及其所有距离为2并且去除距离为1的节点

（在计算好友的好友时会用到，即如果a、b、c三个人都认识，如果仅计算跟a距离为2的人的时候会把b、c也算上，因为a->b->c，或者a->c->b都是通路）




match (a)-[*2]->(b) where a.name='方兴东' and not (a)-[*1]->(b) return a,b 

 
### 查询方兴东和开心战士的共同粉丝

MATCH (p1:Person {name:"方兴东"})-[:HAS_FANS]->(x)<-[:HAS_FANS]-(p2:Person {name:"开心战士"}) return x;
 

注：关联的中括号内数字的含义
n 距离为n
..n 最大距离为n
n.. 最小距离为n
m..n 距离在m到n之间

### 查询 方兴东 到 庄北 的最短路径

match p = shortestPath( (p1:Person {name:"方兴东"})-[:HAS_FRIEND*..3]->(p2:Person {name:"庄北"}) ) return p;



## 5. 查找案例二

```shell

create 
(p3:User {name:"张三"}),
(p4:User {name:"李四"}),
(p5:User {name:"王五"}),
(p6:User {name:"赵六"}),
(p7:User {name:"钱七"}),
(p8:User {name:"孙八"}),
(p9:User {name:"杨九"}),
(p10:User {name:"吴十"}),
(p3)-[:HAS_FRIEND]->(p4),
(p3)-[:HAS_FRIEND]->(p5),
(p4)-[:HAS_FRIEND]->(p6),
(p4)-[:HAS_FRIEND]->(p7),
(p5)-[:HAS_FRIEND]->(p8),
(p5)-[:HAS_FRIEND]->(p9),
(p6)-[:HAS_FRIEND]->(p10);

```

### 查询张三的直接朋友

MATCH (p:User {name : "张三"})-[:HAS_FRIEND]->(f) return p,f;

### 查询张三的朋友的朋友

MATCH (p:User {name : "张三"})-[:HAS_FRIEND]->()-[:HAS_FRIEND]->(f) return p,f;

### 查询张三所有的的朋友，包括朋友的朋友

MATCH (p:User {name : "张三"})-[*]->(f) return p,f;



### 查询张三到吴十的最短路径

match p = shortestPath( (p3:User {name:"张三"})-[:HAS_FRIEND*..15]->(p10:User {name:"吴十"}) ) return p;




## 5.索引

```shell

:schema 列出所有标签的所有记录
:schema ls -l :YourLabel 列出指定标签的索引与约束


CREATE CONSTRAINT ON (p:Person) ASSERT p.name IS UNIQUE //唯一约束 
CREATE INDEX ON :Person(name);  //创建索引






```

## 6.清空

```shell

match(n)
optional match(n)-[r]-()
delete n,r

```


create
(c1:Company {name:"ofo小黄车",id:"1"}),
(c2:Company {name:"摩拜",id:"2"}),
(m1:Manager {name:"肖敏",id:"1"}),
(m2:Manager {name:"陈熙",id:"2"}),
(m3:Manager {name:"薛鼎",id:"3"}),
(m4:Manager {name:"胡玮炜",id:"4"}),
(m5:Manager {name:"王晓峰",id:"5"}),
(m6:Manager {name:"夏一平",id:"6"}),
(s1:Stockholders {name:"真格基金",id:"1"}),
(s2:Stockholders {name:"顺为基金",id:"2"}),
(s3:Stockholders {name:"红杉资本",id:"3"}),
(s4:Stockholders {name:"启明创投",id:"4"}),
(c1)-[:高管]->(m1),
(c1)-[:高管]->(m2),
(c1)-[:高管]->(m3),
(c1)-[:股东]->(s1),
(c1)-[:股东]->(s2),
(c2)-[:高管]->(m4),
(c2)-[:高管]->(m5),
(c2)-[:高管]->(m6),
(c2)-[:股东]->(s3),
(c2)-[:股东]->(s4);







create
(p1:Poster {title:"文章1",id:"1"}),
(p2:Poster {title:"文章2",id:"2"}),
(u1:User {nickname:"A",id:"1"}),
(u2:User {nickname:"B",id:"2"}),
(u3:User {nickname:"C",id:"3"}),
(u4:User {nickname:"D",id:"4"}),
(u5:User {nickname:"E",id:"5"}),
(u6:User {nickname:"F",id:"6"}),
(u7:User {nickname:"G",id:"7"}),
(u1)-[:OPEN]->(p1),
(u1)-[:FORWARD]->(p1),
(u1)-[:SPREAD {roles:["p1"]}]->(u2),
(u2)-[:OPEN]->(p1),
(u2)-[:FORWARD]->(p1),
(u2)-[:SPREAD {roles:["p1"]}]->(u3),
(u2)-[:SPREAD {roles:["p1"]}]->(u1),
(u3)-[:OPEN]->(p1),
(u3)-[:SPREAD {roles:["p1"]}]->(u4),
(u3)-[:SPREAD {roles:["p1"]}]->(u5),
(u3)-[:SPREAD {roles:["p1"]}]->(u6),


(u1)-[:OPEN]->(p2),
(u1)-[:FORWARD]->(p2),
(u1)-[:SPREAD {roles:["p2"]}]->(u2),
(u2)-[:OPEN]->(p2),
(u2)-[:FORWARD]->(p2),
(u2)-[:SPREAD {roles:["p2"]}]->(u3),
(u3)-[:OPEN]->(p2),
(u3)-[:SPREAD {roles:["p2"]}]->(u7);






match (a:User{name:"A"})-[r:SPREAD*..5]->(n) where 1 in r.article return a,n,r;



create(a:User{name:"A"}),
(b:User{name:"B"}),
(c:User{name:"C"}),
(d:User{name:"D"}),
(e:User{name:"E"}),
(f:User{name:"F"}),
(g:User{name:"G"}),
(a)-[:SPREAD1]->(b),
(a)-[:SPREAD1]->(c),
(a)-[:SPREAD1]->(d),
(b)-[:SPREAD1]->(e),
(e)-[:SPREAD1]->(f),
(c)-[:SPREAD2]->(d),
(d)-[:SPREAD2]->(e),
(e)-[:SPREAD2]->(f),
(f)-[:SPREAD2]->(g);

match (a:User{name:"A"})-[r:SPREAD1*..5]->(n) return a,n,r;

START n = node(91018)
MATCH path = (n)-[r:SPREAD1*..5]->(x)
RETURN x

create
(wxa1:WxArticle {nickname:"文章1",id:"1"}),
(wxa2:WxArticle {nickname:"文章2",id:"2"}),
(wxu1:WxUser {nickname:"A",id:"1"}),
(wxu2:WxUser {nickname:"B",id:"2"}),
(wxu3:WxUser {nickname:"C",id:"3"}),
(wxu4:WxUser {nickname:"D",id:"4"}),
(wxu5:WxUser {nickname:"E",id:"5"}),
(wxu6:WxUser {nickname:"F",id:"6"}),
(wxu7:WxUser {nickname:"G",id:"7"}),
(wxa1)-[:SPREAD1]->(wxu1),
(wxa1)-[:SPREAD1]->(wxu5),
(wxa2)-[:SPREAD2]->(wxu6),
(wxa2)-[:SPREAD2]->(wxu7),
(wxu1)-[:SPREAD1]->(wxu2),
(wxu2)-[:SPREAD1]->(wxu3),
(wxu3)-[:SPREAD1]->(wxu4),
(wxu1)-[:SPREAD1]->(wxu6),
(wxu2)-[:SPREAD1]->(wxu7),
(wxu6)-[:SPREAD2]->(wxu1),
(wxu6)-[:SPREAD2]->(wxu3),
(wxu3)-[:SPREAD2]->(wxu4),
(wxu6)-[:SPREAD2]->(wxu7);

match p =  (p1:WxArticle {id:"1"})-[:SPREAD1*..5]->(b) return  p;
match p =  (wxa1:WxArticle {id:"1"})-[:SPREAD1*..5]->(b) return  p;










 
cypher是neo4j官网提供的声明式查询语言，非常强大，用它可以完成任意的图谱里面的查询过滤，我们知识图谱的一期项目 基本开发完毕，后面会陆续总结学习一下neo4j相关的知识。今天接着上篇文章来看下neo4j的cpyher查询的一些基本概念和语法。

一，Node语法

在cypher里面通过用一对小括号()表示一个节点，它在cypher里面查询形式如下：

1，() 代表匹配任意一个节点

2, (node1) 代表匹配任意一个节点，并给它起了一个别名

3, (:Lable) 代表查询一个类型的数据

4, (person:Lable) 代表查询一个类型的数据，并给它起了一个别名

5, (person:Lable {name:"小王"}) 查询某个类型下，节点属性满足某个值的数据

6, (person:Lable {name:"小王",age:23})　节点的属性可以同时存在多个，是一个AND的关系

二，关系语法

关系用一对-组成，关系分有方向的进和出，如果是无方向就是进和出都查询

1,-> 指向一个节点

2,-[role]-> 给关系加个别名

3,-[:acted_in]-> 访问某一类关系

4,-[role:acted_in]-> 访问某一类关系，并加了别名

5,-[role:acted_in {roles:["neo","hadoop"]}]->

访问某一类关系下的某个属性的关系的数据

三，模式语法

模式语法是节点和关系查询语法的结合，通过模式语法我们可以进行我们想要的任意复杂的查询

(p1: Person:Actor {name:"tom"})-[role:acted_in {roles:["neo","actor"]}]-(m1:Movie {title:"water"})
四, 模式变量

为了增加模块化和减少重复，cypher允许把模式的结果指定在一个变量或者别名中，方便后续使用或操作

path = (: Person)-[:ACTED_IN]->(:Movie)

path是结果集的抽象封装，有多个函数可以直接从path里面提取数据如：

nodes(path)：提取所有的节点

rels(path): 提取所有的关系 和relationships(path)相等

length(path): 获取路径长度

五，条件

cypher语句也是由多个关键词组成，像SQL的

select name, count(*) from talbe where age=24 group by name having count(*) >2  order by count(*) desc
多个关键字组成的语法，cypher也非常类似，每个关键词会执行一个特定的task来处理数据

match: 查询的主要关键词

create: 类似sql里面的insert

filter，project，sort，page等都有对应的功能语句

通过组合上面的一些语句，我们可以写出非常强大复杂的语法，来查询我们想要检索的内容，cypher会 自动解析语法并优化执行。

一些实际的用法例子：

1,创建

create (:Movie {title:"驴得水",released:2016})  return p;
执行成功，在neo4j的web页面我们能看到下面的信息

+-------------------+| No data returned. |
+-------------------+Nodes created: 1
Properties set: 2
Labels added: 1
当然cypher也可以一次创建多个数据，并同时添加关系

2,查询

match (p: Person) return p; 查询Person类型的所有数据

match (p: Person {name:"sun"}) return p; 查询名字等于sun的人

match( p1: Person {name:"sun"} )-[rel:friend]->(p2) return p2.name , p2.age 查询sun的朋友的名字和年龄

match (old) ... create (new) create (old)-[rel:dr]->(new) return new 对已经存在的节点和新建的节点建立关系

3,查询或更新

merge 语法可以对已经存在的节点不做改变，对变化的部分会合并

MERGE (m:Movie { title:"Cloud Atlas" })ON CREATE SET m.released = 2012RETURN m
merge .... on create set ... return 语法支持合并更新

4,筛选过滤

cypher过滤也是用的和SQL一样的关键词where

match (p1: Person) where p1.name="sun" return p1;

等同下面的

match (p1: Person {name:"sun"}) return p1

注意where条件里面支持 and ， or ，xor，not等boolean运算符，在json串里面都是and

除此之外，where里面查询还支持正则查询

match (p1: Person)-[r:friend]->(p2: Person) 
where p1.name=~"K.+" or p2.age=24 or "neo" in r.rels 
return p1,r,p2
关系过滤匹配使用not

MATCH (p:Person)-[:ACTED_IN]->(m)WHERE NOT (p)-[:DIRECTED]->()RETURN p,m
5，结果集返回

MATCH (p:Person)RETURN p, p.name AS name, upper(p.name), coalesce(p.nickname,"n/a") AS nickname, { name: p.name,  label:head(labels(p))} AS person
结果集返回做去重

match (n) return distinct n.name;
6,聚合函数

cypher支持count,sum,avg,min,max

match (: Person) return count(*)

聚合的时候null会被跳过 count 语法 支持 count( distinct role )

MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)<-[:DIRECTED]-(director:Person)RETURN actor,director,count(*) AS collaborations
7,排序和分页

MATCH (a:Person)-[:ACTED_IN]->(m:Movie)RETURN a,count(*) AS appearancesORDER BY appearances DESC SKIP 3 LIMIT 10;
8, 收集聚合结果

MATCH (m:Movie)<-[:ACTED_IN]-(a:Person)
RETURN m.title AS movie, collect(a.name) AS cast, count(*) AS actors
9, union 联合

支持两个查询结构集一样的结果合并

MATCH (actor:Person)-[r:ACTED_IN]->(movie:Movie)
RETURN actor.name AS name, type(r) AS acted_in, movie.title AS title
UNION （ALL）
MATCH (director:Person)-[r:DIRECTED]->(movie:Movie)
RETURN director.name AS name, type(r) AS acted_in, movie.title AS title
10, with

with语句给cypher提供了强大的pipeline能力，可以一个或者query的输出，或者下一个query的输入 和return语句非常类似，唯一不同的是，with的每一个结果，必须使用别名标识。

通过这个功能，我们可以轻而易举的做到在查询结果里面在继续嵌套查询。

MATCH (person:Person)-[:ACTED_IN]->(m:Movie)
WITH person, count(*) AS appearances, collect(m.title) AS movies
WHERE appearances > 1RETURN person.name, appearances, movies
注意在SQL里面，我们想过滤聚合结果，需要使用having语句但是在cypher里面我们可以配合with语句使用 where关键词来完成过滤

11，添加约束或者索引



