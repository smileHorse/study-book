# `Vue` - 组件

# 1 基本用法

组件需要注册后才能使用。注册分为全局注册和局部注册。

### 1.1 全局注册

全局注册后，任何`Vue`实例都可以使用。

~~~vue
Vue.component('my-component', {
	template: '<div>这里是组件的内容</div>'
})
~~~

`my-component`就是注册的组件自定义标签名称，推荐使用小写加减号分割的形式命名。

`template`的DOM结构必须被一个元素包含，如果直接写成“这里是组件的内容”，不带`<div></div>`是无法渲染的。

### 1.2 局部注册

在`Vue`实例中，使用`components`选项可以局部注册组件，注册后的组件只有在该实例作用域下有效。组件中也可以使用`components`选项来注册组件，使组件可嵌套。

~~~vue
<script>
	var Child = {
        template: '<div>局部注册组件的内容</div>'
    }
    
    var app = new Vue({
        el: '#app',
        components: {
            'my-component': Child
        }
    })
</script>
~~~

`Vue`组件的模板在某些情况下会受到HTML的限制，比如`<table>`内规定只允许是`<tr> <td> <th>`等这些表格元素，所以在`<table>`内直接使用组件是无效的。（如果使用的是字符串模板，是不受限制的，比如`.vue`单文件用法）。这种情况下，可以使用特殊的`is`属性来挂载组件：

~~~vue
<div id="app">
    <table>
        <tbody is="my-component"></tbody>
    </table>
</div>
<script>
	Vue.component('my-component', {
        template: '<div>....</div>'
    })
</script>
~~~

`tbody`在渲染时，会被替换为组件的内容。

### 1.3 组件选项

除`template`外，组件中类似`Vue`实例，还有`data、computed、methods`等选项。`data`选项和`Vue`实例稍有区别，组件中`data`必须是函数，然后将数据`return`出去。

## 2 使用`props`传递数据

### 2.1 基本用法

通常父组件的模板中包含子组件，父组件要正向的向子组件传递数据或参数，子组件接受到后根据数据或参数的不同来渲染不同的内容，或执行操作。这个正向传递数据的过程就是通过`props`来实现的。

在组件中，使用选项`props`来声明需要从父组件接收到的数据。`props`的值可以有两种，一种是字符串数组，一种是对象。这里先介绍一下字符串数组的格式：

~~~vue
<div id="app">
    <my-component message="来自父组件的数据"></my-component>
</div>
<script>
	Vue.component('my-component', {
        props: ['message'],
        template: '<div>{{ message }}</div>'
    })
</script>
~~~

`props`中声明的数据与`data`函数中返回的数据基本类似，区别在于`props`的来自父级，但都可以在模板`template`及计算属性`computed`和方法`methods`中使用。

由于HTML不区分大小写，当使用DOM模板时，驼峰命名的`props`名称要转为短横分隔命名。（如果使用字符串模板，可以忽略这些限制）：

~~~vue
<div id="app">
    <my-component warning-text="来自父组件的数据"></my-component>
</div>
<script>
	Vue.component('my-component', {
        props: ['warningText'],
        template: '<div>{{ warningText }}</div>'
    })
</script>
~~~

有时候，传递的数据并不是写死的，而是来自父级的动态数据，这时可以使用指令`v-bind`来动态绑定`props`的值。当父组件的数据变化时，也会传递给子组件。例如：

~~~vue
<div id="app">
    <input type="text" v-model="parentMessage">
    <my-component :message="parentMessage"></my-component>
</div>
<script>
	Vue.component('my-component', {
        props: ['message'],
        template: '<div>{{ message }}</div>'
    });
    
    var app = new Vue({
        el: '#app',
        data: {
            parentMessage: ''
        }
    })
</script>
~~~

**注意，如果要接传递数字、布尔值、数组、对象，而且不使用`v-bind`，传递的仅仅是字符串。**

### 2.2 单向数据流

`Vue2.x`通过`props`传递数据是单向的，也就是父组件数据变化时会传递给子组件，但是反过来不行。

