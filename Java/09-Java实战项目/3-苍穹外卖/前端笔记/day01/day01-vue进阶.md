# vue基础回顾+进阶

## 课程内容

- VUE 基础回顾
- 路由 Vue-Router
- 状态管理 vuex
- TypeScript



## 1. VUE 基础回顾

### 1.1 基于脚手架创建前端工程

#### 1.1.1 环境要求

要想基于脚手架创建前端工程，需要具备如下环境要求：

- ​	**node.js** 	前端项目的运行环境
- ​	**npm**          JavaScript的包管理工具
- ​	**Vue CLI**    基于Vue进行快速开发的完整系统，实现交互式的项目脚手架

安装完node.js后，可以通过命令行来查看版本号，如下：

![image-20230925094426687](image/image-20230925094426687.png)

安装 Vue CLI，命令如下：

![image-20230925094630147](image/image-20230925094630147.png)

#### 1.1.2 操作过程

使用 Vue CLI 创建前端工程的方式：

- ​	方式一：vue create 项目名称
- ​	方式二：vue ui

重点介绍使用 vue ui 命令创建前端工程的过程：

第一步：在命令行输入命令 vue ui,在浏览器ui界面中选择前端工程存放的位置

![image-20230925100950987](image/image-20230925100950987.png)

第二步：点击“在此创建新项目”按钮，跳转到创建新项目设置页面

![image-20230925101106692](image/image-20230925101106692.png)

第三步：填写项目名称、选择包管理器为npm，点击“下一步”按钮

![image-20230925101202363](image/image-20230925101202363.png)

第四步：选择 Default(Vue 2)，点击"创建项目"按钮，完成项目的创建

![image-20230925101317873](image/image-20230925101317873.png)

#### 1.1.3 工程结构

工程目录结构：

![image-20230925103418213](image/image-20230925103418213.png)

重点文件或目录介绍：

- node_modules：当前项目依赖的js包
- assets：静态资源存放目录
- components：公共组件存放目录
- App.vue：项目的主组件，页面的入口文件
- main.js：整个项目的入口文件
- package.json：项目的配置信息、依赖包管理
- vue.config.js：vue-cli配置文件

#### 1.1.4 启动前端服务

使用VS Code打开创建的前端工程，启动前端工程：

![image-20230925103813210](image/image-20230925103813210.png)

访问前端工程：

![image-20230925103913672](image/image-20230925103913672.png)

注：要停止前端服务，可以在命令行终端使用 ctrl + C 

前端项目启动后，服务端口默认为8080，很容易和后端tomcat端口号冲突。如何修改前端服务的端口号？

可以在vue.config.js中配置前端服务端口号：

~~~javascript
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  devServer: {
    port: 7070  //指定前端服务端口号
  }
})
~~~

### 1.2 vue基本使用方式

本章节从如下几个方面进行vue回顾：

- vue 组件
- 文本插值
- 属性绑定
- 事件绑定
- 双向绑定
- 条件渲染
- axios

#### 1.2.1 vue 组件

Vue 的组件文件以 .vue 结尾，每个组件由三部分组成：

- 结构 <template>
- 样式 <style>
- 逻辑 <script>

![image-20230925111404674](image/image-20230925111404674.png)

#### 1.2.2 文本插值

作用：用来绑定 data 方法返回的对象属性

用法：{{插值表达式}}

示例：

![image-20230925111739838](image/image-20230925111739838.png)

#### 1.2.3 属性绑定

作用：为标签的属性绑定 data 方法中返回的属性

用法：v-bind:xxx，简写为 :xxx

示例：

![image-20230925112435816](image/image-20230925112435816.png)

#### 1.2.4 事件绑定

作用：为元素绑定对应的事件

用法：v-on:xxx，简写为 @xxx

示例：

![image-20230925112514956](image/image-20230925112514956.png)

#### 1.2.5 双向绑定

作用：表单输入项和 data 方法中的属性进行绑定，任意一方改变都会同步给另一方

用法：v-model

示例：

![image-20230925112600375](image/image-20230925112600375.png)

