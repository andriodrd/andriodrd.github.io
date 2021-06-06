# Vue教程

## 1.	后续课程说明

## 1.1	到底什么是测试开发

```vue
1.一定不仅是测试
	1、非常敏锐的开发视角
	2、极其强大的开发能力
2.一定不是开发
	1、无需严谨的校验
	2、无需完美用户体验
```

## 1.2	测试开发平台是产品吗？

```vue
对于一个不是产品的东西，我们测开（或者领导）对它的要求是什么？
1.效率
	1、两周内开发出一个平台
2.至少能实现基本的自动化测试
3.无需花里胡哨的功能
4.无需严谨的校验
5.能用轮子的尽量用轮子
6.能少写一段代码，就少写一段代码
```

## 1.3	测试开发的价值、理念

```vue
1.看得懂大神写的代码，也能从中获取启发
2.能改大神的代码，去除糟粕，取其精华
```

## 1.4	测试开发课程到底学什么？

```vue
1.编程思维重于一切
2.轮子
3.快速开发的理念（敏捷开发）
```

# 2、简介

```vue
1.学习网址:https://vuejs.org/v2/guide/installation.html
```

## 2.1	什么是Vue.js

```vue
1.前端的三大主流框架之一
	1、Augular.js、React.js、Vue.js
2.简单小巧
	1、使用gzip压缩之后，只有20kb左右
	2、入门容易
3.自动进行响应式更新
	1、只需关注前端业务逻辑，无需操作DOM
4.高级特性
	1、解耦视图与数据
	2、可复用组件
	3、前端路由
	4、状态管理
	5、虚拟DOM
```

## 2.2	MVVM模式

```vue
1.类似于MVC(java)和django(MVT)
2.M
	1、模型
	2、从后端获取的数据
3.V
	1、视图
	2、界面展示
4.VM
	1、视图模型
	2、核心控制
4.实例代码
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue体验</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2.6.12/dist/vue.js"></script>
</head>
<body>
    <!--MVVM模式中的V视图-->
    <div id="id">
        {{ msg }}
    </div>

    <script>
        // Vue实例，会控制这个，id为app元素中的所有内容
        // Vue的实例就是vm控制器
        let vm = new Vue({
            // 控制的区域
            el: '#id',
            // 代码数据，MVVM中的M
            data:{
                msg:'hello,python测试开发大佬们！'
            }
        })
    </script>
</body>
</html>
```

## 2.3	组件化

```vue
1.模块化
	1、以不同的组件，来划分不同的功能模块
2.复用
3.高效
4.解耦
```

# 3、前端工程化、组件化

## 3.1	创建前端vue工程

```vue
1.安装Node.js
	1、https://nodejs.org/dist/v10.16.3/node-v10.16.3-x64.msi
	2、node -v
	# 这里的npm想当于python中的pip安装工具
	3、npm -v 
2.使用淘宝npm镜像源
	npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 3.2	安装vue-cli脚手架

```vue
1.使用npm
	npm install -g @vue/cli
2.或者使用淘宝npm镜像源
	cnpm install -g @vue/cli
```

## 3.3	使用vs code或者webStorm打开创建的工程项目

```vue
1.建议安装git进行源码管理，webStorm可用插件gitee
2.创建项目
	vue create 项目名
3.如果使用gitbash
	winpty vue.cmd create 项目名
4.或者创建默认工程目录
	vue create 项目名 --default(用的最多)
```

## 3.4	入口文件main.js

```vue
1.入口文件main.js讲解
	// 导入Vue.js
    import Vue from 'vue'
    // 导入App.vue根组件
    import App from './App.vue'

    Vue.config.productionTip = false
    // vm实例化
    new Vue({
      // 渲染App根组件,可以渲染Model
      render: h => h(App),
    }).$mount('#app')
    // 挂载.app的div
```

## 3.5	工程项目其余文件目录

```vue
1.node_modules
	1、放一些工程项目的包
2.public
	1、存放一些不需要webpack打包的文件（不需要压缩）
3.src
	1、存放一些需要压缩打包的文件
4.src/assets
	1、存放一些静态文件
5.src/components
	1、存放一些组件化文件