业务中经常遇到两种需要改变`prop`的情况，一种是父组件传递初始值进来，子组件将它作为初始值保存起来，在自己的作用域下可以随意使用和修改。这种情况可以在组件的`data`内再声明一个数据，引用父组件的`prop`，例如：

~~~vue
<div id="app">
    <my-component :init-count="1"></my-component>
</div>
<script>
	Vue.component('my-component', {
        props: ['initCount'],
        template: '<div>{{ count }}</div>',
        data: function() {
            return {
                count: this.initCount
            }
        }
    });
    
    var app = new Vue({
        el: '#app'
    })
</script>
~~~

组件中声明了数据`count`，它在组件初始化时会获取来自父组件的`initCount`，之后就与之无关了，只用维护`count`，可以避免直接操作`initCount`。

另一种情况是`prop`作为需要被转变的原始值传入，这种情况使用计算属性就可以了，例如：

~~~vue
<div id="app">
    <my-component :width="10"></my-component>
</div>
<script>
	Vue.component('my-component', {
        props: ['width'],
        template: '<div :style="style">组件内容</div>',
        computed: {
            style: function() {
                return { width: this.width + 'px' }
            }
        }
    });
    
    var app = new Vue({
        el: '#app',
        data: {
            parentMessage: ''
        }
    })
</script>
~~~

**在`JavaScript`中对象和数组都是引用类型，指向同一个内存空间，所以`props`是对象或数组时，在子组件内改变是会影响父组件的。**

### 2.3 数据验证

当`prop`需要验证时，就需要使用对象的写法。验证的`type`类型可以是`String、Number、Boolean、Object、Array、Function`。`type`也可以是一个自定义构造器，使用`instanceof`检测。例如：

~~~vue
Vue.component('my-component', {
	props: {
		// 必须是数字类型
		propA: Number,
		// 必须是字符串或数字类型
		propB: [String, Number],
		// 布尔值，如果没有定义，默认值为true
		propC: { type: Boolean, default: true },
		// 数字，而且是必传
		propD: { type: Number, required: true },
		// 如果是数据或对象，默认值必须是一个函数来返回
		propE: { type: Array, default: function() { return []; } },
		// 自定义一个验证函数
		propF: { validator: function(value) { return value > 10; } }
	}
});
~~~

当`prop`验证失败时，在开发版本下会在控制台抛出一条警告。

## 3 组件通信

组件关系可分为父子组件通信、兄弟组件通信、跨级组件通信。

### 3.1 自定义事件

当子组件需要向父组件传递数据时，要用到自定义事件。子组件使用`$emit()`来触发事件，父组件使用`$on()`来监听子组件的事件。`$emit()`方法的第一个参数是自定义事件的名称，后面的参数都是要传递的数据，可以不填或填写多个。例如：

~~~vue
<div id="app">
    <p>总数： {{ total }}</p>
    <my-component @increase="handleGetTotal" @reduce="handleGetTotal"></my-component>
</div>
<script>
	Vue.component('my-component', {
        template: '',
        data: function() { return { counter: 0 } },
        methods: {
            handleIncrease: function() {
                this.counter++;
                this.$emit('increase', this.counter);
            },
            handleReduce: function() {
                this.counter--;
                this.$emit('reduce', this.counter);
            }
        }
    });
    
    var app = new Vue({
        data: { total: 0 },
        methods: {
            handleGetTotal: function (total) {
                this.total = total;
            }
        }
    })
</script>
~~~

### 3.2 使用`v-model`

~~~vue
<div id="app">
    <p>总数：{{ total }}</p>
    <my-component v-model="total"></my-component>
</div>
<script>
	Vue.component('my-component', {
        template: '<button @click="handleClick">+1</button>',
        data: function() {
            return { counter: 0 }
        },
        methods: {
            handleClick: function() {
                this.counter++;
                this.$emit('input', this.counter);
            }
        }
    })
</script>
~~~

这次子组件的`$emit()`的事件名是特殊的`input`，在使用组件的父级，并没有在`<my-component>`上使用`@input="handler"`，而是直接使用了`v-model`绑定的一个数据`total`，这可以称作是一个语法糖，代替了自定义事件。

