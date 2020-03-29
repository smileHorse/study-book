# HTML 与 CSS

## HTML

### div和span

​	`<div>`通常被描述为容器，可以用来将一堆相关的元素放在一起。如果使用`<div>`有助于将页面分解为逻辑区，从而保证结构清晰并便于指定样式，那么可以增加更多的结构。如果只是为了在页面中创建大量结构而增加`<div>`，就只会让页面复杂，而没有任何好处。

​	`<div>`允许为块级内容创建逻辑划分，`<span>`元素则采用类似的方式建立内联内容的逻辑分组。

**内联元素上的外边距和内边距与块元素稍有不同，如果一个内联元素四周都增加了外边距，则只能看到左边和右边会增加空间。你也可以对内联元素的上边和下边增加内边距，不过这个内边距不会影响包围它的其他内联元素的间距，所以内边距会与其他内联元素重叠。**

**图像与其他内联元素稍有不同。图像的宽度、内边距和外边距属性都表现的更像是块元素的相应属性。如果使用<img>元素的width属性或者CSS中的width属性设置图像的宽度，浏览器会缩放图像，使它适合你指定的宽度。有时如果你不能自己编辑图像来改变大小，但又希望页面上图像能调整大小，浏览器的这种做法就会很方便。不过要记住，如果依赖浏览器来缩放你的图像，可能会不必要地下载过多的数据。**

### 布局和排版

#### 流体布局

​	流(Flow)实际上就是浏览器在页面上摆放HTML元素所用的方法。浏览器从HTML文件最上面开始，从上到下沿着元素流逐个显示所遇到的各个元素。现在先来考虑块元素，它会在每个块元素之间加一个换行。所以首先会显示文档中的第一个元素，然后是一个换行，然后是第二个元素，接下来又是一个换行，如此继续，从文件最上面一直到文件末尾逐个显示。

​	对于内联元素，则在水平方向上会相互挨着，从左向右流，直到没有更多空间为止，剩下的内容会放在下一行。注意浏览器必须用不同方式分解文本，使它能刚好适合内容区大小。 总体上从左上方流到右下方。

​	当浏览器并排放置两个内联元素时，而且这些元素都有外边距，浏览器会在这些元素之间创建足够的空间，空间大小为这两个内联元素外边距之和。（其实，只要两个垂直外边距碰到一起，它们就会折叠。即使嵌套的元素之间也不例外，如果外面的元素有一个边框，那么两个元素的外边距就不会碰到一起，也就不会进行折叠。）

​	当浏览器上下放置两个块元素时，它会把它们共同的外部件进行折叠。折叠的外边距高度就是最大的外边距高度。

​	通常，我们不会设置内联元素的外边距。只有一个例外，就是图像。对于图像，通常不仅会设置外边距，还会设置内边距和边框。

##### 浮动元素

- 首先指定一个标识id。
- 现在指定一个宽度。对于所有浮动元素都有一个要求：它必须有一个宽度。
- 增加float属性，让它浮动。float属性可以设置为left或right。

**浮动一个元素时，如果希望它在某个元素的后面，就要移动浮动元素的HTML，让它紧挨着放在那个元素下面。**

**浏览器如何将浮动元素和其他元素流入页面？**

1. 首先，浏览器像往常一样，正常地将元素流入页面，从文件最上面开始，逐步移向末尾的元素；
2. 浏览器遇到浮动元素时，会尽可能把它放在最左边或最右边，还会从流中删除这个元素，就好像它浮在页面上一样；
3. 由于这个浮动元素已经从正常的流中删除，所以其他元素会填在这里，就好像没有这个浮动元素一样；
4. 不过，对内联元素定位时，它们会考虑浮动元素的边界，因此会围绕着浮动元素。即块元素在浮动元素的下面，块元素中的内联元素会围绕着这个浮动元素。

**如何解决重叠问题？**

​	用到一个CSS属性：clear。clear属性用来实现当元素流入页面时，在这个元素的左边、右边或两边不允许有浮动内容。例如：

~~~css
#footer {
    clear: right;
}
~~~

浏览器在页面上放置元素时，会查看footer右边有没有浮动元素，如果有，就把footer下移，直到它右边没有浮动元素为止。

#### 冻结布局

​	要把页面变成一个冻结页面，只需要将body中的所有元素放在一个`<div>`中，并在CSS中为这个`<div>`设置一个固定的宽度width即可。

~~~css
#allcontent {
    width: 800px;
}
~~~

#### 绝对定位

