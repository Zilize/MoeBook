### JavaScript中的箭头函数

ES6中的箭头函数实际上就是一种简洁的定义函数的方式。例如下面两种函数定义方法时等价的。箭头前面的括号填写参数，当参数只有一个时，可以省略括号。

```js
const sum = function (num1, num2) {
  return num1 + num2
}
```

```js
const sum = (num1, num2) => {
  return num1 + num2
}
```

对于函数体只有一行的函数，还可以简写成如下形式：

```js
const sum = (num1, num2) => num1 + num2
```

一般什么时候会用箭头函数呢？在函数作为一个参数传入另一个函数时常常会用到箭头函数。

### Vue项目的整体目录结构

在使用脚手架创建项目之后，主要有这样一些结构：

\>build\	存放webpack配置信息的目录

\>config\	存放其他配置信息的目录

\>node_modules\	存放库文件的目录

\>src\	资源目录，我们代码的编辑主要就在这里面进行

\>static\	静态资源，可以放图片之类的资源，build之后会整体打包到dist目录中

\>index.html	网站主页

在src目录下可以安排这样的目录结构：

\>assets\	存放资源的目录，可以在里面创建css文件夹和img文件夹分别存放样式和图片

\>components\	存放组件（.vue），可以在这里存放公共组件

\>views\	存放组件（.vue），在这里存放主要视图比如主页、关于等

\>router\	存放路由相关配置

\>App.vue	根组件

\>main.js	根实例

### 路由与前端发展历程

路由实际上就是辅助进行信息转发的映射表。在早期的Web开发中，常用后端渲染和后端路由技术，后端渲染就是网页在服务器中已经渲染好，最终只有HTML和CSS发送到客户端浏览器，而后端路由就是后端来处理URL和网页文件的映射关系。后端路由有非常明显的缺点：前后端耦合严重，很多前端工作必须由后端人员来做，维护困难。

为了解决这个问题，Web开发进入了前后端分离阶段：后端只负责提供数据，不负责任何界面的内容。可以将整个Web系统分为四个模块：浏览器客户端、静态资源服务器、提供API接口的服务器、数据库。访问网页的整个过程如下：浏览器先通过URL访问静态资源服务器，这时候会获得HTML+CSS+JS，前两个直接渲染，最后一个执行的时候会遇见API的URL，从而进一步去提供API接口的服务器获得数据（Ajax请求），数据返回客户端后继续进行渲染。这个阶段最大的特点就是前端渲染，前后端划分清晰，后端专注数据处理，前端专注交互和可视化。这种情况下API接口能提供给不同客户端比如网页端、移动端，因为他们都用同一套接口，后端无需关注过多。

第三个阶段时单页面富应用阶段（SPA），就是在上一个阶段上添加了前端路由功能。这时候整个网站只有一个HTML页面，输入网址时会将网站的所有资源都下载下来，通过前端路由配置的映射关系来处理页面关系。一旦输入一个子URL，浏览器就会从资源中抽取对应的资源显示出来，而不用重新向服务器进行请求。在Vue中，这些页面就是一个个组件。

### 如何更改URL而使页面不进行刷新

页面的刷新就是浏览器重新向服务器请求页面资源，有两种方法可以更改URL而使页面不进行刷新。

一种是hash函数的方式：打开审查元素工具，在console中输入 location.hash = 'test' 即可更改页面的URL，其中的test可以是任何值。从Network可见此时没有请求新的资源。

另一种是HTML5下的history模式：在console中输入 history.pushState({}, '', 'test') 即可更改页面URL而不进行刷新，pushState实际上是利用栈结构进行处理，能记录多个URL。也可以输入history.replaceState({}, '', 'test') ，但这种方式并不是利用栈结构。可以用history.back()来回退，history.forward()来前进，这两个接口其实就是浏览器的后退前进功能。pushState处理后可以进行后退前进，但是用replaceState后就不行了。

### vue-router的安装和使用

