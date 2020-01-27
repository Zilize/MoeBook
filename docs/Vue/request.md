### axios的基本使用

```shell
npm install axios --save
```

可以用下面的代码进行一个GET请求（默认是GET请求）：

```js
axios({
	url: 'https://jsonplaceholder.typicode.com/posts'
}).then(res => {
	console.log(res);
})
```

还可以在配置中指定请求的方式，如果GET有参数还可以指定参数（就是URL的问号后面部分）：

```js
axios({
	url: 'https://jsonplaceholder.typicode.com/posts',
  method: 'get',
  params: {
    type: 'pop',
    page: 1
  }
}).then(res => {
	console.log(res);
})
```

可以挂在到相应组件中生命周期的created钩子下面，可以附带catch捕捉错误：

```js
export default {
  name: 'app',
  created () {
    axios({
      url: 'https://jsonplaceholder.typicode.com/posts'
    }).then(res => {
      console.log(res);
    }).catch(err => {
      console.log(err);
    })
  }
}
```

### 发送并发请求

```js
axios.all([axios({
  url: 'https://jsonplaceholder.typicode.com/posts'
}), axios({
  url: 'https://jsonplaceholder.typicode.com/posts'
})]).then(res => {
  console.log(res);
})
```

当两个请求都拿到之后才会执行then。注意axios.all返回的是一个数组，可以用spread将其分开来，比如：

```js
axios.all([axios({
  url: 'https://jsonplaceholder.typicode.com/posts'
}), axios({
  url: 'https://jsonplaceholder.typicode.com/posts'
})]).then(axios.spread((res1, res2) => {
  console.log(res1);
  console.log(res2);
}))
```

当然也可以用res[0]和res[1]来取得结果。

补充ES6的语法：对象和数组的解构。

```js
const obj = {
  name: 'John',
  age: 20
}
const {name, age} = obj;

const names = ['John', 'Bob', 'Tom']
const [name1, name2, name3] = names;
```

### axios的全局配置与实例创建

有些公共的参数还有baseURL等可以统一进行设置，可以放在main.js中。比如：

```js
axios.default.baseURL = 'https://jsonplaceholder.typicode.com'
axios.default.timeout = 5000	//毫秒为单位
```

一旦设置了baseURL，那么在axios中可以使用'/posts'作为URL。

强调两点，在进行GET请求时，参数放入params对象中，而进行POST请求时，请求体放入data对象中。比如：

```js
params: {
  id = 2
}
data: {
  key = 'aa'
}
```

在实际使用中一般不会用全局配置，因为那样的话就只能到同一个服务器中请求数据。可以创建axios实例，在实例中进行局部配置，并调用axios实例发送请求。

```js
const instance1 = axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com',
  timeout: 5000
})

instance1({
  url: '/posts'
}).then(res => {
  console.log(res);
})
```

### axios的封装

一定不要在组件代码中直接引入axios并调用！如果自己的项目代码中很多组件都用了某个第三方组件，假设有一天该组件不再维护，则项目代码需要在很多地方改动，这是一个严重的后果。所以在任何时候，使用第三方组件都应该对其进行进一步的封装。在Vue项目的src文件夹下，可以创建network文件夹放置网络请求相关配置文件，可以创建request.js文件来放置我们对axios的封装。

由于封装的结果不止一个，类似于创建axios实例，我们不用export default，而使用export function。例如：

```js
export function 
```

