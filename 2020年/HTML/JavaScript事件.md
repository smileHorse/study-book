# 1 基本概念

- 事件类型(event type)是一个用来说明发生什么类型事件的字符串。比如`mousemove、keydown、load`。
- 事件目标(event target)是发生的事件或与之相关的对象。当讲事件时，必须同时指明类型和目标。在客户端的JavaScript应用程序中，Window、Document和Element对象是最常见的事件目标，但某些事件是由其他类型的对象触发。例如由`XMLHttpRequest`对象触发的`readystatechange`事件。
- 事件处理程序(event handler)或事件监听程序(event listener)是处理或响应事件的函数。应用程序通过指明事件类型和事件目标，在Web浏览器中注册它们的事件处理程序函数。当在特定的目标上发生特定类型的事件时，浏览器会调用对应的处理程序。
- 事件对象(event object)是与特定事件相关并且包含有关该事件详细信息的对象。事件对象作为参数传递给事件处理程序函数。所有的事件对象都有用来指定事件类型的type属性和指定事件目标的target属性。
- 事件传播(event propagation)是浏览器决定哪个对象触发其事件处理程序的过程。对于单个对象的特定事件（比如Window对象的load事件），必须是不能传播的。当文档元素上发生某个类型的事件时，它们会在文档树上向上传播或冒泡(bubble)。事件处理程序能通过调用方法或设置事件对象属性来阻止事件传播，这样它就能停止冒泡且将无法在容器元素上触发事件处理程序。
- 事件传播的另一种形式称为事件捕获(event capturing)，在容器元素上注册的特定处理程序有机会在事件传播到真实目标之前拦截（捕获）它。
- 一些事件有与之相关的默认操作。事件处理程序可以通过返回一个适当的值、调用事件对象的某个方法或设置事件对象的某个属性来阻止默认操作的发生，这被称为“取消”事件。

# 2 事件类型

## 2.1 传统事件类型

## 2.2 DOM事件

## 2.3 HTML 5事件

## 2.4 触摸屏和移动设备事件

# 7 鼠标滚轮事件

​	浏览器通常使用鼠标滚轮滚动或缩放文档，但可以通过取消mousewheel事件来阻止这些默认操作。

​	传递给mousewheel处理程序的事件对象有wheelDelta属性，其指定用户滚动滚轮有多远。远离用户方向的一次鼠标滚轮“单击”的wheelDelta值通常是120，而接近用户方向的一次“单击”的值是-120。在Safari和Chrome中，为了支持使用二维轨迹球而非一维滚轮的Apple鼠标，除了wheelDelta属性，事件对象还有wheelDeltaX和wheelDeltaY，而wheelDeltaY和wheelDelta的值一直相同。

​	除Firefox之外的所有浏览器都支持mousewheel事件，Firefox使用非标准的DOMMouseScroll事件取代mousewheel，使用事件对象的detail属性取代wheelDelta。但是detail属性值的缩放比率和正负符号不同于wheelDelta，detail值乘以-40和wheelDelta值相等。

​	3级DOM事件规范草案标准定义了wheel事件作为mousewheel和DOMMouseScroll的标准版本。传递给wheel事件处理程序的事件对象有deltaX、deltaY和deltaZ属性，以指定三个维度的旋转。这些值乘以-120才和mousewheel事件的wheelDelta值和正负符号相匹配。

~~~javascript
/*
把内容元素装入到一个指定大小（最小是50 * 50）的窗体或视口内
可选参数contentX和contentY指定内容相对于窗体的初始偏移量，如果指定，它们必须 <= 0
这个窗体有mousewheel事件处理程序
它允许用户平移元素或缩放窗体
 */
