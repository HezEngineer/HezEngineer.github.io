---
title: Android 登录界面随软键盘弹出滑动
date: 2016-04-23 08:52:43
tags: android布局 软键盘 Android
categories: android
---

# Android 登录界面随软键盘弹出滑动
![](http://ooot3t1wg.bkt.clouddn.com/soft-slide.gif)

# 思路
在项目中遇到软键盘弹出遮挡登录按钮和登录输入框，需要将登录按钮和和输入框上移；
解决思路:监听软件盘的弹出，计算弹出后软键盘顶部的高度，计算登录按钮底部的高度，计算登录按钮以上的的整个布局需要的偏移量，使用属性动画进行偏移；
# 登录按钮的底部在Y轴的位置

```java
 //按钮底部在Y轴的坐标
 int btnY = 0;
 btn_login.getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
             @Override
             public void onGlobalLayout() {
                 int[] location = new int[2];
                 //获取登录按钮左上定点的坐标
                 btn_login.getLocationOnScreen(location);
                 btnY = location[1] + btn_login.getHeight();
                 btn_login.getViewTreeObserver().removeOnGlobalLayoutListener(this);
             }
         });
```

# 监听软键盘的显示与隐藏

```java
/***
     * 判断软件盘是否弹出
     * @param v
     * @param listener
     * 备注：在不用的时候记得移除OnGlobalLayoutListener
     * */
    public static ViewTreeObserver.OnGlobalLayoutListener doMonitorSoftKeyboard(final View v,final OnSoftKeyBoardListener listener) {
        final ViewTreeObserver.OnGlobalLayoutListener layoutListener = new ViewTreeObserver.OnGlobalLayoutListener() {
            @Override
            public void onGlobalLayout() {
                Rect r = new Rect();
                // 获取屏幕的可见范围保存在矩形r中
                v.getWindowVisibleDisplayFrame(r);
                int screenHeight = v.getRootView().getHeight();
                //软件盘高度 = 屏幕真实高度 - 屏幕可见范围的高度
                int heightDifference = screenHeight-r.bottom;
                boolean isSoftVisible = heightDifference > (screenHeight / 3);
                if(listener != null) {
                    listener.hasShow(isSoftVisible);
                }
            }
        };
        v.getViewTreeObserver().addOnGlobalLayoutListener(layoutListener);
        return layoutListener;
    }
```

# 在软键盘显示时计算登录按钮以上的布局需要偏移的距离 `delta`

```java
	if(isShow) {
			Rect r = new Rect();
			ll_root.getWindowVisibleDisplayFrame(r);
			delta =  (float) Math.abs(r.bottom - btnY);
			AnimUtil.up(ll_login,-delta);
			AnimUtil.up(iv_logo,-delta/3);
		} else {
			AnimUtil.up(ll_login,0);
			AnimUtil.up(iv_logo,0);
		}

```

示例已经上传到[github](https://github.com/HezEngineer/CodePie)。