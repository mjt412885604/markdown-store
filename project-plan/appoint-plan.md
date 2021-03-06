# 运营后台-预约挂号


## 说明
公司线上预约挂号业务、内容管理

## 一期时间节点
- 一期开发时间节点：9月19-11月9号
- 一期整体提测时间节点：11月12-11月19

## git仓库
- 项目名称：PanderOperate
- git@code.aliyun.com:pddwebclient/PanderOperate.git

## 技术架构
> React + React-Router + React-rudex + antd + Webpack

## 产品参与人
- 产品：彭俭
- 服务端：待定
- WEB端：翟智铮

## ESlint代码检测
本项目引入ESlint代码检测工具，请开发人员遵守具体的规则进行编写已提高代码的质量。
例如：

```javascript
"rules": {
    "eqeqeq": 0, // 提示 === 与 ==
    "array-callback-return": 0, // 箭头函数必须一个返回值
    "no-multi-spaces": 1,
    "jsx-quotes": 1,
    "no-undef": 1, //不能有未定义的变量
    "no-restricted-globals": 1, // 验证是否是全局变量
    "no-const-assign": 2, //禁止修改const声明的变量
    "no-delete-var": 2, //不能对var声明的变量使用delete操作符
    "no-dupe-keys": 2, //在创建对象字面量时不允许键重复 {a:1,a:1}
    "no-dupe-args": 2, //函数参数不能重复
    "no-duplicate-case": 2,//switch中的case标签不能重复
    "no-func-assign": 2, //禁止重复的函数声明
    "no-invalid-regexp": 2, //禁止无效的正则表达式
    "no-multiple-empty-lines": [1, { "max": 2 }], //空行最多不能超过2行
    "default-case": 0, //switch语句最后必须有default
    "jsx-a11y/alt-text": 0, // img 必须有一个alt
    "no-script-url": 0, //无效的URL引用
    "radix": 0, // parseInt Missing radix parameter
    // "indent": [1, 4], //缩进风格
    // "newline-after-var": 0, //变量声明后是否需要空一行
    "react/jsx-tag-spacing": 1, // 总是在自动关闭的标签前加一个空格，正常情况下也不需要换行
    "react/jsx-closing-bracket-location": 1, // 遵循JSX语法缩进/格式
    "react/jsx-boolean-value": 1, // 如果属性值为 true, 可以直接省略
    "react/no-string-refs": 0, // 总是在Refs里使用回调函数
    // "react/self-closing-comp": 1, // 对于没有子元素的标签来说总是自己关闭标签
    "react/jsx-key": 1, // map key值
    "react/jsx-uses-vars": 1, // 引用的组件没有使用
    "jsx-a11y/anchor-has-content": 0, // 锚链节必须有内容
}
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

## 项目排期
项目排期分为页面编写与接口联调

**注意：订单和退款审批合并到伟光的订单列表里去，需要做一下模块跳转配置**

### 静态页面编写
| 模块 | 功能 | 负责人 | 周期（h）| 提测时间 | 服务端 | 产品 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| 预约挂号模块 | 医院信息科室修改 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 订单列表 | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 订单详情 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 新增预约列表 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 新增预约-医生主页 | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 新增预约-选择就诊人 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 新增预约-添加就诊人 | 翟智铮 | 2 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 新增预约-创建虚拟卡 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 新增预约-就诊人详情 | 翟智铮 | 2 | 2018-11-9 |  | 彭俭 |
| 预约挂号模块 | 医生排班 | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 已发区 | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 已发区-发布文章 | 翟智铮 | 6 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 已发区-编辑文章 | 翟智铮 | 2 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 已发区-查看文章 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 已发区-待发区 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 板块管理 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 板块管理(添加/编辑) | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 年龄段管理列表 | 翟智铮 | 3 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 年龄段管理(添加/编辑)| 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 素材区列表 | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 素材区详情 | 翟智铮 | 2 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 栏目管理详情 | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 内容管理 | 栏目管理(添加/编辑) | 翟智铮 | 4 | 2018-11-9 |  | 彭俭 |
| 合计 |  |  | 77 |  |  |  |

### 接口联调
>计划7天的工时

## 谨记
```text
最初的90%的代码用去了最初90%的开发时间。余下的10%的代码用掉另外90%的开发时间。
```