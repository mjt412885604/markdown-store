# puppeteer

## 介绍
> Puppeteer is a Node library which provides a high-level API to control Chrome or Chromium over the DevTools Protocol. Puppeteer runs headless by default, but can be configured to run full (non-headless) Chrome or Chromium.

先来看看官方的介绍：
[puppeteer](https://github.com/GoogleChrome/puppeteer?utm_source=gold_browser_extension)是一个Node的一个类库，他提供高级api，通过devtools协议控制chrome或chromium。puppeteer默认运行headless，但也能通过配置运行（非headless）chrome或chromium。

我们可以对他进行简称：`无头浏览器`。

我们日常使用浏览器的步骤为：启动浏览器、打开一个网页、进行交互。而无头浏览器指的是我们使用脚本来执行以上过程的浏览器，能模拟真实的浏览器使用场景。

有了puppeteer，我们就能做包括但不限于以下事情：
- 对网页进行截图保存为图片或 pdf
- 抓取单页应用(SPA)执行并渲染(解决传统 HTTP 爬虫抓取单页应用难以处理异步请求的问题)
- 做表单的自动提交、UI的自动化测试、模拟键盘输入等
- 用浏览器自带的一些调试工具和性能分析工具帮助我们分析问题
- 在最新的Puppeteer环境里做测试、使用最新浏览器特性
- 写爬虫做你想做的事情~

类似于puppeteer还有很多，比如：
- PhantomJS, 基于 Webkit
- SlimerJS, 基于 Gecko
- HtmlUnit, 基于 Rhnio
- TrifleJS, 基于 Trident
- Splash, 基于 Webkit

本文主要介绍 Google 提供的无头浏览器(headless Chrome), 他基于 [Chrome DevTools protocol](https://chromedevtools.github.io/devtools-protocol/ ) 提供了不少高度封装的接口方便我们控制浏览器。

## 注意
> puppeteer中使用了 `async`/`await` 等新特性, 需要使用 v7.6.0 或更高版本的 Node.

## 安装
```
npm i puppeteer
# or "yarn add puppeteer"
```
另外，安装 Puppeteer 时，它会下载最新版本的 Chromium（〜71Mb Mac，〜90Mb Linux，〜110Mb Win），保证与 API 协同工作。


## 启动/关闭浏览器、打开页面
```js
const puppeteer = require('puppeteer');
const devices = require('puppeteer/DeviceDescriptors');

(async () => {
    // 启动浏览器
  const browser = await puppeteer.launch();
  const page = await browser.newPage();

  //  设备模拟
  //   await page.emulate(devices['iPhone 6']);

  // 打开页面
  await page.goto('http://www.baidu.com');

  // 保存图片、全屏模式
  await page.screenshot({ path: 'images/baidu.png', fullPage: true });

  // 关闭浏览器
  await browser.close();
})();
```

## 设置页面视窗大小

```js
// 设置浏览器视窗
    page.setViewport({
        width: 1376,
        height: 768,
    });
```

## 执行脚本
```js
const puppeteer = require('puppeteer');

(async () => {

    // 启动浏览器
    const browser = await puppeteer.launch();
    // 打开页面
    const page = await browser.newPage();
    // 设置浏览器视窗
    page.setViewport({
        width: 1376,
        height: 768,
    });
    // 网页地址
    await page.goto('https://www.doctorpanda.com');

    // 获取视窗信息
    const dimensions = await page.evaluate(() => {
        return {
            width: document.documentElement.clientWidth,
            height: document.documentElement.clientHeight,
            deviceScaleFactor: window.devicePixelRatio
        };
    });
    console.log('视窗信息:', dimensions);

    // 获取 html
    // 获取上下文句柄
    const htmlHandle = await page.$('html');
    // 执行计算
    const html = await page.evaluate(body => body.outerHTML, htmlHandle);
    // 销毁句柄
    await htmlHandle.dispose();
    console.log('html:', html);

    const bodyInnerHTML = await page.$eval('body', dom => dom.innerHTML);
    console.log('bodyInnerHTML: ', bodyInnerHTML);

    // 关闭浏览器
    await browser.close();
})
```


## 自动提交表单
```js
const browser = await puppeteer.launch({ headless: true });
    const page = await browser.newPage();
    await page.setViewport({ width: 1200, height: 700 });

    await page.goto('https://test.doctorpanda.com/operation/#/login');
    const username = await page.$('#username')
    const pwd = await page.$('#password')
    const submit = await page.$('button[type="submit"]')

    await username.type('admin')
    await pwd.type('admin')
    submit.click()
    await page.screenshot({ path: './images/panda-login.png', fullPage: true })
    await page.waitFor(1000);

    await page.goto('https://test.doctorpanda.com/operation/#/system/authManage');
    await page.waitFor(1000)
    await page.screenshot({ path: './images/panda-auth.png', fullPage: true })
    await page.pdf({ path: './images/panda-auth.pdf' })
    await browser.close();

```


## 爬取京东商品数据
```js
const puppeteer = require('puppeteer')
const chalk = require('chalk')
const fs = require('fs')
const xlsx = require('node-xlsx')

const log = console.log
var TOTAL_PAGE = 10 // 总页码数
const URL = 'https://list.jd.com/list.html?cat=670,671,672&ev=exbrand_14026&sort=sort_totalsales15_desc&trans=1&JL=3_%E5%93%81%E7%89%8C_Apple#J_crumbsBar'
var writeDataListAll = []

// 格式化的进度输出 用来显示当前爬取的进度
function formatProgress(current) {
    let percent = (current / TOTAL_PAGE) * 100
    let done = ~~(current / TOTAL_PAGE * 40)
    let left = 40 - done
    return `总共${TOTAL_PAGE}条，当前进度：[${''.padStart(done, '=')}${''.padStart(left, '-')}]  ${percent}%`
}

(async () => {
    const browser = await puppeteer.launch({ headless: true })
    log(chalk.green('服务正常启动'))

    try {
        const page = await browser.newPage()
        await page.setViewport({
            width: 1600,
            height: 768,
        })
        await page.goto(URL, { waitUntil: "networkidle2" })
        log(chalk.yellow('页面初次加载完毕'))

        for (let i = 1; i <= TOTAL_PAGE; i++) {

            // 找到分页的输入框以及跳转按钮
            const nextButton = await page.$('.fp-next')
            // 模拟点击跳转
            await nextButton.click()
        }

        // 所有的数据爬取完毕后关闭浏览器
        await browser.close()

        // 写入数据

        log(chalk.green('服务正常结束'))

        //写入数据
        async function handleData(i) {
            
        }

    } catch (error) {
        console.log(error)
        log(chalk.red('服务意外终止'))
        await browser.close()
    } finally {
        process.exit(0)
    }
})()
```

## 页面性能检测 Puppeteer Trace API

Trace API 主要是利用Chrome Performance，生成页面性能追踪的文件 trace.json,在Chrome 开发者工具中上传该文件，就可以对里面的火焰图去做分析。 事例代码

```js
const puppeteer = require('puppeteer');
const devices = require('puppeteer/DeviceDescriptors');
const iPhone = devices['iPhone 6'];

(async () => {
    try {
        const browser = await puppeteer.launch();

        const page = await browser.newPage();

        await page.emulate(iPhone);

        await page.tracing.start({ path: './trace.json' });
        // await page.goto('https://y.qq.com/m/digitalbum/gold/index.html?_video=true&id=2210323&g_f=tuijiannewupload#index/fans');
        await page.goto('https://www.doctorpanda.com');
        await page.tracing.stop();

        browser.close();
    } catch (error) {
        console.log(error);
    }
})();
```
接下来我们打开Chrome的开发者工具，进入到Performance栏目下，把刚才的trace.json拖上去就能看到数据了