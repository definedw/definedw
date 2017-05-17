---
title: 移动输入框填坑系列
date: 2017-05-16 10:50:20
tags:
categories: 转载
---

>作者：yana@alloyteam
>链接：https://www.qcloud.com/community/article/584124
>来源：腾讯云

输入在移动端是一个很常用的功能，那么输入框必然是一个很重要的部分。然而，移动端输入框总会遇到各种各样的问题，无论是样式还是ios和android两端体验不一致都是很让我们头疼的问题，那么如何使移动web的输入框体验更贴近原生也成了一个需要我们多多思考和研究的问题。

### 一、文字输入限制

我们拿最多可输入16个字为例。当输入字数（注意，不是字符长度）超过16字时，会触发tips提示，并且不能继续输入。
办法一：
textarea可以使用maxlength进行输入字数限制。
但是这个办法只能单纯的限制length，有时并不能真正的结局问题。
办法二：
在将第二个办法之前先来讲讲下面的几种情况：
**1、非直接的文字输入**
什么叫做非直接的文字输入呢？

![1](http://www.alloyteam.com/wp-content/uploads/2017/03/%E9%9D%9E%E7%9B%B4%E6%8E%A5%E8%BE%93%E5%85%A5.gif)

当输入汉字时必然会是非直接输入，需要我们点选才能正式输入。
当我们字数限制为16个字，需要实时检查是否到16字。输入文字时，当有非直接的文字输入时，监听keydown事件和input事件都会直接触发判断字数逻辑，会截断我们正在输入的文字。
解决办法：
监听compositionend（当直接的文字输入时触发）这时，当没选中中文的时候不会进行字数判断。


```javascript
$('#input').on('compositionend', function(e) {
        var len = $(this).val().length;
        if (len > 16) {
            // 提示超过16字
        }
    });
```

**2、emoji表情的输入**

当输入emoji的时候，但是，当输入emoji表情的时候，js中判断emoji表情的length为2，因此emoji正常应该最多只能输入8个，但是ios端却把emoji的length算为1，可以输入16个emoji。这样就导致了两端的体验不同。因此需要在js中来进行字数限制。
再加上汉字输入问题，那么就加入一个标记位，来判断是否是直接的文字输入。然后监听input，限制字数，当超过字数限制的时候，把前16个字截断显示出来就ok了。


```javascript
var cpLock;
$('#input').on('compositionstart', function(e) {
    cpLock = true；
});
$('#input').on('compositionend', function(e) {
    cpLock = false;
});
$('#input').on('input', function(e) {
    if (!cpLock) {
        if (e.target.value.length - 17 >=0) {
            var txt = $(e.target).val().substring(0, 16);
            $(e.target).val(txt);
            // 超过16字提示
        }
    }
});
```

### 二、textarea置底显示问题

ios中的输入体验永远伴随着一个问题，就是当唤起键盘后，整个页面会被键盘压缩，也就是说页面的高度变小，并且所有的fixed全部变为了absolute。
android效果：

![2](https://blog-10039692.file.myqcloud.com/1493200205641_2166_1493200205692.png)

![3](https://blog-10039692.file.myqcloud.com/1493200218789_3178_1493200218749.jpg)

![4](https://blog-10039692.file.myqcloud.com/1493200253851_4072_1493200253802.png)

![5](https://blog-10039692.file.myqcloud.com/1493200262513_4775_1493200262456.jpg)

使用fixed定位
可见android中唤起键盘是覆盖在页面上，不会压缩页面
在ios上的效果：

![6](https://blog-10039692.file.myqcloud.com/1493200279779_2768_1493200279852.png)

![7](https://blog-10039692.file.myqcloud.com/1493200290064_1181_1493200289998.png)

![8](https://blog-10039692.file.myqcloud.com/1493200297450_9854_1493200297374.png)

![9](https://blog-10039692.file.myqcloud.com/1493200304786_3590_1493200304711.png)

那么如果我们需要将**输入框固定在屏幕下方，而当键盘被唤起同时输入框固定在键盘**上方（如下图样式）该如何解决呢？

![10](https://blog-10039692.file.myqcloud.com/1493200316198_2673_1493200316136.png)

![11](https://blog-10039692.file.myqcloud.com/1493200335879_152_1493200335845.png)

![12](https://blog-10039692.file.myqcloud.com/1493200345107_9274_1493200345062.png)

可以看出，键盘会将页面顶上去。那么如果希望可以将输入框和键盘完全贴合，我们可以使用div模拟一个假的输入框，使用定位将真正的输入框隐藏掉，当点击假的输入框的时候，将真正的输入框定位到键盘上方，并且手动获取输入框焦点。

在实现过程中需要注意下面几个问题：

**1、真正的输入框的位置计算：**

首先记录无键盘时的window.innerHeight，当键盘弹出后再获取当前的window.innerHeight，两者的差值即为键盘的高度，那么定位真输入框自然就很容易了

**2、在ios下手动获取焦点不可以用click事件，需要使用tap事件才可以手动触发**


```javascript
$('#fake-input').on($.os.ios?'tap' : 'click', function() {
        initHeight = window.innerHeight;
        $('#input').focus();
    });
```

**3、当键盘收起的时候我们需要将真输入框再次隐藏掉，除了使用失去焦点（blur）方法，还有什么方法可以判断键盘是否收起呢？**

这里可以使用setInterval监听，当当前window.innerHeight和整屏高度相等的时候判断为键盘收起。
**注意：**键盘弹起需要一点时间，所以计算当前屏幕高度也需要使用setInterval

**4、因为textarea中的文字不能置底显示，当输入超过一行textarea需要自动调整高度，**因此将scrollHeight赋值给textarea的height。当删除文字的时候需要height也有变化，因此每次input都先将height置0，然后再赋值。


```javascript
$('#textarea').css('height', 0);
    $('#textarea').css('height', $('#textarea')[0].scrollHeight);
```

未完待续...


