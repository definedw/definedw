---
title: 移动开发遇到问题总结
date: 2017-05-08 09:19:40
tags:
categories: 转载
---
>作者：熊汉彪
>链接：https://zhuanlan.zhihu.com/p/26141351
>来源：知乎

### 布局

众所周知的移动端，一般来说可以分为上中下三个部分，分别为 header、main、footer，其中header、footer 是固定高度，分别固定在页面顶部和页面底部，而 main 是占据页面其余位置，并且可以滚动。基本是以下形式：
![layout](http://upload-images.jianshu.io/upload_images/2741993-6761d169556abcc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/360)

```html
<body>

  <div class="header"></div>

  <div class="main"></div>

  <div class="footer"></div>

</body>
```

根据页面滚动的位置分为两种布局，一种是滚动 body，另一种是固定 body 的高度为100%，在 main 中滚动。第一种布局有个优点，就是页面的地址栏会随着 body 的滚动隐藏起来，并且 Android 设备中，滚动 body 会更加的流畅，如果项目中有类似需求可以考虑。实现布局的方式如下:


```css
body {
  overflow: auto;
}

.header,
.footer {
  position: fixed;
  left: 0;
  right: 0;
  height: 44px;
}

.header {
  top: 0;
}

.footer {
  bottom: 0;
}

.main {
  height: 100%;
  padding: 44px 0;
}
```
第一种情况比较适合长列表页面，整个页面除了 header 和 footer 之外都需要滚动，但很多时候，我们只希望页面的某个元素滚动，这个时候，就采取第二种布局方式。
这种页面布局有三种相对简单的实现方式：

1. fixed 定位
2. absolute 定位
3. flex 定位


最容易想到的实现方式是 fixed 定位，实现方式如下：

```css
html, body {
  height: 100%;
  overflow: hidden;
}
.header,
.footer {
  position: fixed;
  left: 0;
  right: 0;
  height: 44px;
}


.header {
  top: 0;
}


.footer {
  bottom: 0;
}


.main {
  height: 100%;
  padding: 44px 0;
  box-sizing: border-box;
}
```

fixed 定位实现起来简单，在大多数浏览器中也能正常显示，但是 fixed 定位在移动端会有兼容性问题，后面会提到，所以不建议这种实现方式。absolute 定位和 fixed 定位类似，只要把 header 的 footer 的 position 改为 absolute 就可以了。细心的小伙伴可能发现了，这里的 main 没有设置 overflow ，因为这里有一个坑，不管是absolute 定位还是 fixed 定位都一样，为了方便描述，以下只说 fixed 定位(在 absolute 定位也一样成立)。在PC端没有问题，但是在移动端，如果 main 设置了 overflow 为 true，header 会被 main 遮住，对，没有错，虽然是 fixed 定位，但是在移动端，如果 fixed 定位节点后面紧接跟着的兄弟节点是可滚动的(也就是设置了 overflow 为 true )，那么 fixed 节点会被其后的兄弟节点遮住。这个问题解决方式有很多，既然是 fixed 定位后面紧接着可滚动的兄弟节点才会有这个坑，只要让他的条件有一个不成立就好了，有以下解决方案：

1. 让 fixed 定位节点后面不紧接着可滚动的节点
2. 不让 scroll 节点遮住 fixed 节点

**第一种方方案有以下可选方法:**

1. 把所有 fixed 节点放在 scroll 元素后面，即把 header 节点放在 main 节点后面

```html
<body>
  <div class="main"></div>
  <div class="header"></div>
  <div class="footer"></div>
</body>
```
但这样显然不太符合一般人的思维习惯，代码可读性降低。

2. 使 main 不可滚动，给 main 嵌套一层可滚动的子节点


```html
<body>
  <div class="header"></div>
  <div class="main">
    <div class="scroll-container"></div>
  </div>
  <div class="footer"></div>
</body>


<style>
  .main {
    overflow: hidden;
  }
  .scroll-container {
    height: 100%;
    overflow: auto;
  }
</style>

```

**第二种方案有以下可选方法:**

1. 让 scroll 节点不与 fixed 节点有重合


```css
body {
  padding: 44px 0;
}


.main {
  padding: 0;
}
```

2. 给 fixed 节点设置 z-index


```css
.header,
.footer {
    z-index: 8888;
}
```

看到这里可能会有小伙伴觉得，一个简单的布局，还要绕过这么多坑，难道没有简单的方式吗，答案当然是肯定的，那就是第三种实现方式，flex 布局。flex 定位在移动端兼容到了 iOS 7.1+，Android 4.4+,如果使用 autoprefixer 等工具还可以降级为旧版本的 flexbox ，可以兼容到 iOS 3.2 和 Android 2.1。而且用 flex 实现起来相对简单，在各个浏览器里表现也相对一致。实现如下:


```css
body {
  display: flex;
  flex-direction: column;
}
.main {
  flex: 1;
  overflow: auto;
  -webkit-overflow-scrolling: touch;
}
.header {
  height: 44px;
}
.footer {
  height: 44px;
}
```

### fixed 与 input

刚接触移动端 Web 开发的小伙伴应该都会听前辈们说过，不要在有 input 标签的页面使用 fixed 定位，因为这两者在一起的时候，总是会有奇奇怪怪的问题。在 iOS 上，当点击 input 标签获取焦点唤起软键盘的时候，fixed 定位会暂时失效，或者可以理解为变成了 absolute 定位，在含有滚动的页面，fixed 定位的节点和其他节点一起滚动。其实这个问题也很好解决，只要保证 fixed 定位的节点的父节点不可滚动，那么即使 fixed 定位失效，也不会和其他滚动节点一起滚动，影响界面。但是除此之外，还有很多坑比较难以解决，例如 Android 软键盘唤起后遮挡住 input 标签，用户没法看到自己输入的字符串，iOS 则需要在输入至少一个字符之后，才能将对应的 input 标签滚动到合适的位置，所以为了避开这些难以解决的坑，在有表单输入的页面，尽量用absolute 或者 flex 替换 fixed。



### 样式实现

1. iOS 1px实现(Owner）

参考伪元素做法如下：


```scss
/* scss style */
.tran{
    position: relative;
    &::before{
        content: '';
        position: absolute;
        box-sizing: border-box;
        height: 1px;
        left: 0;
        top: 0;
        border-bottom: 1px solid #cfcfcf;
        -webkit-transform-origin: 0 0;
        transform-origin: 0 0;
        -webkit-transform: scale(0.5);
        transform: scale(0.5); 
    }
    
/* mixin */
@mixin bottomLine($c:rgba(207, 207, 207, .95), $t:0, $r:0, $b:0, $l:0) {
    content: " ";
    position: absolute;
    left: $l;
    bottom: $t;
    right: $r;
    height: 1px;
    border-bottom: 1px solid $c;
    color: $c;
    -webkit-transform-origin: 0 100%;
    transform-origin: 0 100%;
    -webkit-transform: scaleY(0.501);
    transform: scaleY(0.501);
}    
```

2. 背景渐变

CSS3 有了渐变背景，可以通过渐变背景实现 1px 的 border，实现原理是设置 1px 的渐变背景，50% 有颜色，50% 是透明的。


```scss
@mixin commonStyle() {
  background-size: 100% 1px,1px 100% ,100% 1px, 1px 100%;
  background-repeat: no-repeat;
  background-position: top, right top,  bottom, left top;
}


@mixin border($border-color) {
  @include commonStyle();
  background-image:linear-gradient(180deg, $border-color, $border-color 50%, transparent 50%),
  linear-gradient(270deg, $border-color, $border-color 50%, transparent 50%),
  linear-gradient(0deg, $border-color, $border-color 50%, transparent 50%),
  linear-gradient(90deg, $border-color, $border-color 50%, transparent 50%);
}
```

**伪类+transform：**

这类方法的实现原理是用伪元素的 box-shadow 或 border 实现 border，然后用 transform缩小到原来的一半。即使有圆角的需求也能很好的实现。


```scss
@mixin hairline-common($border-radius) {
  position: relative;
  z-index: 0;
  &:before {
    position: absolute;
    content: '';
    border-radius: $border-radius;
    box-sizing: border-box;
    transform-origin: 0 0;
  }
}
@mixin hairline($direct: 'all', $border-color: #ccc, $border-radius: 0) {
  @include hairline-common($border-radius);
  &:before {
    transform: scale(.5);
    @if $direct == 'all' {
      top: 0;
      left: 0;
      width: 200%;
      height: 200%;
      box-shadow: 0 0 0 1px $border-color;
      z-index: -1;
    } @else if $direct == 'left' or $direct == 'right' {
      #{$direct}: 0;
      top: 0;
      width: 0;
      height: 200%;
      border-#{$direct}: 1px solid $border-color;
    } @else {
      #{$direct}: 0;
      left: 0;
      width: 200%;
      height: 0;
      border-#{$direct}: 1px solid $border-color;
    }
  }
}
```