#### 1.2.6 条件渲染

作用：根据表达式的值来动态渲染页面元素

用法：v-if、v-else、v-else-if

示例：

![image-20230925112635467](image/image-20230925112635467.png)

#### 1.2.7 axios

[Axios](https://www.axios-http.cn/) 是一个基于 promise 的 网络请求库，作用于浏览器和 node.js 中。使用Axios可以在前端项目中发送各种方式的HTTP请求。

安装命令：npm install axios

导入：import axios from 'axios'

axios 的 API 列表：

![image-20230925112806943](image/image-20230925112806943.png)

参数说明：

- url：请求路径
- data：请求体数据，最常见的是JSON格式数据
- config：配置对象，可以设置查询参数、请求头信息

注：在使用axios时，经常会遇到跨域问题。为了解决跨域问题，可以在 vue.config.js 文件中配置代理：

~~~javascript
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  devServer: {
    port: 7070,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        pathRewrite: {
          '^/api': ''
        }
      }
    }
  }
})
~~~

axios的post请求示例：

~~~javascript
axios.post('/api/admin/employee/login',{
      username:'admin',
      password: '123456'
    }).then(res => {
      console.log(res.data)
    }).catch(error => {
      console.log(error.response)
    })
~~~

axios的get请求示例：

~~~javascript
axios.get('/api/admin/shop/status',{
        headers: {
          token: ‘xxx.yyy.zzz’
        }
      })
~~~

axios提供的统一使用方式示例一（可以发送各种方式的请求）：

![image-20230925113501390](image/image-20230925113501390.png)

axios提供的统一使用方式示例二（可以发送各种方式的请求）：

~~~javascript
axios({
      url: '/api/admin/employee/login',
      method:'post',
      data: {
        username:'admin',
        password: '123456'
      }
    }).then((res) => {
      console.log(res.data.data.token)
      axios({
        url: '/api/admin/shop/status',
        method: 'get',
        params: {id: 100},
        headers: {
          token: res.data.data.token
        }
      })
    }).catch((error) => {
      console.log(error)
    })
~~~

## 2. 路由 Vue-Router

### 2.1 Vue-Router 介绍

vue 属于单页面应用，所谓路由，就是根据浏览器路径不同，用不同的视图组件替换这个页面内容。

![image-20230925142650279](image/image-20230925142650279.png)

![image-20230925142705351](image/image-20230925142705351.png)

如上图所示：不同的访问路径，对应不同的页面展示。

在vue应用中使用路由功能，需要安装Vue-Router：

![image-20230925142957238](image/image-20230925142957238.png)

注：创建完带有路由功能的前端项目后，在工程中会生成一个路由文件，如下所示：

![image-20230925144015768](image/image-20230925144015768.png)

关于路由的配置，主要就是在这个路由文件中完成的。

为了能够使用路由功能，在前端项目的入口文件main.js中，创建Vue实例时需要指定路由对象：

![image-20230925144400285](image/image-20230925144400285.png)

### 2.2 路由配置

首先了解一下路由组成：

- VueRouter：路由器，根据路由请求在路由视图中动态渲染对应的视图组件
- <router-link>：路由链接组件，浏览器会解析成<a>
- <router-view>：路由视图组件，用来展示与路由路径匹配的视图组件

![image-20230925143537869](image/image-20230925143537869.png)

具体配置方式：

1) 在路由文件中配置路由路径和视图的对应关系：

~~~javascript
import Vue from 'vue'
import VueRouter from 'vue-router'
import HomeView from '../views/HomeView.vue'

Vue.use(VueRouter)

//维护路由表，某个路由路径对应哪个视图组件
const routes = [
  {
    path: '/',
    name: 'home',
    component: HomeView
  },
  {
    path: '/about',
    name: 'about',
    component: () => import(/* webpackChunkName: "about" */ '../views/AboutView.vue')
  }
  ,
  {
    path: '/404',
    component: () => import('../views/404View.vue')
  },
  {
    path: '*',
    redirect: '/404'
  }
]

const router = new VueRouter({
  routes
})

