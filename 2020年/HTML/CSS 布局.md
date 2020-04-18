# `CSS` 布局

​	使用`float`属性或`position`属性进行页面布局时有一个显著的缺点，就是第一个`div`元素与第二个`div`元素是各自独立的，如果在第一个`div`元素中加入一些内容，将会使得两个元素的底部不能对齐，导致页面中多出一块空白区域。

## 1 多栏布局（`IE9`及以下版本好像不支持）

​	多栏布局可以解决`float`属性或`position`属性进行页面布局时的缺点。它可以将一个元素中的内容分为两栏或多栏显示，并且确保各栏中内容的底部对齐。

​	在`CSS3`中，通过`column-count/-moz-column-count/-webkit-column-count`属性来使用多栏布局样式，该属性的含义是将一个元素中的内容分为多栏进行显示。

​	使用多栏布局时，需要将元素的宽度`width`设置成多个栏目的总宽度。而使用`float`属性或`position`属性时需要单独设定每个元素的宽度。

~~~html
<head>
	<title>多栏布局的示例</title>
	<style type="text/css">
		div#div1 {
			width: 40em;
			column-count: 2;
			-moz-column-count: 2;
			-webkit-column-count: 2;
		}
		div#div3 {
			width: 100%;
			background-color: yellow;
			height: 200px;
		}
	</style>
</head>
<body>
	<div id="div1">
		<img src="test.png">
		<p>示例文字1.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。</p>
		<p>示例文字2.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。</p>
	</div>
	<div id="div3">
		页面中其他内容
	</div>
</body>
~~~

也可以使用`column-width/-moz-column-width/-webkit-column-width`属性单独设置每一栏的宽度而不设定元素的宽度。使用`column-width`属性指定每栏宽度而不指定元素的宽度时，需要在元素外面单独设定一个容器元素，然后指定该元素的宽度。

~~~html
<head>
	<title>多栏布局的示例</title>
	<style type="text/css">
		div#container {
			width: 42em;
		}
		div#div1 {
			column-count: 2;
			-moz-column-count: 2;
			-webkit-column-count: 2;
			column-width: 20em;
			-moz-column-width: 20em;
			-webkit-column-width: 20em;
		}
		div#div3 {
			width: 100%;
			background-color: yellow;
			height: 200px;
		}
	</style>
</head>
<body>
	<div id="container">		
		<div id="div1">
			<img src="test.png">
			<p>示例文字1.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。</p>
			<p>示例文字2.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。</p>
		</div>
		<div id="div3">
			页面中其他内容
		</div>
	</div>
</body>
~~~

​	可以使用`column-gap/-moz-column-gap/-webkit-column-gap`属性设定多栏之间的间隔距离。

~~~css
div#container {
    width: 45em;
}
div#div1 {
    column-count: 2;
    -moz-column-count: 2;
    -webkit-column-count: 2;
    column-width: 20em;
    -moz-column-width: 20em;
    -webkit-column-width: 20em;
    column-gap: 3em;
    -moz-column-gap: 3em;
    -webkit-column-gap: 3em;
}
~~~

​	可以使用`column-role/-moz-column-role/-webkit-column-gap`属性在栏与栏之间增加一条间隔线，并且设定该间隔线的宽度、颜色等，该属性的属性值的指定方法与`CSS`中的`border`属性的属性值的指定方法相同。

~~~css
column-rule: 1px solid red;
-moz-column-rule: 1px solid red;
-webkit-column-rule: 1px solid red;
~~~

## 2 盒布局（`IE`好像不支持）

​	在`CSS3`中，通过`box/-moz-box/-webkit-box`属性来使用盒布局。

​	在最外层的`div`元素中使用`box`属性，并去除左侧边栏、中间内容、右侧边栏`div`元素的`float`属性。

~~~html
<head>
	<title>使用盒布局的示例</title>
	<style type="text/css">
		#container {
			display: box;
			display: -moz-box;
			display: -webkit-box;
		}
		#left-sidebar {
			width: 200px;
			padding: 20px;
			background-color: orange;
		}
		#contents {
			width: 300px;
			padding: 20px;
			background-color: yellow;
		}
		#right-sidebar {
			width: 200px;
			padding: 20px;
			background-color: limegreen;
		}
		#left-sidebar, #contents, #right-sidebar {
			box-sizing: border-box;
		}
	</style>
