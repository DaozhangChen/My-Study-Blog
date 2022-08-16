# 《CSS知识总结》
这一周学习了有关CSS的相关知识，在此做一篇知识总结，来梳理一下CSS中的重要知识，本博客将包含以下几个内容：
* 浏览器渲染原理
* CSS动画的两种做法（transform和animation）
* 其他内容  
## 浏览器的渲染原理
浏览器在对网页进行渲染的时候，一定会经历以下六个步骤：
* 根据html构建html树（DOM）
* 根据CSS构建CSS树（CSS DOM）
* 合并，变成渲染树（render tree）
* Layout布局（文档流、盒模型、计算大小和位置）
* Paint绘制（边框颜色、文字颜色、阴影画出来）
* Compose合成（根据层叠关系展示画面） 
首先，我们应该要理解树是什么概念，下面放张图就可以非常清楚的知道了：
![树图](/picture/树图.png)
如上图中所示，通过分叉图的方式，将html与css中的标签和属性的父子关系展现出来的分叉形式，就是DOM树。  
浏览器通过收到的html和css代码，分解成这样的两棵树后，再对其进行合并，成为渲染树（render tree），之后又会计算其中文档流、盒模型的大小和位置，计算完成后则会对文档流、盒模型等进行上色处理，处理完成后则是会对所有内容根据它的层叠关系来展示画面，这也就是我们看到的画面。
以上内容是参考这篇文章：https://web.dev/critical-rendering-path-render-tree-construction/， 大家也可以去了解一下。
## CSS动画的两种做法（transform和animation）
transform和animation可能是最常用的两种制作CSS动画的方式了，首先先来了解transform如何来实现动画。
### transform
首先,我们需要了解transform有哪些属性，常用的属性有以下四种：
* 位移 translate
* 缩放 scale
* 旋转 rotate
* 倾斜 skew  
#### translate 位移
translate有以下几种用法：
```css
transform:translateX(50px);(向X方向向左移动50px)
transform:translateY(50px);(向Y方向向下移动50px)
transform:translateZ(50px);(离视点的距离，近大远小，需要定义一个视点,且父容器需要perspective)
transform:translate(x,y);(填写向X,Y轴的移动距离，控制移动)
transform:translate3d(x,y,z);
```
向X轴移动动画如下：
![](/picture/translateX.gif)  
下面要重点讲述如何向Z轴移动，首先需要一个父容器定义一个视点，下面我在这个黑色框中的中心定义一个离用户垂直距离为100像素的视点，其中红色框为黑色框的子元素,设置红色框向Z轴方向上移动50px。
![](/picture/transplateZ.png)
效果如下所示：
![](/picture/Z轴.gif)  
这样看起来红色框离我们的距离越来越近，逐渐放大，当Z轴数值为负数时，则会向下移动，看起来越来越小。
![](/picture/Z轴下.gif)  
**注意：放大的同时，连边框都会等比例放大。**
#### scale 缩放
scale有以下几种用法：
```css
transform:scale(1.5);（放大1.5倍）
transform:scaleX(1.5);（横向放大1.5倍）
transform:scaleY(1.5);（纵向放大1.5倍）
```
X轴和Y轴放大如下所示：  
X轴放大  
![](/picture/scaleX.gif)  
Y轴放大
![](/picture/scaleY.gif)
#### rotate 旋转
rotate用法如下：
```css
transform:rotate(45deg);(Z轴顺时针旋转45°)
transform:rotateX(45deg);(沿着X轴顺时针旋转45°)
transform:rotateY(45deg);(沿着y轴顺时针旋转45°)
```
Z轴旋转图如下：  
![](/picture/rotateZ.gif)
X轴旋转图如下：
![](/picture/rotateX.gif)
Y轴旋转图如下：
![](/picture/rotateY.gif)
#### skew 倾斜
skew用法如下：
```css
skewX(15deg);（往x轴方向倾斜15度）
skewY(15deg);（往y轴方向倾斜15度）
skew(15deg,25deg);（往x轴方向倾斜15度，往y轴方向倾斜25度）
```
以下只示范向X轴倾斜:
![](/picture/skewX.gif)
#### 重要补充
需要完成动画，**必须**加上一句话（transition称为过渡）：
```css
transition:all 1s;(意思是所有属性加上动画效果，1s完成)
```
以上代码的完整组成为：
```css
transition:属性名 时长 过渡方式 延迟;
```
其中：过渡方式有：linear、ease、ease-in、ease-out、ease-in-out、cubic-bezier、step-start、step-end、steps，具体需要靠数学知识  
分别是，线性匀速，先快后慢，先慢再快、先快后慢、先慢再快再慢、剩下的自己查资料。  
并非所有元素都可以过渡  
display:none>>>block就没法过渡  
一般用visibility:hidden>>>>visible  
visibility和display的区别在于，前者会在变换后原地消失，会占位置，后者会直接原地消失，不占位置。  
颜色可以过渡，opacity透明度也可以过渡。  
另外，上面的属性都可以结合使用，比如在旋转的时候也倾斜，就可以写成：  
```css
transform:rotate(15deg) skew(15deg);
```
即可。
### animation
直接看代码：
```css
#demo.start{
  animation: xxx 1.5s;
}


@keyframes xxx {
  0% {
    transform: none;
  }
  66.66%{
    transform: translateX(200px);
  }
  100%{
    transform: translateX(200px) translateY(100px);
  }
}
```
先来看代码中的@keyframes
这个是animation中的关键帧，是为了说明各个阶段的运动终点，比如在代码中的66.66%，说明在动画在进行到66.66%的时间时，应该是处于transform: translateX(200px);的状态，剩下的同理，其中，transform的取值与上述一致。  
除此之外：  
还有一种form和to的写法：
```css
@keyframes xxx {
  form{
    transform: none;
  }
  to{
    transform: translateX(200px) translateY(100px);
  }
}
```
但是这种写法只能规定初始和结束两种时间点的状态，故灵活性没有上一种来得高。  
在上述代码中还需加入animation，这是规定了该动画的完成形式，完整语法如下：
```css
animation:时长丨过渡方式丨延迟丨次数丨方向丨填充模式丨是否暂停丨动画名；
```
里面所有的丨代表空格。  
* 过渡方式取值与transition中的取值相同
* 其余属性在MDN中有详细介绍：https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation 可以自行查看。
## 其他内容
这是我的第三篇博客了，其中如果有错误的地方可以说出来让我知道哦，也没有特别技术上的研究，主要还是作为我的一个学习记录和心得，里面的内容可能有些片面请多多谅解，非常感谢！
