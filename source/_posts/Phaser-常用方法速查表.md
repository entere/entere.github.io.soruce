---
title: Phaser 常用方法速查表
date: 2017-01-03 15:15:42
tags: [Phaser]
---


sprite.input.useHandCursor = true; // 鼠标移上去变手型
sprite.events.onInputDown.add(fn, this); //鼠标按下事件


text2.resolution = 2; //设置文字高分辨率，文字更清晰



1. 游戏画布的尺寸
var width = game.width,
height = game.height;

2. 中心点坐标
var game = new Phaser.Game(...);
var centerX = game.world.centerX,
centerY = game.world.centerY;

3. 随机坐标
var randomX = game.world.randomX,
randomY = game.world.randomY;

4. 精灵对象常用方法
var player = game.add.sprite(...);
//动画
player.animations.add(...);
player.animations.play('');
//倾斜
player.angle = 45;

5. 基本图形
var line = new Phaser.Line(0,0,120,120);
var circle = new Phaser.Circle(game.world.centerX,100,64);
var rect = new Phaser.Rectangle(x,y,width,height);

6. 判断PC或手机

if(game.device.desktop){
//desktop
}
else{
//mobile
}

7. 判断横屏还是竖屏
if(game.scale.isLandscape){
//横屏
game.scale.correct = true;
}
else{
//竖屏
game.scale.correct = false;
}


8. 设置全屏世界
var width = window.innerWidth 或 100%,
height = window.innerHeight 或 100%;


9. 定时器
game.time.events.loop(300,callback,this);


10. 拖曳其它方法

```javascript

var playerW = this.player.width;
//是否正在触摸
var touching = false;
//监听按下事件
game.input.onDown.add(function(pointer){
    //palyer.x是主角的横向中心，判断是指触摸点在主角的最左侧到最右侧的坐标范围内，
    //就是点击的是小人本身，未判断y坐标
    if(Math.abs(pointer.x - player.x) < player.width/2){
        touching = true;
    }
});
//监听离开事件
game.input.onUp.add(function(){
    touching = false;
});
//监听滑动事件
game.input.addMoveCallback(function(pointer,x,y,isTap){
    if(!isTap && touching){
        x = mid(x, playerW/2, gWidth - playerW/2);
        player.x = x;
    }
});

function mid(mid,min,max){
    if(typeof min === undefined || min == null){
        min = Number.NEGATIVE_INFINITY;
    }
    if(typeof max == undefined || max == null){
        max = Number.POSITIVE_INFINITY;
    }
    return Math.min(Math.max(min,mid),max);
}

```