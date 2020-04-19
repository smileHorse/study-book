# `CSS`定位元素

​	可见的页面版式主要由三个属性控制：`position`、`display`、`float`属性。其中`position`控制页面上元素间的位置关系，`display`控制元素是否堆叠、并排，还是根本不在页面上出现，`float`提供控制的方式，以便把元素组成多栏布局。

## 1 盒子模型

​	默认情况下，每个盒子的边框不可见，背景也是透明的。`CSS`为边框、内边距、外边距分别规定了简写属性，通过一条声明就可以完成设定。每个简写声明中，内边距和外边距的属性值顺序都是上、右、下、左。属性值之间只能使用空格进行分隔，可以不写出全部的4个属性，没有写出的属性值，就使用对边的属性值。

```css
{margin-top: 5px; margin-right: 10px; margin-bottom: 12px; margin-left: 8px}
{margin: 5px 10px 12px 8px;}
{margin: 12px 10px 6px}
{margin: 12px 10px}
{margin: 12px}
```

​	另外，每个盒子的属性也分为3个粒度，到底选择哪个粒度的属性，要看你想选择哪条边，以及那条边的哪个属性。这三种粒度从一般到特殊分别举例如下：

~~~css
// 1.全部3个属性，全部4条边
{border: 2px solid red;}
// 2.1个属性，全部四条边
{border-style: dashed;}
// 3.1个属性，1条边
{border-left-style: dashed;}
~~~

​	边框有4个属性：`border-width,border-style,border-color,border-radius`，不过`border-radius`不影响盒模型的定位。默认情况下，边框的三个相关的属性值分别为：

~~~css
{border-width:medium; border-style: none; border-color:black;}
~~~

​	由于`border-style`的默认值为`none`，所以不会显示盒子的边框。

​	**中和外边距和内边距：**`推荐大家把下面的规则作为样式表中的第一条规则： * {margin: 0; padding: 0;}。这条规则把所有元素的默认外边距和内边距都设为零。应用该样式表后，所有默认的外边距和内边距都会消失。然后就可以为那些真正需要内外边距的元素添加内外边距。因为不同浏览器的默认内外边距不一样，通过这种方式，可以在各浏览器中获得一致的效果。推荐使用Eric Meyer写的重置样式表reset.css，下载地址https://meyerweb.com/eric/tools/css/reset/。`

### 1.4 叠加外边距

​	垂直外边距会进行叠加，较宽的外边距决定了两者之间的叠加外边距，水平外边距不会叠加。

### 1.5 外边距的单位

​	根据经验，为文本元素设置外边距时通常需要混合使用不同的单位。一个段落的左、右外边距可以使用像素，以便该段文本始终与包含元素边界保持固定间距，不受字号变大或变小的影响。而对于上、下边距，以`em`为单位则可以让段间距随字号变化而相应增大或减小。比如：

~~~css
p { font-size: 1em; margin: 0.75em 30px; }
~~~

​	这样，段落的垂直间距始终会保持为字体高度的3/4。如果用户增大了字号，那么不仅段落中的文本会变大，段间距也会成比例增大。这样，页面的整体布局也会比较协调一致。与此同时，使用像素单位的左、右外边距不会改变。

## 2 盒子有多大

- 结论一：没有设置`width`属性的元素始终会扩展到填满其父元素的宽度为止。添加水平边框、内边距和外边距，会导致内容宽度减少，减少量等于水平边框、内边距和外边距之和。
- 结论二：为设定了宽度的盒子添加边框、内边距和外边距，会导致盒子扩展得更宽。实际上，盒子的`width`属性设定的只是盒子内容区的宽度，而非盒子要占据的水平宽度。
- `CSS3`新增加了一个`box-sizing`属性，通过它可以将有宽度的盒子也设定成具有默认的`auto`状态下的行为。

## 3 浮动`float`和清除'clear'

​	`CSS`设计`float`属性的主要目的，是为了实现文本绕排图片的效果。然而，这个属性也成了创建多栏布局最简单的方式。