function enclose(content, framewidth, frameheight, contentX, contentY) {
    // 这些参数不仅仅是初始值
    // 它们保存当前状态，能被mousewheel处理程序使用和修改
    framewidth = Math.max(framewidth, 50);
    frameheight = Math.max(frameheight, 50);
    contentX = Math.min(contentX, 0) || 0;
    contentY = Math.min(contentY, 0) || 0;

    // 创建frame元素，且设置CSS类名和样式
    var frame = document.createElement("div");
    frame.className = "enclosure";
    frame.style.width = framewidth + "px";
    frame.style.height = frameheight + "px";
    frame.style.overflow = "hidden";
    frame.style.boxSizing = "border-box";
    frame.style.webkitBoxSizing = "border-box";
    frame.style.MozBoxSizing = "border-box";

    // 把frame放入文档中，并把内容移入frame中
    content.parentNode.insertBefore(frame, content);
    frame.appendChild(content);

    // 确定元素相对于frame的位置
    content.style.position = "relative";
    content.style.left = contentX + "px";
    content.style.top = contentY + "px";

    // 我们需要针对下面一些特定的浏览器怪癖进行处理
    var isMacWebkit = (navigator.userAgent.indexOf("Macintosh") !== -1 &&
                        navigator.userAgent.indexOf("WebKit") !== -1);
    var isFirefox = (navigator.userAgent.indexOf("Gecko") !== -1);

    // 注册mousewheel事件处理程序
    frame.onwheel = wheelHandler;   // 未来浏览器
    frame.onmousewheel = wheelHandler;  // 大多数当前浏览器
    if (isFirefox) {
        frame.addEventListener("DOMMouseScroll", wheelHandler, false);
    }
    
    function wheelHandler(event) {
        var e = event || window.event;

        // 查找wheel事件对象，mousewheel事件对象、DOMMouseScroll事件对象的属性
        // 从事件对象中提取旋转量
        // 绽放delta以便一次鼠标滚轮“单击”相对于屏幕的缩放增量是30像素
        // 如果未来浏览器在同一事件上同时触发wheel和mousewheel，这里最终会重复计算，
        // 所以，希望取消wheel事件将阻止mousewheel事件的产生
        var deltaX = e.deltaX * -30 ||      // wheel事件
                    e.wheelDeltaX / 4 ||    // mousewheel
                    0;                       // 属性未定义
        var deltaY = e.deltaY * -30 ||                  // wheel事件
                    e.wheelDeltaY / 4 ||                // Webkit中的mousewheel事件
                    (e.wheelDeltaY == undefined &&     // 如果没有2D属性
                        e.wheelDelta / 4) ||            // 那么就用1D的滚轮属性
                    e.detail * -1 ||                   // Firefox的DOMMouseScroll事件
                    0;                                  // 属性未定义

        // 在大多数浏览器中，每次鼠标滚轮单击对应的delta是120
        // 但是，在Mac中，鼠标滚轮似乎对速度更敏感，
        // 其delta值通常要大120倍，使用Apple鼠标至少如此
        // 使用浏览器测试解决这个问题
        if (isMacWebkit) {
            deltaX /= 30;
            deltaY /= 30;
        }

        // 如果在Firefox中得到mousewheel或wheel事件，那么就不需要DOMMouseScroll事件
        if (isFirefox && e.type !== "DOMMouseScroll") {
            frame.removeEventListener("DOMMouseScroll", wheelHandler, false);
        }

        // 获取内容元素的当前尺寸
        var contentbox = content.getBoundingClientRect();
        var contentwidth = contentbox.right - contentbox.left;
        var contentheight = contentbox.bottom - contentbox.top;
        
        if (e.altKey) { // 如果按下Alt键，就可以调整frame大小
            if (deltaX) {
                framewidth -= deltaX;   // 新宽度，但不能比内容大
                framewidth = Math.min(framewidth, contentwidth);
                framewidth = Math.max(framewidth, 50);  // 且也不能比50小
                frame.style.width = framewidth + "px";  // 在frame上设置它
            }
            if (deltaY) {
                frameheight -= deltaY;
                frameheight = Math.min(frameheight, contentheight);
                frameheight = Math.max(frameheight - deltaY, 50);
                frame.style.height = frameheight + "px";
            }
        } else {    // 如果没有按下Alt键，就可以平移frame的内容
            if (deltaX) {
                // 不能再滚动了
                var minoffset = Math.min(framewidth - contentwidth, 0);
                // 把deltaX添加到contentX中，但不能小于minoffset
                contentX = Math.max(contentX + deltaX, minoffset);
                contentX = Math.min(contentX, 0);
                content.style.left = contentX + "px";   // 设置新的偏移量
            }
            if (deltaY) {
                var minoffset = Math.min(frameheight - contentheight, 0);
                // 把deltaY添加到contentY，但不能小于minoffset
                contentY = Math.max(contentY + deltaY, minoffset);
                contentY = Math.min(contentY, 0);
                content.style.top = contentY + "px";
            }
        }

        // 不让这个事件冒泡，阻止任何默认操作
        // 这会阻止浏览器使用mousewheel事件滚动文档
        // 希望对于相同的鼠标滚动
        // 调用wheel事件上的preventDefault()也能阻止mousewheel事件的产生
        if (e.preventDefault) e.preventDefault();
        if (e.stopPropagation) e.stopPropagation();
        e.cancelBubble = true;  // IE事件
        e.returnValue = false;  // IE事件
        return false;
    }
}
~~~

