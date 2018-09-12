# H5-预约挂号


## 说明
提供用户进行在线预约挂号服务

## 技术架构
> React + React-Router + React-rudex + Wbbpack + NodeJs + wechet-SDK

基于React-cli的基础上做的二次更改，内部所用组件全部基于weui.css进行的封装

## ESlint代码检测
本项目引入ESlint代码检测工具，请开发人员遵守具体的规则进行编写已提高代码的质量。
例如：

```javascript
"no-multi-spaces": 1,
"jsx-quotes": 1,
"no-undef": 2, //不能有未定义的变量
"no-const-assign": 2, //禁止修改const声明的变量
"no-delete-var": 2, //不能对var声明的变量使用delete操作符
"no-dupe-keys": 2,//在创建对象字面量时不允许键重复 {a:1,a:1}
"no-dupe-args": 2,//函数参数不能重复
"no-duplicate-case": 2,//switch中的case标签不能重复
"no-func-assign": 2,//禁止重复的函数声明
"no-invalid-regexp": 2,//禁止无效的正则表达式
"no-multiple-empty-lines": [1, { "max": 2 }],//空行最多不能超过2行
"default-case": 2,//switch语句最后必须有default
```

## 代码规范
1. 项目文件语义化。
2. 项目代码易读性要强，面向对象编程思维编程，关键位置表明注释。
3. 项目尽量模块化，分类性要强。
4. 项目引用插件要是稳定的、在维护性的、大场的最号，能手写的插件尽量手写。
5. 小图片尽量合并或者转码，减少网络请求量。
6. 移动端css布局尽量使用`flex`布局加大开发效率。
7. js使用`es6`编码方式加大开发效率。
8. 使用`Promise`,`Object`, `async、await`要进行编译为ES5的支持，避免出现兼容问题。
9. git代码提交要`commit`一下此次提交的改动内容。
10. git不允许在`master`上面开发，最新的代码需要个人负责的项目结束后合并到`master`分支上。
11. 减少git分支过多的创建，每次个人负责的项目开发完毕后删除多余的`git分支`，保持git分支的整洁性。
12. 大家项目中遇到什么问题一定要及时上报与沟通避免耽误开发进度。有好的技术或者博客可以分享下。

## 项目排期

| 模块 | 功能 | 负责人 | 周期（h）| 提测时间 | 服务端 |
| :--: | :--: | :--: | :--: | :--: | :--: |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 功能a | 首页 | 马敬涛 | 4 | 2018-11-20 | 佚名 |
| 合计 |  |  | 36 |  |  |



***table***
<table>
    <thead>
        <tr>
            <th>模块</th>
            <th>功能</th>
            <th>负责人</th>
            <th>周期（h）</th>
            <th>提测时间</th>
            <th>服务端</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>功能a</td>
            <td>首页</th>
            <td rowspan="3">马敬涛</th>
            <td style="text-align:center;">4</th>
            <td rowspan="6">2018-11-20</th>
            <td rowspan="5" style="text-align:center;">佚名</td>
        </tr>
        <tr>
            <td>功能a</td>
            <td>首页</th>
            <td style="text-align:center;">4</th>
        </tr>
        <tr>
            <td>功能a</td>
            <td>首页</th>
            <td style="text-align:center;">4</th>
        </tr>
        <tr>
            <td>功能a</td>
            <td>首页</th>
            <td rowspan="3">李小冉</th>
            <td style="text-align:center;">4</th>
        </tr>
        <tr>
            <td>功能a</td>
            <td>首页</th>
            <td style="text-align:center;">4</th>
        </tr>
        <tr>
            <td>功能a</td>
            <td>首页</th>
            <td style="text-align:center;">4</th>
            <td style="text-align:center;">佚名2</td>
        </tr>
    </tbody>
</table>


## 谨记
```text
最初的90%的代码用去了最初90%的开发时间。余下的10%的代码用掉另外90%的开发时间。
```