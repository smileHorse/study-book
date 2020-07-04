# 1 语法的改变

## 1.1 HTML 5中的标记方法

### 内容类型(`ContentType`)

HTML 5的文件扩展符与内容类型保持不变。扩展符仍然为`“.html”`或`".htm"`，内容类型仍然为`“text/html”`。

### `DOCTYPE`声明

HTML 5中刻意不使用版本声明，一份文档将会适用所有版本的HTML。声明如下：

~~~html
<!DOCTYPE HTML>
~~~

另外，当使用工具时，也可以在`DOCTYPE`声明方式中加入SYSTEM识别符：

~~~html
<!DOCTYPE HTML SYSTEM "about:legacy-compat">
~~~

### 指定字符编码

在HTML 4中，使用meta元素的形式指定文件中的字符编码：

~~~html
<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
~~~

在HTML 5中，使用以下方式（对于文件的字符编码推荐使用`UTF-8`）:

~~~html
<meta charset="UTF-8">
~~~

两种方法都有效，可以使用任意一种，但是不能两种混用。

## 1.2 HTML 5确保的兼容性

### 可以忽略标记的元素

HTML 5中，元素的标记可以省略。具体来说，分为”不允许写结束标记“、”可以省略结束标记“和”开始标记和结束标记全部可以省略“三种类型。

- 不允许写结束标记的元素：`area、base、br、col、command、embed、hr、img、input、keygen、link、meta、param、source、track、wbr`。

- 可以省略结束标记的元素：`li、dt、dd、p、rp、rt、optgroup、option、colgroup、thead、tbody、tfoot、tr、td、th`。

- 可以省略全部标记的元素：`html、head、body、colgroup、tbody`。

”不允许写结束标记的元素“是指不允许使用开始标记和结束标记将元素括起来的形式，只允许使用`<元素/>`的形式进行书写。

”可以忽略全部标记的元素“是指该元素可以完全被忽略。即使标记被忽略了，该元素还是以隐式的方式存在的。

### 具有boolean值的属性

对于具有boolean值的属性，例如disabled与`readonly`等，当只写属性而不指定属性值时，表示属性值为true；如果想要将属性值设为false，则可以不使用该属性。另外，要想将属性值设定为true时，也可以将属性名设定为属性值，或将空字符串设定为属性值。

~~~html
<!-- 只写属性不写属性值代表属性为true -->
<input type="checkbox" checked>
<!-- 不写属性代表属性为false -->
<input type="checkbox">
<!-- 属性值 = 属性名，代表属性为true -->
<input type="checkbox" checked="checked">
<!-- 属性值 = 属性名，代表属性为true -->
<input type="checkbox" checked="">
~~~

### 省略引号

HTML 5中，当属性值不包括空字符串、“<”、“>”、“=”、单引号、双引号等字符时，属性值两边的引号可以省略。

# 2 新增的元素与废弃的元素

## 2.1 新增的结构元素

- section：表示页面中的一个内容区块，比如章节、页眉、页脚或页面的其他部分。它可以与`h1` - `h6`等元素结合使用，标示文档结构。

- article：表示页面中的一块与上下文不相关的独立内容。比如一篇文章。

- aside：表示article元素的内容之外的、与article元素的内容相关的辅助信息。

- header：表示页面中一个内容区块或整个页面的标题。

- footer：表示页面或页面中一个内容区块的脚注。比如包含创作者的姓名、创建日期、联系信息等内容的区块。

- `nav`：表示页面中导航链接的部分。

- figure：表示一段独立的流内容，一般表示文档主体流内容中的一个独立单元。使用`figcaption`元素为figure元素组添加标题。

- main：表示网页中的主要内容。主内容区域指与网页标题或应用程序中本页面主要功能直接相关或进行扩展的内容。

## 2.2 新增的其他元素

