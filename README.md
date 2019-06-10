# vueplugin

> A Vue.js plugin

## Build Setup

```
# install dependencies
npm install sumyhhplugin --save

# main.js中引入
import myPlugin from 'sumyhhplugin'
Vue.use(myPlugin);
```



# 发布一个vue组件到npm

### 1. 项目初始化

　　首先，要创建项目，封装vue的插件，以前我们初始化vue工程都是用 webpack 的完整配置模板，也就是：

```
vue init webpack my-project
```

　　但是我们要写的是一个简单的vue组件，不需要依赖那么多而庞大的配置，所以，这里我们用简介版本的`webapck`配置模板：也就是

```
vue init webpack-simple my-project
```

　　同学你说什么？两者的差异？给个链接，自己去看哈～ <https://segmentfault.com/a/1190000011402931>

　1.0. 开始之前，说下需求：传入两个数，进行求和输出。先做个简单案例😝

　　1.1. 初始化完成后：

1.2. 既然是封装组件，那我们在src下面创建一个 myPlugin 文件夹规整一点吧，然后，我们在 myPlugin 下面放我们的插件，但是考虑到万一要写很多个的情况，这里我们就区分一下吧（如果个人习惯不想区分也行，只是方便管理），当前组件的相关文件我们给一个 sumFuntion 文件夹名字，下面创建 sum-function.vue 和 index.js 先，结构变成下面这样：

![image-20190610160428237](/Users/yhh/Library/Application Support/typora-user-images/image-20190610160428237.png)

1.3. 接下来，打开终端，目录切换到当前开发目录（这里是sumyhhplugin），安装依赖，启动项目：

```
npm install
npm run dev
```

　然后，我们来写 sum-function.vue ，自然是我们的组件代码：

```
<template>
  <div class="calculate">
    <p>{{a}}+{{b}}={{sum}}</p>
    <input type="text" v-model="a" style="width:30px;text-align:center" @blur="sumFunc"/>
    <span class="symbol">+</span>
    <input type="text" v-model="b" style="width:30px;text-align:center" @blur="sumFunc"/>
    <span class="symbol" @click="sumFunc"> = </span>
    <span class="item">{{sum}}</span>
  </div>
</template>
<script>
  export default({
    name:'addFunc',
    props:['num1','num2'],
    data() {
      return{
        a: this.num1 ? this.num1 : 0,
        b: this.num2 ? this.num2 : 0,
        sum: 0,
      }
    },
    mounted() {
      this.sumFunc();
    },
    methods:{
      sumFunc() {
        
        let a = Number(this.a);
        let b = Number(this.b);
        if(isNaN(a) || isNaN(b)) {
          
          return;
        }else{
          this.sum = a + b;
          this.$emit('getSumFromChild',this.sum);
        }
      }
    }
  })
</script>
<style>
.calculate{
  width: 100%;
  line-height: 26px;
  .item{
    
  }
  .symbol{
    
  }
}
  
</style>
```

1.4.写好了组件，我们本地看下效果先：

　　（1）打开 src/App.vue 文件，将下面代码复制，全部替换掉原来的代码：

```
<template>
  <div id="app">
    <h2>calculate</h2>
    <sum-function :num1="num1" :num2="num2" v-on:getSumFromChild="receiveChildSum"></sum-function>
    
    <p>从子组件获取到的值：{{sumFromChild}}</p>
  </div>
</template>

<script>
import sumFunction from '../src/myPlugin/sumFuntion/sum-function'; // 引入
export default {
  name: 'app',
  data () {
    return {
      num1: 4,
      num2: 5,
      sumFromChild:0,
    }
  },
  components:{ //注册插件
    sumFunction
  },
  methods:{
    receiveChildSum(sum){ //自定义事件，接收子组件的和
      this.sumFromChild = sum;
    }
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

（2）执行 npm run dev 后页面效果如下：

![image-20190610160720638](/Users/yhh/Library/Application Support/typora-user-images/image-20190610160720638.png)

这样我们的组件就写好了，完美！接下来，要怎么把它处理好，让我们可以发布到npm上面去，可以向别人家的npm包一样，散布到世界的每一个应用中😝



　1.5. 继续我们sumFuntion／index.js 文件，目的：将该组件作为 Vue 插件

```
// sumFunction 插件对应组件的名字
import sumFunction from './sum-function';

// Vue.js 的插件应当有一个公开方法 install 。第一个参数是 Vue 构造器，第二个参数是一个可选的选项对象
// 参考：https://cn.vuejs.org/v2/guide/plugins.html#%E5%BC%80%E5%8F%91%E6%8F%92%E4%BB%B6
// 此处注意，组件需要添加name属性，代表注册的组件名称，也可以修改成其他的
 
