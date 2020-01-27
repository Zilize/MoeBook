### Hello World

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue Demo</title>
</head>
<body>
    <div id="app">
        <input type="text" v-model="name" placeholder="Your name">
        <h1>Good morning, {{ name }}</h1>
    </div>
    <script src="https://unpkg.com/vue/dist/vue.min.js"></script>
    <script>
        var app = new Vue({
            el: '#app',
            data: {
                name: '',
            }
        })
    </script>
</body>
</html>
```

该程序实现了双向数据绑定：Vue实例数据中的name变量将依赖于input标签改变（由v-model指定），而花括号中的name显示Vue实例数据中的name变量。因此在渲染得到的网页输入框中输入文本时，h1标签对应的标题文本会自动改变。

id为app的元素为Vue实例app的根容器，可以在Vue实例中通过以下语句指定根容器：

```javascript
el: document.getElementById('app')  //或者是'#app'
```

Vue实例中的data也可以通过传入对象myData来实现，这时候myData.name和app.name默认建立了绑定关系。

### 生命周期

Vue实例从创建到销毁会经历一系列的过程，为了方便在合适的时机执行一些逻辑，可以使用生命周期钩子。

- created  实例创建完后调用，但el尚未挂载
- mounted  el挂载到实例上后调用
- beforeDestroy  实例销毁之前调用

```javascript
var app = new Vue({
    el: '#app',
    data: {
        name: '',
    },
    created: function() {
        console.log(this.name);
    },
    mounted: function() {
        console.log(this.$el);
    }
})
```

$el直接访问id为app的元素，因此会在控制台上输出一段HTML代码。

### 插值与表达式

双大括号{{ }}可以在HTML中显示Vue实例中的数据，括号内可以插入单个表达式，但是不支持语句或流控制，还可以使用过滤器进行文本的处理。

如果确实想要在HTML中输出{{ }}，则可以使用v-pre跳过编译：

```html
<span v-pre> {{ Hello }}</span>
```

如果想要通过插值在DOM中插入一段HTML代码可以使用v-html：

```html
<span v-html="link"></span>
```

```javascript
data: {
    link: "<a href='https://baidu.com/''>Baidu</a>"
}
```

还可以在大括号中调用Vue实例methods中的方法，这时候需要加括号进行调用。

### v-bind 与 v-on

v-bind用于动态更新HTML元素上的属性：

```html
<a v-bind:href="url">Link</a>
```

url是Vue实例中的数据，当url改变时，该HTML标签就会随之改变。

v-on用于绑定事件监听器：

```html
<button v-on:click="handleClick">ClickMe!</button>
```

点击后就会调用handleClick方法。除了click还有dblclick、keyup、mousemove等监听器。

注意双引号内调用方法时不需要加括号。引号内除了调用方法还可以使用内联语句比如赋值语句等。

为了简化代码的书写，可以使用单个冒号" : "来代替"v-bind: "，用"@"来代替"v-on: "。

### 特别注意

1. JavaScript变量的作用域带来的问题：JS没有块作用域，有方法作用域，因此在嵌套调用方法时，在第二层的方法外面可以使用如下语句避免因作用域变动导致this所指的对象发生变化：

```javascript
var _this = this;
```

2. 什么时候需要括号？什么时候不需要括号？在插值的大括号中：方法需要括号，计算属性和变量不需要括号。在标签属性中：方法和变量都不需要括号。两种情况都不需要分号。

### 计算属性与方法

计算属性和方法的定义方式一模一样，只不过计算属性放在computed对象中，而方法放在methods对象中。此外计算属性不能传递参数。既然方法的功能更灵活，那为什么还需要计算属性呢？

原因在于计算属性是依赖缓存的。对于methods来说，任何变量发生变化，methods中所有的方法都会被重新执行；对于computed来说，只有某个计算属性依赖的变量发生变化，它才会被重新计算。因此对于处理大量数据的情况来说，使用计算属性能优化性能。

### 动态绑定样式

回想v-bind可以动态绑定属性，而HTML元素的CSS类和样式也是属性，因此就可以利用v-bind来动态绑定元素的样式。

```html
<div id="app">
    <div class="static" :class="{ 'active': isActive, 'error': isError }"></div>
</div>
<script>
    var app = new Vue({
        el: "#app",
        data: {
            isActive: ture,
            isError: false
        }
    })
</script>
```

内层div元素始终属于static类，其是否属于active和error类视isActive和isError变量而定。这里用了v-bind的简化写法，就是在class属性名前面加一个冒号。这里用对象来给class赋值，称为“对象语法”，另外还有“数组语法”，此处略去。上面的div标签渲染之后的结果为：

```html
<div class="static active"></div>
```

当class的表达式过长或者逻辑复杂时可以绑定一个计算属性返回一个对象，也可以放在data中。

使用v-bind:style还可以绑定内联样式，同样也可以放在data和computed中。绑定内联样式同样也有数组语法。

```html
<div id="app">
  <div :style="{ 'color': color, 'fontSize': fontSize + 'px' }">TEXT</div>
</div>
<script>
  var app = new Vue({
    el: '#app',
    data: {
      color: 'red',
      fontSize: 14
    }
  })
</script>
```

