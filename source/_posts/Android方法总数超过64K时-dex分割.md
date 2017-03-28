---
title: 'Android方法总数超过64K时,dex分割'
date: 2015-10-25 21:54:47
tags: [Android]
---


项目在编辑的过程中报如下错误

<!-- more -->

```
Error:The number of method references in a .dex file cannot exceed 64K. Learn how to resolve this issue at https://developer.android.com/tools/building/multidex.html
```
根据Error中提供的[文档](https://developer.android.com/tools/building/multidex.html)，按如下方法分割dex

打开module:app的 build.gradle

1.在dependencies 中添加
`compile 'com.Android.support:multidex:1.0.0'`

```
dependencies {
    
    compile 'com.android.support:multidex:1.0.0'
}
```
2.在 defaultConfig 中添加

`multiDexEnabled true`

```
defaultConfig {
    
    multiDexEnabled true
}
```

3.在 AndroidManifest.xml 中的  application 标签中添加 :

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.vieboo.test">
     <application
        android:name="android.support.multidex.MultiDexApplication"
        
    </application>
</manifest>
```


再次编辑 ok~