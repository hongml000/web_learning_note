# init project
npm 

# 移动端需要做出的更改
1. 下载reset.css
2. 下载border.css
3. 将这些文件放到项目assets文件目录，一般新建一个styles目录放置，然后导入
```js
// main.js
import './assets/reset.css'
import './assets/border.css'
```
4. 更改index.html文件
```html
<!-- content后面增加的三个属性值是为了让用户在移动端保持1：1比例大小，不会因用户放大缩小改变 -->
<meta name="viewport" content="width=device-width,initial-scale=1.0,mininum-scale=1.0,maxinum-scale=1.0,user-scalable=1.0">
```
5. 使用npm安装stylus，一种更好管理css样式表的方法
```js
cd my-project
npm install stylus --save  
npm install stylus-loader --save     
// save是说明无论在开发环境还是生产环境都要使用这个依赖包

// 也可以使用sass的
npm install node-sass --save
npm install sass-loader --save
// 修改对应配置 build/webpack.base.conf.js中有 rules下添加
{
test: /\.scss$/,
loader: ['style','css','sass']
}
// 在vue中修改<style lang='sass'>
```

# 修改导入的路径配置
1. 在CSS中，引入样式得使用 @import 'xx'，在import的路径中，想使用@代表src，要在前面加个~
```css
<style lang="sass" scoped>
    @import '@/assets/styles/reset.css'
    .header{
        height:
    }
</style>
```
2. 除了@代表src，也可以自定义路径代表符
在build/webpack.base.conf.js中,找到@相关定义部分
```js
resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      // 可以在这下面增加自定义的路径,比如
      'styles': resolve('src/assets/styles'),
    }
  }
```
那么在其它地方导入时,
```js
// js文件
import 'styles/reset.css'
```
```css
/* css文件 */
@import '~styles/reset.css'
```

# iconfont 阿里巴巴矢量图标库
地址： https://www.iconfont.cn/  
注册： 可使用github账号登录  
1. 首页--图标库--官方图标库--大麦官方图标库--搜索或直接找到想要的图标--加入购物车
2. 新建一个项目，可以与自己本地库一样，这样方便查找
3. 将购物车的东东加入项目my-project
4. 下载至本地，解压，一般会有以下文件：
   * demo.css
   * demo_index.html
   * iconfont.css
   * iconfont.eot
   * iconfont.js
   * iconfont.svg
   * iconfont.ttf
   * iconfont.woff
   * iconfont.woff2
5. 我们一般只使用字体文件和iconfont.css文件：
   * iconfont.eot
   * iconfont.svg
   * iconfont.ttf
   * iconfont.woff
   * iconfont.woff2
   * iconfont.css
6. 将字体文件丢到 /src/assets/styles/iconfont/
   将iconfont.css丢到 /src/assets/styles/
7. 在iconfont.css中修改字体的引入，在iconfont.xxx前面加入现在的目录,data那个不用改
```css
@font-face {font-family: "iconfont";
  /* 修改这个路径 */
  /* src: url('iconfont.eot?t=1555239430265'); IE9  */
  src: url('./iconfont/iconfont.eot?t=1555239430265'); /* IE9 */ 
  src: url('./iconfont/iconfont.eot?t=1555239430265#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('data:xxx') format('woff2'),  /* data这部分不用管 */
  url('./iconfont/iconfont.woff?t=1555239430265') format('woff'),
  url('./iconfont/iconfont.ttf?t=1555239430265') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
  url('./iconfont/iconfont.svg?t=1555239430265#iconfont') format('svg'); /* iOS 4.1- */
}

/* 可删除这部分，也可不删*/
.icon-fanhui:before {
  content: "\e624";
}

.icon-jiantouxia:before {
  content: "\e62d";
}

.icon-sousuo:before {
  content: "\e632";
}

```
8. 修改main.js文件
```js
import 'styles/iconfont.css'
```
9. 在.vue中的应用，这里用个span标签，然后使用一个十六进制数表示图标（在iconfont的官网上，查看自己项目的，在对应图标下点击复制代码）
```html
<!-- Header.vue中的模板 -->
<div class="header-left">
    <span class="iconfont" >&#xe624;</span>    
</div>
```
10. 在文件中引用公用scss样式  
在styles文件目录下，新建一个variables.scss文件
```css
$bgColor : #00bcd4;
```
在.vue文件中导入并使用
```html
// 使用scoped说明这个样式只对当前文件有效
<style rel="stylesheet/scss" lang="scss" scoped>
    @import '~styles/variable.scss';
        .header{
          background-color: $bgColor;
      }
</style>
```


# 发送AJAX请求
vue官方推荐使用axios，它能根据服务自动更换对应请求

在一个页面，如果在每个组件中都发一次请求是不合理的。我们只用在index.vue中请求一次就好

怎么使用呢？

## 在首页中scirpt中，先导入axios
```js
import axios from 'axios'
```

## 在script方法中定义axios请求
```js
methods: {
  getrequest () {
    axios.get('/static/mock/index.json').then(this.getHomeInfoSucc)
  },
  getHomeInfoSucc (res) {
    console.log(res.data.msg)
  }
},

//也可以简写成，但注意，如果是赋值的话，就不支持这个写法
methods: {
  getrequest () {
    axios.get('/static/mock/index.json').then(res => console.log(res.data.msg))
  }
},
```

## 在生命周期函数中发送请求，created () { this.getrequest() }
```html
<script>
// 导入模块，注意，在这里导入的是局部组件，所以要在逻辑内声明组件
import HomeHeader from './Header'
import axios from 'axios'
export default {
  // 导出的接口模块名
  name: 'home',
  components: {
    // ES6省略写法 HomeHeader: HomeHeader,
    HomeHeader
  },
  methods: {
    getHomeInfo () {
      axios.get('/static/mock/index.json').then(this.getHomeInfoSucc)
    },
    getHomeInfoSucc (res) {
      console.log(res.data.msg)
    }
  },
  created () {
    this.getHomeInfo()
  }
}
</script>
```

## static中的文件，是可以直接在服务器上不定义路由，也能直接访问到的
http://localhost:8080/static/mock/index.json  
由此可以直接访问json文件，所以可以用这个来作请求测试  

## 父组件向子组件传递数据

### 在父组件中，向子组件传递
```html
    <home-header :msg="msg"></home-header>
```
并定义msg数据
```js
  data () {
    return {
      msg: ''
    }
  },
    methods: {
    getHomeInfo () {
      axios.get('/static/mock/index.json').then(this.getHomeInfoSucc)
    },
    getHomeInfoSucc (res) {
      this.msg = res.data.msg
    }
  },
```
### 子组件接收
在逻辑里写props接收属性
```js
  props: {
    msg: String
  }
```
在模板中显示属性
```html
<div class="header-right">
  {{msg}}
  <span class="iconfont">&#xe62d;</span>
</div>
```