6.src/App.vue
	1、是工程化项目的根组件
7.src/main.js
	1、是工程化文件的入口文件
8.src/App.vue组件讲解
    // template用于展示给用户，相当于MVVM模式中的V
    <template>
      <div id="app">
        <img alt="Vue logo" src="./assets/logo.png">
        <HelloWorld msg="Welcome to Your Vue.js App"/>
      </div>
    </template>

    <script>
    // 导入需要用到的其余组件
    import HelloWorld from './components/HelloWorld.vue'

    export default {
      // 定义组件名称
      name: 'App',
      components: {
        // 声明子组件
        HelloWorld
      }
    }
    </script>

    <style>
    #app {
      font-family: Avenir, Helvetica, Arial, sans-serif;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
      text-align: center;
      color: #2c3e50;
      margin-top: 60px;
    }
    </style>


子组件：
  <script>
  export default {
    name: 'HelloWorld',
    props: {
      // 从父组件获取msg信息
      msg: String
    }
  }
  </script>

备注：
	js中的{}相当于是一个对象，[]相当于是列表。

子组件：
	<template>
    <div>
        <!--插值表达式-->
        <p>{{message}}</p>
    </div>
    </template>

    <script>
        export default {
            name: "greeting",
            data(){
                return{
                    message:"Hello,Python测开大佬们！"
                }
            }
        }
    </script>

    <style scoped>

    </style>	
```

# 4、基础知识



## 4.1	插值表达式&绑定属性

```vue
<template>
    <div>
        <!--插值表达式-->
        <p>{{message}}</p>
        <!-- v-开头的为vue中指令，使用v-bind来绑定属性-->
        <span v-bind:title="dream">学好vue不是梦！</span>
    </div>
</template>

<script>
    export default {
        name: "greeting",
        data(){
            return{
                message:"Hello,Python测开大佬们！",
                dream:"小目标"
            }
        }
    }
</script>

备注；
	v-bind:属性可以简写为：
```

## 4.2	v-if和v-show

```vue
<template>
    <div>
        <!--插值表达式-->
        <p>{{message}}</p>
        <!-- v-开头的为vue中指令，使用v-bind来绑定属性-->
        <span v-bind:title="dream">学好vue不是梦！</span>
        <!--v-if：每次都会重新删除或者创建元素，而v-show不会重新进行dom的删除和创建操作，只是切换了元素的display属性-->
        <p v-if="isNotMM">不会开车的的老师不是好老师</p>
        <p v-show="isNotMM">不会开车的的老师不是好老师</p>
    </div>
</template>

<script>
    export default {
        name: "greeting",
        data(){
            return{
                message:"Hello,Python测开大佬们！",
                dream:"小目标",
                isNotMM:"true"
            }
        }
    }
</script>

<style scoped>

</style>

备注：
	1、如果是频繁的使用的话，建议使用v-show,否则v-if
	2、v-if,v-else-if,v-else
```

## 4.	v-for

```vue
<template>
    <table>
        <!--v-for循环-->
        <tr>
            <th v-for="(project,key) in project_headers" v-bind:key="key">{{project}}</th>
        </tr>
        <tr v-for="item in projects" v-bind:key="item">
            <td>{{item.name}}</td>
            <td>{{item.leader}}</td>
            <td>{{item.app_name}}</td>
        </tr>
    </table>
</template>

<script>
    export default {
        name: "ProjectsList",
        data(){
            return{
                project_headers:['项目名称','项目负责人','应用名称'],
                projects:[
                    {name:"前程贷项目",leader:"奔奔",app_name:"P2P平台应用"},
                    {name:"探索火星项目",leader:"越野",app_name:"P2P平台应用"},
                    {name:"海底捞项目",leader:"夏夏",app_name:"P2P平台应用"}
                ]
            }
        }
    }
</script>

<style scoped>
    table{
        margin: 20px auto;
        border-collapse: collapse;
        width: 30%;
    }
    td,th{
        border: 1px solid #dddddd;
        padding: 5px;
    }
</style>
```

## 4.4	使用UI框架来实现

```vue
    1、element-ui网址：https://element.eleme.cn/#/en-US/component/quickstart