export default router
~~~

2) 在视图组件中配置 router-link标签，用于生成超链接

~~~html
<router-link to="/">Home</router-link> |
<router-link to="/about">About</router-link> |
<router-link to="/test">Test</router-link> |
~~~

3) 在视图组件汇总配置router-view标签

~~~html
<!--视图组件展示的位置-->
<router-view/>
~~~



要实现路由跳转，可以通过标签式和编程式两种：

- 标签式：<router-link to="/about">About</router-link>
- 编程式：this.$router.push('/about')



**问题思考：**如果用户访问的路由地址不存在，该如何处理？

可以通过配置一个404视图组件，当访问的路由地址不存在时，则重定向到此视图组件，具体配置如下：

~~~javascript
  {
    path: '/404',
    component: () => import('../views/404View.vue')
  },
  {
    path: '*',
    redirect: '/404' //重定向
  }
~~~

### 2.3 嵌套路由

嵌套路由：组件内要切换内容，就需要用到嵌套路由（子路由），效果如下：

在App.vue视图组件中有<router-view>标签，其他视图组件可以展示在此

![image-20230925153930006](image/image-20230925153930006.png)

ContainerView.vue组件可以展示在App.vue视图组件的<router-view>位置

![image-20230925153854881](image/image-20230925153854881.png)

ContainerView.vue组件进行了区域划分（分为上、左、右），在右边编写了<router-view>标签，点击左侧菜单时，可以将对应的子视图组件展示在此

![image-20230925154346635](image/image-20230925154346635.png)

实现步骤：

第一步：安装并导入 [elementui](https://element.eleme.io/)，实现页面布局（Container 布局容器）---ContainerView.vue

~~~html
<template>
  <el-container>
    <el-header>Header</el-header>
    <el-container>
        <el-aside width="200px">
        </el-aside>
        <el-main>
        </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {

}
</script>

<style>
.el-header, .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }
  
  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }
  
  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
  }
  
  body > .el-container {
    margin-bottom: 40px;
  }
  
  .el-container:nth-child(5) .el-aside,
  .el-container:nth-child(6) .el-aside {
    line-height: 260px;
  }
  
  .el-container:nth-child(7) .el-aside {
    line-height: 320px;
  }
</style>
~~~

第二步：提供子视图组件，用于效果展示  ---P1View.vue、P2View.vue、P3View.vue

~~~html
<template>
  <div>
    这是P1 View
  </div>
</template>

<script>
export default {

}
</script>

<style>
.el-header, .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }
  
  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }
  
  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
  }
  
  body > .el-container {
    margin-bottom: 40px;
  }
  
  .el-container:nth-child(5) .el-aside,
  .el-container:nth-child(6) .el-aside {
    line-height: 260px;
  }
  
  .el-container:nth-child(7) .el-aside {
    line-height: 320px;
  }
</style>
~~~

第三步：在 src/router/index.js 中配置路由映射规则（嵌套路由配置）

~~~javascript
   {
    path: '/c',
    component: () => import('../views/container/ContainerView.vue'),
    //嵌套路由（子路由），对应的组件会展示在当前组件内部
    children: [//通过children属性指定子路由相关信息（path、component）
      {
        path: '/c/p1',
        component: () => import('../views/container/P1View.vue')
      },
      {
        path: '/c/p2',
        component: () => import('../views/container/P2View.vue')
      },
      {
        path: '/c/p3',
        component: () => import('../views/container/P3View.vue')
      }
    ]
  }
~~~

第四步：在ContainerView.vue 布局容器视图中添加<router-view>，实现子视图组件展示

~~~html
<el-main>
    <router-view/>
</el-main>
~~~

第五步：在ContainerView.vue 布局容器视图中添加<router-link>，实现路由请求

~~~html
<el-aside width="200px">
    <router-link to="/c/p1">P1</router-link><br>
    <router-link to="/c/p2">P2</router-link><br>
    <router-link to="/c/p3">P3</router-link><br>
</el-aside>
~~~

