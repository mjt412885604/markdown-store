# pddoctor-mobile
pddoctor-mobild开发一些移动端与APP端嵌套的页面、一些移动端推广页面项目

## 项目结构
```
project
|--config (webpack,version版本控制)
|  |--polyfills.js (ES6、ES7兼容处理)
|  |--version.js (版本控制)
|  |--webpack.config.dev.js 
|  |--webpack.config.prod.js 
|--dist (线上发布包)
|--public
|  │--index.html 
|  │--favicon.ico
│--scripts   
│  |--build.js (node生产环境打包)
│  |--start.js (webpack启动)
|  |--test.js (代码自动化检测，暂时不用)
|--src (项目编码区)
|  |--api (项目所有ajax请求存放区)
|  |--components (公共组件存放区)
|  |--css (全局css配置)
|  |--images (图片)
|  |--mixins (react高阶函数)
|  |--router (react-router-dom router路由)
|  |--store (rudex react-rudex 全局数据状态管理)
|  |--utils (工具类)
|  |--views (单页面编辑区域)
|  |--index.js (主入口)
|--.editorconfig
|--.gitignore
|--package.json
|--server.js (express静态服务)
```

## 项目启动
运行环境：node

#### 第一步：安装依赖
```shell
npm install or cnpm install
```

####  第二步：启动本地服务
```shell
npm run dev
npm run start
```
浏览器访问： http://localhost:3000

#### 第三步：项目Build

```sh
npm run build:dev   开发环境
npm run build:test  测试环境
npm run build:pre   预发环境
npm run build:prod  线上环境
```

## 项目第三方插件依赖
```text
    "react": "^16.4.0",
    "react-dom": "^16.4.0",
    "react-loadable": "^5.4.0",
    "react-redux": "^5.0.7",
    "react-router-dom": "^4.2.2",
    "react-transition-group": "^2.4.0",
    "redux": "^4.0.0",
    "redux-thunk": "^2.3.0",
    "weui": "^1.1.3",
    "axios": "^0.18.0",
    "classnames": "^2.2.5",
    "js-cookie": "^2.2.0",
    "moment": "^2.22.2",
    "prop-types": "^15.6.1"
```

## 项目代码规范

1.项目文件语义化。

>**例如：页面名称膳食管理 （禁止一个项目中文件散乱存放）**  

| 项目 | 命名 |
| -- | -- |
| pages | <code>views/dietManage</code> |
| ajax | <code>api/dietManage</code> |
| css | <code>css/dietManage</code> |

2.项目代码易读性要强，面向对象编程思维编程，关键位置表明注释。

>**例如：一个膳食管理ajax请求**  

```javascript
// 进餐详情
export const getDinnerDetail = (params) => (
    fetch({
        url: 'dinner/detail',
        params
    })
)
```
3.项目尽量模块化，分类性要强。

4.项目引用插件要是稳定的、在维护性的、大场的最号，能手写的插件尽量手写。

5.小图片尽量合并或者转码，减少网络请求量。

```
** 禁止小图片不合并不处理上传到阿里云OSS资源服务器上面
弊端
1.起不到减少请求的作用
2.起不到加载速度快
3.不便于项目管理，假如每个人都把图片放到OSS上面会导致大家都不知道具体那个图片在哪里、有没有。
```
6.移动端css布局尽量使用flex布局加大开发效率。

7.js使用es6编码方式加大开发效率。

8.使用Promise,Object, async、await要进行编译为ES5的支持，避免出现兼容问题。

> **例如一个Promise请求**
```
export const getDinnerDetail = (params) => (
    fetch({
        url: 'dinner/detail',
        params
    })
)

// 获取请求数据
async function getDinnerDetailData(){
    var result = await getDinnerDetail() 
}

getDinnerDetailData()
注意：ES7 如果不做特殊处理会导致babel编译为ES5代码后不兼容。而且即使编译后会造成代码量过大的原因，原本5KB的文件编译后28KB。
```
9.git代码提交要commit一下此次提交的改动内容。

10.git不允许在master上面开发，最新的代码需要个人负责的项目结束后合并到master分支上。

11.减少git分支过多的创建，每次个人负责的项目开发完毕后删除多余的git分支，保持git分支的整洁性。

12.大家项目中遇到什么问题一定要及时上报与沟通避免耽误开发进度。有好的技术或者博客可以分享下。

```
谨记：最初的90%的代码用去了最初90%的开发时间。余下的10%的代码用掉另外90%的开发时间。
```