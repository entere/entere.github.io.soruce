---
title: 【每周一文】Android中如何给layout资源文件分类
date: 2017-06-25 22:27:39
tags: [Android, 资源文件]
---

> 在开发过程中，layout 中资源文章数量过多，查找起来就不那么方便了，那么，如何给资源文件进行分类呢？

<!-- more -->

# 一、在 layout 文件夹下建子目录

比如需要创建如下目录：

> 把用户相关的布局文件放在用户目录下，那么就需要建这样的目录 res>>layout>>user>>layout

> 把钱包相关的布局文件放在钱包目录下，那么就需要建这样的目录 res>>layout>>wallet>>layout

请注意不要忘记创建 layout 目录

# 二、配置 app/build.gradle(Module:app)

在app/build.gradle 文件中  android{} 中添加如下配置：

```

sourceSets {
        main {
            res.srcDirs =
                    [
                            'src/main/res/layout/user',
                            'src/main/res/layout/wallet',
                            'src/main/res'
                    ]
        }
    }

```

完整的 build.gradle 文件看起来像是这样的

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    defaultConfig {
        applicationId "me.entere.myapp"
        minSdkVersion 15
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    sourceSets {
        main {
            res.srcDirs =
                    [
                            'src/main/res/layout/user',
                            'src/main/res/layout/wallet',
                            'src/main/res'
                    ]
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })

    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.android.support:design:25.3.1'
    compile 'com.android.support:cardview-v7:25.3.1'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
    testCompile 'junit:junit:4.12'
}


```

# 三、把布局文件移动到对应目录

你可以把布局文件移动到对应目录了，这样layout目录看起来是不是干净许多～

