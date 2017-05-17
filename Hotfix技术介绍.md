#1.热补丁技术
##1.1 热补丁技术概览
文章总结了当前[主流的热补丁技术](https://juejin.im/entry/57964afba34131005a96a5b8)，全面介绍了各项热补丁技术的优缺点，及其应用场景。

##1.2 原理解析

###Native方案：

####（1）[AndFix原理解析 ](http://w4lle.github.io/2016/03/03/Android%E7%83%AD%E8%A1%A5%E4%B8%81%E4%B9%8BAndFix%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90/)

###Java方案：
####（1）[Tinker原理解析](http://w4lle.github.io/2016/12/16/tinker/)
####（2）[Robust方案](http://tech.meituan.com/android_robust.html)：借鉴自Instant run  

##1.3 热补丁技术基础
Android类加载机制是插件化和java层热补丁技术的基础，文章从源码入手，详细介绍了[Andoid类加载机制](http://www.jianshu.com/p/3afa47e9112e)。
