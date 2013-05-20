MobileWeb-Dev-Wiki
==================

A Wiki for Mobile Web Dev

Problems List
====
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