​	浮动就是把元素从常规文档流中拿出来，原来紧跟其后的元素就会在空间允许的情况下，向上提升到与浮动元素平起平坐。浮动原则就像是：“尽量把这个元素网上放，能放多高放多高，直到碰到某个元素的边界为止。”

​	**浮动非图片元素时，必须给它设定宽度，否则后果难以预料。图片无所谓，因为它本身有默认的宽度。**

​	使用`clear`属性的元素，可以呆在浮动元素的下面。

### 3.1 围住浮动元素的三种方法

​	由于浮动元素脱离了文档流，其父元素也看不到它了，因而也不会包围它，这种情况会对布局产生破坏性的影响。这里以一个例子来介绍三种包围浮动元素的三种方法，原始脚本为：

~~~html
<section>
	<img src='...'>
    <p>It's fun to float.</p>
</section>
<footer>Here is the footer element that runs across the bottom of the page.</footer>
~~~

~~~css
section { border: 1px solid blue; margin: 0 0 10px 0; }
img { float: left; }
p { margin: 0; }
footer { border: 1px solid red; }
~~~

​	这种样式下，标题和`footer`都会跑到`img`的右边，不是我们需要的效果。

- 方法一：为父元素添加`overflow: hidden`

这种方式很简单，但是不太直观，为父元素添加`overflow:hidden;`，以强制它包围浮动元素：

~~~css
section { border: 1px solid blue; margin: 0 0 10px 0; overflow: hidden; }
~~~

实际上，`overflow: hidden`声明的真正用途是防止包含元素被超大内容撑大。应用该属性后，包含元素依然保持其设定的宽度，而超大的子内容则会被容器剪切掉。除此之外，`overflow: hidden`还有另一个作用，即它能可靠地强迫父元素包含其浮动的子元素。

- 方法二：同时浮动父元素

~~~css
section { border: 1px solid blue; margin: 0 0 10px 0; float: left; width: 100%; }
img { float: left; }
p { margin: 0; }
footer { border: 1px solid red; clear: left; }
~~~

​	浮动`section`以后，不管其子元素是否浮动，它都会紧紧的包围住它的子元素。因此，需要用`width: 100%`再让`section`与浏览器容器等宽。另外，由于`section`现在也浮动了，所以`footer`会努力往上挤到它的旁边去。为了强制`footer`呆在`section`下方，要给它应用`clear: left`。

- 方法三：添加非浮动的清除元素

  给父元素的最后添加一个非浮动的子元素，然后清除该子元素。由于包含元素一定会包围非浮动的子元素，而且清除会让这个子元素位于浮动元素的下方，因此包含元素一定会包含这个子元素，以及前面的浮动元素。

~~~html
<section>
	<img src='...'>
    <p>It's fun to float.</p>
    <div class="clear_me"></div>
</section>
<footer>Here is the footer element that runs across the bottom of the page.</footer>
~~~

~~~css
section { border: 1px solid blue; margin: 0 0 10px 0; }
img { float: left; }
p { margin: 0; }
.clear_me { clear: both; }
footer { border: 1px solid red; }
~~~

​	也可以通过`CSS`的伪元素来添加这个清除元素的方法：

~~~html
<section class="clearfix">
	<img src='...'>
    <p>It's fun to float.</p>
</section>
<footer>Here is the footer element that runs across the bottom of the page.</footer>
~~~

~~~css
/*规则中的其他声明是为了确保这个伪元素没有高度，而且在页面上不可见。*/
.clearfix::after {
    content: '.';
    display: block;
    height: 0;
    visibility: hidden;
    clear: both;
}
~~~

​	这三种方法的使用要因地制宜。比如，不能在下拉菜单的顶级元素上应用`overflow: hidden`，否则作为其子元素的下拉菜单就不会显示了。因为下拉菜单需要显示在其父元素区域的外部，而这恰恰是`overflow:hidden`所要阻止的。再比如，不能对已经靠自动外边距居中的元素使用“浮动父元素”方法，否则它就不会再居中，而是根据浮动值浮动到左边或右边了。