# 8 拖放事件



# 9 文本事件

​	在键盘事件中，keydown和keyup是低级事件，而keypress是较高级的事件，它表示产生了一个可打印字符。3级DOM事件规范草案定义一个更通用的textinput事件，不管来源（键盘、粘贴或拖放形式的数据传输、亚洲语言输入法、声音或手写识别系统），无论何时用户输入文本时都会触发它。textinput事件可能尚未得到支持，但Webkit浏览器支持一个类似的textInput（使用大写字母I）事件。

​	textinput/textInput事件传递一个简单的事件对象，它有一个用于保存输入文本的data属性，另一个inputMethod属性是建议用于指定输入源，但它尚未实现。对于键盘输入，data属性通常只保存单个字符，但其他输入源通常可能包含多个字符。

​	一个keypress事件表示输入的单个字符。事件对象以数字Unicode编码的形式指定字符，所以必须用String.fromCharCode()把它转换成字符串。在大多数浏览器中，事件对象的keyCode属性指定了输入字符的编码。但是由于历史的原因，Firefox使用的是charCode属性。大多数浏览器只在当产生可打印字符时触发keypress事件。但是Firefox在产生非打印字符时也触发keypress事件。为了检测这种情况（这样就能忽略非打印字符），可以查找有charCode属性但值为0的事件对象。

​	可以通过取消textinput/textInput/keypress事件来阻止字符输入，这意味着可以使用这些事件来过滤输入。

~~~javascript
/*
过滤<input>元素的键盘输入

这个模块查找文档中拥有“data-allowed-chars”属性的所有<input type="text">元素
它为所有这类元素都注册keypress、textinput、textInput事件处理程序
来限制用户只能输入出现在许可属性值中的字符
如果<input>元素也有一个“data-messageid”属性，
那么认为这个值是另一个文档元素的id
如果用户输入了不允许的字符，那么会显示消息元素
如果用户输入了允许的字符，那么会隐藏消息元素
这个信息id元素用于向用户说明拒绝输入的原因
它通常应该由CSS控制样式，那么它开始不可见

下面是使用整个模块的HTMl代码示例
邮政编码: <input id="zip" type="text" data-allowed-chars="0123456789" data-messageid="zipwarn">
<span id="zipwarn" style="color: red;visibility:hidden">只支持数字</span>
 */
whenReady(function () { // 当文档加载完毕时，运行这个函数
    // 查找所有<input>元素
    var inputelts = document.getElementsByTagName("input");
    // 遍历它们
    for (let i = 0; i < inputelts.length; i++) {
        var elt = inputelts[i];
        // 跳过不是文本域或没有data-allowed-chars属性的元素
        if (elt.type != "text" || !elt.getAttribute("data-allowed-chars")) {
            continue;
        }

        // 在input元素上注册事件处理程序函数
        if (elt.addEventListener) {
            elt.addEventListener("keypress", filter, false);
            elt.addEventListener("textinput", filter, false);
            elt.addEventListener("textInput", filter, false);
        } else {
            elt.attachEvent("onkeypress", filter);
        }
    }

    // 这是用于过滤用户输入的keypress、textinput、textInput事件处理程序
    function filter(event) {
        // 获取时间对象和目标元素对象
        var e = event || window.event;  // 标准或IE模型
        var target = e.target || e.srcElement;  // 标准或IE模型
        var text = null;    // 输入的文本

        // 获取输入的字符或文本
        if (e.type == "textinput" || e.type == "textInput") text = e.data;
        else {  // 这是传统的keypress事件
            // 对于可打印键的keypress事件，Firefox使用charCode
            var code = e.charCode || e.keyCode;
            // 如果按下的是任何形式的功能键，不要过滤它
            if (code < 32 ||    // ASCII控制字符
                e.charCode == 0 ||  // 功能键（仅指Firefox）
                e.ctrlKey || e.altKey) {    // 按下辅助键
                return;
            }
            // 把字符编码转化为字符串
            text = String.fromCharCode(code);
        }

        // 现在需要从input元素中查找所需信息
        var allowed = target.getAttribute("data-allowed-chars");
        var messageid = target.getAttribute("data-messageid");
        if (messageid) {
            var messageElement = document.getElementById(messageid);
        }

        // 遍历输入文本中的字符
        for (var i = 0; i < text.length; i++) {
            var c = text.charAt(i);
            if (allowed.indexOf(c) == -1) {
                // 如果存在不合法字符，显示消息元素
                if (messageElement) {
                    messageElement.style.visibility = "visible";
                }

                // 取消默认行为，所以不会插入文本
                if (e.preventDefault) e.preventDefault();
                if (e.returnValue) e.returnValue = false;
                return false;
            }
        }

        // 如果所有的字符都合法，隐藏存在的消息元素
        if (messageElement) {
            messageElement.style.visibility = "hidden";
        }
    }
})
~~~

