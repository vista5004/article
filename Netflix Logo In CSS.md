# Netflix Logo In CSS
*这篇博客是[Gregor Adams](https://twitter.com/gregoradams)关于他如何设法如何用CSS重现Netflix商标。Gregor当他涉足CSS方面是star增速冠军。能在这里谈论他也是非常荣誉的。*<p>
几个月前我尝试Netflix（一家在线影片租赁提供商），立即就把我迷住了。我观看了一些列以前在别处看的节目。每一集电视剧或者电影都已Netflix动画开始。<p>
![Original animated Netflix logo](http://hugogiraudel.com/images/netflix-logo-in-css/logo.gif)<p>
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
下面是在CODEPEN实现的小例子：（原demo是页面中嵌入的iframe实现嵌入CODEPEN，但是markdown没有嵌入iframe的方法，所以采用代码段来展示，并且把原demo的jade和scss写法转换成css方便没有使用过两种技术的读者阅读）<p>
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
接下来我要对所有的字母应用这个样式，中间的字母不要变化。右边的字母朝着相反的方向倾斜，并且字母高度发生变化。<p>
为了实现这些需要增加一些新逻辑：我使用SASS的标准语法来实现。<p>
*scss代码*
```
.logo {
  perspective: 1000px;
  perspective-origin: 50% 0;
  font-size: 8em;
  display: inline-flex;

  span {
    font-family: impact;
    display: block;

    $letters: 7;
    @for $i from 1 through $letters {
      $offset: $i - ceil($letters / 2);
      $trans: if($offset > 0, -89.5deg, 89.5deg);

      &:nth-child(#{$i}) {
        // trans/de-form the letters
        transform-origin: 50% + 50%/$offset 200%;
        font-size: if($offset == 0,
          0.85em,
          0.9em + 0.015*pow(abs($offset),2));
        transform:
            if($offset == 0, scale(1, 1), scale(95.9 - abs($offset) * 10, 1))
            if($offset == 0, translatey(0%), rotatey($trans));
      }
    }
  }
}
```
*为了方便不懂scss同学立即，这是我编译后的css代码*
```
.logo {
  perspective: 1000px;
  perspective-origin: 50% 0;
  font-size: 8em;
  display: inline-flex;
}
.logo span {
  font-family: impact;
  display: block;
}
.logo span:nth-child(1) {
  transform-origin: 33.33333333% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(89.5deg);
}
.logo span:nth-child(2) {
  transform-origin: 25% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(89.5deg);
}
.logo span:nth-child(3) {
  transform-origin: 0% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(89.5deg);
}
.logo span:nth-child(4) {
  transform-origin: Infinity% 200%;
  font-size: 0.85em;
  transform: scale(1, 1) translatey(0%);
}
.logo span:nth-child(5) {
  transform-origin: 100% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(-89.5deg);
}
.logo span:nth-child(6) {
  transform-origin: 75% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(-89.5deg);
}
.logo span:nth-child(7) {
  transform-origin: 66.66666667% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(-89.5deg);
}
```
这里是demo代码块（原demo是页面中嵌入的iframe实现嵌入CODEPEN，但是markdown没有嵌入iframe的方法，所以采用代码段来展示，并且把原demo的jade和scss写法转换成css方便没有使用过两种技术的读者阅读）<p>
*jade*
```
div(class="fig--2")
  style. 
    @import 'http://codepen.io/pixelass/pen/yydGPL.css';
  .logo
    span N
    span E
    span T
    span F
    span L
    span I
    span X
```
*html*
```
<div class="fig--2">
  <style>@import 'http://codepen.io/pixelass/pen/yydGPL.css';</style>
  <div class="logo">
    <span>N</span>
    <span>E</span>
    <span>T</span>
    <span>F</span>
    <span>L</span>
    <span>I</span>
    <span>X</span>
  </div>
</div>
```
*scss*
```
.fig--2 .logo {
  perspective: 1000px;
  perspective-origin: 50% 0;
  font-size: 8em;
  display: inline-flex;
}
.fig--2 .logo span {
  font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
  display: block;
}
.fig--2 .logo span:nth-child(1) {
  transform-origin: 33.33333333% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(89.5deg);
}
.fig--2 .logo span:nth-child(2) {
  transform-origin: 25% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(89.5deg);
}
.fig--2 .logo span:nth-child(3) {
  transform-origin: 0% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(89.5deg);
}
.fig--2 .logo span:nth-child(4) {
  transform-origin: Infinity% 200%;
  font-size: 0.85em;
  transform: scale(1, 1) translatey(0%);
}
.fig--2 .logo span:nth-child(5) {
  transform-origin: 100% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(-89.5deg);
}
.fig--2 .logo span:nth-child(6) {
  transform-origin: 75% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(-89.5deg);
}
.fig--2 .logo span:nth-child(7) {
  transform-origin: 66.66666667% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(-89.5deg);
}

```
*css*
```
.fig--2 .logo {
  perspective: 1000px;
  perspective-origin: 50% 0;
  font-size: 8em;
  display: inline-flex;
}
.fig--2 .logo span {
  font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
  display: block;
}
.fig--2 .logo span:nth-child(1) {
  transform-origin: 33.33333333% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(89.5deg);
}
.fig--2 .logo span:nth-child(2) {
  transform-origin: 25% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(89.5deg);
}
.fig--2 .logo span:nth-child(3) {
  transform-origin: 0% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(89.5deg);
}
.fig--2 .logo span:nth-child(4) {
  transform-origin: Infinity% 200%;
  font-size: 0.85em;
  transform: scale(1, 1) translatey(0%);
}
.fig--2 .logo span:nth-child(5) {
  transform-origin: 100% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(-89.5deg);
}
.fig--2 .logo span:nth-child(6) {
  transform-origin: 75% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(-89.5deg);
}
.fig--2 .logo span:nth-child(7) {
  transform-origin: 66.66666667% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(-89.5deg);
}
```
###一个用于阴影的函数
写一个实现3d效果和阴影的函数。我把视频停在某一帧，并且仔细查看细节。<p>.
![Image extracted from the original animated Netflix logo](http://hugogiraudel.com/images/netflix-logo-in-css/shadow.png)<p>
正如你所看到的，当这个阴影到达右下角，3d效果的消失点在中间。现在知道我们函数需要做什么了。<p>
我们将会在keyframes中调用这个函数，所以我们希望他能处理一些值，例如：<br>
<ul>
  <li>color</li>
  <li>x</li>
  <li>y</li>
  <li>blur</li>
  <li>mix</li>
</ul>
我们还需要一个参数来定义阴影的深度。<p>
![My CSS implementation of the previously shown image](http://hugogiraudel.com/images/netflix-logo-in-css/shadow-css.png)<p>
下面就是用来处理这些需求的函数。<p>
```
/// 在特定方向创创建三维阴影
/// @author Gregor Adams
/// @param  {Number}        $depth - 阴影长度
/// @param  {Unit}          $color - 阴影颜色
/// @param  {Unit}          $x     - 在x轴上到下一个阴影的距离
/// @param  {Unit}          $y     - 在y轴上到下一个阴影的距离
/// @param  {Unit}          $blur  - text-shadow的模糊距离
/// @param  {Color|false}   $mix   - 添加一个可选的颜色来混合
/// @return {List}          - 返回text-shadow列表
@function d3($depth, $color, $x: 1px, $y: 1px, $blur: 0, $mix: false) {
  $shadow: ();
  @for $i from 1 through $depth {
    // append to the existing shadow
    @if type-of($mix) != 'color' {
      $shadow: append($shadow, round($i * $x) round($i * $y) $blur $color, comma);

    } @else {
      $shadow: append($shadow, round($i * $x) round($i * $y) $blur mix($mix, $color, 0.3%*$i), comma);
    }
  }
  @return $shadow;
}
```
这个函数对于sass菜鸟或者只使用基本语言特性的开发者和设计师来说可能有点难理解，所以让我来详细解释一下.<p>
我以一个<code>$shadow</code>的变量开始，<code>list</code>是一个空的列表。
```
$shadow: ();
```
我是从1开始循环到列表的深度。通过Sass意味着我们迭代包括以下的值。
<ul>
  <li>from 0 to 5 = 0, 1, 2, 3, 4</li>
  <li>from 0 through 5 = 0, 1, 2, 3, 4, 5</li>
</ul>
每一次迭代我都添加一个text-shadow到这个列表。所以最后这个列表看起来就是下面这个样子：<br/>
```
$shadow: (0 1px 0 red, 1px 2px 0 red, 2px 3px 0 red, ...);
```
使用的时候就像下面这样：<br/>
```
text-shadow: d3(5, red, [$x], [$y], [$blur], [$mix]);
```
$x,$y,$blur和$mix都是可选的参数。我已经提到我将会在keyframes中调用这个函数，所以我需要可选择性的改变他们。 $mix允许添加第二个颜色，实现这个阴影从一种颜色淡出成另外一种颜色。<p>
下面是例子：
*jade*
```
div(class="fig--3")
  style. 
    @import 'http://codepen.io/pixelass/pen/XJLOXg.css';
  .logo
    span N
    span E
    span T
    span F
    span L
    span I
    span X
  p(style="height: 50px")
```
*html*
```
<div class="fig--3">
  <style>@import 'http://codepen.io/pixelass/pen/XJLOXg.css';</style>
  <div class="logo">
    <span>N</span>
    <span>E</span>
    <span>T</span>
    <span>F</span>
    <span>L</span>
    <span>I</span>
    <span>X</span>
  </div>
  <p style="height: 50px"></p>
</div>
```
*scss*
```
/// Create a 3d-shadow in a certain direction
/// @author Gregor Adams
/// @param  {Number}        $depth - 阴影长度
/// @param  {Unit}          $color - 阴影颜色
/// @param  {Unit}          $x     - 在x轴上到下一个阴影的距离
/// @param  {Unit}          $y     - 在y轴上到下一个阴影的距离
/// @param  {Unit}          $blur  - text-shadow的模糊距离
/// @param  {Color|false}   $mix   - 添加一个可选的颜色来混合
/// @return {List}          - 返回一个text-shadow列表
@function d3($depth, $color, $x: 1px, $y: 1px, $blur: 0, $mix: false) {
  $shadow: ();
  @for $i from 1 through $depth {
    // append to the existing shadow
    @if type-of($mix) != 'color' {
      $shadow: append($shadow, round($i * $x) round($i * $y) $blur $color, comma);
    } @else {
      $shadow: append($shadow, round($i * $x) round($i * $y) $blur mix($mix,$color,0.3%*$i), comma);
    }
  }
  @return $shadow;
}
$c_fg:         #f00;
$c_bg:         #fff;
$c_3d:         #f2f2f2;
$c_shadow:     #dadada;
$c_shadow-mix: #6998da;
.fig--3 {
  .logo {
    perspective: 1000px;
    perspective-origin: 50% 0;
    font-size: 8em;
    display: inline-flex;
    span {
      font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
      display: block;
      color: $c_bg;
      $letters: 7;
      @for $i from 1 through $letters {
        $offset: $i - ceil($letters / 2);
        $trans: if($offset > 0, -89.5deg, 89.5deg);
        &:nth-child(#{$i}) {
          // trans/de-form the letters
          transform-origin: 50% + 50%/$offset 200%;
          font-size: if($offset == 0,
            0.85em,
            0.9em + 0.015*pow(abs($offset),2));
          transform: 
              if($offset == 0, scale(1, 1), scale(95.9 - abs($offset) * 10, 1)) 
              if($offset == 0, translatey(0%), rotatey($trans));
           text-shadow: 
            d3(15, $c_3d, if($offset == 0, 0, -0.25px * $offset), 0), 
            d3(30, $c_shadow, 2px, 3px, 3px, $c_shadow-mix);
        }
      }
    }
  }
}
```
*css*
```
.fig--3 .logo {
  perspective: 1000px;
  perspective-origin: 50% 0;
  font-size: 8em;
  display: inline-flex;
}
.fig--3 .logo span {
  font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
  display: block;
  color: #fff;
}
.fig--3 .logo span:nth-child(1) {
  transform-origin: 33.33333% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(89.5deg);
  text-shadow: 1px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 3px 0 0 #f2f2f2, 4px 0 0 #f2f2f2, 5px 0 0 #f2f2f2, 5px 0 0 #f2f2f2, 6px 0 0 #f2f2f2, 7px 0 0 #f2f2f2, 8px 0 0 #f2f2f2, 8px 0 0 #f2f2f2, 9px 0 0 #f2f2f2, 10px 0 0 #f2f2f2, 11px 0 0 #f2f2f2, 11px 0 0 #f2f2f2, 2px 3px 3px #dadada, 4px 6px 3px #d9dada, 6px 9px 3px #d9d9da, 8px 12px 3px #d9d9da, 10px 15px 3px #d8d9da, 12px 18px 3px #d8d9da, 14px 21px 3px #d8d9da, 16px 24px 3px #d7d8da, 18px 27px 3px #d7d8da, 20px 30px 3px #d7d8da, 22px 33px 3px #d6d8da, 24px 36px 3px #d6d8da, 26px 39px 3px #d6d7da, 28px 42px 3px #d5d7da, 30px 45px 3px #d5d7da, 32px 48px 3px #d5d7da, 34px 51px 3px #d4d7da, 36px 54px 3px #d4d6da, 38px 57px 3px #d4d6da, 40px 60px 3px #d3d6da, 42px 63px 3px #d3d6da, 44px 66px 3px #d3d6da, 46px 69px 3px #d2d5da, 48px 72px 3px #d2d5da, 50px 75px 3px #d2d5da, 52px 78px 3px #d1d5da, 54px 81px 3px #d1d5da, 56px 84px 3px #d1d4da, 58px 87px 3px #d0d4da, 60px 90px 3px #d0d4da;
}
.fig--3 .logo span:nth-child(2) {
  transform-origin: 25% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(89.5deg);
  text-shadow: 1px 0 0 #f2f2f2, 1px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 3px 0 0 #f2f2f2, 3px 0 0 #f2f2f2, 4px 0 0 #f2f2f2, 4px 0 0 #f2f2f2, 5px 0 0 #f2f2f2, 5px 0 0 #f2f2f2, 6px 0 0 #f2f2f2, 6px 0 0 #f2f2f2, 7px 0 0 #f2f2f2, 7px 0 0 #f2f2f2, 8px 0 0 #f2f2f2, 2px 3px 3px #dadada, 4px 6px 3px #d9dada, 6px 9px 3px #d9d9da, 8px 12px 3px #d9d9da, 10px 15px 3px #d8d9da, 12px 18px 3px #d8d9da, 14px 21px 3px #d8d9da, 16px 24px 3px #d7d8da, 18px 27px 3px #d7d8da, 20px 30px 3px #d7d8da, 22px 33px 3px #d6d8da, 24px 36px 3px #d6d8da, 26px 39px 3px #d6d7da, 28px 42px 3px #d5d7da, 30px 45px 3px #d5d7da, 32px 48px 3px #d5d7da, 34px 51px 3px #d4d7da, 36px 54px 3px #d4d6da, 38px 57px 3px #d4d6da, 40px 60px 3px #d3d6da, 42px 63px 3px #d3d6da, 44px 66px 3px #d3d6da, 46px 69px 3px #d2d5da, 48px 72px 3px #d2d5da, 50px 75px 3px #d2d5da, 52px 78px 3px #d1d5da, 54px 81px 3px #d1d5da, 56px 84px 3px #d1d4da, 58px 87px 3px #d0d4da, 60px 90px 3px #d0d4da;
}
.fig--3 .logo span:nth-child(3) {
  transform-origin: 0% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(89.5deg);
  text-shadow: 0px 0 0 #f2f2f2, 1px 0 0 #f2f2f2, 1px 0 0 #f2f2f2, 1px 0 0 #f2f2f2, 1px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 2px 0 0 #f2f2f2, 3px 0 0 #f2f2f2, 3px 0 0 #f2f2f2, 3px 0 0 #f2f2f2, 3px 0 0 #f2f2f2, 4px 0 0 #f2f2f2, 4px 0 0 #f2f2f2, 2px 3px 3px #dadada, 4px 6px 3px #d9dada, 6px 9px 3px #d9d9da, 8px 12px 3px #d9d9da, 10px 15px 3px #d8d9da, 12px 18px 3px #d8d9da, 14px 21px 3px #d8d9da, 16px 24px 3px #d7d8da, 18px 27px 3px #d7d8da, 20px 30px 3px #d7d8da, 22px 33px 3px #d6d8da, 24px 36px 3px #d6d8da, 26px 39px 3px #d6d7da, 28px 42px 3px #d5d7da, 30px 45px 3px #d5d7da, 32px 48px 3px #d5d7da, 34px 51px 3px #d4d7da, 36px 54px 3px #d4d6da, 38px 57px 3px #d4d6da, 40px 60px 3px #d3d6da, 42px 63px 3px #d3d6da, 44px 66px 3px #d3d6da, 46px 69px 3px #d2d5da, 48px 72px 3px #d2d5da, 50px 75px 3px #d2d5da, 52px 78px 3px #d1d5da, 54px 81px 3px #d1d5da, 56px 84px 3px #d1d4da, 58px 87px 3px #d0d4da, 60px 90px 3px #d0d4da;
}
.fig--3 .logo span:nth-child(4) {
  transform-origin: Infinity% 200%;
  font-size: 0.85em;
  transform: scale(1, 1) translatey(0%);
  text-shadow: 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 0 0 0 #f2f2f2, 2px 3px 3px #dadada, 4px 6px 3px #d9dada, 6px 9px 3px #d9d9da, 8px 12px 3px #d9d9da, 10px 15px 3px #d8d9da, 12px 18px 3px #d8d9da, 14px 21px 3px #d8d9da, 16px 24px 3px #d7d8da, 18px 27px 3px #d7d8da, 20px 30px 3px #d7d8da, 22px 33px 3px #d6d8da, 24px 36px 3px #d6d8da, 26px 39px 3px #d6d7da, 28px 42px 3px #d5d7da, 30px 45px 3px #d5d7da, 32px 48px 3px #d5d7da, 34px 51px 3px #d4d7da, 36px 54px 3px #d4d6da, 38px 57px 3px #d4d6da, 40px 60px 3px #d3d6da, 42px 63px 3px #d3d6da, 44px 66px 3px #d3d6da, 46px 69px 3px #d2d5da, 48px 72px 3px #d2d5da, 50px 75px 3px #d2d5da, 52px 78px 3px #d1d5da, 54px 81px 3px #d1d5da, 56px 84px 3px #d1d4da, 58px 87px 3px #d0d4da, 60px 90px 3px #d0d4da;
}
.fig--3 .logo span:nth-child(5) {
  transform-origin: 100% 200%;
  font-size: 0.915em;
  transform: scale(85.9, 1) rotatey(-89.5deg);
  text-shadow: -1px 0 0 #f2f2f2, -1px 0 0 #f2f2f2, -1px 0 0 #f2f2f2, -1px 0 0 #f2f2f2, -2px 0 0 #f2f2f2, -2px 0 0 #f2f2f2, -2px 0 0 #f2f2f2, -2px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -4px 0 0 #f2f2f2, -4px 0 0 #f2f2f2, -4px 0 0 #f2f2f2, 2px 3px 3px #dadada, 4px 6px 3px #d9dada, 6px 9px 3px #d9d9da, 8px 12px 3px #d9d9da, 10px 15px 3px #d8d9da, 12px 18px 3px #d8d9da, 14px 21px 3px #d8d9da, 16px 24px 3px #d7d8da, 18px 27px 3px #d7d8da, 20px 30px 3px #d7d8da, 22px 33px 3px #d6d8da, 24px 36px 3px #d6d8da, 26px 39px 3px #d6d7da, 28px 42px 3px #d5d7da, 30px 45px 3px #d5d7da, 32px 48px 3px #d5d7da, 34px 51px 3px #d4d7da, 36px 54px 3px #d4d6da, 38px 57px 3px #d4d6da, 40px 60px 3px #d3d6da, 42px 63px 3px #d3d6da, 44px 66px 3px #d3d6da, 46px 69px 3px #d2d5da, 48px 72px 3px #d2d5da, 50px 75px 3px #d2d5da, 52px 78px 3px #d1d5da, 54px 81px 3px #d1d5da, 56px 84px 3px #d1d4da, 58px 87px 3px #d0d4da, 60px 90px 3px #d0d4da;
}
.fig--3 .logo span:nth-child(6) {
  transform-origin: 75% 200%;
  font-size: 0.96em;
  transform: scale(75.9, 1) rotatey(-89.5deg);
  text-shadow: -1px 0 0 #f2f2f2, -1px 0 0 #f2f2f2, -2px 0 0 #f2f2f2, -2px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -4px 0 0 #f2f2f2, -4px 0 0 #f2f2f2, -5px 0 0 #f2f2f2, -5px 0 0 #f2f2f2, -6px 0 0 #f2f2f2, -6px 0 0 #f2f2f2, -7px 0 0 #f2f2f2, -7px 0 0 #f2f2f2, -8px 0 0 #f2f2f2, 2px 3px 3px #dadada, 4px 6px 3px #d9dada, 6px 9px 3px #d9d9da, 8px 12px 3px #d9d9da, 10px 15px 3px #d8d9da, 12px 18px 3px #d8d9da, 14px 21px 3px #d8d9da, 16px 24px 3px #d7d8da, 18px 27px 3px #d7d8da, 20px 30px 3px #d7d8da, 22px 33px 3px #d6d8da, 24px 36px 3px #d6d8da, 26px 39px 3px #d6d7da, 28px 42px 3px #d5d7da, 30px 45px 3px #d5d7da, 32px 48px 3px #d5d7da, 34px 51px 3px #d4d7da, 36px 54px 3px #d4d6da, 38px 57px 3px #d4d6da, 40px 60px 3px #d3d6da, 42px 63px 3px #d3d6da, 44px 66px 3px #d3d6da, 46px 69px 3px #d2d5da, 48px 72px 3px #d2d5da, 50px 75px 3px #d2d5da, 52px 78px 3px #d1d5da, 54px 81px 3px #d1d5da, 56px 84px 3px #d1d4da, 58px 87px 3px #d0d4da, 60px 90px 3px #d0d4da;
}
.fig--3 .logo span:nth-child(7) {
  transform-origin: 66.66667% 200%;
  font-size: 1.035em;
  transform: scale(65.9, 1) rotatey(-89.5deg);
  text-shadow: -1px 0 0 #f2f2f2, -2px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -3px 0 0 #f2f2f2, -4px 0 0 #f2f2f2, -5px 0 0 #f2f2f2, -6px 0 0 #f2f2f2, -6px 0 0 #f2f2f2, -7px 0 0 #f2f2f2, -8px 0 0 #f2f2f2, -9px 0 0 #f2f2f2, -9px 0 0 #f2f2f2, -10px 0 0 #f2f2f2, -11px 0 0 #f2f2f2, -12px 0 0 #f2f2f2, 2px 3px 3px #dadada, 4px 6px 3px #d9dada, 6px 9px 3px #d9d9da, 8px 12px 3px #d9d9da, 10px 15px 3px #d8d9da, 12px 18px 3px #d8d9da, 14px 21px 3px #d8d9da, 16px 24px 3px #d7d8da, 18px 27px 3px #d7d8da, 20px 30px 3px #d7d8da, 22px 33px 3px #d6d8da, 24px 36px 3px #d6d8da, 26px 39px 3px #d6d7da, 28px 42px 3px #d5d7da, 30px 45px 3px #d5d7da, 32px 48px 3px #d5d7da, 34px 51px 3px #d4d7da, 36px 54px 3px #d4d6da, 38px 57px 3px #d4d6da, 40px 60px 3px #d3d6da, 42px 63px 3px #d3d6da, 44px 66px 3px #d3d6da, 46px 69px 3px #d2d5da, 48px 72px 3px #d2d5da, 50px 75px 3px #d2d5da, 52px 78px 3px #d1d5da, 54px 81px 3px #d1d5da, 56px 84px 3px #d1d4da, 58px 87px 3px #d0d4da, 60px 90px 3px #d0d4da;
}
```
###组装在一起
因为我已经创造了许多我需要的部分，现在可以建立动画里。
#####弹出（动画进入）
我使用两个上面已经定义的变量$offset和$trans，动画有三个阶段，我需要仔细的决定何时到达某点。<p>
```
@keyframes pop-out {
  0% {
    transform:
      if($offset == 0, scale(1, 1), scale(95.9 - abs($offset) * 10, 1))
      if($offset == 0, translatey(0%), rotatey($trans));
    text-shadow:
      d3(15, rgba($c_3d, 0), 0, 0),
      d3(50, rgba($c_shadow, 0), 0, 0);
  }
  50% {
    transform:
      if($offset == 0, scale(1.2, 1.2), scale(126.2 - abs($offset) * 10, 1.2))
      if($offset == 0, translatey(-16%), rotatey($trans));
    text-shadow:
      d3(15, $c_3d, if($offset == 0, 0, -0.25px * $offset), 1px),
      d3(50, $c_shadow, 1px, 3px, 3px, $c_shadow-mix);
  }
  100% {
    transform:
      if($offset == 0, scale(1.1, 1.1), scale(116.2 - abs($offset) * 10, 1.1))
      if($offset == 0, translatey(-12%), rotatey($trans));
    text-shadow:
      d3(15, $c_3d, if($offset == 0, 0, -0.25px * $offset), 1px),
      d3(50, $c_shadow, 1px, 3px, 3px, $c_shadow-mix);
  }
}
```
#####淡出（动画结尾）
同样的步骤实现淡出的效果。
```
@keyframes fade-back {
  0% {
    transform:
      if($offset == 0, scale(1.1, 1.1), scale(116.2 - abs($offset) * 10, 1.1))
      if($offset == 0, translatey(-12%), rotatey($trans));
    text-shadow:
      d3(15, $c_3d, if($offset == 0, 0, -0.25px * $offset), 1px),
      d3(50, $c_shadow, 1px, 3px, 3px, $c_shadow-mix);
  }
  20% {
    transform:
      if($offset == 0, scale(1.05, 1.05), scale(105.9 - abs($offset) * 10, 1.05))
      if($offset == 0, translatey(-7%), rotatey($trans));
    text-shadow:
      d3(15, rgba($c_3d, 0), 0, 0),
      d3(50, rgba($c_shadow, 0), 0, 0);
  }
  100% {
    transform:
      if($offset == 0, scale(1, 1), scale(95.9 - abs($offset) * 10, 1))
      if($offset == 0, translatey(0%), rotatey($trans));
    text-shadow:
      d3(15, rgba($c_3d, 0), 0, 0),
      d3(50, rgba($c_shadow, 0), 0, 0);
  }
}
```
##### 改变字体颜色
还需要提供一个动画改变字体颜色。
```
@keyframes change-color {
  0% {
    color: $c_bg;
  }
  100% {
    color: $c_fg;
  }
}
```
##### 触发这个动画
现在我们可以像下面这样把动画连接在一起。
```
animation-name: pop-out, fade-back, change-color;
animation-duration: 4s, 2s, 0.1s;
animation-delay: 0s, 2s, 3.2s
```
上面的代码只是一个近似的实现，每个字母有不同的动画延迟和间隔，可以点击这里查看最终的实现效果[Netflix animation in pure CSS](http://codepen.io/pixelass/pen/MYYReK)。<p>
最后通知一下，我使用了一些不可思议的技巧来实现在纯CSS中再次触发动画，我将会在接下来的文章中解释。<p>
做实验的时候并不是十分高兴，因为写文章的时候我想到了其它几个提高效果的方法。<p>
为了写这篇文章我重新写了整个Sass代码，但是我仍然觉得我嫩提升一些部分。这就是我不间断做实验的主要原因。让我变得更加聪明，和在一些以前没有涉足过的方向有新的突破。<p>
我几乎没有在实际的项目中用到这样的技术，但是我经常使用函数来提升效果。不论如何希望你喜欢这篇文章。<p>
*[Gregor Adams](https://twitter.com/gregoradams)是一位来自Hamburg的前端开发者，他对CSS和Sass有极大的热情。从他的[codepen](http://codepen.io/pixelass/)中可以看出他强大的CSS技术。*
######原文链接：[Netflix Logo In CSS](http://hugogiraudel.com/2015/04/15/netflix-logo-in-css/#first-concept)