```shell
npm install vue-router --save
```

第一步：导入路由对象，调用Vue.use(VueRouter)

第二步：创建路由实例，并且传入路由映射配置

第三步：在Vue实例中挂载创建的路由实例

在创建Vue项目的时候选择router系统就会帮忙进行创建路由。在src下创建router文件夹，在文件夹下创建index.js文件，用来存放所有的路由配置。在index.js中：

```js
import VueRouter from 'vue-router'
import Vue from 'vue'

//1.通过Vue.use()来安装插件
Vue.use(VueRouter)

//2.创建VueRouter对象
const routes = [
  
]

const router = new VueRouter({
  //配置路由和组件之间的映射关系
  routes  //ES6语法：为了代码整洁直接传入所需的数组，数组在外面定义
})

//3.将router对象挂载到Vue实例中
//需要在本文件中导出，在main.js中通过import导入，然后在Vue实例中通过router: router来调用
//回忆ES6语法，router: router又可以直接写成router
export default router

```

如何使用vue-router？

第一步：创建路由组件

第二步：配置路由映射：组件和路径映射关系

第三步：使用路由：通过<router-link>和<router-view>

首先我们先创建路由组件，比如可以创建Home.vue：

```html
<template>
  <div>
    <h1>I'm Home!</h1>
  </div>
</template>

<script>
  export default{
    name: "Home"
  }
</script>

<style>
</style>
```

类似地还可以创建About.vue。之后可以在router/index.js中配置路由映射，需导入组件并更改数组配置：

```js
import Home from '../components/Home'
import About from '../components/About'

const routes = [	//数组中一个对象对应一个组件
  {
    path: '/home',
    component: Home
  },
  {
    path: '/about',
    component: About
  }
]
```

但现在这两个组件中的内容还不能显示，因为没有被调用，所以需要在App.vue组件中进行调用。为什么在App.vue中的组件能够显示呢？因为在main.js的根实例中有render: h => h(App)，可见App组件会被渲染。在App.vue的template中：

```html
<div id="app">
  <router-link to="/home">Home</router-link>
  <router-link to="/about">About</router-link>
  <router-view></router-view>
</div>
```

router-link和router-view是vue-router全局注册的组件，在任何地方都可以用。在上面的代码中如果只有router-link是不够的，他们只能更改URL，但是还没有指定组件的内容显示在什么地方。这时候需要用router-view来指定组件显示的位置。其占位功能类似于插槽。

### 路由细节处理

在上面得到的结果中访问网站时并不会出现首页视图内容，需要点击首页才行，这是因为还没有配置默认路由。可以在routes数组中配置默认路由：

```js
{
	path: '/',
	redirect: '/home'
}
```

现在还有一个问题，由于现在的路由用的是hash模式所以会出现井号，需要在index.js文件中创建VueRouter实例时指定使用history模式：

```js
const router = VueRouter({
  routes,
  mode: 'history'
})
```

router-link会默认被渲染成a标签，也可以对其设置tag属性渲染成button或者li等：

```html
<router-link to='/home' tag='button'>Home</router-link>
```

router-link默认用pushState来更改URL，因此浏览器中可以后退前进。如果希望取消后退前进功能，可使用replace模式：

```html
<router-link to='/home' tag='button' replace>Home</router-link>
```

还可以在router-link中添加active-class属性指定当前显示组件对应的导航项的样式。也可以在VueRouter实例中指定linkActiveClass属性来更改。

### 代码实现路由跳转

事实上也可以不用router-link标签来实现路由跳转，这样可以更灵活地自定义我们的导航。比如使用button进行跳转：

```html
<button @click="homeClick">Home</button>
<button @click="aboutClick">About</button>
```

再实现监听调用的这两个函数（在export default中的methods中实现）：

```js
homeClick () {
  this.$router.push('/home')
}
```

这时候使用pushState来跳转的，可以在上面讲push改为replace即可实现取消后退前进。