### 3.2 没有父元素时如何清除

​	在没有父元素作为容器的情况下，最简单的办法就是给一个浮动元素应用`clear: both`，强迫它定位在前一个浮动元素下方。然而，在空间足以容纳多个元素向上浮动时，可以对浮动元素旁边的元素应用前面提到的`.clearfix`规则。

## 4 定位

​	`CSS`布局的核心是`position`属性，对元素盒子应用这个属性，可以相对于它在常规文档流中的位置重新定位。`position`有4个属性值：`static, relative, absolute, fixed`，默认值是`static`。

- `static:`静态定位。每个元素都处在常规文档流中。
- `relative:`相对定位，相对的是它原来在文档流中的位置或者默认位置。光设置这个属性还看不出来布局有什么不一样，可以使用`top、right、bottom、left`属性来改变它的位置。多数情况下，只用`top`和`left`就可以实现我们想要的效果。要注意，除了这个元素自己相对于原始位置挪动了一下之外，页面没有发生任何变化。换句话说，这个元素原来占据的空间并没有动，其他元素也没有动。
- `absolute:`绝对定位。同样需要配合`top、right、bottom、left`使用，它会把元素彻底从文档流中拿出来，然后相对于它的定位上下文进行移动。绝对定位元素默认的定位上下文是`body`元素。
- `fixed:`固定定位。固定定位元素的定位上下文是视口（浏览器窗口或手持设备的屏幕），因此它不会随页面的滚动而移动。

### 4.1 定位上下文

​	把元素的`position`属性设定为`relative, absolute, fixed`后，继而可以使用`top、right、bottom、left`属性，相对于另一个元素移动该元素的位置。这里的另一个元素就是该元素的定位上下文。

​	绝对定位元素默认的定位上下文是`body`元素。这是因为`body`元素是标记中所有元素唯一的祖先元素。而实际上，绝对定位元素的任何祖先元素都可以成为它的定位上下文，只要把相应祖先元素的`position`设定为`relative`即可。

## 5 显示属性

​	块级元素和行内元素的区别：

- 块级元素，比如段落、标题、列表等，在浏览器中上下堆叠显示。

- 行内元素，比如`a、span、img`，在浏览器中左右并排显示，只有前一行没有空间时才会显示到下一行。

  块级元素和行内元素互相切换的魔法如下：

~~~css
p { display: inline; }	/*默认为block*/
a { display: block; }	/*默认为inline*/
~~~

​	还可以设置`display`的属性为`none`，该元素及所有包含在其中的元素，都不会在页面中显示。它们原来占据的空间也都会被回收，就好像相关的标记根本不存在一样。与此相对的是`visibility`属性，这个属性最常用的两个相对的值是`visible(默认值)`和`hidden`。设置为`hidden`时，元素会隐藏，但它占据的页面空间仍然虚位以待。

## 6 背景

​	每个元素盒子都可以想象成由两个图层组成。元素的前景层包括内容（如文本和图片）和边框，元素的背景层可以用实色填充（`background-color`属性），也可以包含任意多个背景图片（`background-image`属性），背景图片叠加在背景颜色之上。

- `background-color:`以设定的颜色填充背景图层。(`color`属性设定的是前景图层，前景色会影响元素的内容。如果没有设定边框颜色，边框就会使用`color`属性设定的颜色作为边框颜色)。
- `background-image:`默认情况下，背景图片会以元素左上角为起点，沿水平和垂直方向重复出现，最终填满整个背景区域。

~~~css
{ background-image: url(images/blue.png);}
~~~

指定背景图片来源的方式，与`img`标签中的方式不同，图片地址两边不用加引号，当然加了也没有问题。

