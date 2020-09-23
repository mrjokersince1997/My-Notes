# vue

---

## vue 概念

### 前端框架

前端开发进化过程： **原生JS 》jQuery 等类库 》Vue 等前端框架**

- jQuery 等类库提供了已封装好的 JS 方法集合，用户在前端开发中可以直接调用（可以使用多个）。
- Vue 等前端框架提供了完整的项目解决方案，用户在前端开发中必须按照特定框架规范进行开发（只能选择一个）。

目前主流的前端框架有 Vue 、 React 、 Angular 。

### vue 特征

Vue 主要有以下两大特征：

1. 响应式数据绑定：数据发生改变，视图自动更新（开发者不再关注 dom 操作，进一步提高开发效率）。
   
2. 可组合视图组件：视图按照功能切分成基本单元（易维护，易重用，易测试）。


### vue 使用

- **引入外部文件(CDN)**

```html
<!-- 开发环境版本，包含命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

<!-- 生产环境版本，优化文件大小和响应速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

- **命令行工具(CLI)**

vue-cli 是基于 Node.js 的 vue 快捷开发工具，使用前首先要下载并安装 Node.js 开发环境。

1. 安装脚手架

```bash
$ npm install @vue/cli -g          # 全局安装安装 vue-cli 工具
```

> `@vue/cli` 适用于 vue 2.X ，`vue-cli` 适用于旧版本。

2. 创建项目并使用

```bash
# 方式一
$ vue create project-name          # 直接创建项目
$ npm run dev                      # 开发环境启动项目（可配置）        
$ npm run build                    # 运行环境启动项目（可配置）     

# 方式二
$ vue ui                           # 开启图形化工具，用来创建和管理项目
```

---

## vue 对象

vue 对象是管理 vue 的基本单元，开发者可以在 JS 代码中创建 vue 对象。

在 vue 对象中，必须通过 `el` 指定 vue 对象作用域。

```vue
<script>
    var app = new Vue({
      el: '#app',
      ...
    });
</script>
```

### 数据显示

在 vue 对象中，通过 `data` 存储 vue 对象中的数据。

```html
<!-- 数据显示 -->
<p v-text="message"></p>              <!-- v-text -->
<p>Word is {{ message }}</p>          <!-- 插值表达式：可以对内容进行扩展 --> 
<p v-html="message"></p>              <!-- v-html: 可以直接插入 html 元素 -->  

<!-- vue 对象 -->
<script>
    var app = new Vue({
      el: '#app',
      // 数据
      data: {
        message: 'Hello Vue',         // 数据
        data: []                      // 数组数据
      },
    });
    app.message="GoodBye Vue.";       // vue 对象数据可以被 JS 代码更新
</script>
```

### 方法调用

- 在 vue 对象中，通过 `methods` 定义 vue 对象中的方法。
- 在 vue 对象中，通过 `computed` 定义计算属性，重复调用时会基于缓存直接返回之前的计算结果，提高效率。

```html
<!-- 方法调用 -->
<button @click="quit"></button>       
<button @click="showLog('Hello')"></button>        