​	一个元素绝对定位时，浏览器要做的首先就是将它从流中完全删除，然后浏览器将这个元素放置在top和right属性指定的位置上（也可以使用left和bottom指定位置）。

​	流中的元素不会将其内联元素围绕在一个绝对定位元素周围，它们完全不知道页面上有这个绝对定位的元素。

​	绝对定位元素可以分层放置，一个元素可以放置在另一个绝对定位元素上面。这取决于它们的z-index属性。z-index大的元素放在上面。

​	position属性有4个值：static、absolute、fixed和relative。static是默认值，元素会放在正常的文档流中，而不是由你来指定元素为止。fixed是将元素放在相对于浏览器窗口的一个位置上，而不是相对于页面，所以固定元素永远也不会移动。relative定位会让元素正常的流入页面，不过在页面上显示之前要进行偏移。相对定位常用于更高级的定位和特殊效果。

~~~css
#sidebar {
    position: absolute;
    top: 100px;
    right: 200px;
    width: 280px;
}
~~~

#### CSS表格布局

##### 为表格显示增加HTML结构

1. 首先，创建一个`<div>`表示整个表格，行和列要嵌套在这个`<div>`中。
2. 接下来，对于表格中的每一行，要创建一个`<div>`，其中包含行内容。
3. 然后，对于每一列，只需要一个块元素作为该列内容。

~~~html
<div id="tableContainer">
    <div id="tableRow">
        <div id="main">....</div>
        <div id="sidebar">....</div>
    </div>
</div>
~~~

##### 使用CSS创建表格显示

~~~css
div#tableContainer {
    display: table;
}
div#tableRow {
    display: table-row;
}
#main {
    display: table-cell;
}
#sidebar {
    display: table-cell;
}
~~~



#### 凝胶布局

​	介于流体和冻结之间的布局，叫做凝胶布局。它会锁定页面中内容区的宽度，不过会将它在浏览器中居中。

~~~css
#allcontent {
    width: 800px;
    margin-left: auto;
    margin-right: auto;
}
~~~





## CSS

### 入门

| background-color | 控制元素的背景颜色                                |
| ---------------- | ------------------------------------------------- |
| background-image | 在元素后面放置一个图像                            |
| border           | 在一个元素周围加边框，可以为实线边框、虚线边框... |
| color            | 设置文本元素的字体颜色                            |
| font-weight      | 控制文本的粗细                                    |
| font-size        | 让文本更大或更小                                  |
| font-style       | 设置斜体文本                                      |
| left             | 指定一个元素的左边所在位置                        |
| letter-spacing   | 在字母之间设置间距                                |
| line-height      | 设置一个文本元素中的行间距                        |
| list-style       | 改变列表中列表项的外观                            |
| padding          | 在一个元素边缘和它的内容之间设置空间              |

#### 媒体查询

可能想针对将要显示页面的设备类型（桌面PC、笔记本电脑、平板、手机、打印机等）来调整页面的样式，可以在`<link>`元素中使用media属性指定应用这个样式表的设备类型，通过创建一个媒体查询来指定设备类型，媒体查询需与设备匹配。

~~~html
<linK href=“loung-mobile.css” rel="stylesheet" media="screen and (max-device-width: 480px)">
~~~

这个查询指定了有屏幕的设备，而且屏幕宽度不超过480像素。

~~~html
<linK href=“loung-print.css” rel="stylesheet" media="print">
~~~

这个查询用来匹配打印机设备。

查询中常用的属性包括：max-device-width、min-device-width，以及显示方向orientation等。

要为CSS指定有特定属性的设备，还有一种方法：不是在link标记中使用媒体查询，还可以直接写在CSS中：

~~~css
// 如果设备屏幕宽度大于480px就使用这些规则
@media screen and (min-device-width: 481px) {
    #guarantee {
        margin-right: 250px;
    }
}
// 如果设备屏幕宽度小于等于480px就使用这些规则
@media screen and (max-device-width: 480px) {
    #guarantee {
        margin-right: 30px;
    }
}
// 如果要打印这个页面，就使用这些规则
@media print {
    body {
       font-family: Times, "Times New Roman", serif; 
    }
}
~~~

#### CSS快捷方式

~~~css
body {
    padding: 10px 0px 20px 10px; // 上、 右、 下、 左
    padding: 10px;	// 四边的内边距
    padding: 10px 20px;	// 上下、左右
    margin: 10px 0px 20px 10px;	// 上、 右、 下、 左，其他快捷方式和padding相同
    border: thin solid #007e7e;
    border: solid thin #007e7e;	// border的快捷方式简写border-style/border-width/border-color,可以采用任何顺序
    background: white url(images/cook.gif) repeat-x;	// 简写background-color/background-image/background-repeat/background-position，也可以采用任何顺序
}
~~~

