# Element-UI

## Vue-element

### 创建项目

```shell
vue init webpack xxxx
```

```shell
# 进入工程目录
cd xxxx
# 安装 vue-router
npm install vue-router --save-dev
# 安装 element-ui
npm i element-ui -S
# 安装依赖
npm install
# 安装 SASS 加载器
npm install sass-loader node-sass --save-dev
cnpm install sass-loader node-sass --save-dev #加速
# 启动测试
npm run dev
```

### 快速上手

删除所有不需要的东西，App.vue如下

```vue
<template>
  <div id="app">
  </div>
</template>

<script>

export default {
  name: 'App'
}
</script>

<style>

</style>
```

main.js如下

```js
import Vue from 'vue';
import App from './App';

new Vue({
  el: '#app'
})
```

先创建 views(只关注视图的组件存放处) 和 router(路由文件) 文件夹，在其中分别新建main.vue和Login.vue,以及index.js。

**Login.vue为复制的登录页面模板**

```vue
<template>
  <div>
    <!-- el开头的就是element的组件（这里是表单） -->
    <el-form ref="loginForm" :model="form" :rules="rules" label-width="80px" class="login-box">
      <h3 class="login-title">欢迎登录</h3>
      <el-form-item label="账号" prop="username">
        <el-input type="text" placeholder="请输入账号" v-model="form.username"/>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input type="password" placeholder="请输入密码" v-model="form.password"/>
      </el-form-item>
      <el-form-item>
        <!--登录按钮，绑定了click事件，调用onSubmit方法-->
        <el-button type="primary" v-on:click="onSubmit('loginForm')">登录</el-button>
      </el-form-item>
    </el-form>

    <!--这里是一个提示框，即如果我们输入错误，就会弹窗-->
    <el-dialog
      title="温馨提示"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">
      <span>请输入账号和密码</span>
      <span slot="footer" class="dialog-footer">
    <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
    </span>
        </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data(){
    return{
      form:{
        username:'',
        password:''
      },
      //  表单验证，需要在el-form-item元素中增加prop属性
      rules:{
        username:[
          {required:true,message:'账号不可为空',trigger:'blur'}
        ],
        password:[
          {required:true,message:'密码不可为空',trigger:'blur'}
        ]
      },
      //  对话框显示和隐藏
      dialogVisible:false
    }
  },
  methods:{
    onSubmit(formName) {
      //	为表单绑定验证功能
      this.$refs[formName].validate((valid) => {
        if(valid){
          //  使用vue-router路由到指定页面，该方式称之为编程式导航
          this.$router.push("/main");
        }else{
          this.dialogVisible = true;
          return false;
        }
      })
    }
  }
}
</script>

<style scoped>
.login-box{
  border: 1px solid #DCDFE6;
  width: 350px;
  margin: 108px auto;
  padding: 35px 35px 15px 35px;
  border-radius: 5px;
  -webkit-border-radius:5px;
  -moz-border-radius:5px;
  box-shadow: 0 0 25px #909339;
}
.login-title{
  text-align: center;
  margin: 0 auto 40px auto;
  color: #303133;
}
</style>
```

main.vue

```vue
<script>
export default {
  name: "main"
}
</script>

<template>
  <h1>首页</h1>
</template>

<style scoped>

</style>
```

在router文件夹下的index.js文件中写入配置

```js
import Vue from "vue";
import VueRouter from "vue-router";
import Main from "../views/main.vue";
import Login from "../views/Login.vue";

Vue.use(VueRouter);

export default new VueRouter({
  routes:[
    {
      path:'/main',
      component:Main
    },
    {
      path:'/login',
      component:Login
    }
  ]
});
```

在App.vue中新增 router-view标签

```vue
<template>
  <div id="app">
    <router-view></router-view> 
  </div>
</template>

<script>

export default {
  name: 'App'
}
</script>

<style>

</style>
```

更改main.js如下

```js
import Vue from 'vue';
import App from './App';
import router from "./router";
import Element from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(router);
Vue.use(Element);


new Vue({
  el: '#app',
  router,//  配置router。
  render: h => h(App)//  配置ElementUI。官网拷贝。
})
```

## Element-plus

安装element-plusy以及它的图标库@element-plus/icons-vue

```shell
pnpm install element-plus @element-plus/icons-vue
```

使用，在main.ts中引入

```typescript
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css'
//@ts-ignore
import zhCn from 'element-plus/dist/locale/zh-cn.mjs'

app.use(ElementPlus, {
    locale: zhCn
})
```

注意要加注释`//@ts-ignore`忽略当前文件ts类型的检测否则有红色提示(打包会失败)

zh-cn.mjs为中文插件

