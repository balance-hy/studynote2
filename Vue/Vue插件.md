## Vite

### [在配置中使用环境变量](https://cn.vitejs.dev/config/#using-environment-variables-in-config)

环境变量通常可以从 `process.env` 获得。

注意 Vite 默认是不加载 `.env` 文件的，因为这些文件需要在执行完 Vite 配置后才能确定加载哪一个，举个例子，`root` 和 `envDir` 选项会影响加载行为。不过当你的确需要时，**你可以使用 Vite 导出的 `loadEnv` 函数来加载指定的 `.env` 文件。**

```js
import { defineConfig, loadEnv } from 'vite'

export default defineConfig(({ command, mode }) => {
  // 根据当前工作目录中的 `mode` 加载 .env 文件
  // 设置第三个参数为 '' 来加载所有环境变量，而不管是否有 `VITE_` 前缀。
  const env = loadEnv(mode, process.cwd(), '')
  return {
    // vite 配置
    define: {
      __APP_ENV__: JSON.stringify(env.APP_ENV),
    },
  }
})
```

## pinia

集中式管理状态容器，用于任意组件间通信，其实就是给定了一个全局的仓库存储状态，组件访问这些状态即可

核心概念：state、action、getters

### 快速开始

```shell
npm install pinia
```



在src目录下新建store目录，目录中新建index.js（大仓库），和modules目录，该目录存放小仓库

index.js

```js
//创建大仓库
import { createPinia } from 'pinia'
let store=createPinia();
//对外暴露，安装仓库
export default store;
```

在main.js中使用它

```js
import { createApp } from 'vue'
import App from './App.vue';
import store from "@/store";

const app=createApp(App)
app.use(store)
app.mount('#app');
```

可以创建小仓库，用大仓库管理小仓库，在modules目录新建info.js

选项式写法：

```js
//定义info小仓库
import {defineStore} from "pinia";

//该方法有两个参数，第一个参数为：仓库名字 第二个参数为：仓库配置对象
//defineStore方法执行会返回一个函数，函数的作用就是让组件可以获取到仓库数据
let useInfoStore=defineStore("info",{
    //存储数据：state
    state:()=>{
        return{
            count:99,
            arr:[1,2,3,4,5]
        }
    },
    actions:{
        //这里写方法，注意这里的this指向当前的info对象
        updateNum(){
            this.count++;
            console.log(this);
        }
    },
    getters:{
        //这里写计算属性
        total(){
            let sum=this.arr.reduce((prev,next)=>{
                return prev+next;
            },0)
            return sum;
        }
    }
});
export default useInfoStore;
```

使用：

```vue
<script setup>
  import { ref } from 'vue'
  import useInfoStore from "@/store/modules/info";
  let infoStore=useInfoStore();
  const updateCount=()=>{
    infoStore.updateNum();
  }
</script>

<template>
  <div>
    <h1>{{infoStore.count}}</h1>
    <button @click="updateCount">更新Num</button>
    <p>{{infoStore.total}}</p>
  </div>
</template>
```

组合式写法：

```js
//定义info小仓库
import {defineStore} from "pinia";
import {ref,computed} from "vue";


let useInfoStore=defineStore("info",()=>{
    let count=ref(99);
    let arr=ref([1,2,3,4,5])
    //这里是计算属性，需要导入computed
    const total=computed(()=>{
        return arr.value.reduce((prev,next)=>{
            return prev+next;
        },0)
    })
    return{
        count,
        total,
        updateNum(){
            count.value++;
        }
    }
});
export default useInfoStore;
```

### 核心概念

#### 定义Store

在深入研究核心概念之前，我们得知道 Store 是用 `defineStore()` 定义的，它的第一个参数要求是一个**独一无二的**名字：

```js
import { defineStore } from 'pinia'

// 你可以对 `defineStore()` 的返回值进行任意命名，但最好使用 store 的名字，同时以 `use` 开头且以 `Store` 结尾。(比如 `useUserStore`，`useCartStore`，`useProductStore`)
// 第一个参数是你的应用中 Store 的唯一 ID。
export const useAlertsStore = defineStore('alerts', {
  // 其他配置...
})
```

这个**名字** ，也被用作 *id* ，是必须传入的， Pinia 将用它来连接 store 和 devtools。为了养成习惯性的用法，将返回的函数命名为 *use...* 是一个符合组合式函数风格的约定。

`defineStore()` 的第二个参数可接受两类值：Setup 函数或 Option 对象。

##### Option Store

