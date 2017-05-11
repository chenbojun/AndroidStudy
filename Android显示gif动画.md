Android官方并未提供直接显示gif图片资源的功能，我们可以通过Fresco图片加载库实现。Fresco的功能非常强大，建议有兴趣的同学参考官网了解更多细节，以下只针对“加载本地gif动画功能”：

1.  添加Gradle依赖：

    ```java
    dependencies {
        // fresco库依赖
        compile 'com.facebook.fresco:fresco:1.0.1'
        // 支持GIF动图，需要添加
        compile 'com.facebook.fresco:animated-gif:1.0.1'
    }
    ```
    
2.  自动播放本地gif动画：

	* xml文件
    
    ```java
    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/ic_arrow"
        android:layout_width="15dp"
        android:layout_height="15dp"
        android:layout_marginBottom="5dp"/>
    ```
	* java代码
	
	```java
	     // 设置箭头gif
        final SimpleDraweeView simpleDraweeView = (SimpleDraweeView)findViewById(R.id.ic_arrow);
        Uri uri = new Uri.Builder()
                .scheme(UriUtil.LOCAL_RESOURCE_SCHEME)
                .path(String.valueOf(R.drawable.ic_bill_guide_arrow_gif))
                .build();
        DraweeController controller = Fresco.newDraweeControllerBuilder()
                .setUri(uri)
                .setAutoPlayAnimations(true)
                .build();
        simpleDraweeView.setController(controller);
	```
3.  手动控制gif动画的播放|停止

	以上代码实现为加载本地res中的drawable资源文件，且设置为自动播放模式。如果需要手动控制gif动画的开始|结束，需要通过**android.graphics.drawable.Animatable**控制！
	
	```java
	ControllerListener controllerListener = new BaseControllerListener<ImageInfo>() {
	    @Override
	    public void onFinalImageSet(String id, @Nullable ImageInfo imageInfo, @Nullable Animatable anim) {
	        if (anim != null) {
	          // 图片资源加载完成的回调函数
	          anim.start();
	        }
	    }
};

	Uri uri;
	DraweeController controller = Fresco.newDraweeControllerBuilder()
	    .setUri(uri)
	    .setControllerListener(controllerListener)
	    // other setters
	    .build();
	mSimpleDraweeView.setController(controller);
	```
	DraweeController向外提供Animatable的接口，当Animatable不为null，可以直接通过Animatable控制gif动画的播放。
	
	```java
	Animatable animatable = mSimpleDraweeView.getController().getAnimatable();
	if (animatable != null) {
	  	animatable.start();
	  	// later
	  	animatable.stop();
	}
	```
	
4.  参考资料：

	http://frescolib.org/docs/index.html
	
	http://frescolib.org/docs/animations.html#playing-animations-manually
	