2、2019最受欢迎的前端7个UI框架
	https://zhuanlan.zhihu.com/p/61403630
3、element ui
	饿了么UI框架
4、安装配置
	1.使用淘宝cnpm安装
		cnpm i element-ui -S
	2.导入element-ui
		// 导入elementui和css文件
		1.import ElementUI from 'element-ui';
		2.import 'element-ui/lib/theme-chalk/index.css';
		// 在创建vue实例之前需要将element-ui插件加入到vue中
		1.Vue.use(ElementUI);

5、示例
	<!--ref为一个元素或者子组件的应用-->
    <!--组件中定义的所有的ref都会放在当前组件$ref中-->
	<!--el-table也是子组件-->
	<template>
      <el-table
              ref="multipleTable"
              :data="tableData"
              style="width: 100%"
              @selection-change="handleSelectionChange">
          <el-table-column
                  type="selection"
                  width="55">
          </el-table-column>
          <el-table-column
                  property="name"
                  label="项目名称"
                  width="120">
          </el-table-column>
          <el-table-column
                  property="leader"
                  label="项目负责人"
                  width="120">
          </el-table-column>
          <el-table-column
                  property="app_name"
                  label="应用名称"
                  width="120">
          </el-table-column>
      </el-table>
  </template>

  <script>
      export default {
          name: "ProjectsListNew",
          data(){
              return{
                  project_headers:['项目名称','项目负责人','应用名称'],
                  tableData:[
                      {name:"前程贷项目",leader:"奔奔",app_name:"P2P平台应用"},
                      {name:"探索火星项目",leader:"越野",app_name:"P2P平台应用"},
                      {name:"海底捞项目",leader:"夏夏",app_name:"P2P平台应用"}
                  ]
              }
          }
      }
  </script>

  <style scoped>

  </style>
```

## 4.5	v-on绑定事件

```vue
<!--v-on为vue中指令，用于绑定事件（各种事件），事件触发之后哦，可以执行js操作-->
<p v-on:click="message='路人'">{{message}}</p>

备注：；v-on:  可以缩写为@

方法：
	1、方式一
	methods:{
		changeUsername:function(){
			// this就相当于self
			this.username="一个没有整容需求的人"
		}	
	}
	2、方式二
	methods: {
      toggleSelection(rows) {
        if (rows) {
          rows.forEach(row => {
            this.$refs.multipleTable.toggleRowSelection(row);
          });
        } else {
          this.$refs.multipleTable.clearSelection();
        }
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      }
    }
```

## 4.6	v-model

```vue
1、数据双向绑定
2、v-bind只能将model中的数据传给绑定的属性
3、v-model既可以将model中的数据传递给绑定的属性，也可以将用户输入的内容传给model来处理
4、v-model只能在input、textarea、select元素上使用
```

## 4.7	vue实例生命周期

```vue
1、第一个阶段是vue创建的阶段：new vue()
2、第二个阶段是监控Data对象数据变化：observe data
3、第三个阶段是vue内部初始化事件：init events
4、第四个阶段是判断是否有el的属性，有则进行模板的渲染
5、第五个阶段是进行挂载操作：mounted->实时监控数据的变化，随时更新dom
6、第六个阶段是声明周期结束阶段：destroyed->vue实例销毁的阶段
7、影响声明周期的东西：钩子函数->
	1.beforeCreate:在执行第二个阶段之前的钩子函数
	2.created:在执行第4个阶段模板渲染之前执行的钩子函数
	3.mounted:在执行第五个阶段进行挂载操作执行的钩子函数：往往放置的是aginx请求的数据，可以随时更新dom
8、示例
	created(){
		console.log('实例创建之后，能获取到this');
		console.log('username为：this.username');
	},
	mounted(){
		console.log('vue实例挂载到dom树之后');
	},
```

# 5、组件

## 5.1	组件声明

```vue
1、局部组件
2、全局组件
	1.在声明全局组件也需要在入口文件main.js中声明
	2.导入子组件
		import HelloWorld from './components/HelloWorld'
	3.创建全局组件
		Vue.component(id:'hello-world',HelloWorld);
	4.在别的组件中只需要声明全局组件即可
		<hello-world/>
