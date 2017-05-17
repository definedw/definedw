---
title: CSS渐变
date: 2017-05-15 15:41:17
tags:
categories: 转载
---

>作者：valar_cC
>链接：http://www.jianshu.com/p/3b9c19746d6c
>来源：简书

### 线性渐变

`linear-gradient(90deg,red 20%,blue 50%,yellow 80%);`

![image](http://upload-images.jianshu.io/upload_images/1880075-8c79e7ec3e7433e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

很显然通过这张图，你会大概的明白设置这些参数的作用。虽然我并没有用什么文字去解释它。（所以当你看不明白定义的时候，一定要实践。）
接下来，我们要搞点事情。我们将颜色的分隔点重叠。


```css
    width: 300px;
    height: 200px;
    background: linear-gradient(90deg,blue 100px,#fff 100px,#fff 200px,red 200px);
```

![images](http://upload-images.jianshu.io/upload_images/1880075-16eab0f37a7575be.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

没错这就是上述渐变代码产生的效果，是不是感觉打破你以前对渐变的印象。
下面我们利用linear-gradient实现更酷的效果，比如:

![image1](http://upload-images.jianshu.io/upload_images/1880075-14621e5f69dbdece.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

是不是感觉已经突破自己对渐变的认识。让我来说一下实现这个背景的思路：这是个有规律的图案，我们要找到它的基本图案，我相信你已经找到了。
我们需要用到的知识点：

* background支持声明多个linear-gradient，通过逗号分隔；
* 当你声明多个linear-gradient，最先声明的，离用户越近。（这里就需要我们考虑遮盖的问题，一般采用transparent）；
* 还没掌握background的简写方式，可是不行的哦；
* background-repeat、background-size和background-position的合理结合。


```css
    width: 410px;
    height: 410px;
    background: linear-gradient(rgb(2,222,222) 10px, transparent 10px) repeat left top / 40px,
                linear-gradient(90deg,rgb(2,222,222) 10px, transparent 10px) repeat left top / 40px;
```

你看看，以前实现这样的效果，我们只能苦苦哀求美工切图，现在在CSS3的浪潮中，我们可以自给自足（^_^）。
而且通过渐变我们可以实现背景颜色的动画，而不需要消耗额外的HTML元素达到我们预期的效果。例子：

![image2](http://upload-images.jianshu.io/upload_images/1880075-708595a213206216.gif?imageMogr2/auto-orient/strip)


```sass
@mixin menuaction($color) {
        background: linear-gradient($color 100%, transparent 100%) no-repeat center bottom / 100% 10%;
        &:hover {
            background-size: 100% 100%;
            color: #fff;
        }
    }
```

### 径向渐变

基本上径向渐变与线性渐变差不多，只不过它是由中心点向外扩散。所以我这里就不再赘述。
话不多说，先画个同心圆：

![image3](http://upload-images.jianshu.io/upload_images/1880075-d518dca3db7dbc35.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```css
    border-radius: 50%;
    background: radial-gradient(circle,rgb(22,222,111) 0,rgb(22,222,111) 50px,red 50px,red 100px, rgb(222,222,1) 100px, rgb(222,222,1) 150px,rgb(222,2,111) 150px);
```

最后以什么结束呢，哈哈最近各种优惠券，那我们用渐变的知识来搞张优惠券吧：

![image4](http://upload-images.jianshu.io/upload_images/1880075-23ba21064bfa2145.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```css
    width: 300px;
    height: 120px;
    background: radial-gradient(transparent 0, transparent 5px, rgb(247,245,201) 5px) no-repeat,
                radial-gradient(transparent 0, transparent 5px, rgb(247,245,201) 5px) no-repeat,
                radial-gradient(transparent 0, transparent 5px, rgb(247,245,201) 5px) no-repeat,
                radial-gradient(transparent 0, transparent 5px, rgb(247,245,201) 5px) no-repeat,
                radial-gradient(transparent 0, transparent 5px, rgb(247,245,201) 5px) no-repeat,
                radial-gradient(transparent 0, transparent 5px, rgb(247,245,201) 5px) no-repeat,
                radial-gradient(#fff 0, #fff 10px, rgb(247,245,201) 10px) no-repeat,
                radial-gradient(#fff 0, #fff 10px, rgb(247,245,201) 10px) no-repeat,
                linear-gradient(90deg,transparent 10px, rgb(247,245,201) 10px);
            background-size: 20px 20px,20px 20px,20px 20px,20px 20px,20px 20px,20px 20px,60px 60px,60px 60px,100% 100%;
            background-position: -10px 0,-10px 20px,-10px 40px,-10px 60px,-10px 80px,-10px 100px,60px -30px,60px 90px,left center;
```

上面代码应该把size和position放在简写属性里（我就不改了。。。），到此大家应该会对渐变有个新的理解吧。