​	keypress、textinput事件是在新输入的文本真正插入到聚焦的文档元素前触发，这就是在这些事件处理程序能够取消事件和阻止文本插入的原因。浏览器也实现了在文本插入到元素后才触发的input事件类型input。虽然这些事件不能取消，不能指定其事件对象中的最新文本，但它们能以某种形式提供元素文本内容发生改变的通知。例如，如果想确保输入框中输入的任何文本都是大写，那么可以像如下这样使用input事件：

~~~html
<input type="text" oninput="this.value = this.value.toUpperCase();">
~~~

​	HTML 5标准化了input事件，除IE浏览器之外的所有浏览器都支持它。在IE中，可以使用不标准的propertychange事件检测文本输入元素的value属性改变来实现相似的效果。

~~~javascript
function forceToUpperCase(element) {
    if (typeof element === "string") {
        element = document.getElementById(element);
    }
    element.oninput = upcase;
    element.onpropertychange = upcaseOnPropertyChange;

    // 简单案例：用于input事件的处理程序
    function upcase(event) {
        this.value = this.value.toUpperCase();
    }
    // 疑难案例：用propertychange事件的处理程序
    function upcaseOnPropertyChange(event) {
        var e = event || window.event;
        // 如果value属性发生改变
        if (e.propertyName === "value") {
            // 移除onpropertychange处理程序，避免循环调用
            this.onpropertychange = null;
            // 把值都变为大写
            this.value = this.value.toUpperCase();
            // 然后恢复原来的propertychange处理程序
            this.onpropertychange = upcaseOnPropertyChange;
        }
    }
}
~~~

# 10 键盘事件

​	按下或释放按键时，会发生keydown和keyup事件。它们由辅助键（一般指Shift、Ctrl、Alt、Window、Command和Fn键）、功能键（一般指类似F1、F2这些以F加数字组成的键）和字母数字键产生。如果用户按键时间足够长会导致它开始重复，那么在keyup事件到达之前会收到多个keydown事件。

​	这些事件相关的事件对象都有数字属性keyCode，指定了按下的键是哪个。对于产生可打印字符的按键，keyCode值是按键上出现的主要字符的Unicode编码。无论Shift键处于什么状态，字母键总是产生大写keyCode值。但数字键产生的keyCode值就是出现在对应键上的数字。对于不可打印键，keyCode属性将是一些其他值。

​	键盘事件对象有altKey、ctrlKey、metakey和shiftKey属性，当事件发生时，如果对应的辅助键被按下，那么它们会被设置为true。

​	keydown、keyup事件以及keyCode值尚未标准化，但适当的跨浏览器兼容性是可行的。3级DOM事件规范草案标准化了keydown和keyup事件类型，但没有尝试标准化keyCode。相反，它定义了新属性key，它会以字符串的形式包含键名。如果按键对应的是一个可打印字符，那么key属性将仅仅是这个可打印字符。如果按键是功能键，那么key属性将是像“F2”、“Home”、“Left”这样的值。

​	key属性尚未在任何浏览器中实现。但是，像Safari和Chrome这类基于Webkit的浏览器为这些事件的事件对象定义了一个keyIdentifier属性。类似key，keyIdentifier是字符串而非数字。对于功能键，它是像“Shift”、“Enter”这样的值。对于可打印字符，该属性保存了这个字符的Unicode编码的字符串表示形式，例如对于A键，它是“U+0041”。

