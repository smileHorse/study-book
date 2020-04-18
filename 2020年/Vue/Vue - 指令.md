# Vue - 指令

​	Vue.js的指令是带有特殊前缀`v-`的HTML特性，它绑定一个表达式，并将一些特性应用到DOM上。

## 1 基本指令

### 1.1 `v-cloak`

`v-cloak`不需要表达式，它会在`Vue`实例结束编译时从绑定的HTML元素上移除，经常和CSS的`display:none`配合使用。

一般情况下，`v-cloak`是一个解决初始化慢导致页面闪动的最佳实践，对于简单的项目很实用，但对于工程化的项目，比如使用`webpack`和`vue-router`时，项目的HTML结构只有一个空的div元素，剩下的内容都是由路由去挂载不同的组件完成的，所以不再需要`v-cloak`。

~~~vue
<div id="app" v-cloak>
    {{ message }}
</div>
~~~

当网速较慢，`Vue.js`文件还没加载完时，在页面上会显示`{{ message }}`的字样，直到`Vue`创建实例、编译模板时，DOM才会被替换，所以这个过程屏幕是有闪动的。只要加上一句CSS语句就可以解决问题：

~~~css
[v-cloak] {
    display: none;
}
~~~

### 1.2 `v-once`

​	`v-once`也不需要表达式，作用是定义它的元素或组件只渲染一次，包括元素或组件的所有子节点。首次渲染后，不再随数据的变化重新渲染，将被视为静态内容。

~~~vue
<div id="app">
    <span v-once>{{ message }}</span>
</div>
~~~

## 2 条件渲染指令

### 2.1 `v-if v-else-if v-else`

​	`Vue.js`的条件指令可以根据表达式的值在DOM中渲染或销毁元素/组件。表达式的值为真时，当前元素/组件及其子节点将被渲染；为假时将被移除。如果一次判断的是多个元素，可以在`Vue.js`内置的`<template>`元素上使用条件指令，最终的渲染结果不会包含该元素：

~~~vue
<div id="app">
    <p v-if="status === 1">status为1时显示</p>
    <p v-else-if="status === 2">status为2时显示</p>
    <p v-else>其他情况显示</p>
    
    <template v-if="status === 1">
    	<p>文本1</p>
    	<p>文本2</p>
    </template>
</div>
~~~

`Vue`在渲染元素时，出于效率考虑，会尽可能地复用已有的元素而非重新渲染，比如下面的示例：

~~~vue
<div id="app">
    <template v-if="type === 'name'">
    	<label>用户名</label>
		<input placeholder="输入用户名">
    </template>
    <template v-else>
    	<label>邮箱</label>
		<input placeholder="输入邮箱">
    </template>
</div>
~~~

键入内容后，切换类型时虽然DOM变了，但是之前在输入框键入的内容并没有改变，只是替换了`placeholder`的内容，说明`<input>`元素被复用了。

如果不希望这么做，可以使用`Vue.js`提供的`key`属性，它可以让你自己决定是否要复用元素，`key`的值必须是唯一的，增加了`key`的元素不会再被复用：

~~~vue
<div id="app">
    <template v-if="type === 'name'">
    	<label>用户名</label>
		<input placeholder="输入用户名" key="name-input">
    </template>
    <template v-else>
    	<label>邮箱</label>
		<input placeholder="输入邮箱" key="mail-input">
    </template>
</div>
~~~

### 2.2 `v-show`

`v-show`的用法与`v-if`基本一致，只不过`v-show`是改变元素的CSS属性`display`。当`v-show`表达式的值为false时，元素会隐藏。`v-show`不能在`<template>`上使用。

### 2.3 `v-if`与`v-show`的选择

`v-if`是真正的条件渲染，它会根据表达式适当地销毁或重建元素及绑定的事件或子组件。若表达式初始值为false，则一开始元素/组件并不会渲染，只有当条件第一次变为真时才开始编译。

`v-show`只是简单的CSS属性切换，无论条件真与否，都会被编译。相比之下，`v-if`更适合条件不经常改变的场景，因为它切换开销相对较大，而`v-show`适用于频繁切换条件。

## 3 列表渲染指令`v-for`

### 3.1 基本用法

`v-for`指令用于将一个数组遍历或枚举一个对象循环显示。它的表达式需要结合`in`或`of`来使用，类似`item in items`或`item of items`的形式。