- video：用于定义视频，比如电影片段或其他视频流。
- audio：用于定义音频，比如音乐或其他音频流。
- embed：用来插入各种多媒体，格式可以是`Midi、Wav、AIFF、AU、Mp3`等。
- mark：主要用来在视觉上向用户呈现那些需要突出显示或高亮显示的文字。
- progress：表示运行中的进程。比如用来显示JavaScript中耗费时间的函数的进程。
- meter：表示度量衡，仅用于已知最大值和最小值的度量。必须定义度量的范围，既可以在元素的文本中，也可以在min/max属性中。
- time：用于表示日期或时间，也可以同时表示两者。
- ruby：表示ruby注释（中文注音或字符）。
- rt：表示字符（中文注音或字符）的解释或发音。
- `rp`：在ruby注释中使用，以定义不支持ruby元素的浏览器所显示的内容。
- `wbr`：表示软换行。`wbr`和`br`的区别是：`br`元素是此处必须换行，而`wbr`元素意思就是浏览器窗口或父级元素的宽度足够宽时，不必进行换行；而当宽度不够时，主动在此处换行。`wbr`元素对于字符型的语言用处比较大，对于中文貌似没有多大用处。
- canvas：表示图形，比如图标或其他图像。这个元素本身没有行为，仅提供一块画布，但把一个绘图`API`展示给客户端JavaScript，以使脚本能够把想绘制的东西绘制到这块画布上。
- command：表示命令按钮，比如单选框、复选框或按钮。
- details：表示用户要求得到并且可以得到的细节信息。它可以与summary元素配合使用。summary元素提供标题或图例。标题是可见的，用户点击标题时，会显示细节信息。summary元素应该是details元素的第一个子元素。
- `datalist`：表示可选数据的列表，与input元素配合使用，可以制作出输入值的下拉列表。
- `datagrid`：表示可选数据的列表，它以树形列表的形式显示。
- `keygen`：表示生成密钥。
- output：表示不同类型的输出，比如脚本的输出。
- source：为媒介元素（比如video和audio）定义媒介资源。
- menu：表示菜单列表。当希望列出表单控件时使用该标签。
- dialog：表示对话框。

## 2.3 新增的input元素的类型

- email：表示必须输入email地址的文本输入框。
- `url`：表示必须输入URL地址的文本输入框。
- number：表示必须输入数值的文本输入框。
- range：表示必须输入一定范围内数字值的文本输入框。
- Date Pickers ：HTML 5 拥有多个可供选取日期和时间的新型文本输入框。
  - date：选取日、月、年。
  - month：选取月、年。
  - week：选取周、年。
  - time：选取时间（小时和分钟）。
  - `datetime`：选取时间、日、月、年（`UTC`时间）。
  - `datetime-local`：选取时间、日、月、年（本地时间）。

## 2.4 废弃的元素

- 能使用`CSS`替代的元素：例如`basefont、big、center、font`等。
- 不再使用frame框架：`frameset、frame与noframes`。
- 只有部分浏览器支持的元素：`applet、bgsound、blink、marquee`等。
- 其他被废弃的元素：`rb、acrionym、dir、isindex`等。

# 3 新增的属性与废弃的属性

## 3.1 新增的属性