- `background-repeat:`可选属性值包括`repeat/repeat-x/repeat-y/no-repeat`；

  `CSS3`还规定了另外两个值，但尚未得到浏览器的支持，以控制背景图片重复确切的次数，即所有图片都是完整的，不会出现半张图片的现象。

  - `background-repeat: round:`为确保图片不被剪切，通过调整图片大小来适应背景区域；
  - `background-repeat: space:`为确保图片不被剪切，通过在图片间添加空白来适应背景区域；

- `background-position:`这个属性有5个关键字值：`top、left、bottom、right和center`，这些关键字中的任意两个组合起来都可以作为该属性的值。比如，`top left`表示把图片放到元素的左上角位置，`center center`把图片放在元素的中心位置。

  要注意，`background-position`属性同时设定元素和图片的原点。原点决定了元素和图片中某一点的水平和垂直坐标。默认情况下，`background-position`的原点位于左上角。换句话说，元素的左上角和图片的左上角是对齐的，随后图片向各个方向重复。`background-position: center center`设定图片的中心点与元素的中心点重合，然后再向各个方向重复。

  通过把`background-position`设定为`50% 50%`，把`background-repeat`设定为`no-repeat`，可以实现图片在背景区域内居中的效果。

~~~
设定背景位置时可以使用三种值：关键字、百分比、绝对或相对单位的数值。可以使用两个值分别设定水平和垂直位置。
关键字值的顺序不重要，left bottom和bottom left意思相同。为了设定的值在所有浏览器中都有效，最好不要换用关键字值与数字值。
使用数字（比如40% 30%）时，第一个值表示水平位置，第二值表示垂直位置。要是只设定一个值，则将其用来设定水平位置，而垂直位置会被设为center。
在使用关键字和百分比值的情况下，设定的值同时用于元素和图片。换句话说，如果设定了33% 33%，则图片水平33%的位置与元素水平33%的位置对齐，垂直方面也一样。
像素之类的绝对单位数字就不一样了。要是用像素单位来设定位置，那么图片的左上角会被放在距离元素左上角指定位置的地方。
有意思的是，还可以使用负值。这样就可以把图片的一部分定位到元素外部，从而在元素中只能看到部分图片。
~~~

- `background-size:`这个属性用来控制背景图片的尺寸，可以给它设定的值及含义如下：
  - `50%：`缩放图片，使其填充背景区的一半；
  - `100 px 50px:`把图片调整到100像素宽，50像素高；
  - `cover：`拉大图片，使其完全填满背景区；保持等宽比；
  - `contain：`缩放图片，使其恰好适合背景区；保持等宽比；
- `background-attachment:`该属性控制滚动元素内的背景图片是否随元素滚动而移动。这个属性的默认值是`scroll`，即背景图片随元素移动。如果属性值为`fixed`，那么背景图片不会随元素滚动而移动。`background-attachment:fixed`最常用于给`body`元素中心位置添加淡色水印，让水印不随页面滚动而移动。
- `background(简写属性):`该属性可以用来设定所有背景相关的值。声明中少写了哪些属性的值，就会使用相应属性的默认值。
- `background-clip（目前尚未得到广泛支持）:`控制背景绘制区域的范围，比如可以让背景颜色和背景图片只出现在内容区，而不出现在内边距区域。默认情况下，背景绘制区域是扩展到边框外边界的。
- `background-origin（目前尚未得到广泛支持）:`控制背景定位区域的原点，可以设定为元素盒子左上角以外的位置。比如，可以设定以内容区左上角作为原点。
- `background-break（目前尚未得到广泛支持）:`控制分离元素（比如跨越多行的行内盒子）的显示效果。

## 6.1 多背景图片

​	`CSS3`可以给元素背景添加多个背景图片，例如：

~~~css
p {
    background:
        url(images/turq_sprial.png) 30px -10px no-repeat,
        url(images/pink_sprial.png) 145px 0px no-repeat,
        url(images/gray_sprial.png) 140px -30px no-repeat, #ffbd75;
}
~~~

​	这里，为了防止图片加载失败时元素背景处于默认的透明状态，也在最后一条声明中加上了背景颜色。要注意的是，代码中先列出的图片显示在上方，更接近前景。

