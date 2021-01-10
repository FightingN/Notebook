[TOC]

- 该文档总结工作中遇到的bug及可复用的代码

# 1.vue实现全局组件的自动化注册

- 问题:每次项目里在components文件夹下的组件都要重复引入注册才能使用
- 解决:实现components文件夹下的组件自动注册为全局组件
- [参考文档webpack](https://webpack.js.org/guides/dependency-management/#requirecontext)
- [使用require.context实现前端工程自动化](https://www.jianshu.com/p/c894ea00dfec)

1. 在components下新建index.js文件

   ![image-20201130140304000](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201130140304000.png)

   ```js
   import Vue from 'vue'
   
   const components = require.context('components/', true, /\.vue$/)
   components.keys().forEach(filename => {
     console.log('filename', filename)
     // filename:./fabric/fabric-index.vue 先把./替换成空，再把.vue替换成空 用/分割成数组
     const arr = filename.replace(/\.\//, '').replace(/\.vue$/, '').split('/')
     // 定义组件的名字
     const componentName = arr[arr.length - 2]
     // 找到默认的组件
     const componentConfig = components(filename).default
     // 注册
     Vue.component(componentName, componentConfig)
   })
   
   // const requireAll = requireContext => requireContext.keys().map(requireContext)
   // const req = require.context('./svg-icon/svg', false, /\.svg$/)
   // requireAll(req)
   ```

   

2. 在main.js引入

![image-20201130140148886](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201130140148886.png)

3. 组件就可以直接使用不用引入和注册（组件名是外层的文件夹的名称）

![image-20201130143308654](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201130143308654.png)

# 2. 在项目中使用svg图片

- [npm下载](https://www.npmjs.com/package/svg-sprite-loader)
- [vue官网](https://cn.vuejs.org/v2/cookbook/editable-svg-icons.html)
- [gitHub案例](https://github.com/sdras/vue-sample-svg-icons/)

## 1.为什么使用svg

[1](https://www.caniuse.com/#search=svg).兼容性，现在svg的基本兼容性已经很好了。在[canIuse](https://www.caniuse.com/#search=svg)中可以查看。

2.相比较传统的图片，svg在放大的时候不会失真

3.因为svg是xml形式的，利于浏览器seo搜索，所以一般logo强烈建议用svg

4.可以修改颜色和宽高（fill,width,height)

5.性能，性能是前端很重要的一个指标，svg相比iconfont图标整体加载性能要好，svg sprites与png sprites相比性能也不差。

6.如果使用png的雪碧图，在新增图标的时候需要修改雪碧图，并且修改之后可能因为浏览的缓存问题导致图片加载不出。svg不存在这个问题

## 2.常见的使用svg的方式有

lnline svg, img svg, background svg

1. inline svg 是直接将svg写在html的文件中

2. img svg 是用img标签引入svg

  3 .background svg 是在css,background属性中设置svg文件

## 3.在vue中使用

1. 下载svg-sprite-loader插件

   ```js
   npm install svg-sprite-loader --save-dev
   ```

2. 配置webpack--vue.config.js

   ```js
   // 调整 webpack 配置
     chainWebpack: config => {
       config.module
         .rule('svg')
         .include.add(join('src/components/svg-icon/svg'))
         .end()
         .test(/\.svg$/)
         .use('file-loader')
         .loader('svg-sprite-loader')
         .options({
           symbolId: 'icon-[name]'
         })
     },
   ```

3. 新建components下的svg-icon/svg文件夹
   
   - 该文件夹主要存放阿里矢量图库的svg图标

![image-20201201103426612](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201201103426612.png)

4. 新建components/svg-icon/index.vue组件

   ```js
   <template>
     <svg :class="svgClass"
          aria-hidden="true"
          :style="{fill:fill}">
       <use :xlink:href="iconName"></use>
     </svg>
   </template>
   
   <script>
   export default {
     name: 'svg-icon',
     props: {
       iconClass: {
         type: String,
         required: true
       },
       className: {
         type: String
       },
       fill: {
         type: String,
         default: 'currentColor'
       }
     },
     computed: {
       iconName () {
         return `#icon-${this.iconClass}`
       },
       svgClass () {
         if (this.className) {
           return 'svg-icon ' + this.className
         } else {
           return 'svg-icon'
         }
       }
     }
   }
   </script>
   
   <style scoped>
   .svg-icon {
     width: 1em;
     height: 1em;
     vertical-align: -0.15em;
     overflow: hidden;
   }
   </style>
   
   ```

5. 将新建components/svg-icon/index.vue的组件注册成全局组件

```js
components/index.js
import Vue from 'vue'

const components = require.context('components/', true, /\.vue$/)
components.keys().forEach(filename => {
  console.log('filename', filename)
  // filename:./fabric/fabric-index.vue 先把./替换成空，再把.vue替换成空 用/分割成数组
  const arr = filename.replace(/\.\//, '').replace(/\.vue$/, '').split('/')
  // 定义组件的名字
  const componentName = arr[arr.length - 2]
  // 找到默认的组件
  const componentConfig = components(filename).default
  // 注册
  Vue.component(componentName, componentConfig)
})
//引入项目中所有的svg文件
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg-icon/svg', false, /\.svg$/)
requireAll(req)
```

6. 在main.js中将components/index.js文件引入
7. svg使用
   - 随便一个页面

```vue
 <svg-icon icon-class="mine"
                  fill="#fff"></svg-icon>

```

- mine指的svg图片的名称

# 3.vue中的多页模式搭建

##  1. 使用场景

- 只针对我遇到的项目，如小程序中要开发多个H5页面，但是每个H5页面的功能又都是独立的，此时就适合用vue的多页模式

## 2.使用

**1、思路**

使用Vue搭建多页应用。所有系统都在同一目录下。配置多入口多出口。每个系统之间可以链接。每个系统内依然采用Vue单页应用开发。

**2、组件复用性**

可以将所有的系统公共组件放到系统目录最外面，以达到组件复用。在系统内部依然将自己独立的组件封装，复用。这样可以最大限度的提高组件的复用性。

**3、路由**

每个系统单独进行路由配置

**4、数据管理**

每个系统数据仓库单独处理

## 3.初步搭建

1. 创建项目

   ```js
   vue create multi-page-demo
   ```

2. 将vue单页面内容中原有的内容全部清空掉，不然到时候回有一些不必要的路径报错
3. 新建src/pages文件夹

![image-20201201173613949](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201201173613949.png)

4. vue.config.js

   ```js
   const path = require('path')
   module.exports = {
     lintOnSave: false,
     pages: {
       index: {
         // page 的入口
         entry: 'src/pages/index/main.js',
         // 模板来源
         template: 'public/index.html',
         // 在 dist/index.html 的输出
         filename: 'index.html',
         // 当使用 title 选项时，
         // template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>
         title: 'Index Page',
         // 在这个页面中包含的块，默认情况下会包含
         // 提取出来的通用 chunk 和 vendor chunk。
         // chunks: ['chunk-vendors', 'chunk-common', 'index']
       },
       // 当使用只有入口的字符串格式时，
       // 模板会被推导为 `public/subpage.html`
       // 并且如果找不到的话，就回退到 `public/index.html`。
       // 输出文件名会被推导为 `subpage.html`。
       about: 'src/pages/about/main.js'
     }
   }
   
   ```

5. 页面路由访问

   - index：http://localhost:8080/ 
- about：http://localhost:8080/about.html#/

6. 配置完一定要记得重启一下应用不然不起作用

# 4.vue中多页模式打包

[官网文档](https://cli.vuejs.org/zh/guide/mode-and-env.html)

- 多页应用的每个页面也可能会放在不同的服务器上，这时候如果往每个服务器上都放置完整的资源包，就会显得过于臃肿。于是我们可能就需要将某个页面单独打包出来。

1. 配置环境变量

   ![image-20201202100502658](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201202100502658.png)

- package.json

  ```js
      "build:index": "vue-cli-service build --mode prod-index",
  
  ```

  

- vue.config.js

  ```js
   // 打包文件夹名称
    outputDir: process.env.VUE_APP_OUTPUTDIR,
  ```

  ![image-20201202100714202](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201202100714202.png)

2. 打包

   ![image-20201202100950053](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201202100950053.png)

# 5.vue中解决跨域问题

[webpack](https://www.webpackjs.com/configuration/dev-server/#devserver-proxy)

[vue-element-admin解决跨域方法](https://panjiachen.gitee.io/vue-element-admin-site/zh/guide/advanced/cors.html)

## 1.什么是跨域

**跨域**：由于浏览器同源策略，凡是发送请求url的协议、域名、端口三者之间任意一个与当前页面地址不同即为跨域。存在跨域的情况：

网络协议不同，如http协议访问https协议。

端口不同，如80端口访问8080端口。

域名不同，如qianduanblog.com访问baidu.com。

子域名不同，如abc.qianduanblog.com访问def.qianduanblog.com。

域名和域名对应ip,如[www.a.com](http://www.a.com)访问20.205.28.90.

## 2.在vue中如何解决跨域

- vue.config.js
- **在vue中使用proxy进行跨域的原理是：将域名发送给本地的服务器（启动vue项目的服务,loclahost:8080），再由本地的服务器去请求真正的服务器**

```js
 // 开发环境 dev-server
  devServer: {
    proxy: {
      '/api': {
        target: 'https://epdc-luzhou-antest.elinkservice.cn/epdc-api/',//设置你调用的接口域名和端口号
        pathRewrite: {
            /* 重写路径，当我们在浏览器中看到请求的地址为：http://localhost:8080/api/core/getData/userInfo 时
            实际上访问的地址是：http://121.121.67.254:8185/core/getData/userInfo,因为重写了 /api
           */
          '^/api': '',//这里理解成用‘/api’代替target里面的地址，后面组件中我们掉接口时直接用api代替 比如我要调用'http://40.00.100.100:3002/user/add'，直接写‘/api/user/add’即可
        },
        changeOrigin: true,//這裡true表示实现跨域
      }
    },
  },
```

- '^/api': '' *//当你的接口中没有/api字眼时，采用这种，直接替换成空即可*
- '^/api': '/api'   //当你的接口中刚好有/api 时，采用这种方式*

# 6.vue项目构建优化

## 1.包分析工具

构建代码之后，到底是什么占用了这么多空间？使用 webpack-bundle-analyzer

- vue.config.js

```js
const WebpackBundleAnalyzer = require('webpack-bundle-analyzer');

chainWebpack: (config) => {
    if (process.env.NODE_ENV === 'development') {
      config
        .plugin('webpack-bundle-analyzer')
        .use(WebpackBundleAnalyzer.BundleAnalyzerPlugin);
    }
  },
```

[官网process.env.NODE_ENV](https://cli.vuejs.org/zh/guide/mode-and-env.html)

- process.env.NODE_ENV：运行npm run serve 默认的环境变量是development

- 修改process.env.NODE_ENV的方式有两种

  通过--mode来指定

  ```js
  "serve": "vue-cli-service serve --mode development",
  ```

  - 新建.env环境变量文件，在文件中指定，命令行指定要运行的文件

## 2.开启 Gzip

### 1.为什么开启Gzip

　在你的项目使用了[Vue的路由懒加载](https://www.cnblogs.com/loeyo/p/12340580.html)、[Vue使用CDN引用项目组件，减少项目体积](https://www.cnblogs.com/loeyo/p/11715502.html) 后，还是觉得项目加载速度慢，效果不尽如人意的时候，还有一个“瘦身项目”可以完成，那就是利用nginx和webpack来使用gzip压缩功能。

通常来讲更小的体积就意味着更快的加载速度，也就对应着更好用户体验

### 2.Gzip

[git地址](https://github.com/webpack-contrib/compression-webpack-plugin)

1. 下载

```js
npm install compression-webpack-plugin --save-dev
```



2. Vue.config.js

```js
chainWebpack: (config) => {
  config
    .plugin('CompressionPlugin')
    .use(CompressionPlugin, []);
},
```

- 以上很多配置都未写，使用时需要写上，而且后台必须配合设置才能生效

## 3.路由懒加载

[vue官网](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html#%E6%8A%8A%E7%BB%84%E4%BB%B6%E6%8C%89%E7%BB%84%E5%88%86%E5%9D%97)

```js
const Foo = () => import('./Foo.vue')
const router = new VueRouter({
  routes: [
    { path: '/foo', component: Foo }
  ]
})
```

或者把组件按组分块(未了解和实践)

- ## 把组件按组分块

  有时候我们想把某个路由下的所有组件都打包在同个异步块 (chunk) 中。只需要使用 [命名 chunk](https://webpack.js.org/guides/code-splitting-require/#chunkname)，一个特殊的注释语法来提供 chunk name (需要 Webpack > 2.4)。

  ```js
  const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
  const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
  const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
  ```

  

## 4.Preload & Prefetch

[csdn文章](https://blog.csdn.net/vivo_tech/article/details/109485871)

[webpack](https://webpack.js.org/guides/code-splitting/#prefetchingpreloading-modules)

[csdn文章](https://blog.csdn.net/weixin_41855143/article/details/103427765)

### 1.prefetching

- webpack4.6.0以上支持prefetching 特性，prefetch 是一种 resource hint，用来告诉浏览器在页面加载完成后，利用空闲时间提前获取用户未来可能会访问的内容。

- 一般使用了prefetching会和路由懒加载造成冲突

# 7.vue中的动态组件

[vue官网](https://cn.vuejs.org/v2/api/#component)

![image-20201203161233343](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201203161233343.png)

# 8.vue中H5页面的适配

[csdn文章](https://blog.csdn.net/wx1035589113/article/details/109580887)

[gitHub](https://github.com/amfe/lib-flexible)

[px2rem-postcss-gitHub](https://github.com/songsiqi/px2rem-postcss)

1.下载依赖

```js
npm i lib-flexible或者是amfe-flexible
npm install postcss-px2rem --save
```

2.在main.js引入

```js
import 'lib-flexible'
```

3.vue.config.js

```js
  // css相关
  css: {
    // 将组件内的 css 提取到 一个单独的文件
    extract: true,

    // 是否开启 css source map
    sourceMap: false,

    // 为预处理器的 loader 传递自定义选项
    loaderOptions: {
      css: {},
      postcss: {
        plugins: [
          require('postcss-px2rem')({
            remUnit: 37.5
          })
        ]
      }
    }
  },
```

- remUnit: 这里要写成 37.5, 因为在`amfe-flexible`中宽度为375px时候 1rem为37.5, `postcss-px2rem`的作用是将`px`单位自动转为`rem`单位, 在进行调试的时候一般将浏览器的模拟为 `iphone6/7/8`, 宽度正好为375px

- ##### `postcss-px2rem`会自动将`px`转为`rem`, 若是不想使用自动转化可以使用单位大写 `Px`或者 `PX`, 常用在设置边框宽度上

4.效果

![image-20201203171752843](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20201203171752843.png)

# 8.vue项目中的别名

[vue官网](https://cli.vuejs.org/zh/config/#chainwebpack)

[csdn文章](https://blog.csdn.net/linfeng886/article/details/85800540)

[webpack官网](https://webpack.js.org/configuration/resolve/#resolvealias)

- @ 等价于 /src 这个目录
- 引入文件路径时简化操作修改路径别名

Vue.config.js

```js
 const path = require('path')

function join (dir) {
  return path.join(__dirname, dir)
}
// 调整 webpack 配置
  chainWebpack: config => {
    config.plugins.delete('preload-index')
    config.resolve.alias
      .set('api', join('src/api'))
      .set('assets', join('src/assets'))
      .set('components', join('src/components'))
      .set('directives', join('src/directives'))
      .set('filters', join('src/filters'))
      .set('multiPages', join('src/multiPages'))
      .set('store', join('src/store'))
      .set('style', join('src/style'))
      .set('utils', join('src/utils'))
  },
```

```js
import centerNumBg from 'assets/images/center-num-bg.png'
```

# 9.vue单元测试

[Vue Test Utils](https://vue-test-utils.vuejs.org/zh/installation/)

[csdn文章](https://blog.csdn.net/yujin0213/article/details/108223587)

## 1.为什么要进行vue单元测试

- 节省手动测试的时间
- 减少研发新特性时产生的bug
- 自动化测试在大型项目中可以维护更复杂的基础代码

# 10 vue中的H5页面在小程序中调试

- 小程序经常会嵌套H5页面，在页面应用还没部署到服务器上之前，如何在本地调试？

  - 小程序中web-view的跳转链接写自己本地电脑的ip地址如：

    ```js
     'http://10.10.10.86:9090/navigatetoApp.html#/navigatetoApp'
    ```

  - 项目正常启动，小程序需要扫开发者工具的预览才可以看
  - 手机小程序需要打开开发者调试模式，手机的网需要和电脑的wifi是同一个