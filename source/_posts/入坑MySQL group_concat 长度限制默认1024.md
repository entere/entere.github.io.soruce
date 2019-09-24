---
title: 入坑MySQL group_concat 长度限制默认1024
date: 2019-09-24 21:45:54
tags: [MySQL]
---

# 问题描述

开发过程中发现部分数据量稍大的业务 用group_concat出错，sql 如下

```sql

select
        challenge_id,tilemap_id,name,intro,chapter_id,
        IFNULL(solution_tips,''),fail_again_tips,suggest_function,IFNULL(suggest_solution,''),suggest_line_number,pass_conditions,
        is_show_grid,is_show_mirror,is_mouse,goal,reward_code_coin,
        reward_experience,reward_experience_unicorn,reward_experience_york,reward_experience_hygeia,reward_items_boy,
        reward_items_girl,reward_cert,npcs_in_backpack,npcs_in_bosswar,IFNULL(bosswar_url,''),
        IFNULL(video_url,''),IFNULL(background_music,''),IFNULL(gametv,''),IFNULL(audio,''),
        (select concat('[',group_concat('{"command_id":',game_commands.command_id,',"name":"',game_commands.name,'","cover_url":"',game_commands.cover_url,'","description":"',game_commands.description,'","example":"',game_commands.example,'","code":"',game_commands.code,'"}'),']') from game_commands where json_contains(game_challenges.command_ids, json_array(game_commands.id))) as command_ids from game_challenges where challenge_id = 47864220

```

# 问题解决

在MySQL中 group_concat 长度限制默认1024，如果大于1024会被截断

<!-- more -->

在MySQL配置文件(my.ini)中默认无该配置项，使用默认值时，值为1024，可在客户端执行下列语句修改：

```sql

SET GLOBAL group_concat_max_len = 1024;

```

该语句执行后重启客户端即可用语句：

```sql
show variables like "group_concat_max_len";
```

<!-- more -->

查看到已经修改成功，该语句在执行后，mysql重启前一直有作用，但mysql一旦重启，则会恢复默认的设置值。

要彻底修改，在MySQL配置文件(my.ini)中加上

```bash
group_concat_max_len = -1 -1为最大值或填入你要的最大长度

```

并重启mysql

在客户端执行语句：


```sql
show variables like "group_concat_max_len";
```

如果为自己修改的值或4294967295（设置为-1时）则修改正确。





