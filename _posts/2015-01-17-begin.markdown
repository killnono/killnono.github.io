---
layout: post
title:  "[未完成]如何做androidUI适配的初期工作"
categories: funny
author: "killnono"
---



###关于Android设备适配
[Android Developer 对适配的介绍](https://developer.android.com/training/basics/supporting-devices/screens.html).  
Android设备使用两个常规的属性:    
1. **size**".（尺寸,3.5寸,5.0寸),对应在项目里 small, normal, large, xlarge;  
资料上对其定义是:

+ small：这种屏类似低分辨率的QVGA屏幕。对于小屏的最小布局尺寸大约是320x426dp。例如QVGA低分辨率和VGA高分辨率.  
+ normal：这种屏类似中等分辨率的HVGA屏幕。对于普通屏幕的最小布局尺寸大约是320x470dp。如，WQVGA低分辨率屏、HVGA中等分辨率屏、WVGA高分辨率屏.  
+ large：这种屏类似中等分辨率的VGA屏幕，对于大屏幕的最小布局尺寸大约是480x640dp。例如VGA和WVGA的中等分辨率屏.  
+ xlarge：这种屏被认为比传统的中等分辨率的HVGA屏幕大。针对xlarge屏的最小布局尺寸大约是720x960dp。在大多数情况下，这种超大屏幕的设备因为太大而要放到背包中来携带，而且最有可能的是平板样式的设备。
注意：使用尺寸限定符不意味着资源仅用于这个尺寸的屏幕。如果没有用限定符提供与当前设备配置相匹配的可选资源，那么系统会使用与配置最接近的资源.   
     
2. **density**.(密度,160dpi,240dpi...),对应在项目里 low (ldpi), medium (mdpi), high (hdpi), extra high (xhdpi)。  

###UI设计和适配原则
#####1. 默认界面规格  
```
320 * 480px(mdpi);   
480 * 800px(hdpi);  
720 * 1280px(xhdpi); 
```
当然,这个标准是基于手机版本，对于如何简单兼容pad下面会提到下。

#####2. 如何设计界面和定义标注尺寸
早几年前，我们一般都是按照480 * 800 px来设计;  
随着Android设备的发展,目前让设计师基于720*1280px分辨率为基本尺寸设计,根据默认规格,dpi为320;  
```
px = dip * density /160;
2px = 1dip.  
```

也就是基于360 * 640 dp规格设计。
设计师在做其他控件，字体大小注释时，可以直接从px->dp来标注(其实也可以直接px标注，但是考虑到后面我们需要简单的做pad的适配)，建议UI规范时已dp为单位。

自此，我们基本解决了如何设计定义AndroidPhone规格和尺寸。

#####3. 出现的问题
如果按照以上规格来设计，因为是基于320dpi(xhdpi)来开发。
一般UI实际完后，最后的素材其实是分为:  
**图片（drawable）**  
**尺寸值（dimen）**

尺寸值上的适配我们基本根据设计规格360 * 640 

Android3.2开始,引入了精确适配，理论上可以适配任意像素宽度，高度，屏幕密度的平台，
需用以下方式添加限定符
其中w1280dp表示屏幕宽度为1280dp，h752dp表示屏幕高度为752dp，160dpi表示屏幕密度，其中屏幕宽，高必须以dp为单位，在知道屏幕像素宽高度的情况下可以通过公式：1dp = （目标屏幕密度/标准密度）*px 转换成dp单位。


1. lala  
2. 指导 [google 官方指导](http://developer.android.com/guide/practices/screens_support.html).
3. 实践 [google 官方实践](https://developer.android.com/training/multiscreen/screensizes.html) 


```java
{
   printf("准备开始写博客了");
}
```