```

## 5.2	组件传值

```vue
1、子组件接受父组件的数据
	1.使用props属性指定，从父组件接收数据
2、子组件给父组件传数据
```

## 5.3	solt插槽

```vue
1、插槽
2、命名插槽
3、插槽作用域
4、示例
	子组件定义
	<!--插槽slot用法-->
    <!--第一种方式-->
    <!--<slot></slot>-->
    <!--第二种方式-->
    <slot><p>我们一起学猫叫！</p></slot>
    <!--第三种方式-->
    <slot name="part1"></slot>
    <slot name="part2"></slot>
    <!--插槽作用域-->
    <slot name="part3" :user="username"></slot>
    <slot name="part4" user="恋恋"></slot>
	父组件引用
	<HelloWorld msg="Welcome to Your Vue.js App">
      <!--默认情况下，在子组件开始标签和结束标签中间添加的内容会忽略-->
      <p>添加插槽</p>
      <!--在vue2.6之前版本-->
      <p slot="part1">一起喵喵喵喵喵！</p>
      <!--在vue2.6之后版本，v-slot:可以省略为#-->
      <template v-slot:part2>
        <p>在你面前撒个娇！</p>
      </template>
      <!--插槽作用域-->
      <!--在vue2.6之前版本-->
      <!--<p slot="part3" slot-scope="scope">-->
      <!--{{scope.user}}:一起喵喵喵喵喵！-->
      <!--</p>-->
      <!--在vue2.6之后版本，v-slot:可以省略为#-->
      <template #part3="scope">
        <p>{{scope.user}}:在你面前撒个娇！</p>
      </template>
      <template #part4="{user}">
        <p>{{ user }}:在你面前撒个娇！</p>
      </template>

    </HelloWorld>
```

# 6、路由

```vue
1、学习网址：https://router.vuejs.org/installation.html
```

## 6.1	简介

```vue
1、创建单页面应用
2、官方路由组件，实现前端路由功能
```

## 6.2	安装

```vue
1、npm install vue-router
```

## 6.3	简单路由

```vue
1、创建路由规则文件
	// 导入vue-router组件
  import Vue from 'vue'
  import VueRouter from 'vue-router'

  // 1. Use plugin.
  // This installs <router-view> and <router-link>,
  // and injects $router and $route to all router-enabled child components
  Vue.use(VueRouter)

  // 2. Define route components，已有组件的话只需要导入组件即可。
  import greeting from '../components/greeting'
  import HelloWorld from '../components/HelloWorld'
  import ProjectsList from '../components/ProjectsList'
  import ProjectsListNew from '../components/ProjectsListNew'

  // 3. Create the router
  const router = new VueRouter({
      mode: 'history',
      routes: [
          // router的一个数据对象就是一条路由
          { path: '/greeting', component: greeting },
          { path: '/HelloWorld', component: HelloWorld },
          { path: '/ProjectsList', component: ProjectsList },
          { path: '/ProjectsListNew', component: ProjectsListNew },
      ]
  })

  router.beforeEach((to, from, next) => {
      if (to.query.delay) {
          setTimeout(() => {
              next()
          }, Number(to.query.delay))
      } else {
          next()
      }
  })
    
  // 4. 导入路由
  export  default router;

2、将router挂载到vue实例中
    1.
    // 导入vue.router对象
	import router from './router/index'
    2.
    // 放入Vue实例当中
    new Vue({
    // 渲染App根组件,可以渲染Model
    router,
    render: h => h(App),
  }).$mount('#app');
    
    根组件文件中进行路由的加载，不再加载子路由的声明，改为router-views的声明
    <!--展示路由的页面内容-->
    <router-view></router-view>
