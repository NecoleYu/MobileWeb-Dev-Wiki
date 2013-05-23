MobileWeb-Dev-Wiki
==================

A Wiki for Mobile Web Dev

Problems List
====

**贡献者：** [岑安](https://github.com/hongru)

***
- **利用`overflow:auto`原生滚动引起的问题？**

**Bugs**
> 
1. Android2.3以下不支持 
2. 体验上差异，默认都没有缓动效果，且不流畅
3. 事件代理会有问题，在Android4.0及其以下版本基本都会有这个问题，就是当采用overflow原生滚动时，靠近被滚动内容底部附近的位置，获取事件触发元素的时候（e.target），会出现诡异bug，一种表现是这一部分区域事件无法触发。另一种表现是获取到的元素是遥远的元素。


**Solution**

>一种不优雅的治标的办法是在被滚动内容底部留出一块无用的地方，但是极其局限而且相当龊。<br/>
更好的办法是放弃`overflow`原生的滚动，而是自己代码控制触屏上的容器scroll。比如使用iscroll或者自己根据需求定制写一个。<br/>
比如etao触屏版使用的就是自己写的轻量级scroller [EScroll](https://github.com/hongru/escroll)

***

- **在Mobile端使用浮层可能遇到的问题**

**Bugs**
>
1. Android上touch事件穿透的问题，表现为在Android设备上的浮层上元素绑定有touch事件时，触摸事件会穿透浮层，并造成浮层下的元素或link被点中，造成期望外的结果。
2. 若想要浮层表现为占据全屏，常规的思路为设置绝对定位的浮层的高宽为可视区域的高宽，这样做会带来的额外的工作，一个是浮层一旦被限制高度后，内部的scroller就要自己来做。另一个是当可是区域有改变时（比如横竖屏切换，或者触摸键盘升起），resize的控制可能会带来额外的bug。
3. 在浮层中的input text 输入框，当focus的时候，在Android上不会随着软键盘的升起而自动抬高，可能会造成软键盘挡住输入框的问题。


**Solution**
>
1.Andriod上touch事件穿透的问题，在高版本4.0+上可以通过
```javascript
preventDefault
stopPropagation
```
fix掉。低版本上（2.2，2.3）上，可能的话建议在浮层里用click事件代替touch事件。

2. 如果有全屏的浮层需求，建议两种实现方式：
  - 一种是浮层即使要设置定高，不设置为可是区域的定高，而是设置为整个内容区域占的高度，因为通常浮层所在的主页面的内容区高度都会大于可是区域高度，这样在大部分需求里就可以避免自己来做scroller。很大程度上也避免了可是区域高度变化造成的影响。
  - 另一种如果设置定高刚好为可是区域高度时，自己做内部内容滚动的scroller，建议把不需要的滚动的触碰区域的touchmove事件都preventDefault掉（当前是在不影响需求的情况下）。这样做很大程度上能规避绝对定位的浮层被拖乱或者定位错误等诡异bug。

3. 浮层中的input输入框focus时在Android中一律都无法随着软键盘的抬起而抬起，建议的办法是ua判断是否Android，并在Android上处理focus事件，人为抬高输入框，比如将输入框上内容隐藏掉...

***

- **Android4.0上使用`-webkit-user-modify: read-write-plaintext-only;`的副作用**

**Bugs**
>
在Android4.0上使用这个属性可以优化输入框focus时外框不出现已经输入框右侧清除内容的按钮不出现的问题，但是副作用是会导致这个输入框不能随着软键盘的升起而自动抬起。<br/>
Android2.2，2.3不存在这个问题，因为他们根本不支持这个css样式。

**Solution**
>
谨慎使用这个双刃剑。

***

- **ios5.1版本上input输入框使用`transition`属性并有变化时，残影问题**

**Bugs**
>
目前发现在ios5.1版本上input输入框使用`transition-duration`类似属性变化高宽时。input的`display`属性为`block`时，会在变化时留下残影。

**Solution**
>
将该input[text]的`display`属性设置为`inline-block`可解决残影。

***

- **ios 上使用transform属性做动画时，闪屏问题**

**Bugs**
>
如题，ios上使用transform属性做动画时会有闪屏问题。

**Solution**
>
尝试全局加上
```css
-webkit-transform-style: preserve-3d; 
-webkit-backface-visibility: hidden;
```

***

## Position Fixed Bug
贡献者：华雷
* 问题的描述：ios5以上版本已经支持position：fixed。当点击导航锚点或window.scrollTo等方法触发scroll事件时，如果不改变fixed元素的位置（top\left）没有什么问题；但是，在事件句柄中改变fixed元素的位置，元素位置不会立即改变，而是只在手指滚动屏幕时改变生效，出现新的状态。（详细描述参考：http://weedygarden.net/2012/04/ios5-position-fixed-bug/）
* 解决方案：参考：http://stackoverflow.com/questions/7826868/fixed-position-navbar-only-clickable-once-in-mobile-safari-on-ios5#10030251  评论部分。

html:: At the end of my container div, i added an empty div, with no height/width

   <div id="device"></div>

</div> <!--! end of #container -->

js:: Just before the scroll animation, i give the div height of 200px.

$('#device').css('height', '200px');

immediately on complete of the animation, i take the height away

$('#device').css('height', '0px');  

That is it. Sweet hack magic. I hope that helps. If you want a working example, http://ryanore.com Currently I'm in progress so I wouldn't normally drive any links to it, but hey it's for a good cause.

经理解整理出简单demo如下：
<!DOCTYPE HTML>
<html>
<head>
  <meta charset="UTF-8" />
	<title></title>
    <script src="zepto.js" type="text/javascript"></script>
</head>
<body style="height:1900px;">


    <a id="ida" href="#cc" style="font-size:100px;">cc</a>
    <div id="cc" style="height:500px;background:blue;width:200px;margin-top:1000px;">cccc</div>
    <div id="bb" style="position:fixed;top:0;right:0;height:300px;background:yellow;width:200px;">bbbbb</div>
    <div id="dd"></div>

    <script type="text/javascript">
        $('#ida').click(function (e) {
            e.preventDefault();

            $('#dd').css('height', '20px');
            window.scrollTo(0, 700);
            setTimeout(function () {
                $('#dd').css('height', '0px');
                document.getElementById('bb').style.top = 250 + 'px';
            }, 100);
        });
    </script>
</body>
 * 生效平台：iOS/Android（此方案解决了主要问题，但仍存在“闪屏”瑕疵，希望谁碰到此问题，能探索出更好方案）



## 解决在 touch 事件上的 相关bug
贡献者：玄寂
* 问题的描述：给定一个容器绑定 touch 的 相关事件 。 如果里面有一张图 如果一直按在这个图上 出现针对图片的选项 比如保存 之类的 。 当然过程中会检测到 touchstart 但是 检测不到放开时候 的 touchend 。 如果不是图 是文本 也是同样的操作 但是 会检测到 end。
* 解决方案：可以直接给某个元素设置 e{-webkit-touch-callout:none;} 。 这个方式实际是阻止了系统所有的回调。
* 生效平台：iOS/Android


## 去除 长期 按在文本上 出现选择区域，并提示拷贝
贡献者：完颜
* 问题的描述：ios上 长时间 按在文本上 会 出现选择区域，并提示拷贝 ，有时候可能并不需要这类功能
* 解决方案：可以直接给某个元素设置 e{-webkit-user-select:none} 。
* 生效平台：iOS（Android未知）

## 浏览器 click 事件延时触发问题
贡献者：鬼道

* 问题的描述：多数浏览器（不是所有）的 click 事件会在点击后300ms左右触发，原因是浏览器希望判断是否双拍击（double tap），原始的解释参见google这篇 [Creating Fast Buttons for Mobile Web Applications](https://developers.google.com/mobile/articles/fast_buttons)

> The problem with this approach is that mobile browsers will wait approximately 300ms from the time that you tap the button to fire the click event. The reason for this is that the browser is waiting to see if you are actually performing a double tap. 

* 推荐直接使用或参考：[Hammer.js](http://eightmedia.github.io/hammer.js/) | [fastclick](https://github.com/ftlabs/fastclick)

## android input 占位文字问题
贡献者：玄道、鬼道

复现条件：

0. Android（已知4.x）系统自带浏览器
0. input 内容为空
0. input 获得焦点时，浏览器会将其关联label的内容作为占位文字显示

详情可参见鬼道友情提供的[demo](http://demo.tmall.net/u/guidao/input-label.html)。

## iframe bug
待调研
