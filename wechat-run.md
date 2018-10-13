# pdd-mobile-react本地跑项目教程

## 添加webpack.local.config.js配置
```javascript
    devServer: {
        contentBase: './',
        port: 4001,
        historyApiFallback: true, //不跳转
        inline: true, //实时刷新,
        proxy: {
            "/panda-user-web": { target: "http://test.doctorpanda.com", changeOrigin: true },
            "/panda-doctor-web": { target: "http://test.doctorpanda.com", changeOrigin: true },
            "/panda-operate-web": { target: "http://test.doctorpanda.com", changeOrigin: true },
            "/panda-h5-web": { target: "http://test.doctorpanda.com", changeOrigin: true },
            "/panda-payment-web": { target: "http://test.doctorpanda.com", changeOrigin: true },
            "/panda-user-center-web": { target: "http://test.doctorpanda.com", changeOrigin: true },
            "/panda-wechat-web": { target: "http://test.doctorpanda.com", changeOrigin: true },
        }
    }
    // 将本地接口localhost:4001请求全部转发到 http://test.doctorpanda.com下
```


## 更改package.json启动命令
```json
"start": "webpack-dev-server --config webpack.local.config.js",
```

## 删除node_modules文件并且重新安装所有依赖

```
cnpm i
```

## 启动项目



## 如何运行本地项目

1. 先跑测试服务的公众号
- 先打开微信开发者工具
- 例如：输入http://test.doctorpanda.com/H5QusAns/doctorList.html
- 登录成功后拿到微信返回的openId、token
- 点击右边开发者控制台-``application``下面的``Session Storage``会看到该域名下面的所有缓存值

![blockchain](https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/
u=702257389,1274025419&fm=27&gp=0.jpg)

2. 启动本地项目服务
-  ``npm run start``
- 找到对应的文件doctorList.html
- 更改域名 ``localhost:4001/static/quiz/doctorList.html``打开服务
- 
```javascript
<script src="./dist/common.js?v=108"></script>
<script src="./dist/quiz/doctorList.js?v=108"></script>

更改路径为

<script src="../../dist/common.js?v=108"></script>
<script src="../../dist/quiz/doctorList.js?v=108"></script>
```

- F12打开开发者控制台点击``application`` - ``Session Storage`` 添加相对应的openId、token等

- 刷新页面即可访问