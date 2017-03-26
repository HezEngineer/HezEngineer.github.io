---
title: training-Graphics&Animation-Bitmaps-1
date: 2016-04-01 20:08:13
tags: bitmap
categories: android
---

# Loading large bitmaps efficently

  使用`Bitmap`可以提升UI的交互性，但是使用不当会造成异常`java.lang.OutOfMemory:bitmap size excedds VM budget`;原因如下：  
1. 移动设备会限制系统资源；系统至少为应用保留`16MB`的内存空间；.[Android Compatibility Definition Document(CDD)](http://source.android.com/compatibility/downloads.html#android-60) <strong>Section 3.7</Strong> Virtual Machine  Compatibility 中规定可以在多种设备下保持应用流畅度的最小运行时内存；应用应该被优化到可以在该最小运行时内存执行；当然，不同的设备会适当的提高这个限制；<br>2. `Bitmap`特别占用内存；例如：Galaxy Nexus 使用2592x1936（五百万像素）照相机；而`Bitmap`的配置为`ARGB_8888`(android2.3以上默认采取该格式，这意味每一个像素需要占用4Bytes)，如果把该图片加载到内存，需要占用`19MB`(2592x1936x4 bytes)的内存；<br>3. app中UI经常需要一次加载多个`Bitmap`，控件`ListView`,`GridView`,`ViewPager`的通常包含多个Bitmap，并且在屏幕的不可见区域仍然有很多Bitmap等着手指一滑把他们加载到可视区域；



# How

很多时候，图片的分辨率要高于手机屏幕的分辨率，为了匹配显示图片的控件，减少内存的开销，我们需要图片的低分辨率版本；<br>
### Read Bitmap Dimensions and Type<br>
[BitmapFactory](http://developer.android.com/intl/zh-cn/reference/android/graphics/BitmapFactory.html):根据不同的图片来源创建`Bitmap`对象，包括文件路径，流，字节数组；根据数据源选择合适的解码方法；使用这些方法构造`Bitmap`对象时需要分配内存；因此很容易导致`OOM`。不同的方法有不同的解码选项，我们需要通过`BitmapFactory.Options`来指定解码选项；例如在解码时，当指定`inJustDecodeBounds`属性为`true`来避免分配内存，这样会为`Bitmap`对象返回`null`，但是可以获得`outWidth`,`outHeight`,`outMimeType`；这个技巧可以让我们提前读取图片的尺寸以及类型从而避免构建`Bitmap`对象时的内存分配;<br>
`BitmapFactory.Options options = new BitmapFactory.Options()`<br>`options.inJustDecodeBounds = true`<br>`BitmapFactory.decodeResources(getResources, R.id.images, options)`<br>`int imageWidth = options.outWidth`<br>`int imageHeight = options.outHeight`<br>`String imageType = options.outMimeType`<br>测试图 分辨率：5760x3240 类型: .jpg<br>测试结果：outWidth = 5760; outHeight=3240 outMimeType = image/jpeg![](http://img1.gamersky.com/image2010/06/20100626h_246/01.jpg)<br>注意：为了避免OOM，在解码之前应先检查它的尺寸；除非你相信提供给你的尺寸数据符合可用的内存要求；<br>
### Load a Scaled Down Version to memory<br>
现在已经知道了图片的尺寸，接下来我们可以考虑决定是要将原图还是将修改后的图加载到内存中；我们要考虑以下几个因素：<br>
1. 评估加载完整图片耗费的内存；<br>
2. 加载这张图片时的涉及到的其他内存需求<br>
3. 加载这张图片的`ImageView`或者其他UI控件的尺寸；<br>
4. 屏幕的分辨率以及尺寸；<br>例如：不应该把一个分辨率1024x768的图片加载到一个分辨率128x96的`ImageView`中<br>为了得到图片的低分辨率版本，我们需要设置`BitmapFactory.Options`中`inSampleSize`的值；例如图片的分辨率2048x1536，`inSampleSize`为4，那得到的位图的分辨率为512x384(假如我们采取的是ARGB_8888格式)。下面这个方法告诉我们怎么计算sample size：<br>

```java

    public static int calculateInSampleSize (BitmapFactory.Options options,   
      int reqWidth, int reqHeight) {
        final int height = options.outHeight;
        final int width = options.outWidth;
        int inSampleSize = 1;
        if (height > reqHeight || width > reqWidth) {
          final int halfWidth = height /2;
          final int halfHeight = width /2;
          while (halfWidth / inSampleSize > reqWidth && halfHeight/inSampleSize> reqHeight) {
            inSampleSize *=2；
          }
        }
          return inSampleSize;
      }  

```

注意：inSampleSize最终为2的幂，否则会向下取最靠近的2的幂的值；<br><br>
获取到inSampleSize之后，下面的方法来获得缩小后的图片的低分辨率版本；

```java
	public static Bitmap decodeSampledBimapFromResources (Resources res, int resId, int reqWidth, int reqHeight) {
		BitmapFactory.Options options = new BitmapFactory.Options();
		options.inJustDecodeBounds = true;
		BitmapFactory.decodeResource(res, resId, options);
		options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);
		options.inJustDecodeBounds = false;
		return BitmapFactory.decodeResource(res, resId, options);
	}
```

经过该方法后我们可以将任意大小的图片解码成低分辨率的版本来加载到内存和布局控件中；



# question

1. [手机分辨率与相机像素的问题](http://mobile.163.com/14/0421/08/9QBDVKM7001117A8_all.html)
2. 当我将上述图片降低分辨率时，我设置的reqWidth = 200, reqHeight =200, 我得到inSampleSize =16，解码后，得到的outWidth = 1080 outHeight = 606 而不是5760/16=360和3240/16=202   why?
3. 我将ImageView的layout_width = 200px，layout_height=200px；得到的效果不是方形；why？
<br>

# Conclusion

 对于高分辨率的图片，为了获得低分辨率的版本，我们应先解码图片的边界，获得图片的尺寸及类型， 然后根据我们希望得到的分辨率获得inSamplseSize,再进行图片的全部解码；
