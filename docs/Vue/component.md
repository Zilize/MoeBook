### 组件示例

组件是可复用的Vue实例，可以通过component来定义：

```js
Vue.component('button-counter', {
    data: function() {
        return {
            count: 0
        }
    },
    template: "<button @click='count++'>You clicked me {{ count }} times.</button>"
})
```

定义之后可以在任何的new Vue创建的根实例对应的根容器中，把button-counter当作自定义组件来使用。由于组件也是Vue实例，因此也可以接受data、computed、methods和生命周期钩子等选项，el除外（根实例特有）。

要注意data不像在Vue的根实例中，它必须定义为函数。因为JS没有块级作用域，必须通过函数返回才能建立自己的作用域，从而在进行组件的复用时不会出现数据相互影响的情况。

### 组件注册

通过Vue.component可以进行全局注册，第一个参数就是组件的名称。全局注册后组件可以在任何新的Vue根实例中，也可以在其他的子组件中使用。但是全局注册会导致性能的降低，所以对于一些不常用的组件常使用局部注册。

```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }

new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

**局部注册的组件在其子组件中不可用**。这时候ComponentA和ComponentB都能在根实例中使用，但是ComponentA不能在ComponentB中使用，必须进行注册。

```js
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}
```

### 模块系统

在使用webpack时（使用模块系统风格），在一个组件中对另一个组件进行注册的代码像这样：

```js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  },
  // ...
}
```

注意在新的ES版本中在对象中放置变量名 ComponentA 实际上是 ComponentA: ComponentA 的缩写。这意味着ComponentA同时是**用在模板中的自定义元素的名称**和**包含了这个组件选项的变量名**。

在用Vue脚手架搭建工程时会在 src 目录下有 components 目录，用于存放各个组件，文件后缀为vue。于是可以在局部注册之前导入相应的组件：

```js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```

在JavaScript中export用于导出模块。export和export default的区别在于：前者可以在文件中到处多个模块，而在引入模块时必须使用原有的名字；后者在文件中只能导出一个模块，该模块可以是匿名模块，引入时必须为其指定名称。

在Vue脚手架中由于添加了相关的配置，在导入模块时可以用"@"来代替"src"，各个组件文件的后缀".vue"、 ".js"、 ".json"等可以不用加。[参考这里](https://www.cnblogs.com/win-and-coffee/p/10186649.html)。在Vue脚手架中，单个".vue"文件就代表一个组件，组件可以被分离成三个部分，分别代表HTML模板（也就是template），处理逻辑（写在script中），样式（写在style中）。例如：

```html
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <Ping/>
  </div>
</template>

<script>
import Ping from './components/Ping'

