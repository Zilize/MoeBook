### 基本指令

初始化一个网页时可能会出现带有双大括号的插入指令，这是因为网页加载需要时间，而最开始DOM还没被替换。为了解决这个问题可以在根容器中加上v-cloak，并添加样式：

```html
<style>
    [v-cloak] {
        display: none;
    }
</style>

<div id="app" v-cloak>
    <h1>Good morning, {{ name }}</h1>
</div>
```

在标签中加上v-once可以指定该元素或组件只渲染一次，即指定其为静态组件。

### 条件渲染指令

v-if，v-else-if，v-else可以根据其后跟着的表达式的真假值判断是否要渲染当前标签元素，注意v-else-if一定要紧跟v-if，v-else一定要紧跟v-else-if或v-if。注意JavaScript里面的三个等号。

```html
<div v-if="status === 1"><p>Hello</p></div>
```

Vue在渲染元素时会尽可能地复用元素，可以用key来指定元素从而决定是否要复用元素。详见[Vue手册](https://cn.vuejs.org/v2/guide/conditional.html)。

v-show和v-if的用法基本一致，只不过v-show只是改变元素的CSS属性display来确定是否显示该元素。使用v-show时就算没有显示元素，在网页检查元素工具中仍然能看到该元素。总而言之，使用v-show时，元素总是会被编译。

v-if的开销较大，而v-show的开销较小，后者更适用于频繁切换的场景。

**注意**：v-show不能在template中使用。

### 列表渲染指令

v-for用于对数组或对象数据进行迭代处理，如下例中books是Vue实例中的数组数据，可以在HTML中使用v-for：

```html
<ul>
  <li v-for="book in books">{{ book.name }}</li>
</ul>
```

book是当前数组元素的别名，in是分隔符，也可以用of来代替in作为分隔符。

支持一个可选参数作为当前项的索引：

```html
<ul>
  <li v-for="(book, index) in books">{{ index }} - {{ book.name }}</li>
</ul>
```

v-for也可以用于template，对一组元素进行渲染。v-for可以用于对象，此时有两个可选参数，分别是键名和索引：

```html
<ul>
  <li v-for="(value, key, index) in user">
    {{ index }} - {{ key }}: {{ value }}
  </li>
</ul>
```

[数组更新]([https://cn.vuejs.org/v2/guide/list.html#%E6%95%B0%E7%BB%84%E6%9B%B4%E6%96%B0%E6%A3%80%E6%B5%8B](https://cn.vuejs.org/v2/guide/list.html#数组更新检测))等内容可参考链接中官方手册。

**注意**：v-for不要与v-if一同使用

### 事件监听

v-on指令用于事件监听。常用的事件句柄有：

|    属性     |           描述           |
| :---------: | :----------------------: |
|   onclick   |         单击事件         |
| ondblclick  |         双击事件         |
|  onkeydown  |    某个键盘的键被按下    |
| onkeypress  | 某个键盘的键被按下或按住 |
|   onkeyup   |    某个键盘的键被松开    |
| onmousedown |     某个鼠标键被按下     |
| onmousemove |         鼠标移动         |
| onmouseout  |     鼠标从某元素移开     |
| onmouseover |   鼠标被移到某元素之上   |
|  onsubmit   |      提交按钮被点击      |

可以使用修饰符：

- .stop  阻止事件
- .prevent  阻止默认事件
- .self  事件发生在该元素本身时触发
- .once  只触发一次

监听键盘事件时可以使用按键修饰符：

```html
<input @keyup.13="submit">
```

按键修饰符提供了一些快捷名称：

- .enter
- .tab
- .delete
- .esc
- .space
- .up
- .down
- .left
- .right

一下修饰符可以组合使用：

- .ctrl
- .alt
- .shift
- .meta （Mac中时Command， Windows下是窗口键）

```html
<div @click.ctrl="handle">Handle</div>
```

### 表单输入绑定

v-model指令可以用来实现双向的数据绑定。参见[表单输入绑定](https://cn.vuejs.org/v2/guide/forms.html)。