注意：子路由变化，切换的是【ContainerView 组件】中 `<router-view></router-view>` 部分的内容

问题思考：

1.对于前面的案例，如果用户访问的路由是 /c，会有什么效果呢？

![image-20230925160657497](image/image-20230925160657497.png)

2.如何实现在访问 /c 时，默认就展示某个子视图组件呢？

配置重定向，当访问/c时，直接重定向到/c/p1即可，如下配置：

![image-20230925160730746](image/image-20230925160730746.png)

## 3. 状态管理 vuex

### 3.1 vuex 介绍

- vuex 是一个专为 Vue.js 应用程序开发的状态管理库
- vuex 可以在多个组件之间共享数据，并且共享的数据是响应式的，即数据的变更能及时渲染到模板
- vuex 采用集中式存储管理所有组件的状态

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的**状态 (state)**。Vuex 和单纯的全局对象有以下两点不同：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

安装vuex：npm install vuex@next --save

vuex中的几个核心概念：

- state：状态对象，集中定义各个组件共享的数据
- mutations：类似于一个事件，用于修改共享数据，要求必须是同步函数
- actions：类似于mutation，可以包含异步操作，通过调用mutation来改变共享数据

### 3.2 使用方式

本章节通过一个案例来学习vuex的使用方式，具体操作步骤如下：

第一步：创建带有vuex功能的前端项目

![image-20230926094537147](image/image-20230926094537147.png)

注：在创建的前端工程中，可以发现自动创建了vuex相关的文件(src/store/index.js)，并且在main.js中创建Vue实例时，需要将store对象传入，代码如下：

~~~javascript
import Vue from 'vue'
import App from './App.vue'
import store from './store'

Vue.config.productionTip = false

new Vue({
  store,//使用vuex功能
  render: h => h(App)
}).$mount('#app')
~~~

第二步：在src/store/index.js文件中集中定义和管理共享数据

~~~javascript
import Vue from 'vue'
import Vuex from 'vuex'
import axios from 'axios'

Vue.use(Vuex)

//集中管理多个组件共享的数据
export default new Vuex.Store({
  //集中定义共享数据
  state: {
    name: '未登录游客'
  },
  getters: {
  },
  //通过当前属性中定义的函数修改共享数据，必须都是同步操作
  mutations: {
  },
  //通过actions调用mutation，在actions中可以进行异步操作
  actions: {
  },
  modules: {
  }
})
~~~

第三步：在视图组件中展示共享数据

~~~html
<template>
  <div class="hello">
    <h1>欢迎你，{{$store.state.name}}</h1>
  </div>
</template>
~~~

注：$store.state为固定写法，用于访问共享数据

第四步：在mutations中定义函数，用于修改共享数据

~~~javascript
  //通过当前属性中定义的函数修改共享数据，必须都是同步操作
  mutations: {
    setName(state,newName) {
      state.name = newName
    }
  },
~~~

第五步：在视图组件中调用 mutations 中定义的函数

![image-20230926102311897](image/image-20230926102311897.png)

注：mutations中定义的函数不能直接调用，必须通过状态对象的 commit 方法来调用

第六步：如果在修改共享数据的过程中有异步操作，则需要将异步操作的代码编写在actions的函数中

~~~javascript
  //通过actions调用mutation，在actions中可以进行异步操作
  actions: {
    setNameByAxios(context){
      axios({ //异步请求
        url: '/api/admin/employee/login',
        method: 'post',
        data: {
          username: 'admin',
          password: '123456'
        }
      }).then(res => {
        if(res.data.code == 1){
          //异步请求后，需要修改共享数据
          //在actions中调用mutation中定义的setName函数
          context.commit('setName',res.data.data.name)
        }
      })
    }
  },
~~~

注：在actions中定义的函数可以声明context参数，通过此参数可以调用mutations中定义的函数

第七步：在视图组件中调用actions中定义的函数

![image-20230926103114922](image/image-20230926103114922.png)

注：在actions中定义的函数不能直接调用，必须通过 **this.$store.dispatch('函数名称')** 这种方式调用

## 4. TypeScript

