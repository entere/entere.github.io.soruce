---
title: 如何在生产环境选择composer包版本
date: 2016-08-25 23:47:23
tags: [PHP, Composer]
---


相信 Composer 对你来说已经相当熟悉了，不过对于包的版本，我觉得应该还有不少同学不是那么清楚各种写法到底是啥意思。

<!-- more -->

语义化版本

首先，我们来了解一个东西：语义化版本

版本格式：主版本号.次版本号.修订号，版本号递增规则如下： 主版本号：当你做了不兼容的 API 修改， 次版本号：当你做了向下兼容的功能性新增， 修订号：当你做了向下兼容的问题修正。 先行版本号及版本编译信息可以加到“主版本号.次版本号.修订号”的后面，作为延伸。

语义化版本一般我们使用 3 个部分来表示一个版本，例如：1.4.23，1 为主版本号，4 为次版本号，23 为修订号或者补丁版本号。当然你肯定也见过 1.0.0-alpha1 这样在后面添加修饰符号来表示先行版本。

那在 composer 使用过程中我们通常会有这几种写法：

# 不限定版本

极不推荐这样玩哦

使用 * 号来表示版本的时候，composer 会根据你配置中的 minimum-stability 的值情况来决定安装最新的 dev 还是 stable 版本。 比如：

```
"require": {
    "overtrue/wechat": "*"
}
```

根据上面语义化版本的定义，这样写就相当于允许大版本的安装，那你的代码在 composer 更新依赖后可能就跑不起来了（如果第三方包作者做了大版本更新）。

# 使用 dev- 前缀加分支名

我们在自己开发一个包的时候，经常会用 dev-master 或者 dev-develop 来指定版本，它表示使用该分支下最新的提交。

比如：

```
"require": {
    "overtrue/wechat": "dev-master"
}
```

这个也是不推荐在生产环境使用的，因为它其实与 * 没有太大的差别，不过 * 在 minimum-stability 为 stable 时是安装最新的稳定版。但是二者都无法保证 API 兼容性。

# 使用 ~ 约束符锁定小版本的方式

这种方式比较常用，也是比较安全的，比如我们希望安装 >= 1.2 并且 < 2.0 的版本时，根据语义化版本的定义，次版本号的变化是新增功能，所以 API 是稳定的，也就是可以安全更新的。 你可以写成：

```
"require": {
    "overtrue/wechat": "~1.2"
}
```

如果你希望次版本都不要更新，只允许修订版本（补丁版本）的变化，>= 1.1.15 并且 < 1.2.0，则写成：

```
"require": {
    "overtrue/wechat": "~1.1.15"
}

```

所以，你应该看出规律了，~ 的作用是允许表达式中最后一位变到最大值，~1.1 表示可以为 大于等于 1.1 的任何版本，比如 1.1.0、1.2.0、1.3.5 、1.99.9999、 1.9999.999999 都可以安装，但是不能安装 2.0.0， 同理，~1.1.2 表示 大于等于 1.1.2 的任何版本，比如 1.1.2、1.1.3、1.1.99、 1.1.9999 都可以安装。

# 使用 ^ 约束符锁定大版本

上面 ~ 表示最后一位可变，前面几位都不可变，那 ^ 的作用不一样的是：^ 锁定不允许变的第一位，其实学过正则的同学都知道 ^ 表示起始，^a 表示以 a 开头的全部。

所以， ^1.2 表示任意大于等于 1.2 的 1.x.x 版本，比如 1.2.0、1.2.1、1.3.0、1.9.99999 等。只要前面的 1 并且大于 ^ 后面指定的 1.2 都满足条件。

# 锁定版本范围

有时候我们的使用场景要求只能安装某些版本范围内的时候，可以使用 >、<、>=、<=、| 这些符号来组合，比如：>= 1.3 <1.6、>=1.3 | >=1.7 、3.0|4.0 等。这样的使用场景并不多，根据你的情况来调整用法就好。

# 最后就是使用具体版本号

使用 =1.2.34 或者 1.2.34 都是指定了具体的版本号， composer 不会考虑检查新版本来安装。

注意

如果你的版本是 1.0 以下，0.0.1，0.9.99999 等这样的版本的时候， ^ 的作用与 ~ 一样，也就是说：

^0.0.3 表示：>=0.0.3 < 0.0.4

所以需要注意这个问题，之所以这样设计是有原因的：主版本号为零（0.y.z）的软件处于开发初始阶段，一切都可能随时被改变。这样的公共 API 不应该被视为稳定版。

所以不要掉进这个坑哦。

总结

无论你是包的作者，还是使用者，正确使用版本是非常重要的，尤其对于有一定使用量的包作者来讲，严格遵守语义化版本的规范是对你的用户负责。最后引入 semver.org 官网的一句话：

记住， 语义化的版本控制就是透过版本号的改变来传达意义。若这些改变对你的使用者是重要的，那就透过版本号来向他们说明。

[转自overtrue.me](http://overtrue.me/articles/2017/08/about-composer-version-constraint.html)

