# `CSS`字体和文本

# 1 字体

​	网页中的字体有三个来源：

- 用户机器中安装的字体。

- 保存在第三方网站中的字体。最常见的是`Typekit`和`Google`，可以使用`link`标签把它们链接到自己的页面上。

- 保存在你的WEB服务器上的字体。这些字体可以使用`@font-face`规则随网页一起发送给浏览器。

  **字体和文本之间的区别：**

  ~~~
  字体是“文字的不同体式”或“字的形体结构”。每组字体都是由一组具有独特样式的字母、数字和符号组成的。根据外观，字体可以分为不同的类别（font collection)，包括衬线字体(serif)、无衬线字体(sans-serif)和等宽字体(monospace)。每一类字体可以分为不同的字体族(font family)，比如Times和Helvetica。而字体族中又可以包含不同的字型(font face)，反映相应字体族基本设计的不同变化，比如Times Roman、Times Bold、Helvetica Condensed和Bodoni italic。
  文本就是一组字或字符，比如文章标题、段落正文等，跟使用什么字体无关。
  CSS为字体和文本分别定义了属性。字体属性主要描述一类字体的大小和外观，比如使用什么字体族、多大字号、粗体还是斜体；文本属性描述对文本的处理方式，比如行高或字符间距有多大，有没有下划线或缩进。
  ~~~

### 1.1 字体族`font-family`

​	`font-family`用于设定元素中的文本使用哪种字体。一般来说，应该为整个页面设定一种主字体，然后只对那些需要使用不同字体的元素再应用`font-family`。要为整个页面设定字体，可以设定`body`元素的`font-family`属性：

~~~css
body { font-family: verdana, sans-serif; }
~~~

​	根据测试，`font-family`的值不区分大小写。但是也不能修改在线字体库生成的字体名，否则可能无法使用它们提供的定制字体。		

​	用户机器上的字体不多，而且可能随时变化，因为不敢保证一定能使用某种字体来显示网页。为此，在指定文本的字体时，需要多列出几种后备字体，以防第一种字体无效，整个字体的列表也叫字体栈。

~~~css
/*如果字体名多于一个单词，带有空格，应该加上引号*/
body { font-family: "trebuchet ms", tahoma, sans-serif; }
~~~

​	这个字体栈相当于说：使用`trebuchet ms`字体显示这个文档，如果没有这个字体，就使用`tahoma`代替，如果也没有这个字体，就随便找一种机器里有的`sans-serif`字体吧。**注意，给字体栈的最后一项指定一个通用字体类非常重要，这是一种最保险的方法。**

​	有以下5种通用字体类：

- `serif:`衬线字体，在每个字符笔画的末端会有一些装饰线；
- `sans-serif:`无衬线字体，字符笔画的末端没有装饰线；
- `monospace:`等宽字体，每个字符的宽度相等（也称代码体）；
- `cursive:`草书体或手写体；
- `fantasy:`不能归入其他类别的字体；

一组比较通用的衬线字体为`helvetica, arial, sans-serif`；无衬线字体为`"hoefler text", georgia, times, serif;`。

**大部分浏览中都可以使用的字体：**

~~~
serif:	Georgia, Palatino/Book Antiqua, Times New Roman;
sans-serif:	Arial, Arial Black, Arial Narrow, Tahoma, Trebuchet MS, Verdana;
monospace:	Courier New, Lucida Console/Monaco;
cursive:	Comic Sans MS;
fantasy:	Impact;
~~~

### 1.2 字体大小`font-size`

​	`font-size`可以继承，改变一个元素的字体大小，可能会导致其子元素字体大小成比例地变化。这是因为浏览器样式表在设定所有元素的字体大小时，使用的是相对单位`em`。默认情况下，`1em`等于16像素。

- 绝对字体大小

  绝对单位是用像素、关键字、派卡(pica)或英寸设定字体大小。这种方式下设定多大就多大，与祖先元素的字体大小无关。

- 相对字体大小

  相对单位是使用百分比、`em`或`rem（根元素的字体大小）`设定字体大小。该元素的字体大小要相对于最近的”被设定过字体大小“的祖先元素来确定。比如：

  ~~~html
  <body>
      <p>This is <strong>very important!</strong></p>
  </body>
  
  <style>
      p { font-size: .75em; }
      strong { font-size: .75em; }
  </style>
  ~~~

  `p`元素的文本为12像素（body的16像素基准大小 * 0.75 = 12），折合成点单位是9点。`strong`元素的大小为9像素（16 * 0.75 * 0.75 = 9）。

  **如果想使用em，但又需要设定具体的像素大小，可以把body的font-size设定为62.5%。这样等于把基准大小从16像素改为10像素（16 * 0.625 = 10）。然后，em与像素的对应关系就十分明确了，比如`1em`等于10像素，`1.5em`等于15像素。**

  `CSS3`新增了一个相对单位`rem（root em）`，这个单位与`em`的区别在于使用`rem`为元素设定字体大小时，仍然是相对大小，但相对的只是`HTML`根元素。通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。目前，除了`IE8`及更早版本外，所有浏览器均以支持`rem`。对于不支持它的浏览器，可以多写一个绝对单位的声明，这些浏览器会忽略使用`rem`设定的字体大小：

  ~~~css
  /*IE8及之前的IE浏览器使用14像素*/
  p { font-size: 14px; font-size: .875rem; }
  ~~~

