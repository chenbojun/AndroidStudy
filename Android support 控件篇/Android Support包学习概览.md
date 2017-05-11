#Android Support包学习笔记

##support-v4

##support-v7

###v7 palette 库
v7 palette 支持库包含 Palette 类，此类让您可以从图像中抽取突出颜色。例如，音乐应用可以使用 Palette 对象从专辑封面抽取主要颜色，然后使用这些颜色创建一个色彩协调的歌曲标题卡。

此库的 Gradle 构建脚本依赖关系标识符如下所示：

	com.android.support:palette-v7:24.2.0


##support-design

主要提供Material Design风格的控件。核心类CoordinatorLayout、CoordinatorLayout.Behavior。一般配合CoordinatorLayout布局，子View实现behavior，从而达到子Views间协调的作用。

###AppbarLayout

###CollapsingToolbarLayout

###CoordinatorLayout

###TabLayout

###BottomSheetView

###FloatingActionButton

###SnackBar

###NavigationView

###Transition转场动画

##ConstraintLayout???


##参考资料
[1]官方简介：
https://developer.android.com/topic/libraries/support-library/features.html

[2]面对开发者的Material 
Design：https://developer.android.com/training/material/index.html