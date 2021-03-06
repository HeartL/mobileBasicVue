## 基于 vue-cli 的移动项目开发基础模板整合


#### 使用说明

node,npm 已安装，clone 本地后：


```
npm install

npm run dev

```
主要添加了 reset.css ,引入阿里 iconfont ,已经分类好的文件结构，加入了移动端适配方式，加入 less，搭建了基本的开发框架结构，可直接使用，UI 框架可在后面选择合适的，整个本身没有难度，只是方便自己开发整合一下。

如有纰漏及疑问，邮件联系：*onlyfangsiyu@163.com*

---

## 说明
### 目录
1 样式部分

 1.1 reset.css

 1.2 less-loader

 1.3 设计标准

 1.4 阿里 iconfont 使用

 1.5 移动端适配

 1.6 图片资源

2 跨域配置

3 接口，域名的配置

 3.1 本地配置
 3.2 测试，生产环境配置

4 Hbuild 打包 APP

5 相关移动端 UI 框架


----------

### 样式部分
**1. [reset.css](https://meyerweb.com/eric/tools/css/reset//) 最好自己看一遍，熟悉一下，添加自己的东西**
 
```
/* http://meyerweb.com/eric/tools/css/reset/ 
   v2.0 | 20110126
   License: none (public domain)
*/

html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
```
个人感觉里面还有可添加的内容，比如输入框各个浏览器默认的 outline 样式等

**2. 如果你用 less 写，你需要安装 less-loader,其他同理**
 
```
npm install --save-dev less-loader less
```
同时在 webpack.base.conf 里面增加

```
{
        test: /\.less$/,
        loader: 'style-loader!css-loader!less-loader'
      }
```

**3. 可能你还要一个 base.less ，定义一些设计给的标准（最好要一下设计标准，或者自己提取一些设计稿中的相同元素设定）**

```
// 颜色定义规范
@color-theme: #42b983;
@color-background: #222;
@color-text: #fff;
@color-btn: #42b983;
```

**4. 阿里 [iconfont](http://www.iconfont.cn/help/detail?spm=a313x.7781069.1998910419.15&helptype=code) 的使用**

具体可以参考官方文档，推荐 font-class 引用的方式使用（代码里可读性更好），注意修改 css 路径和在 main.js 内加入
```
import "./assets/fonts/iconfont.css"
```

**5. 各移动端适配**

采用 iphone 6/7/8 的 750px 设计稿为标准（和设计对接好）
js独立在 fitMobile.js 里面（参考网络）

```
// 根据设备设置 html 的 fontsize
var fitMobile = function () {
    var docEl = document.documentElement,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
        recalc = function () {
            var clientWidth = docEl.clientWidth;
            if (!clientWidth) return;
            docEl.style.fontSize = 100 * (clientWidth / 750) + 'px'; //设计稿750px宽
        };
    if (!document.addEventListener) return;
    window.addEventListener(resizeEvt, recalc, false);
    document.addEventListener('DOMContentLoaded', recalc, false);
}

export {fitMobile};
```
main.js 里面引入

```
//引入移动端适配 js
import {fitMobile} from "./assets/js/public"
Vue.use(fitMobile);
```

其中的 ==docEl.style.fontSize = 100 * (clientWidth / 750) + 'px'== 的数值 750 是设计稿尺寸，可以自己更改，这样 1rem 对应设计稿上 100px,

**6. 图片资源**

统一放在 assets 下的 images 下；

基本样式部分结束；

- 全局样式：index.less
- 局部样式：写在各自文件里或者你直接写 index.less，后面也可以，也方便维护，看个人喜好

#### 跨域配置

目前我这边遇到过两种情况：

1. 项目允许跨域，这个好办，后台解决就行
2. 本身项目就不允许跨域的，调用测试环境接口时会出问题，后台也没必要修改，毕竟上线了还要改回去，前端解决，设置 webpack 如下：
在 config 文件夹 index.js 里面修改 proxyTable 内容：

```
proxyTable: {
      '/mobile': { //根路经
        target: 'http://XXX.XXX.XX.XX:XX', // 测试域名
        secure: false, //如果是https接口，需要配置这个参数
        changeOrigin: true, // 如果接口跨域，需要进行这个参数配置
        pathRewrite: {
          '^/mobile': '/mobile'  //重写接口
        }
      }
    },
```

### 接口，域名的配置

1.本地配置

```
    host: 'localhost', // run 之后的访问域名，可设置成电脑 ip 方便局域网手机输入地址测试
    port: 8080, // 端口号
    autoOpenBrowser: true, // npm run dev 之后可以自动打开默认浏览器调试
```

2.测试，生产环境配置

```
    //dev.env.js
    module.exports = merge(prodEnv, {
		NODE_ENV: '"development"',
		API_ROOT: '"//192.168.1.111/api"'  //配置开发环境地址
	})

	//prod.env.js
    module.exports = merge(prodEnv, {
		NODE_ENV: '"development"',
		API_ROOT: '"//www.google.com/api"'  //配置开发环境地址
	})
```
### Hbuild 打包 APP

（当然你也可以用其他工具，比如：[Cordova](http://cordova.axuer.com/)）

根据官网论坛里面提到的，需要修改 config 文件夹 index.js 里面修改 build 内容：

```
assetsPublicPath: './',
```
是的，就是在前面加个 .  即可，然后正常 build 之后，放进 hbuild 正常操作即可；

### 相关移动端 UI 框架

==框架选择需要谨慎！==

**Mint UI：**
饿了么出品，不过感觉不维护了的样子，半年多没更新了

地址：http://mint-ui.github.io/#!/zh-cn 

**iView：**
一套基于 Vue.js 的高质量 UI 组件库

地址：https://www.iviewui.com/

**Muse-UI：**
基于 Vue 2.0 优雅的 Material Design UI 组件库

地址：https://muse-ui.org/#/zh-CN

**Cube UI：**
滴滴开源，基于 Vue.js 实现的精致移动端组件库

地址：https://didi.github.io/cube-ui/#/zh-CN

**Vant：**
有赞开源，轻量、可靠的移动端 Vue 组件库

地址：https://youzan.github.io/vant/#/zh-CN/intro


**YDUI Touch：**
一只基于Vue2.x的移动端&微信UI

地址：http://vue.ydui.org/

等，就不在一一列出来

2018-07-10