- 表单相关的属性
  - 可以对`input(type=text)、select、textarea、button`元素指定`autofocus`属性。让元素在画面打开时自动获得焦点。
  - 可以对`input(type=text)、textarea`元素指定placeholder属性。提示用户可以输入的内容。
  - 可以对`input、output、select、textarea、button、fieldset`指定form属性，声明它属于哪个表单，然后将其放置在页面中的任意位置，而不是表单之内。
  - 可以对`input(type=text)、textarea`元素指定required属性。表示在用户提交时进行检查，检查该元素内一定要有输入内容。
  - 为input元素增加几个新的属性：`autocomplete、min、max、multiple、pattern与step`。同时还有一个新的list元素与`datalist`元素配合使用。`datalist`元素与`autocomplete`属性配合使用。multiple属性允许在上传文件时一次上传多个文件。
  - 为input和button元素增加新属性`formaction、formenctype、formmethod、formnovalidate、formtarget`，它们可以重载form元素的`action、enctype、method、novalidate、target`属性。
  - 为`fiedset`元素增加disabled属性，用于把它的子元素设为disabled状态。
  - 为input、button、form元素增加`novalidate`属性，该属性可以取消提交时进行的有关检查，表单可以被无条件提交。
  - 为所有可使用标签（label元素）的表单元素（包括非隐藏的input元素（type属性值不等于hidden）、`button、select、textarea、meter、output、progress、keygen`）定义一个labels属性，属性值为一个`NodeList`对象，代表该元素所绑定的标签元素所构成的集合。
  - 可以在标签（label元素）内部放置一个表单元素，并且通过该标签的control属性访问该表单元素。
  - 针对`input和textarea`元素，增加`SelectionDirection`属性。当用户在这两个元素中通过鼠标选取部分文字时，可以使用该属性来获取选取方向。当用户正向选取文字时，该属性值为“forward”；当用户反向选取文字时，该属性值为“backward”；当用户没有选取任何文字时，该属性值为“forward”。
  - 对`checkbox`元素添加indeterminate属性，以说明复选框处于“尚未明确是否选取”状态。
  - 对类型为image的input元素添加用于指定图片按钮中图片高度的height属性与图片宽度的width属性。
  - 对`textarea`元素新增用于限定可输入文字个数的`maxlength`属性，与用于指定表单提交时是否在文字换行处添加换行符的wrap属性。
- 链接相关的属性
  - 为a、area元素增加media、download、ping属性。media属性规定目标URL是为什么类型的媒介/设备进行优化的。download属性用于让用户下载目标链接所指向的资源，而不是直接打开该目标链接，这些属性均只能在`href`属性存在时使用。
  - 为area元素增加`hreflang、rel`属性，以保持与a、link元素的一致。
  - 为link元素增加新属性sizes。该属性可以与icon元素结合使用（通过rel属性），该属性指定关联图标（icon元素）的大小。
  - 为base元素增加target属性，主要目的是保持与a元素的一致。
- 其他属性
  - 为`ol`元素增加start、reversed属性。start属性定义列表的开始编号，reversed属性指定列表倒序显示。
  - 为meta元素增加`charset`属性，因为这个属性已经得到广泛支持，而且为文档的字符编码的指定提供了一个比较良好的方式。
  - 为menu元素增加新的属性type、label。label属性为菜单定义一个可见的标注，type属性让菜单可以以上下文菜单、工具条、列表菜单三种形式出现。
  - 为style元素增加scoped属性，用来规定样式的作用范围。
  - 为script元素增加`async`属性，它定义脚本是否异步执行。
  - 为`html`元素增加属性manifest，开发离线web应用程序时它与`API`结合使用，定义一个URL，在这个URL上描述文档的缓存信息。
  - 为`iframe`元素增加三个属性：`sandbox、seamless、srcdoc`。用来提高页面安全性，防止不信任的web页面执行某些操作。

## 3.2 废弃的属性

# 4 全局属性

​	所谓全局属性，是指可以对任何元素都使用的属性。

## 4.1 `contentEditable`属性

`contentEditable`是由微软开发的，被其他浏览器反编译并投入使用的一个全局属性。该属性的主要功能是允许用户编辑元素中的内容，所以该元素必须是可以获得鼠标焦点的元素，而且在点击鼠标后要向用户提供一个插入符号，提示用户该元素中的内容允许编辑。它是一个布尔值属性，可以被指定为true或false。

该属性还有个隐藏的inherit状态。属性值为true时元素允许编辑；属性值为false时元素不允许编辑；当未指定true或false时，则有inherit状态来决定。如果父元素可编辑，则该元素是可编辑的。

另外，还有一个`isContentEditable`属性。当元素可编辑时，该属性为true；当元素不可编辑时，该属性为false。

在编辑完元素的内容后，要想保存其中的内容，只能把该元素的`innerHTML`发送到服务器端进行保存。因为改变元素内容后该元素的`innerHTML`内容也会随之改变，目前还没有特定的`API`来保存编辑后元素的内容。

