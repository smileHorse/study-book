# 1 `<script>`元素

​	向HTML页面中插入JavaScript的主要方法，就是使用`<script>`元素。HTML 4.01为`<script>`定义了下列6个属性：

- `async`：可选。表示应该立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或等待加载其他脚本。只对外部脚本文件有效。
- `charset`：可选。表示通过`src`属性指定的代码的字符集。由于大多数浏览器会忽略它的值，因此这个属性很少有人用。
- `defer`：可选。表示脚本可以延迟到文档完全被解析或显示之后再执行。只对外部脚本文件有效。
- `language`：已废弃。
- `src`：可选。表示包含要执行代码的外部文件。
- `type`：可选。可以看成是`language`的替代属性；表示编写代码使用的脚本语言的内容类型（MIME类型）。不过，这个属性不是必须的，如果没有指定这个属性，默认值为`text/javascript`。

​    包含在`<script>`元素内部的JavaScript代码将被从上到下依次解释。在解释器对`<script>`元素内部的所有代码求值完毕之前，页面中的其他内容都不会被浏览器加载或显示。

​	在使用`<script>`嵌入JavaScript代码时，记住不要在代码中的任何地方出现`</script>`字符串。因为按照解析嵌入式代码的规则，当浏览器遇到字符串`</script>`时，就会认为那是结束的`</script>`标签。可以通过转义字符解决这个问题：`<\/script>`。

​	无论如何包含代码，只要不存在`defer`和`async`属性，浏览器都会按照`<script>`元素在页面中出现的顺序对它们依次进行解析。

## 1.1 标签的位置

​	按照传统的做法，所有`<script>`元素都应该放在页面的`<head>`元素中。这种做法的目的是把所有外部文件（包括`CSS`文件和JavaScript文件）的引用都放在相同的地方。

~~~html
<html>
    <head>
        <script type="text/javascript" src="example.js"></script>
    </head>
    <body>
        <!-- 内容 -->
    </body>
</html>
~~~

​	放在`<head>`元素中，意味着必须等到全部JavaScript代码都被下载、解析和执行完成后，才能开始呈现页面的内容。对于需要很多JavaScript代码的页面来说，会导致浏览器在呈现页面时出现明显的延迟，而延迟期间的浏览器窗口将是一片空白。为了避免这个问题，现代Web应用程序一般都把全部JavaScript引用放在`<body>`元素中页面内容的后面。

~~~html
<html>
    <head>
    </head>
    <body>
        <!-- 内容 -->
        <script type="text/javascript" src="example.js"></script>
    </body>
</html>
~~~

​	这样，在解析包含的JavaScript代码之前，页面的内容将会完全呈现在浏览器中。而用户也会因为浏览器窗口显示空白页面的时间缩短而感到打开页面的速度加快了。

## 1.2 延迟脚本

​	`defer`属性的用途是表明脚本在执行时不会影响页面的构造。也就是说，脚本会被延迟到整个页面都解析完毕后再运行。**因此，在`<script>`元素中设置`defer`属性，相当于告诉浏览器立即下载，但延迟执行。**

~~~html
<html>
    <head>
        <script type="text/javascript" src="example1.js"></script>
        <script type="text/javascript" src="example2.js"></script>
    </head>
    <body>
        <!-- 内容 -->
    </body>
</html>
~~~

​	虽然我们把`<script>`元素放在了文档的`<head>`元素中，但其中包含的脚本将延迟到浏览器遇到`</html>`标签后再执行。 HTML 5 规范要求脚本按照它们出现的先后顺序执行，因此第一个延迟脚本会先于第二个延迟脚本执行，而这两个脚本会先于 `DOMContentLoaded` 事件执行。在现实当中，延迟脚本并不一定会按照顺序执行，也不一定会在 `DOMContentLoaded` 事件触发前执行，因此最好只包含一个延迟脚本。

​	前面提到过， defer 属性只适用于外部脚本文件。这一点在 HTML 5 中已经明确规定，**因此支持HTML 5 的实现会忽略给嵌入脚本设置的 defer 属性**。 IE 4～ IE 7 还支持对嵌入脚本的 defer 属性，但IE 8 及之后版本则完全支持 HTML 5 规定的行为。

​	IE 4、 Firefox 3.5、 Safari 5 和 Chrome 是最早支持 defer 属性的浏览器。其他浏览器会忽略这个属
性，像平常一样处理脚本。**为此，把延迟脚本放在页面底部仍然是最佳选择。  **

## 1.3 异步脚本

​	与 defer 类似， `async` 只适用于外部脚本文件，并告诉浏览器立即下载文件。但与 defer不同的是，标记为 `async`的脚本并不保证按照指定它们的先后顺序执行。

~~~html
<html>
    <head>
        <script type="text/javascript" src="example1.js"></script>
        <script type="text/javascript" src="example2.js"></script>
    </head>
    <body>
        <!-- 内容 -->
    </body>
</html>
~~~

​	在以上代码中，第二个脚本文件可能会在第一个脚本文件之前执行。因此，确保两者之间互不依赖非常重要。指定 `async` 属性的目的是不让页面等待两个脚本下载和执行，从而异步加载页面其他内容。**为此，建议异步脚本不要在加载期间修改 DOM。**
​	异步脚本一定会在页面的 load 事件前执行，但可能会在 `DOMContentLoaded` 事件触发之前或之后执行。支持异步脚本的浏览器有 Firefox 3.6、 Safari 5 和 Chrome。  

# 2 嵌入代码与外部文件

​	在 HTML 中嵌入 JavaScript 代码虽然没有问题，但一般认为最好的做法还是尽可能使用外部文件来包含 JavaScript 代码。不过，并不存在必须使用外部文件的硬性规定，但支持使用外部文件的人多会强调如下优点。

- 可维护性：遍及不同 HTML 页面的 JavaScript 会造成维护问题。但把所有 JavaScript 文件都放在一个文件夹中，维护起来就轻松多了。而且开发人员因此也能够在不触及 HTML 标记的情况下，集中精力编辑 JavaScript 代码。
- 可缓存：浏览器能够根据具体的设置缓存链接的所有外部 JavaScript 文件。也就是说，如果有两个页面都使用同一个文件，那么这个文件只需下载一次。因此，最终结果就是能够加快页面加载的速度。
- 适应未来：通过外部文件来包含 JavaScript 无须使用前面提到 `XHTML` 或注释 hack。 HTML 和`XHTML` 包含外部文件的语法是相同的。  