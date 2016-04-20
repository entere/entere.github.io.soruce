---
title: jQuery同步Ajax带来的UI线程阻塞问题及解决办法
date: 2016-04-06 16:33:46
tags: [jQuery, Ajax]
---

> 遇到同样的问题，发现很多人都遇到过，记录下来

[转载自吕大豹](http://www.cnblogs.com/lvdabao/p/3744030.html)

俗话说不作死就不会死，今天作死了一回，写了一个比较二逼的函数，遇到了同步Ajax引起的UI线程阻塞问题，在此记录一下。

事情起因是这样的，因为页面上有多个相似的异步请求动作，本着提高代码可重用性的原则，我封装了一个名为getData的函数，它接收不同参数，只负责获取数据，然后把数据return。基本的逻辑剥离出来是这样的：

```javascript

function getData1(){
    var result;
    $.ajax({
        url : 'p.php',
        async : false,
        success: function(data){
            result = data;
        }
    });

    return result;
}
```

　　这里的ajax不能用异步的，否则函数返回时，result还未赋值，会出错。所以我加了async:false。看起来好像没什么问题。我调用这个函数可以正常的得到数据。

```javascript

$('.btn1').click(function(){
    var data = getData1();
    alert(data);
});
```

　　接下来，要加另外一个功能，由于ajax请求有一定的耗时，所以我需要在发出请求前页面有个loading效果，即显示一张“正在加载”的gif图片，想必大家也都见过。所以我的处理函数就变成了这样：

```javascript

$('.btn1').click(function(){
    $('.loadingicon').show();
    var data = getData1();
    $('.loadingicon').hide();
    alert(data);
});

```

　　请求之前显示loading图片，请求完成后把它隐藏。看起来也没什么问题。为了看清效果，我的p.php代码sleep了3秒，如下：

```php

<?php
sleep(3);
echo ('aaaaaa');
?>

```

　　但是我运行的时候问题出现了，我点击按钮并未像预想的那样出现这个loading图片，页面什么反应也没有。排除良久找到了原因，就在async:false这里。

　　浏览器的渲染(UI)线程和js线程是互斥的，在执行js耗时操作时，页面渲染会被阻塞掉。当我们执行异步ajax的时候没有问题，但当设置为同步请求时，其他的动作（ajax函数后面的代码，还有渲染线程）都会停止下来。即使我的DOM操作语句是在发起请求的前一句，这个同步请求也会“迅速”将UI线程阻塞，不给它执行的时间。这就是代码失效的原因。

setTimeout解决阻塞问题
　　既然明白了问题在哪里，我们就来针对性想办法。为了不让同步ajax请求阻塞线程，我想到了setTimeout，把请求的代码放到sestTimeout中，让浏览器重启一个线程来操作，不就解决问题了吗？于是乎，我的代码就变成了这样：

```javascript

$('.btn2').click(function(){
        $('.loadingicon').show();
        setTimeout(function(){
            $.ajax({
                url : 'p.php',
                async : false,
                success: function(data){
                    $('.loadingicon').hide();
                    alert(data);
                }
            });
        }, 0);
});
```


　　setTimeout的第二个参数设为0，浏览器会在一个已设的最小时间后执行。不管三七二十一先运行起来看看。

　　结果loading图片显示出来了，但是！！！图片怎么不动呢，我明明是一张动态gif图。这个时候我很快就想到了，虽然同步请求延迟执行了，但是它执行期间还是会把UI线程给阻塞。这个阻塞相当牛逼，连gif图片都不动了，看起来像一张静态图片一样。

　　结论很明显，setTimeout治标不治本，相当于把同步请求“稍稍”异步了一下，接下来还是会进入同步的噩梦，阻塞线程。方案失败。

是时候用Deferred了
　　jQuery在1.5版本之后，引入了Deferred对象，提供的很方便的广义异步机制。详情可参看阮一峰老师的这篇文章http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html。

　　于是我用Deferred对象改写了代码，如下：

```javascript

function getData3(){
        var defer = $.Deferred();
        $.ajax({
            url : 'p.php',
            //async : false,
            success: function(data){
                defer.resolve(data)
            }
        });
        return defer.promise();
}    

$('.btn3').click(function(){
        $('.loadingicon').show();
        $.when(getData3()).done(function(data){
            $('.loadingicon').hide();
            alert(data);
        });
});
```


　　可以看到我在ajax请求中去掉了async:false，也就是说，这个请求又是异步的了。另外请注意success函数中的这一句：defer.resolve(data)，Deferred对象的resolve方法可传入一个参数，任意类型。这个参数可以在done方法中拿到，所以我们异步请求来的数据就可以以这样的方式来返回了。

　　至此，问题得到了解决。Deferred对象如此强大且方便，我们可以好好利用它。

　　我的全部测试代码如下，有意的同学可以拿去测一下：

```html

<button class="btn1">async:false</button>
<button class="btn2">setTimeout</button>
<button class="btn3">deferred</button>
    
<img class="loadingicon" style="position:fixed;left:50%;top:50%;margin-left:-16px;margin-top:-16px;display:none;" src="loading2.gif" alt="正在加载" />
<script>

    function getData1(){
        var result;
        $.ajax({
            url : 'p.php',
            async : false,
            success: function(data){
                result = data;
            }
        });

        return result;
    }

    $('.btn1').click(function(){
        $('.loadingicon').show();
        var data = getData1();
        $('.loadingicon').hide();
        alert(data);
    });


    
    $('.btn2').click(function(){
        $('.loadingicon').show();
        setTimeout(function(){
            $.ajax({
                url : 'p.php',
                async : false,
                success: function(data){
                    $('.loadingicon').hide();
                    alert(data);
                }
            });
        }, 0);
    });



    function getData3(){
        var defer = $.Deferred();
        $.ajax({
            url : 'p.php',
            //async : false,
            success: function(data){
                defer.resolve(data)
            }
        });
        return defer.promise();
    }    

    $('.btn3').click(function(){
        $('.loadingicon').show();
        $.when(getData3()).done(function(data){
            $('.loadingicon').hide();
            alert(data);
        });
    });</script>

 ```

PS:Firefox有做优化？

　　上述问题在chrome和IE9中测试结论一致。但是我在Firefox中测试时，同步ajax并未阻塞掉UI线程，也就是说这个问题根本不存在。我用其他代码做了测试，在Firefox中js线程确实是会阻塞UI线程，这个没有疑问。那可能的一个猜测就是Firefox对同步ajax做了优化，事实到底是什么，我暂未得知。有高人知道还请指点。


