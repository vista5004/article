# Netflix Logo In CSS
*这篇博客是[Gregor Adams](https://twitter.com/gregoradams)关于他如何设法如何用CSS重现Netflix商标。Gregor当他涉足CSS方面是star增速冠军。能在这里谈论他也是非常荣誉的。*<p>
几个月前我尝试Netflix（一家在线影片租赁提供商），立即就把我迷住了。我观看了一些列以前在别处看的节目。每一集电视剧或者电影都已Netflix动画开始。<p>
<center>![Alt text](http://hugogiraudel.com/images/netflix-logo-in-css/logo.gif)</center><p>
我突然开始想用CSS来实现。所以看了几集后就到[CodePen](http://codepen.io/pixelass/)来实现这个logo。<p>
###第一个概念
我的第一个实施方式是有点不洁的，因为我尝试采用一点新东西。<p>
例如：我想使用纯CSS技术来实现它，并且我也想当我点击按钮的时候，这个动画在执行一次，所以我要使用一些不可思议技巧。幸运的是当我写CSS代码的时候，总会有一些窍门跑到我的袖套里面。<p>
我们来谈论一下实际的动画。<p>
我录下这个动画并且在Quicktime中循环播放，这样我可以详细检查。我倾向于这么做，因为它允许我停在某些特定帧弄清楚到底发生了什么。<p>
这个商标：</br>
1.以一个白屏幕开始。</br>
2.弹出白色的3D字母。</br>
3.投射阴影。</br>
4.消失。</br>
5.把字体颜色变成红色。</br>
这就是我需要实现的动画步骤。但是这里有另外一些关于这个logo的东西需要解决：**字母在商标中心是倾斜的。**<p>
大家一直问我如何做到这些。<p>
我做了许多3D实验，所以这对我来说不是很难。<p>
###使字母变倾斜
以这个词“Netflix”的一些基本标记开始。<p>
```
<div class="logo">
  <span>N</span>
  <span>E</span>
  <span>T</span>
  <span>F</span>
  <span>L</span>
  <span>I</span>
  <span>X</span>
</div>
```
我用类<code>logo</code>做了一个包装，并且用<code>span</code>标签包裹每一个字母。<p>
然后我在Y轴上旋转这个字母并且在X轴上缩放这个字母以保持它的原始宽度。重要的部分是在<code> class="logo"</code>包装上设置一个景深<code>perspective </code>，并且定义它的景深原点<code> perspective-origi</code>。
```
// 基础的字母样式
span {
  font-size: 8em;
  font-family: impact;
  display: block;
}
// 开启三维效果
.logo {
  perspective: 1000px;
  perspective-origin: 50% 0;
}

//给字母变换
.logo span {
  transform-origin: 0 0;
  transform: scaleX(80) rotateY(89.5deg);
}
```
这里还有一些其它的方式来实现这些，例如使用一个不同景深（比如500px），旋转角度（比如9deg）和扭曲值（比如0.5），但是这些值能实现我最需要的效果。<p>
下面是在CODEPEN实现的小例子：(原demo是页面中嵌入的iframe实现嵌入CODEPEN，但是markdown没有嵌入iframe的方法，)<p>
*jade实现*
```
div(class="fig--1")
  style. 
    @import 'http://codepen.io/pixelass/pen/raEojV.css';
  p Original:
  span N
  p Transformed:
  .logo
    span N
```
*html实现*
```
<div class="fig--1">
  <style>@import 'http://codepen.io/pixelass/pen/raEojV.css';</style>
  <p>Original:</p><span>N</span>
  <p>Transformed:</p>
  <div class="logo"><span>N</span></div>
</div>
```
*scss实现*
```
.fig--1 {
  span {
    font-size: 8em;
    font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
    display: block;
  }
  .logo {
    perspective: 1000px;
    perspective-origin: 50% 0;
    span {
      transform-origin: 0 0;
      transform: scaleX(80) rotateY(89.5deg);
    }
  }
}
```
*css实现*
```
.fig--1 span {
  font-size: 8em;
  font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
  display: block;
}
.fig--1 .logo {
  perspective: 1000px;
  perspective-origin: 50% 0;
}
.fig--1 .logo span {
  transform-origin: 0 0;
  transform: scaleX(80) rotateY(89.5deg);
}
```
<iframe src="//codepen.io/pixelass/embed/raEojV?height=446&theme-id=0&slug-hash=raEojV&default-tab=result&user=pixelass""></iframe>