~~~vue
<div>
    <ul>
        <li v-for="book in books">{{ book.name }}</li>
    </ul>
</div>
~~~

`v-for`表达式支持一个可选参数作为当前项的索引：

~~~vue
<li v-for="(book, index) in books">{{ index }} - {{ book.name }}</li>
~~~

`v-for`也可以用在内置标签`<template>`上，将多个元素进行渲染：

~~~vue
<template v-for="book in books">
	<li>书名：{{ book.name }}</li>
	<li>作者：{{ book.author }}</li>
</template>
~~~

`v-for`也可以用来遍历对象的属性，参数为属性值。遍历对象属性时，有两个可选参数，分别是键名和索引 ：

~~~vue
<span v-for="value in user">{{ value }}</span>

<span v-for="(value, key, index) in user">{{index}} - {{ key }}: {{ value }}</span>
~~~

`v-for`也可以用来迭代整数：

~~~vue
<span v-for="n in 10">{{ n }}</span>
~~~

### 3.2 数组更新

`Vue`包含了一组观察数组变异的方法，使用它们改变数据会触发视图更新：`push()、pop()、shift()、unshift()、splice()、sort()、reverse()`。

使用一些非变异的方法，它们返回一个新数组，不改变原数组，不会触发视图更新。比如`filter()、concat()、slice()`。这个时候可以用新数组来替换原数组，以触发视图更新。

**`Vue`在检测到数组变化时，并不是直接重新渲染这个列表，而是最大化地复用DOM元素。替换的数组中，含有相同元素的项不会被重新渲染，因此新数组替换原数组时，不用担心效率问题。**

下面的变动数组中，`Vue`是不能检测到的，也不会触发视图更新：

- 通过索引直接设置项，比如`app.book[3]={...}`;
- 修改数组长度，比如`app.book.length=1`;

解决第一个问题可以用两种方法实现同样的效果，第一种是使用`Vue`内置的`set`方法：

~~~vue
Vue.set(app.books, 3, {
	name: '...',
	author: '...'
})
~~~

如果是在`webpack`中使用组件化的方式，默认是没有导入`Vue`的，这时可以使用`$set`，例如：

~~~vue
this.$set(app.books, 3, {
	name: '...',
	author: '...'
})
~~~

这里的this指向的就是当前组件实例，即app。非`webpack`模式下，也可以用`$set`方法，例如`app.$set()`。

另一种方法：

~~~vue
app.books.splice(3, 1, {name: '...', author: '...'});
~~~

第二个问题也可以直接用`splice`解决：

~~~vue
app.books.splice(1);
~~~

### 3.3 过滤与排序

​	当你不想改变原数组，想通过一个数组的副本来做过滤或排序的显示时，可以使用计算属性来返回过滤或排序后的数组。

~~~vue
<div id="app">
    <ul>
        <template v-for="book in filterBooks">
        	<li>书名：{{ book.name }}</li>
        	<li>作者：{{ book.author }}</li>
        </template>
    </ul>
</div>
<script>
	var app = new Vue({
        el: '#app',
        data: { books: [....] },
        computed: {
            filterBooks: function() {
                return this.books.filter(function (book) {
                    return book.name.match(/JavaScript/);
                })
            },
            sortedBooks: function() {
                return this.books.sort(function (a, b) {
                    return a.name.length < b.name.length;
                })
            }
        }
    })
</script>
~~~

## 4 方法与事件

### 4.1 基本用法

使用`v-on`指令处理`Vue`事件。`@click`的表达式可以直接使用`JavaScript`语句，也可以是一个`Vue`实例中`methods`选项内的函数名。

~~~vue
<div id="app">
    <button @click="counter++">+ 1</button>
    <button @click="handleAdd">+ 1</button>
    <button @click="handleAdd(10)">+ 10</button>
</div>
<script>
	var app = new Vue({
        el: '#app',
        data: {
            counter: 0
        },
        methods: {
            handleAdd: function(count) {
                count = count || 1;
                this.counter += count;
            }
        }
    })
</script>
~~~

`@click`调用的方法名后可以不跟`()`。此时，如果该方法有参数，默认会将原生事件对象`event`传入。在大部分业务场景中，如果方法不需要传入参数，为了简便可以不写`()`。

当`ViewModel`销毁时，所有的事件处理器都会自动删除，无须自己清理。

`Vue`提供了一个特殊变量`$event`，用于访问原生DOM事件：