~~~javascript
/*
Keymap.js:绑定键盘事件和处理程序函数

这个模块定义了一个Keymap类
这个类的实例表示按键标识符到处理程序函数的映射
Keymap能配置到HTML元素上以处理keydown事件
当此类事件发生时，Keymap会使用它的映射来调用合适的处理程序

当创建Keymap时，能传入一个JavaScript对象，它表示Keymap绑定的初始设置。
对象的属性名是按键标识符，而属性值是事件处理程序函数
在创建Keymap之后，
通过给bind()方法传入按键标识符和处理程序函数可以添加一个新的绑定
通过给unbind()方法传入按键标识符来移除绑定

通过给Keymap的install()方法传入像document对象这样的HTML元素，然后就可以使用它
install()方法给指定的对象添加onkeydown事件处理程序
当调用这个处理程序时，它判断按下键的按键标识符，
如果有这个按键标识符的任何绑定，就调用对应的处理程序函数
一个Keymap可以在多个HTML元素上配置

按键标识符
按键标识符是一个区分大小写的字符串，它表示按键加上同一时刻按下的辅助键
按键的名称通常是按键上的字符（不会变）
法定的键名包括“A”、“7”、“F2”、“PageUp”、“Left”、“Backspace”和“Esc”

请参阅模块的Keymap.keyCodeToKeyName对象中的键名列表
这里有3级DOM规范定义的键名子集
并且当实现时这个类将使用事件对象的key属性

按键标识符也可能包含辅助键前缀
这些前缀是Alt、Ctrl、Meta和Shift
它们区分大小写，而且必须使用空格、下划线、连字符或“+”来和按键名或彼此分开
例如：“SHIFT+A”、“Alt_F2”、“meta-v”、“ctrl alt left”
在Mac中，Meta是Command键，Alt是Option键
一些浏览器把Windows键映射到Meta辅助键

处理程序函数
处理程序函数在配置Keymap的文档或文档元素上作为其方法调用
并传入两个参数：
1）keydown事件的事件对象
2）按下的按键的标识符
处理程序的返回值就是keydown处理程序的返回值
如果处理程序返回false，Keymap将停止冒泡并取消和keydown事件相关的默认操作

限制
在所有按键上绑定一个事件处理函数是不可能的
操作系统会限制一些按键序列，例如Alt+F4
而浏览器本身也可能限制其他一些按键序列，例如Ctrl+S
这些代码受限于浏览器、OS和本地设置。功能键和有辅助键的功能键工作得很好
而没有辅助键的字母数字键也工作得很好
Ctrl和Alt与字母键盘键的结合非常强健

在美国标准键盘布局上，
能够支持大多数不需要Shift键的标点字符
但是它们不适合其他键盘布局，应该避免
 */

// 这是构造函数
function Keymap(bindings) {
    this.map = {};      // 定义按键标识符 -> 处理程序映射
    if (bindings) {     // 给它复制初始绑定
        for (name in bindings) {
            this.bind(name, bindings[name]);
        }
    }
}

// 绑定指定的按键标识符和指定的处理程序函数
Keymap.prototype.bind = function (key, func) {
    this.map[Keymap.normalize(key)] = func;
};

// 删除指定按键标识符的绑定
Keymap.prototype.unbind = function (key) {
    delete this.map[Keymap.normalize(key)];
};

// 在指定HTML元素上配置Keymap
Keymap.prototype.install = function (element) {
    // 这是事件处理程序函数
    var keymap = this;
    function handler(event) { return keymap.dispatch(event, element); }

    // 现在安装它
    if (element.addEventListener) {
        element.addEventListener("keydown", handler, false);
    } else if (element.attachEvent) {
        element.attachEvent("onkeydown", handler);
    }
};