export default {
  name: 'App',
  components: {
    Ping
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

这个文件名为App.vue，此时Ping就是App的子组件，二者的关联表现在App.vue用import引入Ping组件，在template中又调用了Ping组件。

### 通过Prop向子组件传递数据

在上面的例子中，设想这样的情景：在父组件App的template中多次调用了子组件Ping，而每个Ping中都要展示相同的数据，或者同一个数据集合（比如数组）的不同部分。为了处理这种情况，可以将子组件共有的数据存放在父组件中，通过某种手段将数据从父组件传递到子组件。在Vue中这种手段就是Prop传值。**注意**：父组件可以是根实例。

比如注册了这样一个文章组件用于显示每篇文章的标题：

```js
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

这里 template 中的 title 和 props 中的 title 对应。而文章数据列表存放在Vue根实例中：

```js
new Vue({
  el: '#blog-post-demo',
  data: {
    posts: [
      { id: 1, title: 'My journey with Vue' },
      { id: 2, title: 'Blogging with Vue' },
      { id: 3, title: 'Why Vue is so fun' }
    ]
  }
})
```

因此想要在根容器（根实例对应的HTML模板）中调用blog-post，必须给它传递数据。那怎么样来传递数据呢？当然是在blog-post标签的尖括号内填入类似于属性的键值对实现传值。例如上面两段代码对应的template是：

```html
<blog-post
  v-for="post in posts"
  v-bind:title="post.title"
></blog-post>
```

这里的posts对应的是根实例中data对象里面的数组posts，就是父组件（根实例）的数据；title则对应blog-post组件中props列表内的变量名；post.title对应根实例中相应的数据。**注意**这里一定要用"v-bind:"或":"，因为其实质仍是动态绑定属性。一定要十分清楚变量之间的对应关系。可以到[这里](https://jsfiddle.net/chrisvfritz/sbLgr0ad)试一下，不过不能用"key"，似乎会发生关键字冲突。

### 进一步了解Prop

props一般写成数组形式。

Prop既可以用来传递静态数据，也可以用来传递动态数据。不使用v-bind时传递的总是静态的字符串；如果要传递整型、数组、布尔值、对象等静态数据，则必须使用v-bind，不然会被认为是字符串；传递动态数据一定要使用v-bind。

[参考这里](https://cn.vuejs.org/v2/guide/components-props.html)。

当父组件数据data中的数组、对象通过v-bind传递给子组件的时候传递的实际上是引用，而数字、字符串、布尔值传递的是值。对于传递引用的情况来说，如果在一个子组件中改变了传入的Prop，那么在另一个子组件中该Prop也会发生改变，这不一定是我们想要的效果。

为了解决这个问题，实现从父组件到子组件数据的单向流动，也就是实现**单行向下绑定**（父级 prop 的更新会向下流动到子组件中，但是反过来则不行），可以针对两种不同的应用场景，使用两种不同的方法。

第一种场景是Prop传来一个初始值，而子组件希望把它作为本地的数据自己使用。这时候可以将Prop进来的数据在data中用函数处理一下，得到一个本地的值，比如下面的例子把传进的initCounter转变成了本地的Counter：

```js
props: ['initialCounter'],
data: function () {
  return {
    counter: this.initialCounter
  }
}
```

第二种场景是Prop传来一个初始值，本身必然就需要被处理。这时候Vue官方推荐使用计算属性：

```js
props: ['width'],
template: '<div :style="style">Content</div>',
computed: {
  style: function () {
    return {
      width: this.width + 'px'
    }
  }
}
```

这里其实就是将Prop传入的数字width转为带px后缀的字符串，同样也是需要改变Prop（改变Prop传入的数据）。

这两种方法本质上都是利用JavaScript中的函数来形成局部的作用域，从而避免在子组件中改变数据引发父组件数据改变的后果。目前也不太清楚为什么这里要分两种情况来处理。

[Prop-验证]([https://cn.vuejs.org/v2/guide/components-props.html#Prop-%E9%AA%8C%E8%AF%81](https://cn.vuejs.org/v2/guide/components-props.html#Prop-验证))和[非Prop的特性]([https://cn.vuejs.org/v2/guide/components-props.html#%E9%9D%9E-Prop-%E7%9A%84%E7%89%B9%E6%80%A7](https://cn.vuejs.org/v2/guide/components-props.html#非-Prop-的特性))可参考官方指南。

### 单个根元素

template中的必须只有单个根元素，可以用div将它们括起来。如果其中的内容比较多，可以使用模板字符串或折行转义字符避免报错。可以参考[这里]([https://cn.vuejs.org/v2/guide/components.html#%E5%8D%95%E4%B8%AA%E6%A0%B9%E5%85%83%E7%B4%A0](https://cn.vuejs.org/v2/guide/components.html#单个根元素))。

### 监听子组件事件

考虑这样一种场景：子组件是按钮组件，需要通过子组件按钮来改变父组件data中变量的值并通过{{ }}显示出来。在这种场景下，需要将信息从子组件传递到父组件，这种信息是事件信息。在Vue中可以通过自定义事件来实现。

例如下面的代码中，子组件是两个按钮，分别对应数字的增减，总数存在父组件中。在子组件模板中监听点击事件，触发两个不同的函数handleIncrease和handleReduce，这两个函数能够改变子组件作用域内的counter，希望在调用函数的时候能通过自定义事件将该counter传递给父组件的total。可以用$emit来注册自定义事件increase和reduce，并将子组件内的this.counter作为参数传出去。一旦点击+1的按钮就会触发handleIncrease函数，从而触发父组件中监听的increase事件，父组件同时也收到了传入的参数（子组件的counter），这时候触发父组件的函数handleGetTotal，子组件的counter作为名为total的参数传入，最终改变了父组件中total的值。

```html
<div id="app">
    <p>TotalNumber: {{ total }}</p>
    <my-component
        @increase="handleGetTotal"
        @reduce="handleGetTotal"></my-component>
</div>
<script src="https://unpkg.com/vue/dist/vue.min.js"></script>
<script>
    Vue.component('my-component', {
        template: '\
        <div>\
            <button @click="handleIncrease">+1</button>\
            <button @click="handleReduce">-1</button>\
        </div>',
        data: function () {
            return {
                counter: 0
            }
        },
        methods: {
            handleIncrease: function () {
                this.counter++;
                this.$emit('increase', this.counter);
            },
            handleReduce: function () {
                this.counter--;
                this.$emit('reduce', this.counter);
            }
        }
    });

    var app = new Vue({
        el: '#app',
        data: {
            total: 0
        },
        methods: {
            handleGetTotal: function (total) {
                this.total = total;
            }
        }
    })
</script>
```

### 插槽与内容分发

定义这样一个子组件，在子组件中插入了slot插槽。

```js
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

在调用子组件时，加入自定义的内容：

```html
<alert-box>
  Something bad happened.
</alert-box>
```

最终渲染结果如下：

```html
<div class="demo-alert-box">
  <strong>Error!</strong>
  Something bad happened.
</div>
```

以上演示的是默认插槽。如果有多个插槽，需要用到具名插槽，在slot中用name来指定。在模板中：

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

在调用时使用v-slot来指定插槽名称：

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

为指定名称的全部放入默认的没有名称的插槽中。如果没有默认插槽，那么传入的未指定插槽名称的内容会被丢弃。最后的渲染结果如下：

```html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

**注意**：v-slot只能放在template中，仅有一个例外，参见[这里]([https://cn.vuejs.org/v2/guide/components-slots.html#%E7%8B%AC%E5%8D%A0%E9%BB%98%E8%AE%A4%E6%8F%92%E6%A7%BD%E7%9A%84%E7%BC%A9%E5%86%99%E8%AF%AD%E6%B3%95](https://cn.vuejs.org/v2/guide/components-slots.html#独占默认插槽的缩写语法))。