---
title: 【每周一文】Neo4j教程四：用Laravel实现关系图谱
date: 2016-05-31 22:40:43
tags: [图形数据库, Neo4j]
---

# 前言

项目需要从零开始构建用户的关系图谱。

而以 MySQL 为代表的关系型数据库和以 MongoDB 为代表的 NoSQL 都不适用于有实时价值的数据关系。经过一系列调研，我们从众多图数据库中选中了 Neo4j 来构建用户图谱。

项目用到了 Laravel 框架（Laravel 为 WEB 艺术家创造的 PHP 框架），Neo4j 提供了 PHP 的 SDK，我们选用了 [GraphAware Neo4j PHP Client](https://github.com/graphaware/neo4j-php-client) 

<!-- more -->

# 安装 neo4j-php-client

在Laravel 项目根目录执行如下安装命令

```
composer require graphaware/neo4j-php-client:^4.0

```

# 导入数据

使用laravel 命令 来导入数据，生成命令文件：MigrateUser2Neo4j.php

```
php artisan make:console MigrateUser2Neo4j

```

在 MigrateUser2Neo4j.php 中编写导入数据的代码，如下

```php

<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use GraphAware\Neo4j\Client\ClientBuilder;
use DB;

class MigrateUser2Neo4j extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'noe4j:user {type : node or relation}';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = '把用户关系导入到neo4j';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $type = $this->argument('type');
        if($type == 'node') {
            $this->migrateNode();
        } elseif ($type == 'relation') {
            $this->migrateRelation();
        } else {
            echo "flase";

            //$this->migrateNode();
        }
        
        
        
    }

    /**
     * 导入节点，也就是用户
     * @return [type] [description]
     */
    public function migrateNode() 
    {
        $client = ClientBuilder::create()
            ->addConnection('default', 'http://neo4j:neo4j_2017test@219.238.250.46:7474') // Example for HTTP connection configuration (port is optional)
            //->addConnection('bolt', 'bolt://neo4j:password@localhost:7687') // Example for BOLT connection configuration (port is optional)
            ->build();

        $ids = DB::connection('mongodb')
        ->table('users')
        ->select('uid','name','nickname','avatar')
        ->where('uid','>',63459)
        ->where('group_id',200)

        ->orderBy('uid', 'asc')
        ->get();
        foreach ($ids as $key => $value) {
            $query = 'create (p'.$value['uid'].':Person {name: "'.$value['name'].'",nickname:"'.$value['nickname'].'",avatar:"'.$value['avatar'].'",uid:'.intval($value['uid']).'});';
            $result = $client->run($query);
            \Log::info($query."\r\n");
        }

    }

    /**
     * 导入关系 也就是用户关系
     */

    public function migrateRelation() {
        $client = ClientBuilder::create()
            ->addConnection('default', 'http://neo4j:neo4j_2017test@219.238.250.46:7474') // Example for HTTP connection configuration (port is optional)
            //->addConnection('bolt', 'bolt://neo4j:password@localhost:7687') // Example for BOLT connection configuration (port is optional)
            ->build();

        $ids = DB::connection('mongodb')
            ->table('users_follow')
            ->select('fans_uid','friend_uid')
            ->where('fans_uid','>',604970024)
            ->orderBy('fans_uid', 'asc')
            //->limit(100)
            ->get();
        //print_r($ids);exit;
        foreach ($ids as $key => $value) {

            $query = 'match (p'.$value['friend_uid'].'),(p'.$value['fans_uid'].') where p'.$value['friend_uid'].'.uid='.intval($value['friend_uid']).' and p'.$value['fans_uid'].'.uid='.$value['fans_uid'].' create (p'.$value['friend_uid'].')-[:HAS_FANS]->(p'.$value['fans_uid'].');';
            $result = $client->run($query);
            \Log::info($query."\r\n");


        }



        
    }
}


?>

```

执行如下命令导入节点数据

```
php artisan noe4j:user node

```

执行如下命令导入关系数据

```
php artisan noe4j:user relation

```



# Neo4j 应用

应用一：梳理用户之间的关注关系

比如：把用户关系导入到 Neo4j 中，查看一下 方兴东 用户关系，具体导入代码：




