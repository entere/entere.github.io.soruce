---
title: mysql5.7通过json类型替代关联表
date: 2019-09-18 23:14:34
tags: [MySQL,JSON,关联]
---

学校表:

```sql
create table school(
　　`id`  bigint unsigned primary key not null auto_increment,
　　`name` varchar(10) not null,
　　`tagId` json
);


```



<!-- more  -->

tagId字段为json数组,存有标签表中的id

标签表:

```sql

create table tag(
   `id`  bigint unsigned primary key not null  auto_increment,
   `name`  varchar(10) not null
);

```

在查询时,可以使用group_concat和concat函数手动拼接json:

```sql

select
 *,
 (select concat('[',group_concat('{"id":',tag.id,',"name":"',tag.`name`,'"}'),']')
   from `tag`
   where json_contains(`school`.tagId, json_array(tag.id))) as `tag`
from school limit 10

```


即可得到标签的信息,查询结果的tag字段为手动拼接的json数组

如果需要查询有特定的tagId的学校,可以使用json_contains函数:

```sql

select
 *,
 (select concat('[',group_concat('{"id":',tag.id,',"name":"',tag.`name`,'"}'),']')
   from `tag`
   where json_contains(`school`.tagId, json_array(tag.id))) as `tag`
from school
where json_contains(tagId,json_array(1))
limit 10

```

这样即可得到有1号标签的学校列表