`v-model`还可以用来创建自定义的表单输入组件，进行数据双向绑定，例如：

~~~vue
<div id="app">
    <p>总数：{{ total }}</p>
    <my-component v-model="total"></my-component>
    <button @click="handleReduce">-1</button>
</div>
<script>
	Vue.component('my-component', {
        props: ['value'],
        template: '<input :value="value" @input="updateValue">',
        methods: {
            updateValue: function(event) {
                this.$emit('input', event.target.value);
            }
        }
    });
    
    var app = new Vue({
        el: '#app',
        data: {
            total: 0
        },
        methods: {
            handleReduce: function() {
                this.total--;
            }
        }
    })
</script>
~~~

实现这样一个具有双向绑定的`v-model`组件要满足下面两个要求：

- 接收一个`value`属性；
- 在有新的`value`时触发`input`事件；

### 3.3 非父子组件通信

#### 3.3.1 中央事件总线`bus`

~~~vue
<div id="app">
    {{ message }}
    <component-a></component-a>
</div>
<script>
	var bus = new Vue();
    
    Vue.component('component-a', {
        template: '<button @click="handleEvent">传递事件</button>',
        methods: {
            handleEvent: function() {
                bus.$emit('on-message', '来自组件component-a的内容');
            }
        }
    });

    var app = new Vue({
        ...
        mounted: function() {
            var _this = this;
            bus.$on('on-message', function(msg) {
                _this.message = msg;
            })
        }
    })
</script>
~~~

#### 3.3.2 父链

在子组件中，可以使用`this.$parent`可以直接访问该组件的父实例或组件，父组件也可以通过`this.$children`访问它所有的子组件，而且可以递归向上或向下无限访问，直到根实例或最内层的组件。

**尽管`Vue`允许这样做，但在业务中，子组件应该尽可能地避免以来父组件的数据，更不应该主动去修改它的数据。因为这样使得父子组件紧耦合，只看父组件，很难理解父组件的状态，因为它可能被任意组件修改，理想情况下，只有组件自己能修改它的状态。父子组件最好还是通过`props`和`$emit`来通信。**

#### 3.3.3 子组件索引

当子组件过多时，使用`this.$children`来一一遍历我们需要的组件实例是很困难的，尤其是组件动态渲染时，它们的序列是不固定的。`Vue`提供了子组件索引的方法，用特殊的属性`ref`来为子组件指定一个索引名称。例如：

~~~vue
<div id="app">
    <button @click="handleRef">通过ref获得子组件实例</button>
    <component-a ref="comA"></component-a>
</div>
<script>
	var bus = new Vue();
    
    Vue.component('component-a', {
        template: '<div>子组件</div>',
        data: function() {
            return {
                message: '子组件内容'
            }
        }
    });

    var app = new Vue({
        ...
        methods: {
            handleRef: function() {
                var msg = this.$refs.comA.message;
            }
        }
    })
</script>
~~~

在父组件模板中，子组件标签上使用`ref`指定一个名称，并在父组件内通过`this.$refs`来访问指定名称的子组件。`$refs`只有在组件渲染完成后才填充，并且它是非响应式的。它仅仅作为一个直接访问子组件的应急方案，应当避免在模板或计算属性中使用`$refs`。

## 4 使用`slot`分发内容

​	当需要让组件组合使用，混合父组件的内容与子组件的模板时，就会用到`slot`，这个过程叫做内容分发。`prop`传递数据、`event`触发事件和`slot`内容分发就构成了`Vue`组件的3个`API`来源，再复杂的组件也是由这3个部分构成的。

### 4.1 作用域

父组件模板的内容是在父组件作用域内编译，子组件模板的内容是在子组件作用域内编译。比如父组件有以下模板：

~~~vue
<child-component>{{ message }}</child-component>
~~~

这里的`message`就是一个`slot`，但是它绑定的是父组件的数据，而不是组件`<child-component>`的数据。

因此，`slot`分发的内容，作用域是父组件上的。

### 4.2 `slot`用法

#### 4.2.1 单个`slot`