与 Vue 的选项式 API 类似，我们也可以传入一个带有 `state`、`actions` 与 `getters` 属性的 Option 对象

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0 }),
  getters: {
    double: (state) => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

你可以认为 `state` 是 store 的数据 (`data`)，`getters` 是 store 的计算属性 (`computed`)，而 `actions` 则是方法 (`methods`)。

为方便上手使用，Option Store 应尽可能直观简单。

##### Setup Store

也存在另一种定义 store 的可用语法。与 Vue 组合式 API 的 [setup 函数](https://cn.vuejs.org/api/composition-api-setup.html) 相似，我们可以传入一个函数，该函数定义了一些响应式属性和方法，并且返回一个带有我们想暴露出去的属性和方法的对象。

```js
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  function increment() {
    count.value++
  }

  return { count, increment }
})
```

在 *Setup Store* 中：

- `ref()` 就是 `state` 属性
- `computed()` 就是 `getters`
- `function()` 就是 `actions`

##### 使用 Store

虽然我们前面定义了一个 store，但在我们使用 `<script setup>` 调用 `useStore()`(或者使用 `setup()` 函数，**像所有的组件那样**) 之前，store 实例是不会被创建的：

```vue
<script setup>
import { useCounterStore } from '@/stores/counter'
// 可以在组件中的任意位置访问 `store` 变量 
const store = useCounterStore()
</script>
```

你可以定义任意多的 store，但为了让使用 pinia 的益处最大化(比如允许构建工具自动进行代码分割以及 TypeScript 推断)，**你应该在不同的文件中去定义 store**。

**请注意，`store` 是一个用 `reactive` 包装的对象，这意味着不需要在 getters 后面写 `.value`**，就像 `setup` 中的 `props` 一样，**如果你写了，我们也不能解构它**：

如果想解构store，且属性保持响应性，需要使用 `storeToRefs()`

```vue
<script setup>
import { storeToRefs } from 'pinia'
const store = useCounterStore()
// `name` 和 `doubleCount` 是响应式的 ref
// 同时通过插件添加的属性也会被提取为 ref
// 并且会跳过所有的 action 或非响应式 (不是 ref 或 reactive) 的属性
const { name, doubleCount } = storeToRefs(store)
// 作为 action 的 increment 可以直接解构
const { increment } = store
</script>
```

## VueRouter

### 快速入门 -vue2

vue-router 是一个插件包，使用前先安装

```shell
npm install vue-router --save-dev

# npm install  vue-router@3.1.3 --save-dev
```

[--save 和 --save-dev 的作用和区别](https://blog.csdn.net/cvper/article/details/88728505)

    使用命令 --save 或者说不写命令 --save  ,都会把信息记录到 dependencies 中；
    dependencies 中记录的都是项目在运行时需要的文件；
    
    使用命令 --save-dev 则会把信息记录到 devDependencies  中；
    
    devDependencies 中记录的是项目在开发过程中需要使用的一些文件，在项目最终运行时是不需要的；
    
    也就是说我们开发完成后，最终的项目中是不需要这些文件的；
    
    -S 即--save（保存）
    -D 即--dev（生产）

最新版本

```shell
npm install vue-router@4
```

#### 项目结构

一个刚创建的vue-webpack项目如下图所示

![image-20231107131210721](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311071312176.png)

index.html内容如下

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

src目录下有两个文件，分别为APP.vue和main.js

main.js内容如下

```js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
})
```

可以看到这里面挂载了index.html中class=app的div，并且导入了App组件（即App.vue），并且在template中使用了该组件。

App.vue中内容如下

```vue
<template>
  <div id="app">
    <h1>Vue Router</h1>
  </div>
</template>

<script>

export default {
  name: 'App'
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

#### 使用Router

先给出src目录图

![image-20231107132147694](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311071321012.png)

我们在components目录下创建了两个组件分别是 content.vue 和main.vue 

内容为

```vue
<script>
export default {
  name: "main"
  // name: "content"  
}
</script>

<template>
  <h1>main</h1> 
  <!--  <h1>content</h1>-->
</template>

<style scoped>
/* 这里的scoped 表明该样式仅在该组件生效 */
</style>
```

**新建router目录，创建index.js配置文件**

> 注意前端大部分配置主文件，是默认命名为index.js

在index.js中写入如下内容

```js
import Vue from 'vue'
import VueRouter from "vue-router";//使用VueRouter
import Content from "../components/content.vue";//导入跳转的组件
import Main from "../components/main.vue";//导入跳转的组件

//安装路由
Vue.use(VueRouter);

export default new VueRouter({
  routes:[
    {
      //路由路径
      path:'/main',
      name:'main',
      //跳转的组件
      component:Main
    },
    {
      path:'/content',
      name:'content',
      component:Content
    }
  ]
})
```

配置完成后，在main.js 导入配置文件并使用

```js
import Vue from 'vue'
import App from './App'
import router from "./router";//自定扫描router文件夹下index.js配置文件

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```

在App.vue中增加

- router-link标签：类似于之前的a标签
- router-view标签：路由匹配到的组件将渲染在这里

```vue
<template>
  <div id="app">
    <h1>Vue Router</h1>
    <router-link to="/main">main</router-link>
    <router-link to="/content">content</router-link>
    <router-view></router-view>
  </div>
</template>

<script>

export default {
  name: 'App'
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

完成

#### 去除url中的#号

要去掉井号#，只需在定义路由时配置mode为history即可。

> vue2和vue3不相同，详见https://blog.csdn.net/qq_38870145/article/details/132520250
>
> 参考官网文档为准

```js
import Vue from 'vue'
import VueRouter from "vue-router";//使用VueRouter
import Content from "../components/content.vue";//导入跳转的组件
import Main from "../components/main.vue";//导入跳转的组件

//安装路由
Vue.use(VueRouter);

export default new VueRouter({
  mode: 'history',
  routes:[
    {
      //路由路径
      path:'/main',
      name:'main',
      //跳转的组件
      component:Main
    },
    {
      path:'/content',
      name:'content',
      component:Content
    }
  ]
})
```

需要注意的是，使用history模式时，需要保证后端服务器能够正确响应路由请求。不然，会出现404错误或者页面无法正常显示的情况。因此，我们需要针对后端服务器进行相应的配置。

### 快速入门 -vue3

main.js

```js
import { createApp } from 'vue'
import App from './App.vue';
import router from "@/router";

const app=createApp(App)
app.use(router)
app.mount('#app');
```

新建router文件夹，文件夹新建index.js作为其配置文件，具体内容如下

```js
import {createRouter,createWebHashHistory} from "vue-router";
import Parent from "@/components/Parent.vue";
import Child from "@/components/Child.vue";
const routes=[
    {
        path:'/child',
        component:Child
    },
    {
        path:'/parent',
        component:Parent
    }
]
const router=createRouter({
    history:createWebHashHistory(),
    routes
})
export default router;
```

APP.vue

```vue
<script setup>
  import { ref } from 'vue'
  import Child from "@/components/Child.vue";
  import Parent from "@/components/Parent.vue";

</script>

<template>
  <div>
    <h1>router test</h1>
    <router-link to="/child">Child</router-link>
    <router-link to="/parent">Parent</router-link>
    <router-view></router-view>
  </div>
</template>
```

### 嵌套路由

实际生活中的应用界面，通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件，例如：

```
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

在 `VueRouter` 的参数中使用 `children` 配置：

```js
import Vue from "vue";
import VueRouter from "vue-router";
import Main from "../views/main.vue";
import Login from "../views/Login.vue";
import UserList from "../views/user/List.vue";
import UserProfile from "../views/user/Profile.vue";

Vue.use(VueRouter);

export default new VueRouter({
  routes:[
    {
      path:'/main',
      component:Main,
      children:[
        {
          path:'/user/profile',//    请求链接
          component:UserProfile// 路由（转发）到此组件
        },
        {
          path:'/user/list',//    请求链接
          component:UserList// 路由（转发）到此组件
        }
      ]
    },
    {
      path:'/login',
      component:Login
    }
  ]
});
```

### 代码路由跳转-编程导航

除了使用 `<router-link>` 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。

组合式api简单使用

```js
import { useRouter } from 'vue-router'
//获取路由器
let $router = useRouter()
//跳转
$router.push('/')
```



## axios

### 安装

使用 npm:

```shell
npm install axios
```

使用 cdn:

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

### 使用

```html
<div id="vue">
    <p>{{info.name}}</p>
</div>


<script src="https://cdn.jsdelivr.net/npm/vue@2.7.14"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script src="js/index.js"></script>
<script>
    let vm=new Vue({
        el:"#vue",
        data(){//返回函数，隔离作用域
            return {
                info:{//注意，要属性和response.data中一一对应
                    name:null
                }
            }
        },
        mounted(){ //钩子函数
            axios.get('../data.json').then(response=>this.info=response.data);
        }
    });
</script>
```

### 响应结构

某个请求的响应包含以下信息

```json
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务器响应的头
  headers: {},

   // `config` 是为请求提供的配置信息
  config: {},
 // 'request'
  // `request` is the request that generated this response
  // It is the last ClientRequest instance in node.js (in redirects)
  // and an XMLHttpRequest instance the browser
  request: {}
}
```

使用 `then` 时，你将接收下面这样的响应 :

```js
axios.get('/user/12345')
  .then(function(response) {
    console.log(response.data);
    console.log(response.status);
    console.log(response.statusText);
    console.log(response.headers);
    console.log(response.config);
  });
```

### 二次封装axios

在开发项目的时候避免不了与后端进行交互,因此我们需要使用axios插件实现发送网络请求。

在开发项目的时候，我们经常会把axios进行二次封装。

**目的:**

1. 使用请求拦截器，可以在请求拦截器中处理一些业务(开始进度条、请求头携带公共参数)

2. 使用响应拦截器，可以在响应拦截器中处理一些业务(进度条结束、简化服务器返回的数据、处理http网络错误)


在根目录下创建utils/request.ts

```js
import axios from "axios";
import { ElMessage } from "element-plus";
//创建axios实例
let request = axios.create({
    baseURL: import.meta.env.VITE_APP_BASE_API,
    timeout: 5000
})
//请求拦截器
request.interceptors.request.use(config => {
    return config;
});
//响应拦截器
request.interceptors.response.use((response) => {
    return response.data;
}, (error) => {
    //处理网络错误
    let msg = '';
    let status = error.response.status;
    switch (status) {
        case 401:
            msg = "token过期";
            break;
        case 403:
            msg = '无权访问';
            break;
        case 404:
            msg = "请求地址错误";
            break;
        case 500:
            msg = "服务器出现问题";
            break;
        default:
            msg = "无网络";

    }
    ElMessage({
        type: 'error',
        message: msg
    })
    return Promise.reject(error);
});
export default request;
```
