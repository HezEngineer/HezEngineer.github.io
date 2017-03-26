---
title: TextView的drawable属性
date: 2016-04-23 08:52:43
tags: android布局技巧 TextView
categories: android布局技巧
---
# what
实现Icon+文字在一个视觉元素的需求;
### drawable属性
* android:drawableLeft 图片在文字的左侧  
* android:drawableTop 图片在文字的上方  
* android:drawableRight 图片在文字的右侧  
* android:drawableBottom 图片在文字的下方  
* android:drawableStart 图片在文字的开始处 (API14才有,17生效)  
* android:drawableBottom  图片在文字的结尾 (API14才有,17生效)  
* android:drawablePadding 设置文字与图片之间的距离；  


# how
		<TextView
      	 	  android:id="@+id/plus_tv"
				 android:layout_width="400dp"
            android:layout_height="wrap_content"
       		 android:text="加分"
        	 android:textColor="@android:color/holo_blue_dark"  
        	 android:textSize="36sp"
        	 android:textStyle="bold"
        	 android:typeface="serif"  
       		 style="?buttonBarButtonStyle"
    		   android:gravity="center_horizontal"
        	 android:drawableBottom="@drawable/ic_sentiment_dissatisfied_black_48dp"
        	 android:drawablePadding="20dp"
        	 android:drawableTint="@color/colorAccent"/>  



# question

### 注意的问题
* gravity属性只对文字起作用  
* drawable在其方向上居中显示；
* 当TextView的宽高属性为wrap_content时，宽高取决于文字和图片的较宽或较高者；

### 局限性
* 当drawable的宽高不确定时；例如，drawable为shape时，shape的宽高取决于控件本身的高度；所以shape无法显示；  
* 当需要对drawable进行单独控制时；

参考资料: [使用TextView的drawable属性](http://toughcoder.net/blog/2015/05/20/android-layout-trick-drawable-of-textview/)