## 4.2 `designMode`属性

该属性用来指定整个页面是否可编辑。当页面可编辑时，页面中任何支持上文所述的`contentEditable`属性的元素都变成了可编辑状态。`designMode`属性只能在JavaScript脚本中被编辑修改。该属性有两个值：on和off。指定为on时页面可编辑；指定为off时页面不可编辑。

针对`designMode`属性，各浏览器的支持情况也不相同：

- `IE8`: 不允许；
- `IE9、Firefox、Opera`：允许
- `Chrome、Safari`：使用内嵌frame的方式，该内嵌frame是可编辑的。 

## 4.3 hidden属性

该属性类似与input元素的hidden，功能是通知浏览器不渲染该元素，使该元素处于不可见状态。但是元素中的内容还是被浏览器创建的，页面加载后允许使用JavaScript脚本将该属性取消，取消后该元素变为可见状态。hidden属性是一个布尔值的属性，true表示不可见，false表示可见。

## 4.4 `spellcheck`属性

该属性是HTML 5针对input(type=text)、`textarea`元素提供的一个新属性。对用户输入的文本内容进行拼写和语法检查。它是一个布尔值属性，具有true和false两个值。特殊的地方在于，必须明确声明属性值为true或false。

~~~html
<textarea spellcheck="true">
<input type="text" spellcheck="false">
~~~

## 4.5 `tabindex`属性

过去这个属性在编辑网页时非常有用，但如今控件的遍历顺序是由元素在页面上所处位置决定的。所以不再需要这个作用了。

它还有另一个作用。在默认情况下，只有链接元素与表单元素可以通过按键获得焦点。如果对其他元素使用`tabindex`属性后，也能让该元素获得焦点，那么当脚本中指定focus()语句的时候，就可以让该元素获得焦点。但这样做会有一个副作用：该元素也可以通过按Tab键获得焦点，而这可能不是开发者想要的结果。

把元素的`tabindex`属性值设为-1后就可以解决这个问题了。`tabindex`值为负数后，仍然可以通过编程的方式让元素获得焦点，但按下Tab键时该元素就不能获得焦点。

# 5 新增的事件

| 元素或对象          | 事件           | 触发时机                                                     |
| ------------------- | -------------- | ------------------------------------------------------------ |
| window对象 body元素 | `beforeprint`  | 即将开始打印之前触发                                         |
|                     | `afterprint`   | 打印完毕时触发                                               |
|                     | resize         | 浏览器窗口大小发生改变时触发                                 |
|                     | error          | 页面加载出错时触发                                           |
|                     | `offline`      | 页面变为离线状态时触发                                       |
|                     | `online`       | 页面变为在线状态时触发                                       |
|                     | `pageshow`     | 页面加载时触发，类似于load事件。区别在于load事件在页面第一次加载时触发，而`pageshow`在页面每一次加载时触发。即从网页缓存中读取页面时只触发`pageshow`事件，不触发load事件 |
|                     | `beforeunload` | 当页面被关闭时触发，该事件通知浏览器显示一个用于询问用户是否确实离开页面的确认窗口，可以设置该窗口中的提示文字。Firefox浏览器中开发者不能对其修改 |
|                     | `hashchange`   | 当页面URL地址字符串中的哈希部分（#后面的部分）发生改变时触发 |
| 任何元素            | `mousewheel`   | 当用户鼠标指针悬停在元素上并滚动鼠标滚轮时触发               |
| 任何容器元素        | scroll         | 当元素滚动条被滚动时触发                                     |
| `input/textarea`    | input          | 当用户修改文本框中内容时触发。input事件与change事件的区别为input事件在元素尚未失去焦点时已触发，change事件只在元素失去焦点时触发 |
| form                | reset          | 当用户按下表单元素中type类型为reset的input元素或JavaScript执行脚本中执行表单对象的reset方法时触发 |