~~~vue
<div id="app">
    <a href="..." @click="handleClick('禁止打开', $event)">打开链接</a>
</div>
<script>
	var app = new Vue({
        el: '#app',
        methods: {
            handleClick: function(message, event) {
                event.preventDefault();
                window.alert(message);
            }
        }
    })
</script>
~~~

### 4.2 修饰符

在`@`绑定的事件后加一个点`.`，再跟一个后缀来使用修饰符。`Vue`支持以下修饰符，修饰符也可串联使用：

| 修饰符     | 用法                               |
| ---------- | ---------------------------------- |
| `.stop`    | 阻止事件冒泡                       |
| `.prevent` | 阻止默认事件                       |
| `.capture` | 使用事件捕获模式                   |
| `.self`    | 只当事件在该元素自身触发时触发回调 |
| `.once`    | 只触发一次                         |

在表单元素上监听键盘事件时，还可以使用按键修饰符，比如按下具体某个键时才调用方法：

~~~vue
<input @keyup.13="submit">
~~~

也可以自己配置具体按键：

~~~vue
Vue.config.keyCodes.f1 = 112;	// 全局定义后，就可以使用@keyup.f1
~~~

除了具体的某个`keyCode`外，`Vue`还提供了一些快捷名称：`.enter .tab .delete .esc .space .up .down .left .right .ctrl .alt .shift .meta(Mac下是Command键，Windows下是窗口键)`。

## 5 自定义指令

自定义指令的注册方法和组件很像，也氛围全局注册和局部注册。比如注册一个`v-focus`指令：

~~~vue
// 全局注册
Vue.directive('focus', {
	// 指令选项
});

// 局部注册
var app = new Vue({
	el: '#app',
	directives: {
		focus: {
			// 指令选项
		}
	} 
})
~~~

自定义指令的选项是由几个钩子函数组成的，每个都是可选的：

| 钩子函数           | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| `bind`             | 只调用一次，指令第一次绑定到元素时调用，可以定义一个在绑定时执行一次的初始化操作 |
| `inserted`         | 被绑定元素插入父节点时调用，父节点存在即可调用，不必存在于`document`中 |
| `update`           | 被绑定元素所在模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新 |
| `componentUpdated` | 被绑定元素所在模板完成一次更新周期时调用                     |
| `unbind`           | 只调用一次，指令与元素解绑时调用                             |

每个钩子函数都有几个参数可用，它们的含义如下：

| 参数       | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| `el`       | 指令所绑定的元素，可以用来直接操作DOM                        |
| `binding`  | 一个对象，包含以下属性                                       |
|            | `name`：指令名，不包括`v-`前缀                               |
|            | `value`：指令的绑定值，例如`v-my-directive="1 + 1"`,`value`的值是2 |
|            | `oldValue`：指令绑定的前一个值，仅在`update`和`componentUpdated`钩子中可用。无论值是否改变都可用 |
|            | `expression`：绑定值的字符串形式，例如`v-my-directive="1 + 1"`,`expression`的值是`"1 + 1"` |
|            | `arg`：传给指令的参数。例如`v-my-directive:foo`，`arg`的值是`foo` |
|            | `modifiers`：一个包含修饰符的对象。例如`v-my-directive.foo.bar`，`modifiers`的值是`{foo: true, bar: true}` |
| `vnode`    | `Vue`编译生成的虚拟节点                                      |
| `oldVnode` | 上一个虚拟节点，仅在`update`和`componentUpdated`钩子中可用   |

在大多数使用场景中，我们会在`bind`钩子里绑定一些事件，比如在`document`上用`addEventListener`绑定，在`unbind`中用`removeEventListener`解绑。

### 5.1 一个可从外部关闭的下拉菜单

`clickoutside.js`：

~~~vue
Vue.directive('clickoutside', {
	bind: function(el, binding, vnode) {
		function documentHandler (e) {
			if(el.contains(e.target)) { return false; }
			if(binding.expression) { binding.value(e); }
		}
		e._vueClickOutSide = documentHandler;
		document.addEventListener('click', documentHandler);
	},
	unbind: function(el, binding) {
		document.removeEventListener('click', el._vueClickOutSide);
		delete el._vueClickOutSide;
	}
})
~~~

`index.html`：

~~~html
<div id="app" v-cloak>
    <div class="main" v-clickoutside="handleClose">
        ...
    </div>
</div>
~~~