<!-- vue 对象 -->
<script>
    var app = new Vue({
      el: '#app',
      data: {
        message: ''
      },
      // 方法
      methods: {
        quit () {
          this.router.go(-1)
        },
        showLog (message) {
          this.message = message
          console.log(message)
        }
      },
      // 计算属性
      computed: {
      calc (data) {
        return this.num + data
      }
    });
</script>
```


- 在 vue 对象中，通过 `created` 定义方法，会在创建 vue 对象时自动调用。在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。
- 在 vue 对象中，通过 `mounted` 定义方法，会在创建 vue 对象时自动调用。在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。

```html
<!-- 计算结果 -->    
<p>Word is {{ calc(50) }}</p>     

<!-- vue 对象 -->
<script>
  var app = new Vue({
    el: '#app',
    data: {
      num: ''
    },
    // 创建方法
    created () {
      this.num = 100
    },
    // 计算属性
    
  });
</script>
```


### 数据监听

在 vue 对象中，通过监听器 `watch` 可以在数据发生变化时触发指定的事件。


```html
<input type="text" v-model="name"></p>

<!-- vue 对象 -->
<script>
  var app = new Vue({
    el: '#app',
    data: {
      name: 'MrJoker',
      num: {
        a: 1,
        b: 2,
        c: 3
      }
    },
    // 监听器
    watch: {
      // 监听一
      name (newName, oldName) {
        console.log(oldName + '>>' + newName)
      },
      // 监听二
      num: {
        handler(newNum, oldNum) {
          console.log('num changed');
        },
        immediate: true,                      // 创建数据时也会立即执行
        deep: true                            // 深度监听下属所有数据
      },
      // 监听三
      'num.a': {      
        handler(newName, oldName) {
          console.log('obj.a changed');
        }
      }
    } 
  })
</script>
```

### 数据过滤

在 vue 对象中，通过过滤器 `filter` 可以对要显示的数据进行修饰。

```html
<!-- 使用过滤器 -->
<div>{{message | upper}}</div>                            <!-- 方式一 -->
<div v-bind:id="id | upper"></div>                        <!-- 方式二 -->
<div v-bind:id="id | upper2(2,'hello')"></div>            <!-- 使用过滤器并传递参数 -->

<!-- vue 对象 -->
<script>
  var app = new Vue({
    el: '#app',
    data: {
      name:'Vue',
      message: 'Hello Vue',
      data: []
    },
    // 过滤器
    filter:{
      upper: function(val){
        return val.charAt(0).toUpperCase() + val.slice(1)
      }
    }
  });
</script>

<!-- 全局过滤器 -->
<script>
  Vue.filter('upper2', function(val,arg1,arg2){
    console.log(arg2);
    return val.charAt(arg1).toUpperCase() + val.slice(arg1 + 1);
  })
</script>
```

---

## vue 基础

### 指令绑定

- vue 使用 `v-bind` 绑定属性，表示该属性内容从 vue-data 中加载。可以用 `:` 代替。
- vue 使用 `v-on` 绑定事件，表示该事件从 vue-methods 中加载。可以用 `@` 代替。

```html
<input type=button value="按钮" v-bind:title="message" v-on:click="show">

<!-- img 标签的 src 属性使用插值表达式绑定 -->
<img class="box" src="{{url}}" >

<!-- 判断是否使用 textColor 和 textSize 类 -->
<div class="box" :class="{'textColor':isColor, 'textSize':isSize}">
```

*JS 默认属性均为字符串，但 vue 绑定属性能自动识别数据为数值、布尔型、数组或对象。*


**可绑定事件**

`@click` 点击事件

**事件修饰符**

当父级元素和子级元素被同一个事件触发指令时，会先执行子级元素的指令，再执行父级元素的指令。

- `.prevent` 阻止事件默认行为（比如超链接点击跳转，表单点击提交）。
- `.stop`  阻止冒泡调用，不再执行父级的指令。
- `.capture` 捕获调用，先执行父级的指令再执行子级的指令。
- `.self` 该指令只有元素本身被点击才执行，不会被子级的指令冒泡调用。
- `.once` 事件只触发一次，被触发后该指令无效。

### 表单输入绑定

Vue 使用单向绑定机制：后台数据发生改变后，页面显示会自动同步；但如果页面中表单输入发生变化，后台数据不会发生更新。

vue 使用 `v-model` 实现双向绑定。运用于表单输入元素，输入发生变化后台数据也会实时更新。

```html
<input v-model="age" type="number">
```

**表单域修饰符**

- `number`  转化为数值
- `trim`  去掉首尾空格
- `lazy`  鼠标离开输入元素后才更新（验证用户名是否已被使用时常用）

### 条件渲染

对于 布尔数据

```html
<script>
  new Vue({
    el:'#app',
    data:{
      existA:false,
      existB:true,
      surface:true
    }
  });
</script>
```

```html
<p v-if="existA">你好，我是A</p>
<p v-else-if="existB">你好，我是B</p>  
<p v-else v-show="surface">不好意思，A和B都不在</p>
```

- v-if: boolean 数据判断是否绘制元素
- v-show: boolean 数据判断是否显示 / 隐藏元素

### 列表渲染

对于 数组数据

```html
<script>
  var vm = new Vue({
    el:'#app',
    data:{
      list:[1,2,3,4,5,6],
      user:{
        id:1,
        name:'王东浩'
      },
      userList:[
        {id:1, name:'zs1'},
        {id:2, name:'zs2'},
        {id:3, name:'zs3'}
      ]
    }
  });
</script>
```
- v-for: 迭代显示列表(List)元素

  - 普通数组：`<p v-for="(item,index) in list">索引值是{{i}}，内容为{{item}}</p>`
  - 对象键值对：`<p v-for="(val,key,index) in user">键是{{key}}，内容为{{val}}</p>`

数组 (item,index) 第一个属性为内容；第二个属性为索引。
键值 (val,key,index) 第一个属性为内容；第二个属性为键名；第三个属性为索引。

```html
<!--对象数组-->
<tr :key='user.id' v-for='user in userList'>
  <td>{{user.id}}</td>
  <td>{{user.name}}</td>
</tr>
```

为方便管理元素，一般需要为每项绑定一个唯一的 key 属性：
`<p v-for="item in user" :key="item.id">用户的名字为{{item.name}}</p>`

可以用于循环固定次数：`<p v-for="count in 10">这是第{{count}}次循环</p>`

### 数组数据更新操作(API)

- push
- pop

以上操作直接对原有数组进行修改，页面也会随数据变化实时更新。

- filter

以上操作会产生新的数组，返回值需要重新赋值去更新页面。

`Vue.set(vm.list,1,'new data')` 或者 `Vm.$set(vm.list,1,'new data')` 

响应式修改数组元素

---

## vue 组件

vue 前端框架的基本功能单元是组件，vue 对象本身也是一个组件（根组件）。

### 全局组件

`Vue.component` 用于声明全局组件（不推荐）。

在 vue 中， `template` 表示组件模板，即组件要展示的内容。**模板内只能含有一个根元素！**

```js
Vue.component("greet-bar",{  
  template:'
    <div>
      <p>大家好，我是{{name}}</p>
      <button value="改名" v-on:click="changeName"></button>
    </div>
  ',
  data:function(){
    return {name:"王东浩"}
  },
  methods:{
    changeName:function(){
      this.name="甘甜"
    }
  }
})
```

全局注册的组件可以直接用在任何的 Vue 根实例 (new Vue) 的模板中。


```html
<div id="app">  
  <greet-bar></greet-bar>
  <greet-bar></greet-bar>
</div>

<script>
  new Vue({
    el:"#app",
    data:{}
  });
</script>
```

> html 文件元素名和属性名不区分大小写，因此不可采用驼峰形式。但在 vue 组件中可以作为驼峰形式识别，全局组件命名为 GreetBar 也能被读取。

### 局部组件

为避免用户需要一次性加载过多组件，我们可以定义局部组件，只在指定的 vue 对象中使用。

```js
var greetA = {
  data:function(){
    return {name:"王东浩"}
  ,
  template:'<p>hello {{name}}</p>'
};

var greetB = {
  data:function(){
    return {name:"陈伯言"}
  ,
  template:'<p>hello {{name}}</p>'
};
```

在 vue 中声明要调用的组件，就可以在组件内完成调用。

```html
<div id="app">  
  <greet-a></greet-a>  
  <greet-b></greet-b>
</div>

<script>
  new Vue({
    el:"#app",
    data:{},
    components:{
      'GreetA': GreetA,
      'GreetB': GreetB
    },
    // components: { GreetA, GreetB },  
  });
</script>
```




### vue 单文件组件(.vue)

在实际项目开发中，我们往往为每一个组件创建一个单独的文件来定义。之间的相互调用统一交由 router 管理。

```vue
<template>
  模板内容 html
</template>

<script>
  业务逻辑 export
</script>

<style>
  组件样式 css
</style>
```

---

## 组件交互

### 父组件向子组件传值

在 vue 中， `props` 是单向数据流，用于父组件向子组件传值。

1. 在父组件中定义数据

```html
<div id="app">  
  <greet-bar :first-name='sname' last-name = '赵四'></greet-bar>
</div>

<script>
  new Vue({
    el:"#app",
    data:{sname:"尼古拉斯"}
  });
</script>
```

2. 子组件读取并显示

```js
Vue.component("greet-bar",{
  props::['first-name', 'last-name'],  //也可以使用驼峰式接收 firstName
  template:'
    <div>
      <p>大家好，我是{{first-name + "·" + last-name}}</p>
    </div>
  '
})
```


### 子组件向父组件传值

- **子组件定义事件**

子组件通过触发 `$emit` 事件向父组件传值。

```html
<!-- $emit 须设定事件标记和传递数值 -->
<button @click='$emit("son-data", 0.1)'>点击</button>
```

- **父组件监听事件**

父组件文件中放置的子组件，可以根据事件标记监听事件并调用指定的方法处理。

```html
<!-- $event 为传递数值 -->
<router-view @son-data='handle($event)'> </router-view>
<!-- 可不含，等同于 -->
<router-view @son-data='handle'> </router-view>
```

父组件通过调用的方法，保存或使用子组件传来的值。

```js
handle(data){
  this.sonData = data
}
```

### 非父子组件传值

必须创建一个 vue 对象作为事件中心居中协调，监听两个子组件事件并通过 props 传递给另一个子组件。


### 组件插槽

在组件的 template 中添加 `<slot>默认内容可选</slot>`

可以自动读取 `<greet-bar>内容</greet-bar>` 中的内容并展示。


---

## vue 前后端交互

传统的原生 JS 开发和 jQuery 都使用 ajax 实现前后端交互，存在以下两个问题：

1. 仍需要处理 dom 操作，操作复杂。
2. 交互为同步操作，可能导致一致性问题。

```js
$.ajax({
  url:'http://localhost:8080',
  success:function:(data){
    ret = data;
    console.log(ret);             // 打印更新后的数据
  }
})
console.log(ret);                 // 打印数据，由于同步操作可能数据尚未更新
```

### promise 对象

在 JavaScript 最新版本标准 ES6 中， 定义了 promise 对象获取异步操作的消息。

- resolve 函数： 将 promise 对象的状态标记为成功。
- reject 函数：将 promise 对象的状态标记为失败。

```js
function queryData(url){
  // 创建 promise 对象
  var p = new Promise(function(resolve, reject){
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function(){
      if(xhr.readyState != 4) return;
      if(xhr.readyState == 4 && xhr.status ==200){
        // 执行成功，执行 resolve
        resolve(xhr.responseText);
      }else{
        // 执行失败，执行 reject
        reject("服务器错误");
      }
    };
    xhr.open('get',url);
    xhr.send(null);
  });
  return p;
}
```

**发送请求并获取处理结果**

```js
queryData('http://localhost:8080').then(function(data){
  // 成功执行前者，返回数据为 data
  console.log(data);
},function(info){
  // 失败执行后者，返回数据为 info (可不含)
  console.log(info);
});
```

- `p.then` 获取异步正常执行结果
- `p.catch` 获取异常信息
- `p.finally` 无论正确与否都会触发

**请求嵌套**

```js
// 执行并通过 then 获取处理结果
queryData('http://localhost:8080').then(function(data){
  console.log(data);
  return queryData('http://localhost:8080/next1');
})
// 执行第二次调用的返回数据
.then(function(data){
  console.log(data);
});
```

单一的 Promise 链并不能发现 async/await 的优势，但是，如果需要处理由多个 Promise 组成的 then 链的时候，优势就能体现出来了（很有意思，Promise 通过 then 链来解决多层回调的问题，现在又用 async/await 来进一步优化它）。

**批量处理**

```js
var p1 = queryData('http://localhost:8080/data1');
var p2 = queryData('http://localhost:8080/data2');
var p3 = queryData('http://localhost:8080/data3');
...
Promise.all([p1,p2,p3]).then(
  //所有任务都执行完才能返回结果
);

Promise.race([p1,p2,p3]).then(
  //最先完成者就能返回结果
);
```

### axios 库

axios 是基于 promise 实现的 http 客户端。作为第三方库，比官方的 fetch 功能更为强大。

#### 引入 axios

1. 直接引入
2. 在 vue ui 图形化工具中引入

```html
<!--引入 axios -->
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>

<!--引入 qs , 一般用于处理提交数据-->
<script src="https://cdn.bootcss.com/qs/6.7.0/qs.min.js"></script>
```


#### 全局配置

一般在 main.js 文件中设定，可作用于全局。

```js
axios.defaults.timeout = 3000;                       // 超时时间
axios.defaults.baseURL = "http://localhost:8080"     // 默认地址
axios.defaults.headers['mytoken'] = 'asaffdf123'     // 请求头
```

#### 请求响应

1. **GET / DELETE 请求**：输入 URL 和 params 参数，参数附着在 URL 上。

```js
axios.get('/get',{
  params:{
    id:123
  }
})
.then(function(ret){
  console.log(ret.data.message)
}
```

2. **POST / PUT 请求**：输入 URL 和表单数据，数据以 json 形式传递。

```js
axios.post('/post',{
  uname:'tom',
  password:123456
})
.then(ret=>{
  console.log(ret.data.message)
}
```


> axios 中的 params与 data 传参的区别: 
>     params 传参，参数以 k=v&k=v 格式放置在 url 中传递。
>     data 传参，参数会在 form 表单中。

**对于返回响应结果 ret** 

- `ret.data` : 响应返回数据，可读取返回数据中某一具体属性。
- `ret.headers` : 响应头信息
- `ret.status` : 响应状态码
- `ret.statusText` : 响应状态信息

#### 同步请求

不管是 fetch 和 axios 都是异步发送请求，这是前端界面通用做法。

使用 async/await 可以将 axios 异步请求同步化，async 函数会等待 await 返回数据后再向下执行。

通常放在 try 语句中，如果超时未获得数据则直接进入异常处理。

```js
    async getHistoryData (data) {
      try {
        let res = await axios.get('/api/survey/list/', {
          params: data
        })
        this.tableData = res.data.result
      } catch (err) {
        console.log(err)
      }
    }
```


表单提交自带校验方法 `validate(callback)`{ 直接返回 Promise 对象}，默认 valid 为 true 通过。

```js
// 对于 ID 为 addFormRef 的表单
this.$refs.addFormRef.validate(async valid => {
        if (!valid) return
        const { data: res } = await this.$http.post('adddevice', this.addForm)
        console.log(res)
        if (res.code !== 200) return this.$message.error(res.message)
        this.$message.success(res.message)
        this.$router.go(-1)
      })
```


**拦截器**

对请求或者响应进行加工处理。

1. 对请求加工处理

```js
axios.intercepter.request.use(function(config){
  // 首个函数执行拦截修改功能
  config.headers.mytoken = 'nihao';
  return config;
},function(error){
  // 第二个函数 反馈错误信息
  console.log(error);
}
)
```

2. 对响应结果加工处理

```js
axios.intercepter.response.use(function(res){
  var data = res.data;
  return data;
},function(error){
  console.log(error);
}
)
```

---

## vue 路由

### 什么是路由

路由的作用：把用户远程请求映射到相应的网络资源。可采用以下两种方式：

- 后端路由：服务器根据用户请求 URL 返回 html 页面，浏览器直接显示。（频繁刷新界面）
- 前端路由：服务器根据用户请求 URL 返回 json 数据，浏览根据用户触发事件更新 html 页面。（无法记录历史访问）

现在主流开发使用基于前端路由的 SPA 技术：整个网站只有一个界面，使用 ajax 技术局部更新界面，同时支持浏览器界面的前进后退操作。

### vue router 插件

vue 深度集成了官方路由管理器 vue router。可选【使用用户操作历史或哈希存储历史访问】.

```html
<!--引入 vue router-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue-router.js"></script>
```

开发者在专用的路由 js 文件中定义路由规则。

```js
<script>
  const User = {...};
  const Register = {...};

  // 路由规则
  const router = new VueRouter({
    routes:[
      {path:'/',redirect:'/user'}, // 重定向
      {path:'/user',component:User},
      {path:'/register',component:Register}
    ]
  })

  new Vue({
    el:'#app',
    data:{},
    Router: router
  })
</script>
```

在 vue 组件中，点击 router-link 组件实现页面跳转，预留的 router-view 区域将显示指定组件。

```html
<div id="app">
  <!--路由链接-->
  <router-link to="/user">User</router-link>
  <router-link to="/register">Register</router-link>
  <!--路由占位符，显示位置-->
  <router-view></router-view>
</div>
```

### 嵌套路由

```js
<script>
  const Tab1 = {...};
  const Tab2 = {...};
  const Register = {
    template:'
      <div>
        <router-link to="/register/tab1">Tab1</router-link>
        <router-link to="/register/tab2">Tab2</router-link>
        <router-view />
      </div>
    '
  }

  const router = new VueRouter({
    routes:[
      {path:'/',redirect:'/user'}, // 重定向
      {path:'/user',component:User},
      {
        path:'/register',
        component:Register,
        children:[
          {path:'/register/tab1',component:Tab1},
          {path:'/register/tab2',component:Tab2}
        ]}
    ]
  })

  new Vue({
    el:'#app',
    data:{},
    Router: router
  })
</script>
```

### 动态路由

根据参数自动选择路由

```js
// 动态路径
var router = new VueRouter({
  routes:[
    {path:'/user/:id',component: User}
  ]
})

// 动态显示内容
const User = {
  template:'<div>User {{$route.params.id}}</div>'
}
```

但 $route 的方式传参高度耦合，一般使用 props 将组件和路由解耦。还可以对路由路径进行命名。

```js
var router = new VueRouter({
  routes:[
    {path:'/user/:id',
    name:'user',  // 路由命名
    component: User, 
    props:true}  // 动态路径
  ]
})

// 动态显示内容
const User = {
  props:['id'],
  template:'<div>User {{id}}</div>'
}
```

`<router-link :to="{name:'user',params:{id:3}}">User</router-link>`


### 路由语句执行

#### 查询路由信息

在 vue 组件中，可以通过 `$route` 查询当前路由的详细信息。在组件内，即 this.$route 。

对于路由 /list/type/11?favorite=yes 

```js
{
  path:'/list/type/:id',
  name:'user',  // 路由命名
  component: User, 
  props:true
}
```    
    
- `$route.path`  （字符串）返回绝对路径  $route.path='/list/type/11'
- `$route.params` （对象）动态路径键值对   $route.params.id == 11 
- `$route.query`  （对象）查询参数键值对  $route.query.favorite == 'yes' 
- `$route.name`   （对象）路径名，没有则为空。 $route.name == 'user'
- `$route.router`    路由规则所属的路由器（以及其所属的组件）。
- `$route.matched`    数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象。



#### 执行路由跳转

在 vue 组件中，可以通过调用全局路由对象 `$router` 查的方法实现页面跳转。

push 方法和 <router-link :to="..."> 等同，执行时跳转指定页面。

```js
this.$router.push('home')                                                /home
this.$router.push({ path: 'home' })                                      /home
this.$router.push({ path: 'home', query: { plan: '123' }})               /home?plan=123（附带查询参数）
this.$router.push({ name: 'user', params: { id: 123 }})                  /list/type/123（根据命名跳转可以附带动态路径）
```


go 方法根据历史记录，跳转上一个或下一个页面。

```js
this.$router.go(-1)                  返回之前的页面
```

replace 方法替换当前的页面，和 push 方法的不同在于不会历史记录（一般用于 404 页面）。

```js
this.$router.replace('/')
```


---


## vue 项目结构

vue 项目由上述两种方式自动创建，其项目结构如下：

- **node_module 文件夹**  / 依赖包目录

- **public 文件夹** /  静态资源，外部可直接访问

  - **index.html** / 输出界面
  - **favicon.ico** / 图标

- **src 文件夹** / 组件等资源，由静态资源加载

  - **asserts 文件夹** / css、img 文件
  - **components 文件夹** / vue 文件

- **plugins 文件夹** / 插件文件

- **router 文件夹** / 路由文件

- **App.vue** / 核心组件

- **main.js** / 入口文件

还有一些其他配置文件，比如项目配置文件 package.json。
用户可以创建 vue.config.js 对 vue 进行自定义配置，默认覆盖原配置。

## vue 常用插件


### 组件库

不用自己画组件，可以使网站快速成型。推荐直接在图形化工具内导入。

导入 element-ui 等桌面组件库，bootstrap 等移动端组件库。


### Element UI 组件库

官网：https://element.eleme.cn/#/zh-CN/component/installation


1. 安装依赖包 

```js
npm install element-ui -S
```

2. `main.js` 导入资源

```js
import ElementUI from 'element-ui'; 
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(ElementUI);
```



`$` 符用来绑定事件。

this.$refs.tree.getCheckedKeys());  

$refs代表一个引用：tree表示组件中某个元素（ref属性设为tree）,然后我们可以通过对象调用方法。

https://www.cnblogs.com/my466879168/p/12091439.html 局部修改css 样式

<style lang="scss">
@import '../../styles/custom-menu.scss';
  .menu-form .el-form-item__label {
      text-align: left!important;
      font-size: 20px!important;
      color: #000!important;
      font-weight: normal!important;
}
</style>








在属性前加冒号，表示此属性的值是变量或表达式，如果不加，就认为是字符串，所以抛错。!!!!!!!!