### 4.1 TypeScript 介绍

- TypeScript（简称：TS） 是微软推出的开源语言
- TypeScript 是 JavaScript 的超集（JS 有的 TS 都有）

![image-20230926112525531](image/image-20230926112525531.png)

- TypeScript = Type + JavaScript（在 JS 基础上增加了类型支持）
- TypeScript 文件扩展名为 ts
- TypeScript 可编译成标准的 JavaScript，并且在编译时进行类型检查

![image-20230926112649750](image/image-20230926112649750.png)

在前端项目中使用TS，需要进行安装，命令为：npm install -g typescript

查看TS版本：

![image-20230926112830756](image/image-20230926112830756.png)



TS初体验：

1) 创建 hello.ts 文件，内容如下：

~~~typescript
//定义一个函数 hello，并且指定参数类型为string
function hello(msg:string) {
      console.log(msg)
}

//调用上面的函数，传递非string类型的参数
hello(123)
~~~

2) 使用 tsc 命令编译 hello.ts 文件

![image-20230926142314810](image/image-20230926142314810.png)

可以看到编译报错，提示参数类型不匹配。这说明在编译时TS会进行类型检查。需要注意的是在编译为JS文件后，类型会被擦除。



思考：TS 为什么要增加类型支持 ？

- TS 属于静态类型编程语言，JS 属于动态类型编程语言
- 静态类型在编译期做类型检查，动态类型在执行期做类型检查
- 对于 JS 来说，需要等到代码执行的时候才能发现错误（晚）
- 对于 TS 来说，在代码编译的时候就可以发现错误（早）
- 配合 VSCode 开发工具，TS 可以提前到在编写代码的同时就发现代码中的错误，减少找 Bug、改 Bug 的时间



在前端项目中使用TS，需要创建基于TS的前端工程：

![image-20230926145235469](image/image-20230926145235469.png)

![image-20230926145349976](image/image-20230926145349976.png)

### 4.2 TypeScript 常用类型

TS中的常用类型如下：

| **类型**   | **例**                                 | **备注**                     |
| ---------- | -------------------------------------- | ---------------------------- |
| 字符串类型 | string                                 |                              |
| 数字类型   | number                                 |                              |
| 布尔类型   | boolean                                |                              |
| 数组类型   | number[],string[],  boolean[] 依此类推 |                              |
| 任意类型   | any                                    | 相当于又回到了没有类型的时代 |
| 复杂类型   | type 与 interface                      |                              |
| 函数类型   | () =>  void                            | 对函数的参数和返回值进行说明 |
| 字面量类型 | "a"\|"b"\|"c"                          | 限制变量或参数的取值         |
| class 类   | class Animal                           |                              |

#### 4.2.1 类型标注的位置

基于TS进行前端开发时，类型标注的位置有如下3个：

- 标注变量
- 标注参数
- 标注返回值

![image-20230926145517782](image/image-20230926145517782.png)

#### 4.2.2 字符串、数字、布尔类型

字符串、数字、布尔类型是前端开发中常用的类型

![image-20230926145634149](image/image-20230926145634149.png)

#### 4.2.3 字面量类型

字面量类型用于限定数据的取值范围，类似于java中的枚举

![image-20230926145813932](image/image-20230926145813932.png)

#### 4.2.4 interface 类型

interface 类型是TS中的复杂类型，它让 TypeScript 具备了 JavaScript 所缺少的、描述较为复杂数据结构的能力。

![image-20230926150816090](image/image-20230926150816090.png)

可以通过在属性名后面加上？，表示当前属性为可选，如下：

![image-20230926150946103](image/image-20230926150946103.png)

#### 4.2.5 class 类型

使用 class 关键字来定义类，类中可以包含属性、构造方法、普通方法等

![image-20230926151839881](image/image-20230926151839881.png)

在定义类时，可以使用 implments 关键字实现接口，如下：

![image-20230926152034526](image/image-20230926152034526.png)

在定义类时，可以使用 extends 关键字 继承其他类，如下：

![image-20230926152222634](image/image-20230926152222634.png)