`font`的简写方式：

~~~css
{
    font: font-style font-variant font-weight font-size/line-height font-family;
}
~~~

font-style、font-variant和font-weight都是可选的，可以指定这些属性的任意组合，不过它们必须出现在font-size属性前面。

font-size是必须指定的。

line-height是可选的，如果要指定一个行高，只需要在font-size属性后面加一个`/`，然后设置属性值即可。

font-family名称之间要使用逗号分隔。

~~~css
body {
    font-size: small;
    font-family: Verdana, Helvetica, Arial, sans-serif;
    ling-height: 1.6em;
}

// 简写形式
body {
    font: small/1.6em Verdana, Helvetica, Arial, sans-serif;
}
~~~

#### CSS层叠

​	给定一组样式表中的一组样式，浏览器就是以层叠的方式来确定具体使用哪一种样式。假设你的页面上有一个`<h1>`元素，想知道这个元素的font-size属性，会这么做：

1. 收集所有样式表。包括：Web页面作者自己写的样式表，读者增加的样式表，还有浏览器的默认样式。

2. 找到所有匹配的声明。检查所有样式表，找出所有匹配`<h1>`而且有font-size属性的规则。

3. 对所有匹配的规则排序。按照作者、读者和浏览器从前到后的重要顺序对这些规则排序。

4. 按特定性对所有声明排序。如果一个规则能够更准确的选择一个元素，那么这个规则就更特定。要计算特定性，可以根据规则进行加分，分值越高规则越特定：

   | 0                     | 0                             | 0                         |
   | --------------------- | ----------------------------- | ------------------------- |
   | 选择器包含id，则加1分 | 选择器包含类或者伪类，则加1分 | 选择器包含元素名，则加1分 |

   例如“h1”的分值为001，“h1.blue"分值为011，”#id“的分值为100；

5. 最后，对于冲突的规则，按照它们在各自样式表中的先后顺序进行排序。

**有时候读者可能会覆盖一个样式，在他的属性声明最后放置一个"!important"，类似“font-size: 200% !important”，这样会覆盖作者的样式。**

### 字体和颜色样式

​	在CSS中，字体划分为“字体系列”(Font Family)，可以从中指定希望页面中各个元素使用的字体。

~~~css
body {
    font-family: Verdana, Geneva, Arial, sans-serif;	// 定制页面中使用的字体
    font-size: 14px;	// 控制字体大小
    color: silver;	// 为文本设置颜色
    font-weight: bold;	// 指定字体的粗细(lighter、normal、bold、bolder)
    text-decoration: underline;	// 对文本增加一些装饰(none、underline、overline、line-through)
    font-style: italic;	// 设置字体风格(italic: 斜体文本；oblique：倾斜文本)
}
~~~

#### 使用`font-family`指定字体系列

​	每个font-family包含一组有共同特征的字体。共有5个字体系列：sans-serif、serif、monospace、cursive和fantasy。每个字体系列都包含大量字体。

|            |                                                   |                                                              |
| ---------- | ------------------------------------------------- | ------------------------------------------------------------ |
| sans-serif | Verdana、Arial、Arial Black、Trebuchet MS、Geneva | 包含没有衬线的字体。与serif字体相比，通常认为sans-serif字体在计算机屏幕上更容易识读 |
| serif      | Times、Times New Roman、Georgia                   | 包含有衬线的字体（衬线是字母末端装饰性的小细线）。类似新闻报纸的文字排版 |
| monospace  | Courier、Courier New、Andale Mono                 | 包含固定宽度的字符，这些字体主要用于显示软件代码示例         |
| cursive    | Comic Sans、Apple Chancery                        | 包含看似手写的字体                                           |
| fantasy    | LAST NINJA、Impact                                | 包含有某种风格的装饰性字体                                   |

​	serif字体看起来很高雅、很传统；sans-serif字体外观很清晰，可读性好；Monospace字体就像打字机打出来的；Cursive和Fantasy字体给人一种有趣或者有风格的感觉。

​	通常，指定的font-family包含一个候选字体列表，它们都来自同一个字体系列。字体名称区分大小写，并且最后放一个通用的字体系列名。浏览器会按照候选列表从前到后的顺序依次查找，使用第一个查找到的字体，如果都没有找到，就是用浏览器默认的字体。