// 这个方法基于Keymap绑定分派按键事件
Keymap.prototype.dispatch = function (event, element) {
    // 开始没有辅助键和键名
    var modifiers = "";
    var keyname = null;

    // 按照标准的小写字母顺序构建辅助键字符串
    if (event.altKey) modifiers += "alt_";
    if (event.ctrlKey) modifiers += "ctrl_";
    if (event.metaKey) modifiers += "meta_";
    if (event.shiftKey) modifiers += "shift_";

    // 如果实现3级DOM规范的key属性，获取keyname很容易
    if (event.key) keyname = event.key;
    // 在Safari和Chrome上用keyIdentifier获取功能键键名
    else if (event.keyIdentifier && event.keyIdentifier.substring(0, 2) != "U+")
        keyname = event.keyIdentifier;
    // 否则，使用keyCode属性和后面的编码到键名的映射
    else
        keyname = Keymap.keyCodeToKeyName[event.keyCode];

    // 如果不能找出键名，只能返回并忽略这个事件
    if (!keyname) return;

    // 标准的按键id是辅助键加上小写的键名
    var keyid = modifiers + keyname.toLowerCase();

    // 现在查看按键标识符是否绑定了任何东西
    var handler = this.map[keyid];

    if (handler) {  // 如果这个键有处理程序，调用它
        // 调用处理程序函数
        var retval = handler.call(element, event, keyid);

        // 如果处理程序返回false，取消默认操作并阻止冒泡
        if (retval == false) {
            if (event.stopPropagation) event.stopPropagation();     // DOM模型
            else event.cancelBubble = true;                      // IE模型
            if (event.preventDefault)   event.preventDefault();     // DOM
            else event.returnValue = false;                       // IE
        }

        // 返回处理程序的返回值
        return retval;
    }
};

// 用于把按键标识符转换成标准形式的工具函数
// 在非Mac硬件，我们这里把meta映射到ctrl，
// 这样在Mac中“Meta+C”将变成“Command+C”，其他都是“Ctrl+C”
Keymap.normalize = function (keyid) {
    keyid = keyid.toLowerCase();
    var words = keyid.split(/\s+|[\-+_]/);  // 分割辅助键和键名
    var keyname = words.pop();  // 键名是最后一个
    keyname = Keymap.aliases[keyname] || keyname;   // 它是别名吗？
    words.sort();   // 排序剩下的辅助键
    words.push(keyname);    // 添加到序列化名字后面
    return words.join("_"); // 把它们拼接起来
};

Keymap.aliases = {      // 把按键的常见别名映射到它们的“正式名”
    "escape": "esc",    // 键名使用3级DOM规范的定义和后面的编码到键名的映射
    "delete": "del",    // 所有的键和值都必须小写
    "return": "enter",
    "ctrl": "control",
    "space": "spacebar",
    "ins": "insert"
};

// 传统的keydown事件对象的keyCode属性是不标准的
// 但下面的值似乎可以在大多数浏览器和OS中可行
Keymap.keyCodeToKeyName = {
    // 使用词或方向键的按键
    8: "Backspace", 9: "Tab", 13: "Enter", 16: "Shift", 17: "Control", 18: "Alt",
    19: "Pause", 20: "CapsLock", 27: "Esc", 32: "Spacebar", 33: "PageUp",
    34: "PageDown", 35: "End", 36: "Home", 37: "Left", 38: "Up", 39: "Right",
    40: "Down", 45: "Insert", 46: "Del",

    // 主键盘（非数字小键盘）上的数字键
    48: "0", 49: "1", 50: "2", 51: "3", 52: "4", 53: "5", 54: "6", 55: "7", 56: "8", 57: "9",

    // 字母按键，注意我们不区分大小写
    65: "A", 66: "B", 67: "C", 68: "D", 69: "E", 70: "F", 71: "G", 72: "H", 73: "I",
    74: "J", 75: "K", 76: "L", 77: "M", 78: "N", 79: "O", 80: "P", 81: "Q", 82: "R",
    83: "S", 84: "T", 85: "U", 86: "V", 87: "W", 88: "X", 89: "Y", 90: "Z",

    // 数字小键盘的数字和标点符号按键（Opera不支持这些）
    96: "0", 97: "1", 98: "2", 99: "3", 100: "4", 101: "5", 102: "6", 103: "7", 104: "8", 105: "9",
    106: "Multiply", 107: "Add", 109: "Subtract", 110: "Decimal", 111: "Divide",

    // 功能键
    112: "F1", 113: "F2", 114: "F3", 115: "F4", 116: "F5", 117: "F6",
    118: "F7", 119: "F8", 120: "F9", 121: "F10", 122: "F11", 123: "F12",
    124: "F13", 125: "F14", 126: "F15", 127: "F16", 128: "F17", 129: "F18",
    130: "F19", 131: "F20", 132: "F21", 133: "F22", 134: "F23", 135: "F24",
    // 不需要按下Shift键的标点符号键
    // 连字符不兼容，FF返回的编码和减号一样
    59: ";", 61: "=", 186: ";", 187: "=",   // Firefox和Opera返回59,61
    188: ",", 190: ".", 191: "/", 192: "`", 219: "[", 220: "\\", 221: "]", 222: "'"
};
~~~

