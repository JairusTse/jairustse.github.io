---
layout: post
title: compileSdkVersion、minSdkVersion和targetSdkVersion
category: android
tags: [kotlin,android]
---

compileSdkVersion、minSdkVersion和targetSdkVersion

**常见的配置如下：**

```
android {
    compileSdkVersion 30
    buildToolsVersion "30.0.3"

    defaultConfig {
        minSdkVersion 19
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"
    }
}
```

## minSdkVersion

最好理解，最低支持的Android SDK版本。比minSdkVersion还低的版本不好意思，安装不了。还有个对应的最高版本maxSdkVersion，基本不用，因为系统升级后可能会出现APP不能用的情况。

## compileSdkVersion

**编译期间**用的Android SDK版本（**只编译时用，不会被打包进APK里**）

**总是推荐用最新的**，可能会出现编译警告、编译错误，但是不会被打包到apk里面（因为实际上用的是targetSdkVersion的版本），但是提前用最新的SDK可以避免使用弃用的API（提前修复警告），为以后的targetSdkVersion升级做好准备。

`buildToolsVersion` 是编译工具集的版本号，所以跟 `compileSdkVersion` 放在一起，大版本号也要保持一致（比如上面的 `compileSdkVersion` 是 `30`，`buildToolsVersion` 是 `30.0.3`）。

## targetSdkVersion

targetSdkVersion决定了**运行期间**使用哪个Android SDK版本的特性。（**在用户手机上运行apk**）

targetSdkVersion 是 Android 系统提供前向兼容的主要手段。

**三者合理的关系：**
> minSdkVersion <= targetSdkVersion <= compileSdkVersion
> 
**三者理想的关系：**
> minSdkVersion <= targetSdkVersion == compileSdkVersion


### 通过图片来理解targetSdkVersion的工作原理

#### 一般我们开发时各个 `Sdk Version` 的版本情况：

![](https://gitee.com/jairustes/blogimages/raw/master/img/sdk_version_1.jpg)

- 比 `targetSdkVersion` **高**的SDK，统一用`targetSdkVersion`版本的方法处理；
- 比 `targetSdkVersion` **低**的SDK，需要自己用 `if...else` 兼容各个版本。



#### 如果要升级 `targetSdkVersion` ：
![](https://gitee.com/jairustes/blogimages/raw/master/img/sdk_version_2.jpg)


- 升级 `targetSdkVersion` 能使APP在新SDK中有新的表现；
- 新SDK中的API有改动的话，要增加 `if...else` 的分支；
- 为了兼容低版本，弃用的方法仍然会被保留。







完毕。