​	如果字体名中包含多个单词，需要在CSS中在字体名两边加上引号。

~~~css
body {
    font-family: "Courier New", Courier;
}
~~~

#### 引入Web字体

​	使用@font-face规则，允许你定义一种字体的名字和位置，然后可以在页面中进行使用。

~~~css
@font-face {
    font-family: "Emblema One";
    src: url("http://...//*.woff"),url("http://...//*.ttf");
}
h1 {
    font-family: "Emblema One", sans-serif;
}
~~~

​	@font-face规则告诉浏览器：要加载由src URL指定的字体文件。浏览器会尝试加载每一个src文件，直到找到它能支持的一个文件。一旦加载，会为这个字体分配font-family属性中指定的名字。

​	不同浏览器对很多不同的格式提供了不同程度的支持，支持最广泛的是Web开发字体格式。下面是一些常用的格式，以及各自的文件扩展名：

| TrueType字体          | .tff  |
| --------------------- | ----- |
| OpenType字体          | .otf  |
| Embedded OpenType字体 | .eot  |
| SVG字体               | .svg  |
| Web开放字体格式       | .woff |

​	@font-face不是一个选择器规则，而是一个内置的CSS规则。类似的内置规则还有@import和@media。@import允许导入其他CSS文件，而不是在HTML中通过一个`<link>`链入。@media允许创建特定于某些媒体类型的CSS规则，如印刷页、桌面屏幕或手机等。

​	要定制多个字体时，要为每个字体创建一个单独的@font-face规则，并指定唯一的名字。

#### 使用`font-size`调整字体大小

| px     | 按像素指定字体大小，即告诉浏览器字母高度是多少像素           |
| ------ | ------------------------------------------------------------ |
| %      | 相对于父元素的字体大小指出这个字体有多大                     |
| em     | 类似于百分数，也是一个相对度量单位。这里要指定一个比例因子   |
| 关键字 | 把一个字体大小指定为xx-small、x-small、small、medium、large、x-large或xx-large，浏览器会把这些关键字转换为像素值，使用浏览器中定义的默认值来进行转换 |

​	指定字体大小的一个小秘诀：

1. 选择一个关键字(推荐使用small或medium)，指定它作为body规则中的字体大小。这相当于页面中的默认字体大小。
2. 使用em或百分数，相对于body字体大小指定其他元素的字体大小。

~~~css
body { font-size: small; }
h1 { font-size: 150%; }
h2 { font-size: 120%; }
~~~

#### 设置颜色

在CSS中指定颜色的方法：

- 按名指定颜色，有16种基本颜色和150种扩展颜色可以采用这种方法，颜色名不区分大小写
- 用红绿蓝值指定颜色
- 使用十六进制码指定颜色

~~~css
body {
    background-color: siver;
    background-color: rgb(80%, 20%, 0);
    background-color: rgb(204, 102, 0);
    background-color: #cc6600;
}
~~~

### 盒模型

​	盒模型是CSS看待元素的一种方式，CSS将每一个元素都看作由一个盒子表示，每个盒子由一个内容区以及可选的内边距、边框和外边距组成。

​	内边距和外边距都是透明的，没有颜色也没有样式。不过它们会呈现背景颜色或背景图像。内边距和外边距之间有一个区别：元素的背景颜色或背景图像会延伸到内边距下面，但不会延伸到外边距。

​	**background-image属性用途非常特定，只是要设置一个元素的背景图像。这个属性并不是用来在页面中设置图像，要想设置图像，还得使用<img>元素。使用<img>表示图像在页面中可能有更为重要的原因，比如照片或logo。**

#### 使用border-style设置边框样式

border-style属性可以控制边框的视觉样式，共有8种可用的边框样式，包括实线、虚线、脊线和槽线。

| solid  | 实线边框                               |
| ------ | -------------------------------------- |
| dotted | 虚线样式，看起来像是一系列小店         |
| double | 双线样式，有两条线                     |
| dashed | 破折线样式，就是围绕边框的一组破折线   |
| groove | 槽线样式，看起来就像是页面中的一个槽   |
| inset  | 内凹样式，看起来像是向页面凹进去       |
| outset | 外凸样式，看起来像是从页面凸出来一样   |
| ridge  | 脊线样式，看起来像页面上一个凸起的山脊 |

#### 使用border-width设置边框宽度

border-width属性控制边框的宽度，可以使用关键字或像素来指定边框宽度。