</head>
<body>
	<div id="container">
		<div id="left-sidebar">
			<h2>左侧边栏</h2>
			<ul>
				<li><a href="">超链接</a></li>
				<li><a href="">超链接</a></li>
				<li><a href="">超链接</a></li>
				<li><a href="">超链接</a></li>
				<li><a href="">超链接</a></li>
			</ul>
		</div>

		<div id="contents">
			<h2>内容</h2>
			<p>示例文字1.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。示例文字.相对来说比较长的示例文字。</p>
		</div>

		<div id="right-sidebar">
			<h2>右侧边栏</h2>
			<ul>
				<li><a href="">超链接</a></li>
				<li><a href="">超链接</a></li>
				<li><a href="">超链接</a></li>
			</ul>
		</div>
	</div>
</body>
~~~

### 2.1 盒布局与多栏布局的区别

​	使用多栏布局时，各栏宽度必须是相等的，在指定每栏宽度时，也只能为所有栏指定一个统一的宽度，栏与栏之间的宽度不可能是不一样的。也不可能具体指定什么栏中显示什么内容，因此比较适合使用在显示文章内容的时候，不适合用于安排整个网页中由各元素组成的网页结构的时候。

## 3 弹性盒布局

## 3.1 对多个元素使用`flex`属性

​	在第2节的示例中，我们对代表左侧边栏、中间内容、右侧边栏的三个`div`元素进行了宽度设定。如果我们想让这三个元素的总宽度等于浏览器的宽度，应该如何进行设置？

​	在使用`float`或`position`属性时，我们需要使用包括负外边距(`margin`)在内的比较复杂的指定方法才能达到这个要求。如果使用盒布局，只需要使用一个`flex`属性，使盒布局变为弹性盒布局就可以了。

~~~html
<style type="text/css">
		#container {
			display: flex;
		}
		#left-sidebar {
			width: 200px;
			padding: 20px;
			background-color: orange;
		}
		#contents {
			flex: 1;
			padding: 20px;
			background-color: yellow;
		}
		#right-sidebar {
			width: 200px;
			padding: 20px;
			background-color: limegreen;
		}
		#left-sidebar, #contents, #right-sidebar {
			box-sizing: border-box;
		}
	</style>
~~~

### 3.2 改变元素的显示顺序

​	使用弹性盒布局的时候，可以通过`order`属性来改变各元素的显示顺序。可以在每个元素中增加`order`属性，该属性使用一个表示序号的整数属性值，浏览器在显示的时候根据该序号从小到大显示这些元素。

~~~html
<style type="text/css">
		#container {
			display: flex;
		}
		#left-sidebar {
			order: 3;
			width: 200px;
			padding: 20px;
			background-color: orange;
		}
		#contents {
			order: 1;
			flex: 1;
			padding: 20px;
			background-color: yellow;
		}
		#right-sidebar {
			order: 2;
			width: 200px;
			padding: 20px;
			background-color: limegreen;
		}
		#left-sidebar, #contents, #right-sidebar {
			box-sizing: border-box;
		}
	</style>
~~~

### 3.3 改变元素的排列方向

​	使用弹性盒布局的时候，可以很简单的将多个元素的排列方向在水平方向和垂直方向间进行切换。在`CSS3`中，使用`flex-direction`属性来指定多个元素的排列方向。可指定值如下所示：

- `row:`横向排列，默认值；
- `row-reverse:`横向反向排列；
- `column：`纵向排列；
- `column-reverse:`纵向反向排列；

~~~css
#container {
    display: flex;
    flex-direction: column;
}
~~~

### 3.4 元素宽度与高度的自适应

​	使用盒布局的时候，元素的宽度与高度具有自适应性，可以根据排列方向的改变而改变。水平方向排列时，子元素的宽度为元素中内容的宽度，高度自动变为容器的高度；垂直方向排列时，子元素的高度为元素中内容的高度，宽度自动变为容器的宽度。虽然宽度和高度具有自适应性，但是容器中总是会留出一大片空白的区域。

### 3.5 使用弹性盒布局来消除空白

​	使用弹性盒布局，在其中一个子元素的样式中加入`flex`属性，使该元素的宽度和高度自动扩大，使得参与排列的元素的总宽度和总高度始终等于容器元素的宽度和高度。

### 3.6 对多个元素使用`flex`属性

​	可以对容器的多个元素使用`flex`属性，如：

~~~css
#container {
    display: flex;
    border: solid 5px blue;
    flex-direction: column;
    width: 500px;
    height: 300px;
}
#text-a {
    background-color: orange;
    flex: 2;
}
#text-b {
    background-color: yellow;
    flex: 1;
}
#text-c {
    background-color: limegreen;
    flex: 1;
}
#text-a, #text-b, #text-c {
    box-sizing: border-box;
    font-size: 1.5em;
    font-weight: bold;
}
~~~

