---
title: 【每周一文】Laravel小伎俩Blade模板之forelse的使用
date: 2017-04-11 22:06:51
tags: laravel
---

在 Laravel 模板中，经常用 foreach 循环集合，在循环之前一般需要判断集合是否有值

```php

<?php
@if ($users->count())
    @foreach ($users as $user)
        <li>This is user {{ $user->name }}</li>
    @endforeach
@else
  <p>No users.</p>
@endif

?>

```
<!-- more -->

以上代码是不是很熟悉？

其实，Blade 模板有更为简洁的使用方式：

```php

<?php

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users.</p>
@endforelse

?>

```

细心的同学会发现，Laravel 文档中 blade 说明中已经把 forelse 列出来喽～

```php
<?php

@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse

@while (true)
    <p>I m looping forever.</p>
@endwhile

?>

```



