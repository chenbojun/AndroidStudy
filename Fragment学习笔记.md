#Tip11-Fragment使用技巧-2017年3月21日 19:42
##1）添加Fragment前，判空：
当Activity因为配置发生改变（屏幕旋转）或者内存不足被系统杀死，造成重新创建时，我们的fragment会被保存下来，但是会创建新的FragmentManager，新的FragmentManager会首先会去获取保存下来的fragment队列，重建fragment队列，从而恢复之前的状态。
##2）Fragment间的数据传递：
###不同Activity：
通过Fragment.startActivityForResult()启动另外的Activity、onActivityResult处理返回的intent，通过Fragment.getActivity().setResult()设置返回数据；
###相同的Activity:
通过新创建的Fragment.setTargetFragment（），则新的Fragment需要返回数据时，直接手动调用getTargetFragment.onActivityResult()获取返回数据。
##3）FragmentPagerAdapter vs FragmentStatePagerAdapter
前者适用于pages较少情况；后者适用于pages较多情况。原因参考实现原理。

##4）了解Activity和Fragment的生命周期（很重要，在合适的生命回调函数中处理特定事）
典型bug：FragmentActivity被destroy，重新恢复的时候，内部fragment没有被销毁？
##5）了解FragmentTransaction的add、remove、replace、hide、show、attach、detach等函数的作用，以及何种情况应该使用哪种函数！！！
##6）Fragment的回退栈：
调用FragmentTransaction.addToBackStack()，可保存当前事务，当点击返回时，恢复该事务。

注意点：没有添加到backStack中的fragment，当调用remove时，会销毁整个fragment；添加到backStack的情况下，只会销毁fragment的内部视图。

如果希望跳转到另外一个fragment，返回后保存试图，则前者调用hide（）。

参考资料：http://blog.csdn.net/lmj623565791/article/details/42628537
##7）setRetainInstance
设置当Activity被异常关闭时，保留Fragment实体：可利用无界面的fragment用于储存数据
##8）解决异步加载数据，同时动态更新ProgressDiaglog进度的情况下，如何应对Actiivty的异常关闭（屏幕旋转 | 应用切换至后台时由于内存不足被杀死）？
利用fragment保存异步线程，异步线程刷新UI时，同时保留进度数据于线程中，且根据Activity是否active才刷新UI；

Activity的onCreate函数中，获取保存异步线程的fragment，如果为空，则重新开启线程加载数据和创建对话框显示进度；如果不为空，则根据异步线程中的进度，创建或更新ProgressDialog！