~~~css
border-width: thin;	// thin / medium / thick
border-width: 5px;
~~~

#### 使用border-color设置边框颜色

#### 指定某一边的边框

像外边距和内边距一样，还可以指定一边（上、右、下、左）的边框样式、宽度和颜色。

~~~css
border-left-color: black;
border-top-style: dashed;
border-top-width: thick;
~~~

#### 使用border-radius指定边框圆角

可以在四个角上都创建圆角，或者只对一个角或这4个角的任意组合创建圆角。

~~~css
border-radius: 15px;
border-top-left-radius: 3em;	// 可以使用px或em来指定半径大小。使用em时，边框半径的度量相对于元素的字体大小，与使用em指定font-size时一样
~~~

### 





## HTML5

​	利用HTML5，我们可以对页面稍微做一些调整，把原来的`<div>`换成一些更特定的元素，能够更明确的指示其中包含什么类型的内容。

​	一些比较重要的HTML5元素：

| <article>  | 表示页面中一个独立的组成部分，如一个博客帖子、用户论坛帖子或报道 |
| ---------- | ------------------------------------------------------------ |
| <nav>      | 所包含的内容将作为页面的导航链接                             |
| <header>   | 放在页面顶部的内容，或者放在页面某个区块的顶部               |
| <footer>   | 放在页面底部的内容，或者放在页面某个区块的底部               |
| <time>     | 可能包含日期或时间，也可能同时包含日期和时间                 |
| <aside>    | 包含的内容是对页面内容的补充，如插图或边栏                   |
| <section>  | 一个主体性内容分组，通常包含一个首部header，可能还有一个底部footer |
| <video>    | 用来为页面增加视频媒体                                       |
| <mark>     | 用于突出显示某些文本                                         |
| <audio>    | 在页面中包含声音内容                                         |
| <progress> | 显示任务的完成进度                                           |
| <meter>    | 显示某个范围的度量                                           |
| <canvas>   | 用来在页面中显示用JavaScript绘制的图像和动画                 |
| <figure>   | 用来定义类似照片、图表甚至代码清单等独立的内容               |

**`<section>`和`<article>`有什么区别？**通常可以来考虑：使用`<section>`可以把相关的内容分组在一起；另一方面，用`<article>`包含独立的内容，如一个新闻报道。`<section>`比`<article>`更加通用，但又不及`<div>`通用。如果要增加一个元素以便为页面指定样式，可以使用`<div>`；如果要增加一个元素来标记一些内容，指示这是由相关内容构成的一个结构明确的区块，可以使用`<section>`；如果有些内容可以独立于页面上的其他内容进行重用和分发，那么就使用`<article>`。

### `<time>`元素

~~~html
<time datetime="2012-01-18">2/18/2012</time>
~~~

如果元素内容没有采用官方Internet日期/时间格式来写，就必须有datetime属性。

如果使用datetime属性来指定一个日期和/或一个时间，元素内容可以写你希望的任何内容。

使用官方格式表示日期或时间的一些方法：

| 2012-02-18        | 指定年、月、日                               |
| ----------------- | -------------------------------------------- |
| 2012-02           | 指定年和月                                   |
| 2012              | 指定年                                       |
| 2012-02-18 18:00  | 按24小时增加一个时间                         |
| 05:00             | 只指定一个时间                               |
| 2012-02-18 05:00Z | 如果在日期和时间后面有一个“Z"，这表示UTC时间 |

### `<nav>`导航条

~~~html
<nav>
	<ul>
    	<li><a href="index.html">Home</a></li>
    	<li class="selected"><a href="blog.html">Blog</a></li>
    	<li><a href="">Inventions</a></li>
    	<li><a href="">Locations</a></li>
    </ul>
</nav>
~~~

~~~css
nav {
    background-color: #efe5d0;
    margin: 10px 10px 0px 10px;
}
nav ul {
    marigin: 0px;	// 默认情况下，ul元素会有一个外边距，会让ul稍微偏离
    list-style-type: none;	// 删除列表项的项目符号
    padding: 5px 0px 5px 0px;
}
nav ul li {
    display: inline;	// 这样设置列表项的前后不会再有回车，会想内联元素一样在页面上流入一行
    padding: 5px 10px 5px 10px;
}
nav ul li a:link, nav ul li a:visited {
    color: #954b4b;
    border-bottom: none;
    font-weight: bold;
}
nav ul li.selected {
    background-color: #c8b99c;
}
~~~



## 表格和列表