　sumFunction.install = Vue => Vue.component(sumFunction.name, sumFunction);//注册组件


　// 标签的方式引入，留到后面再另开新篇讨论
  //const install = function(Vue, opts = {}) { 
　//　　Vue.component(sumFunction.name, sumFunction);
　//}

/* 支持使用标签的方式引入 Vue是全局变量时，自动install */
//if (typeof window !== 'undefined' && window.Vue) {
//  install(window.Vue);
//}



export default sumFunction;
```

此处需要注意的是 `install`。 `Vue`的插件必须提供一个公开方法 `install`，该方法会在你使用该插件，也就是 `Vue.use(yourPlugin)`时被调用。这样也就给 `Vue`全局注入了你的所有的组件。

### 2. 修改配置项：　

　　（1）修改 webpack.config.js ，修改完成后执行npm run build 看下是否生效

```
// 执行环境
const NODE_ENV = process.env.NODE_ENV;
console.log("-----NODE_ENV===",NODE_ENV);

module.exports = {
  // 根据不同的执行环境配置不同的入口
  entry: NODE_ENV == 'development' ? './src/main.js' : './src/myPlugin/sumFunction/index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist/',
    filename: 'sumFunction.js',
    library: 'sumFunction', // 指定的就是你使用require时的模块名
    libraryTarget: 'umd', // 指定输出格式
    umdNamedDefine: true // 会对 UMD 的构建过程中的 AMD 模块进行命名。否则就使用匿名的 define
  },
```

​	**library**：指定的就是你使用require时的模块名

　　**libraryTarget**：为了支持多种使用场景，我们需要选择合适的打包格式。常见的打包格式有 CMD、AMD、UMD，CMD只能在 Node 环境执行，AMD 只能在浏览器端执行，UMD 同时支持两种执行环境。显而易见，我们应该选择 UMD 格式。

　　**有时我们想开发一个库，如lodash，underscore这些工具库，这些库既可以用commonjs和amd方式使用也可以用script方式引入。**

　　**这时候我们需要借助library和libraryTarget，我们只需要用ES6来编写代码，编译成通用的UMD就交给webpack了**

　　

​	**umdNamedDefine**：会对 UMD 的构建过程中的 AMD 模块进行命名。否则就使用匿名的 define

（2）修改package.json 文件：

```
// 发布开源因此需要将这个字段改为 false
"private": false,

// 这个指 import sumyhhplugin 的时候它会去检索的路径
"main": "dist/sumFunction.js",
```

（3）修改git上传代码，可以将.gitignore 去掉忽略dist, 把打包的文件也提交上去；

### 3. 执行打包

```
npm run build
```

### 4.提交发布：

　　（1）在npm官网注册账号，地址：https://www.npmjs.com/，注册好之后：

　　　　注意邮箱要验证，会发送验证链接到你的注册邮箱，没有验证的话是不能发布代码的

　　　　看一下package.json 中 author 尽量与 npm 账户一致

　　（2）切换到需要发包的项目根目录下，登录npm账号，输入用户名、密码、邮箱

```
npm login
```

　　（3）npm publish 执行发布：

```
npm publish
```

### 5. 下载安装，应用：

　　（1）确认是否发布成功：官网你的账号下面看一下有没有已经发布的插件，或者：

```
npm install sumyhhplugin --save

//main.js中引入
import myPlugin from 'sumyhhplugin'
Vue.use(myPlugin);
```

### 6.更新发布

只需要更改版本号然后`npm publish`

#### 如果发布的有es6代码

新建`src`目录，把原始文件（es6代码），放入`src`,需要`babael`转化



```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "babel src --out-dir lib"
  },

"devDependencies": {
    "babel-cli": "^6.26.0",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-preset-es2015": "^6.24.1",
    "babel-preset-stage-3": "^6.24.1"
  },
  "dependencies": {
    "transform-runtime": "^0.0.0"
  }
```



#### 错误

`npm ERR! publish Failed PUT 401`
解决过程：

1. 检查仓库是否被设成了淘宝镜像库

```
npm config get registry
```

1. 如是，则设回原仓库

```
npm config set registry=http://registry.npmjs.org
```

1. 登录账号（如未登录）

```
npm login` 或者添加用户 `npm adduser
```

1. 再次发布

```
npm publish
```

1. 如发布成功，则再次将仓库地址设为淘宝镜像地址

```
npm config set registry=https://registry.npm.taobao.org/
```

1. 注意包名不要与线上已有的包名重复
2. 查看npm是否安装成功：

```
npm who am i
```

#### 