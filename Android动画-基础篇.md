#Tip18-动画研究-2017年3月23日 12:53
##1、View动画：
###1）简介：
仅对View的残影进行处理，View本身位置不变，适用于移动中不需要交互的View动画变换。
###2）4种变化：
平移、缩放、旋转、透明度
###3）重要属性：
interpolator（尝试自定义）、shareInterpolator、duration、fillAfter
###4）AnimationUtils:

	loadAnimation(Context, R.anim)加载动画xml文件
	makeXX（）：提供方便的方法快速生成默认的View显示/隐藏动画
###5）Animation：
   支持设置AnimationListener，监听动画的开始结束等。
###6）Transformation
###7）使用方法：
      * XML文件声明动画：
      AnimationUtils.loadAnimation(Context, R.anim)得到Animation，然后调用View.startAnimation(Animation)即可。
      * Java代码：
      AnimationSet、AlphaAnimation、ScaleAnimation、TranslateAnimation、RotateAnimation
      * API14以后（View.animate（）获取ViewPropertyAnimator可以快捷进行各种动画）





##2、View动画的特殊使用场景：
###1）LayoutAnimation：
      * XML实现：
                      <layoutAnimation 
                                android : delay 每个动画延迟时间
                                android : animationOrder 子元素的动画开始顺序
                                android : animation 子元素的出场动画> 
                      在ViewGroup中声明属性android：layoutAnimation即可
      * JAVA实现：
                     LayoutAnimationController =  AnimationUtils.loadLayoutAnimation(Context context, @AnimRes int id)
                     LayoutAnimationController =  new LayoutAnimationController(Animation)
                     ViewGroup.setLayoutAnimation(LayoutAnimationController controller)
##2）Activity的切换效果：
      Activity.overridePendingTransition()
###3）Fragment的切换效果：
	FragmentTransaction.setCustomAnimations(
	            @AnimatorRes int enter,
	            @AnimatorRes int exit, 
	            @AnimatorRes int popEnter, 
	            @AnimatorRes int popExit)

##2、帧动画
	1)XML创建：<animation-list>
	2)View.setBackgroundResource(id)
	3)View.getBackground()->AnimationDrawable
	4)AnimationDrawable.start()

##3、属性动画：
###1)简介：
   属性动画可以对任何对象作动画，甚至可以没有对象。
###2)重要类：
   AnimatorInflater、ValueAnimator、ObejctAnimator、AnimatorSet
###3）使用方法：
     XML：
             关注一些重要的属性
     JAVA代码：
             Animator animator = AnimatorInflater.loadAnimator(Context context, @AnimatorRes int id)
             animator.setTarget(Obejct)
             animator.start()
###4）动画监听：
     Animator.AnimatorListener 监听动画的开始、结束、取消、重复
     Animator.AnimatorUpdateListener 监听动画的过程完成度
###5）内部计算类
####插值器（Interpolator）：
计算随时间流逝，动画的完成百分比
####估值器（TypeEvaluator）：
计算百分比变化，对应的属性值（目前系统提供int、float、color的默认实现，其他类型的属性需要自定义实现）

###6）Todo：属性动画的工作原理（参考Android开发艺术探索）
###7）注意：

	* 对Object的属性abc作动画，必须满足：
	A、object必须要提供setAbc方法，如果动画没有传递初始值，还得提供getAbc方法，因为系统要去取abc属性的初始值（否则直接crash）
	B、object的setAbc方法能通过某种方法反映出来，比如会带来UI变化之类的，否则（看起来动画无效果）
	
	* 解决方案：
	A、有权限的情况下，给对象加上get/set函数；
	B、用一个类来包装原始对象，间接提供get／set方法；
	C、使用ValueAnimator，监听动画过程，自己实现属性的改变。

##4、Todo：Material风格的Transition，Scene实现过渡效果
###1)Transition、TransitionManager
###2)Scene
###3)ActivityOptions
###4)Activity中提供的动画相关方法
###5)Window中提供的动画相关方法
###参考资料：
android.transition包

[https://developer.android.com/training/transitions/index.html](https://developer.android.com/training/transitions/index.html)

##5、使用动画的注意事项：
###1）OOM问题：
帧动画
###2）内存泄漏问题：
主要是无限循环的属性动画（Activity退出时，及时停止）
###3）View动画问题：
仅对View的影像作动画，通过view.clearAnimation()清除效果。
###4）不要使用px：
尽量使用dp，防止在不同设备中的效果不同。
###5）硬件加速？？？使得动画更加流畅！
  （了解什么情况下使用硬件加速，不能盲目使用）
    View.setLayerType().   / API14: View.animate().withLayer()
    
##参考资料：
[Android Developer 培训-动画篇](https://developer.android.com/training/building-graphics.html)

[Android Developer API指南-动画篇](https://developer.android.com/guide/topics/graphics/index.html)

[Android中的动画完全总结(Android Developer官方翻译版)](http://blog.csdn.net/baidu_26994091/article/details/51674951)

《Android开发艺术探索》