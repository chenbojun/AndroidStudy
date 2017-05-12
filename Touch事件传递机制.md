#Tip3-MotionEvent-2017年3月20日 19:04
##MotionEvent
###简介：
记录用户在屏幕上的操作，包括单点触碰、多点触碰等，具体使用参考链接。
###参考资料：
[Android MotionEvent详解](http://www.jianshu.com/p/0c863bbde8eb)





#Tip4-Touch事件传递-2017年3月20日 19:06
##Touch事件传递：
###简介：
View：dispatchTouchEvent

ViewGroup：onInterceptTouchEvent

1）VIewGroup在dispatchTouchEvent函数中，先调用onInterceptTouchEvent判断是否需要拦截Touch事件（默认不拦截，可自定义），需要拦截则直接调用自身的touch处理函数；不需要拦截，则遍历内部View（分别调用子View的dispatchTouchView函数，若其中一个子View消费了TouchEvent，即dispatchTouchView返回true，则结束遍历）。

2）如果View设置了onTouchListener，则在View.dispatchTouchEvent中优先判断是否设置了onTouchListener并调用；然后，若onTouchListener返回true则不再调用View.onTouchEvent，否则调用View.onTouchEvent函数处理Touch事件；

3）View.onTouchEvent默认实现，若disable & clickable & longClickable，则直接返回true（直接消费掉Touch事件）；若enable & clickable & longClickable，则根据Touch事件类型触发mOnClickListener和mOnLongClickListener的回调（mOnLongClickListener回调返回为ture的情况下，若响应了长按事件则不再响应mOnClickListener）；

4）只要某个View消费了TOUCH_DOWN类型的事件，则后续的Touch事件继续传递给该View进行处理。