### 1.3 字体样式`font-style`

​	它的作用仅仅是通过`italic`把正体设为斜体，通过`normal`把斜体设为正体。用`oblique`代替`italic`的效果也一样。

**所谓常规（normal）值：**

~~~
normal这个属性值不仅font-style有，很多其他属性也有，它的作用就是取消所有的特殊样式。
这个值是用来有选择地覆盖某个默认或你设定的全局属性。比如h1到h6默认为粗体，如果想让h3以常规字体出现，只需要设定h3 {font-weight: normal;}。如果你的样式表里声明了a {font-variant: small-caps;}，那网页中的英文链接就会变成小型大写字母。要是想让某一组链接仍然以常规的大小写方式显示，只需加一个声明a.speciallink {font-variant: normal;}。
~~~

### 1.4 字体粗细`font-bold`

可能的值：100、200...900，或者`lighter、normal、bold、bolder`。实际上，这些数字值没有什么作用，对浏览器来说，它只显示属性的两个值：`bold`和`normal`。由于浏览器对数字值的实现各不相同，所以从常规字体切换到粗体可能发生在不同的值上。总之，最好只用`bold`和`normal`这两个值。

### 1.5 字体变化`font-variant`

`font-variant`属性除了`normal`，就只有一个值`small-caps`，这个值会导致所有小写英文字母变成小型大写字母。

### 1.6 简写字体属性`font`

​	使用简写形式时要遵守两条规则，否则浏览器无法正确解释声明的值。

- 规则一：必须声明`font-size`和`font-family`的值；
- 规则二：所有值必须按照如下顺序声明
  - `font-weight`、`font-style`、`font-variant`不分先后；
  - 然后是`font-size`;
  - 最后是`font-family`；

实际上，在设定`font-size`属性时，可以顺便设定`line-height`值，比如`12px/1.5`。这里行高就是字体大小`12px`的1.5倍。

## 2 文本属性

### 2.1 文本缩进`text-indent`

~~~css
p { text-indent: 3em; }
~~~

​	值可以为任何长度值，正负均可。该属性用来设定行内盒子相对于包含元素的起点。默认情况下，这个起点就是包含 元素的左上角。设定正值往右偏移，设定负值往左偏移。

​	`text-indent`是可以被子元素继承的。如果你在一个`div`上设定了`text-indent`属性，那么`div`中的所有段落都会继承该缩进值。然而，与所有继承的`CSS`值一样，这个属性值并不是祖先元素中设定的值，而是计算的值。假如有一个400像素宽的`div`，包含的文本缩进为5%，则缩进的距离是20像素（400 * 0.05）。在这个`div`中有一个200像素宽的段落，作为子元素，它继承了父元素的`text-indent`值，所以它包含的文本也要缩进，但缩进的值不是5%，而是20像素。这样可以确保无论段落多宽，它们的缩进距离都一样。

### 2.2 字符间距`letter-spacing`

~~~css
p { letter-spacing: .2em; }
~~~

​	值可以为任何长度值，正负均可。正值时增大字符间距，负值时缩小字符间距。无论设定字体大小时使用什么单位，设定字符间距一定要用相对单位，以便字符间距能随字体大小同比例变化。

### 2.3 单词间距`word-spacing`

~~~css
p { word-spacing: .2em; }
~~~

​	单词间距与字符间距非常相似，区别在于它只调整单词间距，而不影响字符间距。`CSS`把任何两边有空白的字符和字符串都视作单词。纯中文文本一段之中没有空格，因此`word-spacing`对中文网页几乎没用，但对中英文混排段落可能有用。

### 2.3 文本装饰`text-decoration`

~~~css
p { text-decoration: line-through; }
~~~

​	值可以为：`underline,overline,line-through,blink,none`。`blink`是为文本添加闪烁效果的，应该少用。文本装饰的最主要的应用是控制链接的下划线。

### 2.4 文本对齐`text-align`

~~~css
p { text-align: center; }
~~~

​	值可以为：`left,right,center,justify(两端对齐)`。控制着文本在水平方向对齐的方式，其中，`center`值也可以用来在较大的元素中居中较小的固定宽度的元素或图片。

### 2.5 行高`line-height`

~~~css
p { line-height: 1.5; }
~~~

​	值可以为任何数字值，不用指定单位。`CSS`中行高平均分布在一行文本的上方和下方。比如字体大小是12像素，行高是20像素，则浏览器会在文本的上方和下方各加4像素的空白，以凑足20像素的行高。

​	在设定行高时如果使用了绝对单位，比如像素，那将来增大字体有可能导致行与行之间重叠。

### 2.6 文本转换`text-transform`

~~~css
p { text-transform: capitalize; }
~~~

​	值可以为：`none,uppercase,lowercase,capitalize`。该属性用于转换元素中文本的大小写，它可以设定英文文本首字母大写、全部字母大写和全部字母小写。`capitalize`值会将每个词的首字母转换为大写。

### 2.7 垂直对齐`vertical-align`

~~~css
p { vertical-align: 60%; }
~~~

​	值可以为任意长度值以及`sub,super,top,middle,bottom`等。`vertical-align`以基线为参照上下移动文本，但这个属性只影响行内元素。如果想在垂直方向上对齐块级元素，必须把其`display`属性设定为`inline`。`vertical-align`最常用于公式或化学分子式中的上标和下标，或者用于文本中脚注的角标。