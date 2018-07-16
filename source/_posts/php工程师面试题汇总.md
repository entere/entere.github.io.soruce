---
title: php工程师面试题汇总
date: 2014-03-21 21:11:53
tags:
---

1.写一个函数,尽可能高效的,从一个标准 url 里取出文件的扩展名,例如:/abc/de/fg.php?id=1 需要取出 php 或 .php

答:我是直接用PHP内置函数搞定的,不重复造轮子,估计出题者也是想考察基础知识，主要是解析url和一个返回文件信息的函数,扩展:取得文件后缀名的多种方法;

<!-- more -->

代码如下:

```php

<?php
    /** by  */
    $url = "/abc/de/fg.php?id=1";
    $path = parse_url($url);
    echo pathinfo($path['path'],PATHINFO_EXTENSION);  //php
?> 

```

2.不用strrev函数如何实现字符串翻转？

代码如下:

```php

<?php
function myStrrev($str) {
    $arr = str_split($str);
    $len = count($arr);
    $newstr = "";
    for ($i=$len-1; $i >=0 ; $i--) { 
         $newstr .= $arr[$i];
    }
    return $newstr;
}
echo myStrrev("abcd");
?> 

```

3.用 PHP 实现双向队列

代码如下：

```php

<?php 
    class Deque{

        public $queue = array();

        /**
         * 尾部入对
         * @param [type] $value [description]
         */
        public function addLast($value){
            return array_push($this->queue,$value);
        }

        /**
         * 尾部出队
         * @return [type] [description]
         */
        public function removeLast(){
            return array_pop($this->queue);
        }

        /**
         * 头部入队
         * @param [type] $value [description]
         */
        public function addFirst($value){
            return array_unshift($this->queue, $value);
        }

        /**
         * 头部出队
         * @return [type] [description]
         */
        public function removeFirst(){
            return array_shift($this->queue);
        }

        /**
         * 清空队列
         * @return [type] [description]
         */
        public function makeEmpty(){
            unset($this->queue);
        }

        /**
         * 获取列头
         * @return [type] [description]
         */
        public function getFirst(){
            return reset($this->queue);
        }

        /**
         * 获取列尾
         * @return [type] [description]
         */
        public function getLast(){
            return end($this->queue);
        }

        /**
         * 获取长度
         * @return [type] [description]
         */
        public function getLength(){
            return count($this->queue);
        }
    }
?>


```