3、路由参数类型
	1.路径参数：需要额外定义
   	 // router-link自动帮我们渲染出了a标签
      routes: [
        // router的一个数据对象就是一条路由
        // 在组件中可以通过this.$route.query.属性获取来获取查询字符串参数
        // 在组件中可以通过this.$route.params.username属性获取来获取查询字符串参数
        { path: '/greeting/:username', component: greeting, name: 'greeting'},
      	// 面对这种路由获取参数利用:to="{ name: 'bar', params: { id: 123 }}
      	<li><router-link :to="{ name: 'bar', params: { id: 123 }}">bar</router-link></li>
        { path: '/ProjectsList', component: ProjectsList, name: 'ProjectsList' },
        { path: '/ProjectsListNew', component: ProjectsListNew, name: 'ProjectsListNew' },
    	]
    2.查询字符串参数
       routes: [
        // router的一个数据对象就是一条路由
        // 在组件中可以通过this.$route.query.属性获取来获取查询字符串参数
        // 在组件中可以通过this.$route.params.username属性获取来获取查询字符串参数
        { path: '/greeting/:username', component: greeting, name: 'greeting'},
        { path: '/ProjectsList', component: ProjectsList, name: 'ProjectsList' },
        { path: '/ProjectsListNew', component: ProjectsListNew, name: 'ProjectsListNew' },
    	]
```



## 6.4	嵌套路由

```vue
1、在父路由下再添加子路由
2、可参考nested-routes文件夹下得app.js
3、cv工程师

4、例子

	// 导入vue-router组件
  import Vue from 'vue'
  import VueRouter from 'vue-router'

  // 1. Use plugin.
  // This installs <router-view> and <router-link>,
  // and injects $router and $route to all router-enabled child components
  Vue.use(VueRouter)

  // 2. Define route components，已有组件的话只需要导入组件即可。
  import greeting from '../components/greeting'
  import ProjectsList from '../components/ProjectsList'
  import ProjectsListNew from '../components/ProjectsListNew'

  // 3. Create the router
  const router = new VueRouter({
      mode: 'history',
      routes: [
          // router的一个数据对象就是一条路由
          // 在组件中可以通过this.$route.query.属性获取来获取查询字符串参数
          // 在组件中可以通过this.$route.params.username属性获取来获取查询字符串参数
          {
              path: '/greeting/:username',
              component: greeting,
              name: 'greeting',
              children: [
                  // an empty path will be treated as the default, e.g.
                  // components rendered at /parent: Root -> Parent -> Default
                  // 这里路径为空的话，它会显示2次greeting组件的内容
                  { path: '', component: greeting },
                  { path: '/ProjectsList', component: ProjectsList },
                  // 不加'/'的话，他会在父path的基础上进行拼接出下一级路由即/greeting/:username/ProjectsList
                  { path: 'ProjectsList', component: ProjectsList },
              ]
          },
          { path: '/ProjectsList', component: ProjectsList, name: 'ProjectsList' },
          { path: '/ProjectsListNew', component: ProjectsListNew, name: 'ProjectsListNew' },
      ]
  })

  router.beforeEach((to, from, next) => {
      if (to.query.delay) {
          setTimeout(() => {
              next()
          }, Number(to.query.delay))r
      } else {
          next()
      }
  })

  // 4. 导入路由
  export  default router;

```

# 7、axios

## 7.1	简介

```vue
1、非常流行的请求库
2、vue发起异步请求的标配
```

## 7.2	安装

```vue
1、cnpm install axios -S
```

## 7.3	案例

```vue
1、dogs api:https://dog.ceo/api/breeds/image/random
2、基本操作
	mounted() {
    // 方式一：不推荐
    // axios.get('https://dog.ceo/api/breeds/image/random')
    //   .then(function (response) {
    //     console.log(response);
    //     console.log(response.data);
    //     // 此时的this指的是当前函数的引用，并不是vue实例的引用
    //     this.url = response.data.message;
    //   })
    //   .catch(function (err) {
    //     console.log(err);
    //   });

    // 方式二：箭头函数，箭头函数没有this，所以可以指向vue实例对象
    axios.get('https://dog.ceo/api/breeds/image/random')
      .then(response=> {
        console.log(response);
        console.log(response.data);
        // 此时的this指的是当前函数的引用，并不是vue实例的引用
        this.url = response.data.message;
      })
      .catch(function (err) {
        console.log(err);
      });
  },
3、优化操作
	import axios from 'axios';

    var host = 'https://dog.ceo';
    export const dogs = () =>{
		// 需要使用反引号`，且变量需要使用{}来赋值
        return axios.get(`${host}/api/breeds/image/random`)
    };
```

