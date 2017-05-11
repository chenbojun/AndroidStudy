#AppAarlayout


##官方介绍

```java
/**
 * AppBarLayout is a vertical {@link LinearLayout} which implements many of the features of
 * material designs app bar concept, namely scrolling gestures.
 * <p>
 * Children should provide their desired scrolling behavior through
 * {@link LayoutParams#setScrollFlags(int)} and the associated layout xml attribute:
 * {@code app:layout_scrollFlags}.
 *
 * <p>
 * This view depends heavily on being used as a direct child within a {@link CoordinatorLayout}.
 * If you use AppBarLayout within a different {@link ViewGroup}, most of it's functionality will
 * not work.
 * <p>
 * AppBarLayout also requires a separate scrolling sibling in order to know when to scroll.
 * The binding is done through the {@link ScrollingViewBehavior} behavior class, meaning that you
 * should set your scrolling view's behavior to be an instance of {@link ScrollingViewBehavior}.
 * A string resource containing the full class name is available.
 *
 * <pre>
 * &lt;android.support.design.widget.CoordinatorLayout
 *         xmlns:android=&quot;http://schemas.android.com/apk/res/android&quot;
 *         xmlns:app=&quot;http://schemas.android.com/apk/res-auto&quot;
 *         android:layout_width=&quot;match_parent&quot;
 *         android:layout_height=&quot;match_parent&quot;&gt;
 *
 *     &lt;android.support.v4.widget.NestedScrollView
 *             android:layout_width=&quot;match_parent&quot;
 *             android:layout_height=&quot;match_parent&quot;
 *             app:layout_behavior=&quot;@string/appbar_scrolling_view_behavior&quot;&gt;
 *
 *         &lt;!-- Your scrolling content --&gt;
 *
 *     &lt;/android.support.v4.widget.NestedScrollView&gt;
 *
 *     &lt;android.support.design.widget.AppBarLayout
 *             android:layout_height=&quot;wrap_content&quot;
 *             android:layout_width=&quot;match_parent&quot;&gt;
 *
 *         &lt;android.support.v7.widget.Toolbar
 *                 ...
 *                 app:layout_scrollFlags=&quot;scroll|enterAlways&quot;/&gt;
 *
 *         &lt;android.support.design.widget.TabLayout
 *                 ...
 *                 app:layout_scrollFlags=&quot;scroll|enterAlways&quot;/&gt;
 *
 *     &lt;/android.support.design.widget.AppBarLayout&gt;
 *
 * &lt;/android.support.design.widget.CoordinatorLayout&gt;
 * </pre>
 *
 * @see <a href="http://www.google.com/design/spec/layout/structure.html#structure-app-bar">
 *     http://www.google.com/design/spec/layout/structure.html#structure-app-bar</a>
 */
```

1. 根据以上说法，AppBarLayout通过xml方法或代码方法设置scrollFlags参数，控制自身的scroll行为：

	```java
	 * <p>
	 * Children should provide their desired scrolling behavior through
	 * {@link LayoutParams#setScrollFlags(int)} and the associated layout xml attribute:
	 * {@code app:layout_scrollFlags}.
	 * <p>
	```

2. AppBarLayout必须作为Coordinatorlayout的直接子控件，才能发挥其特征；
3. AppBarLayout另外还需要指定协调的滑动控件（一般为NestedScrollView），设置其behavior为AppBarLayout.ScrollingViewBehavior的实例。

	说明：从而NestedScrollView通知父类CoordinatorLayout自身滑动情况-》父类CoordinatorLayout通知AppBarLayout何时进行scroll-》AppBarLayout根据子控件设置的scrollFlag参数控制子控件的scroll

##重点：scrollFlags参数
（1） scroll:值设为scroll的View会跟随滚动事件一起发生移动。

什么意思呢？简单的说，就是当指定的ScrollView发生滚动时，该View也跟随一起滚动，就好像这个View也是属于这个ScrollView一样。

（2） enterAlways:值设为enterAlways的View,当ScrollView往下滚动(scrollView的content发生滚动 或 scrollView本身位置往下滚的)时，该View都会直接往下滚动。

（3） exitUntilCollapsed：值设为exitUntilCollapsed的View，当这个View要往上逐渐“消逝”时，会一直往上滑动，直到剩下的的高度达到它的最小高度（设置minHeight）后，再响应ScrollView的内部滑动事件。

（4） enterAlwaysCollapsed：是enterAlways的附加选项，**一般跟enterAlways一起使用**，它是指，View在往下“出现”的时候，首先是enterAlways效果，当View的高度达到最小高度（设置minHeight）时，View就暂时不去往下滚动，直到ScrollView滑动到顶部不再滑动时，View再继续往下滑动，直到滑到View的顶部结束。

参考资料：
http://www.jianshu.com/p/d159f0176576