在子组件内使用特殊的`<slot>`元素就可以为这个子组件开启一个`slot`（插槽），在父组件模板内，插入在子组件标签内的所有内容将替代子组件的`<slot>`标签及它的内容。例如：

~~~vue
<div id="app">
    <child-component>
    	<p>分发的内容</p>
    	<p>更多分发的内容</p>
    </child-component>
</div>
<script>
	Vue.component('child-component', {
        template: '<div><slot><p>如果父组件没有插入内容，将作为默认显示</p></slot></div>'
    });
    
    var app = new Vue({
        el: '#app'
    });
</script>
~~~

渲染后的结果为：

~~~html
<div id="app">
    <div>
    	<p>分发的内容</p>
    	<p>更多分发的内容</p>
    </div>
</div>
~~~

**注意，子组件`<slot>`内的备用内容，它的作用域是子组件本身。**

#### 4.2.2 具名`slot`

给`<slot>`元素指定一个`name`可以分发多个内容，具名`slot`可以与单个`slot`共存。例如：

~~~vue
<div id="app">
    <child-component>
        <h2 slot="header">标题</h2>
    	<p>正文内容</p>
    	<p>更多的正文内容</p>
        <div slot="footer">底部信息</div>
    </child-component>
</div>
<script>
	Vue.component('child-component', {
        template: '\    
            <div class="container">\
                <div class="header">\
                    <slot name="header"></slot>\
                </div>\
                <div class="main">\
                    <slot></slot>\
                </div>\
                <div class="footer">\
                    <slot name="footer"></slot>\
                </div>\
            </div>'
    });
        
    var app = new Vue({
        el: '#app'
    });
</script>
~~~

子组件内声明了3个`<slot>`元素，其中在`<div class="main">`内的`<slot>`没有使用`name`特性，它将作为默认`slot`出现，父组件没有使用`slot`特性的元素与内容都将出现在这里。

### 4.3 作用域插槽

作用域插槽是一种特殊的`slot`，使用一个可以复用的模板替换已渲染元素。用法如下例：

~~~vue
<div id="app">
    <child-component>
        <template scope="props">
            <p>来自父组件的内容</p>
            <p>{{ props.msg }}</p>
        </template>
    </child-component>
</div>
<script>
	Vue.component('child-component', {
        template: '<div><slot msg="来自子组件的内容"></slot></div>'
    });
    
    var app = new Vue({
        el: '#app'
    });
</script>
~~~

子组件的模板中，在`<slot>`元素上有一个类似`props`传递数据给组件的写法`msg="xxx"`，将数据传到了插槽。父组件中使用了`<template>`元素，而且拥有一个`scope="props"`的特性，这里的`props`只是一个临时变量。`<template>`内可以通过临时变量`props`访问来自子组件插槽内的数据`msg`。

渲染的结果为：

~~~html
<div id="app">
    <div>
        <p>来自父组件的内容</p>
        <p>来自子组件的内容</p>
    </div>
</div>
~~~

### 4.4 访问`slot`

`Vue2.x`提供了用来访问被`slot`分发的内容的方法`$slots`。

~~~vue
<div id="app">
    <child-component>
        <h2 slot="header">标题</h2>
    	<p>正文内容</p>
    	<p>更多的正文内容</p>
        <div slot="footer">底部信息</div>
    </child-component>
</div>
<script>
	Vue.component('child-component', {
        template: '\    
            <div class="container">\
                <div class="header">\
                    <slot name="header"></slot>\
                </div>\
                <div class="main">\
                    <slot></slot>\
                </div>\
                <div class="footer">\
                    <slot name="footer"></slot>\
                </div>\
            </div>',
        mounted: function() {
        	var header = this.$slots.header;
        	var main = this.$slots.default;
        	var footer = this.$slots.footer;
        	console.log(footer);
        	console.log(footer[0].elm.innerHTML);
    	}
    });
        
    var app = new Vue({
        el: '#app'
    });
</script>
~~~

通过`$slots`可以访问某个具名`slot`，`this.$slots.default`包括了所有没有被包含在具名`slot`中的节点。

## 5 组件高级用法

## 6 其他