​	这样三个子元素都会自动扩大，但第1个子元素的高度并不等于其他2个资源高度的两倍。`flex`属性的含义是将容器中空白的区域按照子元素`flex`属性的总和进行平分，然后按比例分配到各子元素中。

​	`text-a`元素的宽度为`300 / (2 + 1 + 1) * 2 = 150px`；`text-b、text-c`元素的宽度为`300 / (2 + 1 + 1) * 1 = 75px`；

​	可以使用`flex-grow`属性来指定元素高度和宽度，但是该样式属性对于元素宽度的计算方法与`flex`样式属性对于元素宽度或高度的计算方法有所不同。

~~~css
#container {
    display: flex;
    border: solid 5px blue;
    flex-direction: row;
    width: 600px;
    height: 300px;
}
#text-a, #text-b, #text-c {
    box-sizing: border-box;
    font-size: 1.5em;
    font-weight: bold;
    width: 150px;
    flex-grow: 1;
}
#text-a {
    background-color: orange;
}
#text-b {
    background-color: yellow;
    flex-grow: 3;
}
#text-c {
    background-color: limegreen;
}
~~~

使用`flex-grow`属性时，计算过程为：

1. 空白区域的宽度为`600 - 150 * 3 = 150px`;
2. 每个`flex-grow`属性所表示的宽度为`150 / (3 + 1 + 1) = 30px`;
3. `text-a、text-c`元素的宽度为`150 + 30 * 1 = 180px`;
4. `text-b`元素的宽度为`150 + 30 * 3 = 240px`; 

与`flex-grow`属性相对应，可以使用`flex-shrink`属性来指定元素的宽度或高度。区别在于：当元素排列方向为横向排列时，如果子元素的`width`属性值的总和小于容器元素的宽度值，必须使用`flex-grow`属性来调整元素宽度，如果子元素的`width`属性值的总和大于容器元素的宽度值，必须使用`flex-shrink`属性来调整元素宽度；当元素排列方向为纵向排列时，如果子元素的`height`属性值的总和小于容器元素的高度值，必须使用`flex-grow`属性来调整元素宽度，如果子元素的`height`属性值的总和大于容器元素的高度值，必须使用`flex-shrink`属性来调整元素宽度；

~~~css
#container {
			display: flex;
			border: solid 5px blue;
			flex-direction: row;
			width: 600px;
			height: 300px;
		}
		#text-a, #text-b, #text-c {
			box-sizing: border-box;
			font-size: 1.5em;
			font-weight: bold;
			width: 250px;
			flex-shrink: 1;
		}
		#text-a {
			background-color: orange;
		}
		#text-b {
			background-color: yellow;
			flex-shrink: 3;
		}
		#text-c {
			background-color: limegreen;
		}
~~~

使用`flex-shrink`属性时，计算过程为：

1. 子元素宽度总和超过容器与元素的宽度为`250 * 3 - 600 = 150px`;
2. 每个`flex-shrink`属性所表示的宽度为`150 / (3 + 1 + 1) = 30px`;
3. `text-a、text-c`元素的宽度为`250 - 30 * 1 = 220px`;
4. `text-b`元素的宽度为`250 - 30 * 3 = 160px`; 

​    在使用`flex-grow`或`flex-shrink`属性调整子元素宽度时，也可以使用`flex-basis`属性指定调整前的子元素宽度，该样式属性与`width`属性的作用完全相同。

​	可以将`flex-grow、flex-shrink、flex-basis`属性值合并写入`flex`样式属性中，如下所示：

~~~csss
flex: flex-grow样式属性值	flex-shrink样式属性值	flex-basis样式属性值
~~~

​	这样使用时，三个属性值都为可选值，`flex-grow`和`flex-shrink`的默认值为1，`flex-basis`的默认值为`0px`。

### 3.7 控制换行方式

可以使用`flex-wrap`样式属性来指定单行布局或多行布局，可指定值为：

- `nowrap:`不换行；
- `wrap:`换行；
- `wrap-reverse:`虽然换行，但是换行方向与使用`wrap`属性值时的换行方向相反；

可以将`flex-direction`和`flex-wrap`属性值合并写入`flex-flow`属性中：

~~~css
#container {
    flex-direction: row;
    flex-wrap: wrap;
}
/*可合并为*/
#container {
    flex-flow: row wrap;
}
~~~

### 3.8 指定水平方向与垂直方向的对齐方式

#### 3.8.1 弹性盒布局中的一些专用术语