### 建立一个表格

~~~html
<table>
    <caption>...</caption>
    <tr>
    	<th>...</th>
        <th>...</th>
    </tr>
    <tr>
    	<td>...</td>
        <td>...</td>
    </tr>
</table>
~~~

~~~css
table {
    margin-left: 20px;
    margin-right: 20px;
    border: thin solid black;
    caption-side: bottom;	// 把标题移到表格的下方
}
td, th {
    border: thin dotted gray;
    padding: 5px;
}
caption {
    font-style: italic;
    padding-top: 8px;
}
~~~

​	表格单元格确实有内边距和边框，就和盒模型中的一样。但是外边距方面稍微有一些不同，不能单独设置各个表格单元格的外边距，而要为整个表格设置一个共同的间距：`border-spacing`。

~~~css
table {
    border-spacing: 10px 20px;
}
~~~

还有一个属性`border-collapse`属性用来折叠边框，使单元格之间根本没有边框间距。这样，浏览器会忽略表格中设置的所有边框间距，还会把紧挨着的两个边框合并为一个边框。

~~~css
table {
    border-collapse: collapse;
}
~~~

​	使用`nth-child`伪类为表格设置隔行变色，对于这个伪类，状态是一个元素相对于它的兄弟元素的数字顺序：

~~~css
p:nth-child(even) {
    background-color: red;	// 设置偶数行背景色
}
p:nth-child(odd) {
    background-color: green; // 设置奇数行背景色
}
~~~

### 建立一个列表

​	列表的主要属性是`list-style-type`，利用这个属性，可以控制列表中使用的项目符号。

~~~css
li {
    list-style-type: disc;		// 默认的列表标记类型
    list-style-type: circle;	// 简单的圆形标记
    list-style-type: square;	// 方块标记
    list-style-type: none;		// 删除所有列表标记
}
~~~

​	使用`list-style-image`为列表设置标记图像：

~~~css
li {
    list-style-image: url(images/backpick.gif);
    padding-top: 5px;
    margin-left: 20px;
}
~~~



## HTML表单

### `<form>`元素

~~~html
<form action="http://..../....action" method="post">
</form>
~~~

​	`action`属性包含Web服务器的url、脚本所在的文件夹和处理表单的服务器脚本名；`method`属性确定表单数据如何发送到服务器。

### 表单中可存在的控件

~~~html
<input type="text" name="fullname" placeholder="Buckaroo Banzai">
<input type="submit">
<input type="radio" name="hotornot" value="hot">
<input type="radio" name="hotornot" value="not">
<input type="checkbox" name="spice" value="Salt">
<input type="checkbox" name="spice" value="Pepper" checked>
<input type="checkbox" name="spice" value="Garlic">
<input type="number" min="0" max="10" required>
<input type="range" min="0" max="20" step="5">
<input type="color">
<input type="date">
<input type="email">
<input type="tel">
<input type="url">
<input type="password">
<input type="file" name="doc">
<textarea name="comments" rows="10" cols="48"></textarea>
<select name="characters">
    <option value="Buckaroo">Perfect Buckaroo</option>
    <option value="Tommy">Perfect Tommy</option>
</select>
<select name="mult-characters" multiple>
    <option value="Buckaroo">Perfect Buckaroo</option>
    <option value="Tommy">Perfect Tommy</option>
</select>
~~~

​	表单中的每个输入控件都有一个name属性，提交表单时，浏览器会使用这些唯一的名字打包所有数据。

​	可以使用简单的文本为表单元素增加标签，但实际上应该使用`<label>`元素来标记这些标签。`<label>`元素可以提供页面结构的更多信息，更容易地使用CSS对标签设置样式。

​	要使用`<label>`元素，首先为表单元素增加一个id属性，然后设置`<label>`元素的for属性为其相应的id。

~~~html
<input type="radio" name="hotornot" value="hot" id="hot">
<label for="hot">hot</label>
<input type="radio" name="hotornot" value="not" id="not">
<label for="not">not</label>
~~~

### 表单中还能有的元素

​	表单变得越来越大时，在视觉上对元素进行分组很有帮助。使用`<fieldset>`元素，可以用来将公共元素组织在一起。`<fieldset>`又使用另一个元素`<legend>`：

~~~html
<fieldset>
    <legend>Condiments</legend>
    <input type="checkbox" name="spice" value="Salt">
    <input type="checkbox" name="spice" value="Pepper" checked>
    <input type="checkbox" name="spice" value="Garlic">
</fieldset>
~~~