**厂商前缀**

~~~
VSP是为鼓励浏览器厂商尽快采用W3C的CSS3推荐标准，才产生的概念。以transform属性为例：
-moz-transform：skewX(-45deg);		/*Firefox*/
-webkit-transform：skewX(-45deg);	/*Chrome及Safari*/
-ms-transform：skewX(-45deg);		/*IE*/
-o-transform：skewX(-45deg);			/*Opera*/
transform：skewX(-45deg);			/*W3C标准属性*/
以下CSS3属性必须加上VSP:
border-image	linear-gradient		radial-gradient		transform		transform-origin
translate		transition			background*			background-image*
*针对背景图片或渐变
~~~

### 6.2 背景渐变

​	渐变分为两种，一种线性渐变，一种放射性渐变。线性渐变从元素的一端延伸到另一端，放射性渐变则从元素内一点向四周发散。

#### 6.2.1 线性渐变

~~~html
<div class='gradient1'></div>
<div class='gradient2'></div>
<div class='gradient3'></div>

div {
	height: 150px;
	width: 200px;
	border: 1px solid #ccc;
	float: left;
	margin: 16px;
}
/*默认为从上到下*/
.gradient1 {
	background: linear-gradient(#e86a43, #fff);
}
/*从左到右*/
.gradient2 {
	background: linear-gradient(left, #64d1dd, #fff);
}
/*左上到右下, deg是度的意思，-45deg等于把起点从默认的中上设定到了左上*/
.gradient3 {
	background: linear-gradient(-45deg, #e86a43, #fff);
}
~~~

渐变点就是渐变方向上的点，可以在这些点上设定颜色和不透明度。通过设定下一个渐变点的颜色值，就可以控制渐变的效果。可以添加任意多个渐变点，渐变点的位置一般使用整个渐变宽度的百分比来表示。

~~~css
/*50%处有一个渐变点.渐变效果是从开始颜色到渐变点颜色，然后再从渐变点颜色到结束颜色。注意，开始位置和结束位置如果没有声明，默认为0%和100%*/
.gradient1 {
    background: linear-gradient(#64d1dd, #fff 50%, #64d1dd);
}
/*20%和80%处有两个渐变点。这里起点和终点不是0%和100%。此时，在第一个渐变点20%之前，是第一个渐变点声明的实色，而在该点之后，则是到下一个渐变点颜色的过渡。同样，在最后一个渐变点80%之后，该渐变点的颜色就会以实色扩展到元素结束。*/
.gradient2 {
    background: linear-gradient(#e86a43 20%, #fff 50%, #e86a43 80%);
}
/*25%、50%、75%处有三个渐变点*/
.gradient3 {
    background: linear-gradient(#64d1dd, #fff 25%, #64d1dd 50%， #fff 75%, #64d1dd);
}
/*为同一个渐变点设定两种颜色可以得到突变效果*/
.gradient4 {
    background: linear-gradient(#e86a43, #fff 25%, #64d1dd 25%， #64d1dd 75%, #fff 75%, #e86a43);
}
~~~

#### 6.2.2 放射性渐变

渐变属性其实就是函数，可以根据传入的参数来生成渐变。在创建放射性渐变时，可以使用参数指定形状、位置、尺寸、颜色和不透明度。

~~~css
/*默认的渐变形状，即渐变效果会填充元素。如果元素是正方形，那渐变就是圆形*/
.gradient1 {
    background: radial-gradient(#fff, #64d1dd, #70aa25);
}
/*圆形渐变，渐变的形状变得均匀，并在元素最近的边达到了终点。而长边剩下的区域则填充了终点的颜色*/
.gradient2 {
    background: radial-gradient(circle, #fff, #64d1dd, #70aa25);
}
/*把渐变的圆心放到了靠近左上角50px 30px的位置*/
.gradient3 {
    background: radial-gradient(50px 30px, circle, #fff, #64d1dd, #70aa25);
}
~~~