- `main axis:`进行布局时作为布局基准的轴，在横向布局时为水平轴，在纵向布局时为垂直轴；
- `main-start/main-end:`进行布局时的布局起点与布局终点。在横向布局时为容器的左端与右端，在纵向布局时为容器的顶端与底端；
- `cross axis:`与`main axis`垂直相交的轴，在横向布局时为垂直轴，在纵向布局时为水平轴；
- `cross-start/cross-end:``cross axis`轴的起点与终点。在横向布局时为容器的顶端与底端，在纵向布局时为容器的左端与右端。将`flex-wrap`属性值指定为`wrap`且进行横向多行布局时，按从`cross-start`到`cross-end`方向，即从上往下布局，将`flex-wrap`属性值指定为`wrap-reverse`且进行横向多行布局时，按从`cross-end`到`cross-start`方向，即从下往上布局。

#### 3.8.2 `justify-content`属性

​	`justify-content`属性用于指定如何布局容器中除了子元素之外的`main axis`轴方向上的剩余空白部分。当`flex-grow`属性值不为0时，各子元素在`main axis`轴方向上自动填满容器，所以`justify-content`属性值无效。

​	可指定的`justify-content`值如下所示：

- `flex-start:`从`main-start`开始布局所有子元素（默认值）；
- `flex-end:`从`main-end`开始布局所有子元素；
- `center:`居中布局所有子元素；
- `space-between:`将第一个子元素布局在`main-start`处，最后一个子元素布局在`main-end`处，将空白部分平均分配在所有子元素与子元素之间；
- `space-around:`将空白部分平均分配在以下几处，如`main-start`与第一个子元素之间、各子元素与子元素之间、最后一个子元素与`main-end`之间；

#### 3.8.3 `align-items`属性与`align-self`属性

​	`align-items`属性与`justify-content`属性类似，用于指定子元素的对齐方式，但是它用于指定的是`cross axis`轴方向上的对齐方式，可指定的属性值如下所示：

- `flex-start:`从`cross-start`开始布局所有子元素（默认值）；
- `flex-end:`从`cross-end`开始布局所有子元素；
- `center:`居中布局所有子元素；
- `baseline:`如果子元素的布局方向与容器的布局方向不一致，则该值的作用等效与`flex-start`属性值；如果方向一致，则所有子元素中的内容沿基线对齐；
- `stretch:`同一行中的所有子元素高度被调整为最大。如果未指定任何子元素高度，则所有子元素高度被调整为最接近容器的高度（当考虑元素边框及内边距时，当边框宽度与内边距均为0则等于容器高度）；

`align-self`属性与`align-items`属性的区别在于：`align-items`被指定为容器元素的样式属性，用于指定所有子元素的对齐方式，而`align-self`属性被用于单独指定某些子元素的对齐方式。例如将容器元素的`align-items`属性值指定为`center`后，可以将第一个子元素的`align-self`属性值指定为`flex-start`。`align-self`可指定值如下所示：

- `auto:`继承父元素的`align-items`属性值；
- 其他可指定属性值同`align-items`可指定值相同；

#### 3.8.4 `align-content`属性

​	当进行多行布局时，可以指定`align-content`属性指定各行对齐方式。该属性与`align-items`的区别在于：`align-items`属性用于指定子元素的对齐方式，而`align-content`属性用于指定行的对齐方式。可指定值为：

- `flex-start:`从`cross-start`开始布局所有行（默认值）；
- `flex-end:`从`cross-end`开始布局所有行；
- `center:`居中布局所有行；
- `space-between:`将第一行布局在`cross-start`处，将最后一行布局在`cross-end`处，将空白部分平均分配在各行之间；
- `space-around:`将空白部分平均分配在以下几处，如`cross-start`与第一行之间、各行与行之间、最后一行与`cross-end`之间；

## 4 `calc`方法(`IE9`上也可以使用)

​	`CSS3`中新增了一个`calc`方法，可以使用它来自动计算元素的高度、宽度等数值类型的样式属性值。

~~~html
<head>
	<meta charset="utf-8">
	<title>calc方法</title>
	<style type="text/css">
		#container {
			width: 500px;
			background-color: pink;
		}
		#foo {
			width: calc(50% - 100px);
			background-color: green;
		}
	</style>
	<script type="text/javascript">
		function changeWidth() {
			document.getElementById('container').style.width = "1000px";
		}
	</script>
</head>
<body>
	<div id="container">
		<div id="foo">示例div</div>
	</div><br>
	<input type="button" value="修改div宽度" onclick="changeWidth();">
</body>
~~~

`calc`方法的另一个妙用是可以用来对各种不同的计数单位进行混合运算，如：

~~~css
#container {
    height: calc(10em + 3px);
}
~~~

