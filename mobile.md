# 移动端

## 适配

目的：在不同尺寸的手机设备上，页面“相对性的达到合理的展示（自适应）”或者“保持统一效果的等比缩放（看起来差不多）”



### viewport

移动设备默认视口为 ` layout viewport` 。

- `visual viewport`：可视视口，即浏览器可视区域宽度（屏幕宽度），获取：`window.innerWidth/innerHeight` ；
- `layout viewport`：布局视口，即 dom 元素宽度，比屏幕要宽，获取：`document.documentElement.clientWidth/clientHeight` ；
- `idea viewport`：理想视口，即布局视口与可视视口相等。



**完美适配**：

- 不需要用户缩放和横向滚动条就能正常的查看网站的所有内容；

- 显示大小合适，即移动设备视口为 `idea viewport` 。比如一段 14px 文字，无论是在何种密度屏幕、何种分辨率下，显示出来的大小都是差不多的。



**利用 meta 标签对 viewport 进行控制**：

```html
<!-- 
	该标签的作用是让当前的 viewport 宽度等于设备宽度，即实现 idea viewport，同时不允许用户手动缩放。
	content 中属性必须用 ',' 隔开。
	"width=device-width" 与 "initial-scale=1.0" 都可以实现 idea viewport，但是前者在 iphone 和 ipad 上无论竖屏横屏都是竖屏的 idea viewport，后者会导致 IE 出现相同问题，所以一起写可以将上述问题解决。当两者同时存在并冲突时浏览器会取它们两个中 css px 较大的那个值。
-->
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
```

- `width` ：设置 `layout viewport` 的宽度，为一个正整数，或字符串 `device-width` （宽度为设备宽度，即 `visual viewport` ）；
- `initial-scale` ：设置页面的初始缩放值，为一个数字，可以带小数（相对于 `idea viewport` 缩放，即实际宽度会与设备宽度一致，但是改变了每一个 css px 所对应的物理像素值，所以代表当前 viewport 的 css px 会跟着进行缩放）；
- `minimum-scale` ：允许用户的最小缩放值，为一个数字，可以带小数；
- `maximum-scale` ：允许用户的最大缩放值，为一个数字，可以带小数；
- `height` ：设置 layout viewport 的高度，这个属性对我们并不重要，很少使用；
- `user-scalable` ：是否允许用户进行缩放，值为`no` 或 `yes` ，`no` 代表不允许，`yes` 代表允许。



**关于缩放以及 `initial-scale` 的默认值**：

- 缩放：由于缩放时相当于 `idea viewport` 来缩放的，所以缩放值越大，当前 viewport 的宽度就会越小（实际宽度不变，只是每一个 css px 代表的物理像素缩放了，由于 viewport 宽度使用 css px 表示，所以 viewport 宽度缩放了）。例如在 iphone 中，idea viewport 的宽度是 320px，如果我们设置 `initial-scale=2` ，此时 `visual viewport` 的宽度会变为只有 160px 了。放大了一倍，就是原来 1px 的东西变成 2px，但是 1px 变成 2px 并不是把原来的 320px 变为 640px，而是在实际宽度不变的情况下，1px 变得跟原来 2px 的长度一样长了，所以放大2倍后原本需要 320px 才能填满的宽度现在只需要 160px 就能做到了。因此可以得出一个公式：

  `visual viewport 宽度 = ideal viewport 宽度 / 当前缩放值`

  `当前缩放值 = ideal viewport 宽度 / visual viewport 宽度`

  tip ：**大部分浏览器都符合这个理论，但是安卓上原生浏览器以及 IE 有些问题。安卓自带的 webkit 浏览器只有在 initial-scale=1 以及没有设置 width 属性的时候才是显示正常的；而 IE 不管怎么设置，initial-scale 属性的值都为 1**。

- initial-scale 默认值问题：

  安卓设备上的 initial-scale 默认值好像没有办法得到，或者就是干脆没有默认值，需要设置才有作用；

  在 iphone 和 ipad 上，无论你给 `layout viewport` 设置的宽是多少，如果没有指定默认的缩放值，则 iphone 和 ipad 都会自动计算这个缩放值，以达到当前页面不会出现横向滚动条（或者说 viewport 的宽度就是屏幕宽度）的目的。



**css 中的 1px 并不等与设备的 1px** ：

​	在 css 中我们一般使用 px 作为单位，在桌面浏览器中 css 的 1 个像素往往都是对应着电脑屏幕的 1 个物理像素，这可能会造成我们的一个错觉，那就是css中的像素就是设备的物理像素。

​	但实际情况却并非如此，css中的像素只是一个抽象的单位，在不同的设备或不同的环境中，css中的1px所代表的设备物理像素是不同的。在为桌面浏览器设计的网页中，我们无需对这个津津计较，但在移动设备上，必须弄明白这点。

​	在早先的移动设备中，屏幕像素密度都比较低，如 iphone3，它的分辨率为 320x480 ，在 iphone3 上，一个css 像素确实是等于一个屏幕物理像素的。后来随着技术的发展，移动设备的屏幕像素密度越来越高，从 iphone4开始，苹果公司便推出了所谓的 Retina 屏，分辨率提高了一倍，变成640x960，但屏幕尺寸却没变化，这就意味着同样大小的屏幕上，像素却多了一倍，这时，1 个 css 像素是等于 2 个物理像素的。其他品牌的移动设备也是这个道理。例如安卓设备根据屏幕像素密度可分为 ldpi 、mdpi 、hdpi 、xhdpi 等不同的等级，分辨率也是五花八门，安卓设备上的一个 css 像素相当于多少个屏幕物理像素，也因设备的不同而不同，没有一个定论。

​	还有一个因素也会引起 css 中 px 的变化，那就是用户缩放。例如，当用户把页面放大一倍，那么css中1px所代表的物理像素也会增加一倍；反之把页面缩小一倍，css中 1px 所代表的物理像素也会减少一倍。关于这点，在文章后面的部分还会讲到。

​	

**devicePixelRatio** ：

​	在移动端浏览器中以及某些桌面浏览器中，window 对象有一个 `devicePixelRatio` 属性，它的官方的定义为：**设备物理像素**和**设备独立像素**的比例，也就是 `devicePixelRatio = 物理像素 / 独立像素` 。css 中的 px 就可以看做是设备的独立像素，所以通过 devicePixelRatio，我们可以知道该设备上一个 css 像素代表多少个物理像素。例如，在 Retina 屏的 iphone 上，devicePixelRatio 的值为 2，也就是说1个css像素相当于2个物理像素。但是要注意的是，devicePixelRatio 在不同的浏览器中还存在些许的兼容性问题，所以我们现在还并不能完全信赖这个东西。

- 物理像素（ `physical pixel` ）：一个物理像素是显示器（手机屏幕）上最小的物理显示物理单元，在操作系统的调度下，每一个设备都有自己的颜色值和亮度值；
- 设备独立像素（ `density-independent pixel` ）：设备独立像素（也叫密度无关像素），可以认为是计算机坐标系统中得一个点，这个点代表一个可以由程序使用的虚拟像素（比如：css 像素），然后由相关系统转换为物理像素。



### 适配方案

#### 动态设置视口

动态设置视口缩放为 1 / dpr 。

对于安卓，所有设备缩放设为 1，对于 IOS，根据 dpr 不同，设置其缩放为 dpr 倒数，设置页面缩放可以使得1 个 css 像素（ 1px ）由 1 个设备像素来显示，从而提高显示精度；因此，设置 1/dpr 的缩放视口，可以画出 1px 的边框。

不管页面中有没有设置 viewport ，若无，则设置；若有，则改写，设置其 scale 为 1/dpr 。px 单位的适配：

``` javascript
(function (doc, win) {
  const docEl = win.document.documentElement
  const resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize'
  let metaEl = doc.querySelector('meta[name="viewport"]')
  let dpr = 0
  let scale = 0

  // 对 iOS 设备进行 dpr 的判断，对于 Android 系列，始终认为其 dpr 为 1
  if (!dpr && !scale) {
    const isAndroid = win.navigator.appVersion.match(/android/gi)
    const isIPhone = win.navigator.appVersion.match(/[iphone|ipad]/gi)
    const devicePixelRatio = win.devicePixelRatio

    if(isIPhone) {
      dpr = devicePixelRatio
    } else {
      drp = 1
    }

    scale = 1 / dpr;
  }

  /**
    * ================================================
    *   设置 data-dpr 和 viewport
    × ================================================
    */
  docEl.setAttribute('data-dpr', dpr)
  
  // 动态改写 meta: viewport 标签
  if (!metaEl) {
    metaEl = doc.createElement('meta')
    metaEl.setAttribute('name', 'viewport')
    metaEl.setAttribute('content', 'width=device-width, initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no')
    document.documentElement.firstElementChild.appendChild(metaEl)
  } else {
    metaEl.setAttribute('content', 'width=device-width, initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no')
  }
})(document, window)

```



#### rem

- `rem` ：相对于根元素 html 的 font-size 进行计算，浏览器默认根元素大小是 `font-size=16px` ；

- `em` ：相对于父元素的 font-size 进行计算。

根元素 fontSize 公式：`width / fontSize = baseWidth / baseFontSize` 。
   其中，baseWidth , baseFontSize 是选为基准的设备宽度及其根元素大小，width , fontSize 为所求设备的宽度及其根元素大小。

简单实现：

``` javascript
  /**
    * 以下这段代码是用于根据移动端设备的屏幕分辨率计算出合适的根元素的大小
    * 当设备宽度为 375(iPhone6) 时，根元素 font-size=16px 
    */
  (function (doc, win) {
    const docEl = win.document.documentElement
    const resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize'
    
    /**
      * ================================================
      * 设置根元素 font-size
      * 当设备宽度为 375(iPhone6) 时，根元素 font-size=16px
      * ================================================
      */
    function refreshRem() {
      const clientWidth = win.innerWidth
      	|| doc.documentElement.clientWidth
        || doc.body.clientWidth

      if (!clientWidth) return
      const fz = 16 * clientWidth / 375
      docEl.style.fontSize = fz + 'px'
    }

    if (!doc.addEventListener) return
    win.addEventListener(resizeEvt, refreshRem, false)
    doc.addEventListener('DOMContentLoaded', refreshRem, false)
    refreshRem()
  })(document, window)

```

tip ：**设置视口应该放在设置根元素之前，因为视口的缩放会影响 viewport width** 。



**[amfe-flexible](https://github.com/amfe/lib-flexible)**

由于 `viewport` 单位得到众多浏览器的兼容，`lib-flexible` 这个过渡方案已经可以放弃使用，不管是现在的版本还是以前的版本，都存有一定的问题。**建议大家开始使用 `viewport` 来替代此方**。

搭配 `postcss-pxtorem` 使用。

``` 
npm i -S amfe-flexible
```

```javascript
// 导入，vue2 在 main.js 中导入
import '/amfe-flexible/index.js'
```

``` html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
```

源码：

``` javascript
/**
 * @desc Mobile terminal self adaptation. 移动端自适配
 */
;(function flexible(window, document) {
  const docEl = document.documentElement
  const dpr = window.devicePixelRatio || 1

  // adjust body font size
  function setBodyFontSize() {
    if (document.body) {
      document.body.style.fontSize = 12 * dpr + 'px'
    } else {
      document.addEventListener('DOMContentLoaded', setBodyFontSize)
    }
  }
  setBodyFontSize()

  // set 1rem = viewWidth / 10
  function setRemUnit() {
    const rem = docEl.clientWidth / 10
    docEl.style.fontSize = rem + 'px'
    
    // 部分机型（华为、小米部分机型以及微信内置浏览器）设置的 font-size 与实际的 font-size 会有误差，大概的比例都在 1.2499 左右，会导致 rem 无法填满页面。
    const real_font = parseFloat(
      window.getComputedStyle(document.getElementsByTagName('html')[0]).fontSize.split('p')[0]
    )
    if (real_font * 120 * 100 < rem * 10000) {
      docEl.style.fontSize = (rem * 100 * 125) / 10000 + 'px'
    }
  }
  setRemUnit()

  // reset rem unit on page resize
  window.addEventListener('resize', setRemUnit)
  window.addEventListener('pageshow', function (e) {
    if (e.persisted) {
      // browser back
      // 浏览器回退
      setRemUnit()
    }
  })

  // detect 0.5px supports
  // 部分机型不支持 0.5 px
  if (dpr >= 2) {
    const fakeBody = document.createElement('body')
    const testElement = document.createElement('div')
    testElement.style.border = '.5px solid transparent'
    fakeBody.appendChild(testElement)
    docEl.appendChild(fakeBody)
    if (testElement.offsetHeight === 1) {
      // 支持 0.5px
      docEl.classList.add('hairlines')
    }
    docEl.removeChild(fakeBody)
  }
})(window, document)

```



**[postcss-pxtorem](https://www.npmjs.com/package/postcss-pxtorem)**

``` javascript
{
  // default
  // number | function ，根 font-size ，即 1rem，默认只会格式化 font-size。
	rootValue: 16,
    
  // number ，格式化后 rem 小数点后最大位数
  unitPrecision: 5,
    
  /** 
   * array ，要被格式化的属性名
   * 元素格式：
   * 1. 完全匹配。
   * 2. ['*'] 匹配所有属性。
   * 3. 部分匹配：['*position*'] => 'background-position-y'。
   * 4. 不匹配：['!letter-space']
   */
  propList: ['font', 'font-size', 'line-height', 'letter-spacing'],
    
  /**
   * array ，黑名单，元素为选择器，黑名单中的选择器中的属性不会被格式化。
   * 元素格式：
   * 1. string：'body' => '.body-font' 会匹配所有含有该 string 的选择器。
   * 2. 正则：和正则相匹配的选择器才行。
   */
  selectorBlackList: [],
    
  // 替换掉包含 rems 的规则，而不是提供回退
  replace: true,
    
  // boolean ，允许转换在媒体查询中的 px
  mediaQuery: false,
    
  // number ，最小的 px ，小于该值不进行转换
  minPixelValue: 0,
    
  // string | regexp | function ，忽略的文件路径，文件路径下的文件不会被格式化
  exclude: /node_modules/i
}

```

```shell
npm install postcss-pxtorem -D
```

``` javascript
// postcss.config.js
module.exports = {
  plugins: {
    'postcss-pxtorem': {
      rootValue: 37.5,
      propList: ['*'],
      selectorBlackList: []
    }
  }
}

```

可能遇到的坑：

- 插件会转化所有的样式的px。比如引入了三方UI，也会被转化。目前可使用 `selectorBlackList` 字段，来过滤（但是感觉不太靠谱）；

- 如果个别地方不想转化px。可以简单的使用大写的 **PX** 或 **Px** 。

  

#### vw

只适用于移动端，换到平板或 PC 设备时，就会给用户带来不好的体验。

例如：无法在宽屏幕时让内容水平居中。

[postcss-px-to-viewport](https://www.npmjs.com/package/postcss-px-to-viewport)

``` shell
npm install postcss-px-to-viewport -D
```

``` javascript
module.exports = {
  plugins: {
    'postcss-px-to-viewport': {
      unitToConvert: 'px',  // 要转化的单位
      viewportWidth: 750,  // UI 设计稿的宽度
      unitPrecision: 5,  // 转换后的精度，即小数点位数
      propList: ['*'],  // 指定转换的 css 属性的单位，* 表示全部 css 属性的单位都进行转换
      viewportUnit: 'vw',  // 指定需要转换成的视窗单位，默认 vw
      fontViewportUnit: 'vw',  // 指定字体需要转换成的视窗单位，默认 vw
      selectorBlackList: ['font', 'font-size', 'line-height', 'letter-spacing'],  // 指定不转换为视窗单位的类名
      minPixelValue: 1,  // 默认值1，小于或等于 1px 则不进行转换
      mediaQuery: false,  // 是否在媒体查询的 css 代码中也进行转换，默认 false
      replace: true,  // 是否直接更换属性值，而不添加备用属性
      exclude: [],  // 忽略某些文件夹下的文件或特定文件，例如 'node_modules'，使用正则表达式
      include: undefined,  // 如果设置了include，那将只有匹配到的文件才会被转换，例如只转换 'src/mobile' 下的文件（使用正则表达式）
      landscape: false  // 是否处理横屏情况
    }
  }
}

```



## 响应式

**vw + media 查询**

- 在 media 查询中写 pc 端样式，然后通过设置 postcss 中的 `mediaQuery` 为 false ，不转换 media 查询中的 px 。

- 非 media 查询下的样式为 h5 样式，px 会被转换为 vw 。



## 第三方库

### 下载文件

**[fileSaver.js](https://github.com/eligrey/FileSaver.js)**

实例：

```shell
npm install file-saver --save
```

```javascript
// 导入
import FileSaver from 'file-saver'

/**
 * 使用
 * 下列参数对应内容
 * content：文本内容 | 二进制流；
 * type：文件类型 MIME Type；
 * name: 文件名字
 * url：url 地址
 */

/** 
 * 下载文件 
 * const blob = new Blob([content], { type })
 * FileSaver.saveAs(blob, name)
 * 例子如下
 */
const blob = new Blob(['Hello, world!'], { type: 'text/plain;charset=utf-8' })
FileSaver.saveAs(blob, 'hello world.txt')

/**
 * 下载 URL
 * FileSaver.saveAs(url, name)
 * 例子如下
 */
FileSaver.saveAs('https://httpbin.org/image', 'image.jpg')

```



##  系统功能与常见问题

### 调用电话

``` html
<a href="tel:1234567890">Call me</a>
```



### 调用短信

``` html
<a href="sms:1234567890">Send me a SMS</a>
```



### 调用邮件

``` html
<a href="mailto:example@example.com">Email me</a>
```



### 调用图库和文件功能

``` html
<input type="file" accept="image/*">
```



### 弹出数字键盘

输入电话号码：

``` html
<input type="tel">
```

输入纯数字格式

``` html
<input type="number" pattern="\d*">
```



### 忽略自动识别

禁止移动端浏览器自动识别电话和邮箱。

``` html
<meta name="format-detection" content="telephone=no">
<meta name="format-detection" content="email=no">
```



### 唤醒原生应用

URL Scheme 实例：

``` html
<a href="twitter://user?screen_name=OpenAI">Open Twitter</a>
```



### 禁止页面缩放和缓存

``` html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta http-equiv="pragma" content="no-cache">
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
<meta http-equiv="expires" content="0">
```



### 禁止字母大写和自动纠正

禁止字母大写功能和自动纠正功能：

``` html
<input type="text" autocapitalize="off" autocorrect="off">
```

针对特定浏览器配置（ safari 私有属性示例）：

``` html
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black">
```



### 监听屏幕旋转

通过使用 css 媒体查询中的 `orientation` 选择器，您可以监听屏幕的旋转事件，并根据屏幕方向调整样式，以便页面始终保持最佳布局。

``` css
@media (orientation: portrait) {
  /* 在竖屏模式下的样式 */
}

@media (orientation: landscape) {
  /* 在横屏模式下的样式 */
}
```



### 禁止屏幕抖动

通过提前声明滚动容器的 `padding-right` 为滚动条宽度，可以防止滚动条的出现导致屏幕抖动。

``` css
.container {
  padding-right: calc(100vw - 100%);
}
```



### 禁止长按操作

使用 `user-select: none` 和 `-webkit-touch-callout: none` 属性可以禁止用户对元素进行长按操作，防止出现意外的行为。

``` css
.element {
  user-select: none;
  -webkit-touch-callout: none;
}
```



### 禁止字体大小调整

通过设置 `text-size-adjust: 100%` 属性，可以阻止用户在旋转屏幕时浏览器自动调整字体大小。

``` css
body {
  -webkit-text-size-adjust: 100%;
}
```



### 禁止高亮显示

使用 `-webkit-tap-highlight-color: transparent` 属性可以禁止触摸元素时的高亮显示效果，使界面更加平滑和一致。

``` css
.element {
  -webkit-tap-highlight-color: transparent;
}
```



### 禁止动画闪屏

通过使用 `perspective` 、`backface-visibility` 和 `transform-style` 属性，可以解决在移动设备上动画闪屏的问题，提供更流畅的动画效果。

``` css
.element {
  perspective: 1000px;
  backface-visibility: hidden;
  transform-style: preserve-3d;
}
```



### 自定义表单外观

使用 `appearance: none` 属性可以自定义表单元素的样式，使其更符合您的设计需求。

``` css
input[type="text"],
input[type="email"],
textarea {
  appearance: none;
}
```



### 自定义滚动条

通过使用 `::-webkit-scrollbar-*` 伪元素，可以自定义滚动条的样式，使其更加美观。

``` css
.scrollable::-webkit-scrollbar {
  width: 8px;
}

.scrollable::-webkit-scrollbar-thumb {
  background-color: #ccc;
}

.scrollable::-webkit-scrollbar-track {
  background-color: #f1f1f1;
}
```



### 自定义输入占位文本样式

使用 `::-webkit-input-placeholder` 伪元素，可以自定义输入框的占位文本样式，使其更加吸引人。

``` css
input::placeholder {
  color: #999;
}
```



### 调整输入框文本位置

通过设置`line-height: normal`，可以调整输入框的文本位置，使其垂直居中显示。

``` css
input {
  line-height: normal;
}
```



### 对齐下拉选项

通过设置 `direction: rtl` ，可以改变下拉框选项的对齐方式，使其从右向左排列。

``` css
select {
  direction: rtl;
}
```



### 修复点击无效

在苹果系统上，有些元素无法触发 `click` 事件。通过声明 `cursor: pointer` 属性，可以解决这个问题。

``` css
.element {
  cursor: pointer;
}
```



### 识别文本换行

通过设置 `white-space: pre-line` ，可以让浏览器自动处理文本的换行，保留原始的换行符。

``` css
.element {
  white-space: pre-line;
}
```



### 开启硬件加速

使用 `transform: translate3d(0, 0, 0)` 属性可以开启GPU硬件加速，提高动画的流畅性和性能。

``` css
.element {
  transform: translate3d(0, 0, 0);
}
```



### 控制溢出文本

使用 css 的 `text-overflow`、`white-space` 、`-webkit-line-clamp` 和 `-webkit-box-orient` 属性，可以控制文本的单行和多行溢出，使其更加易读。

``` css
.element {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.element.multiline {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
```



### 安卓去掉语音输入按钮

``` css
input::-webkit-input-speech-button {
  display: none;
}
```



### IOS safari 被点击元素会出现半透明灰色遮罩

``` css
body {
  -webkit-tap-highlight-color: rgba(0,0,0,0);
  -webkit-user-modify: read-write-plaintext-only;
}
```



### IOS 禁止保存或拷贝图像

长按图片保存场景下，禁止 IOS 默认识别图像行为。

``` css
img {
  -webkit-touch-callout: none;
}
```



### IOS 微信 H5 页面上下滑动卡顿

给滚动元素加上 `-webkit-overflow-scrolling` 属性。

``` css
body {
  -webkit-overflow-scrolling: touch;
}
```



### IOS 默认输入框内阴影重置

阻止 iOS 默认的美化页面的策略 `-webkit-appearance：none` 。

``` css
input {
  border: 0;
  -webkit-appearance: none;
}
```



### 手机底部刘海和页面背景色不一致

通过指定 body 的背景色来解决。

``` css
body {
  background-color: #fff;
  // or 暗色模式
  // background-color: #000;
}
```



### iPhoneX 系列手机适配

在 iPhoneX 系列手机上，头部或底部区域可能会出现刘海遮挡文字或点击区域的情况，或者出现黑底或白底的空白区域。

原因：iPhoneX 及以上版本手机采用了特殊的设计，包括状态栏、圆弧展示角、传感器槽、主屏幕指示器和屏幕边缘手势。为了适配这些特性，头部、底部及侧边栏都需要做特殊处理，使 content 尽可能地处于安全区域内。

解决方案：

- 设置 viewport-fit meta 标签为 cover，使内容能够填充所有区域，并对 iPhone X 进行特殊适配。

  同时设置 `maximum-scale=1, minimum-scale=1` ，会导致 `viewport-fit=cover` 没生效，只需要设置一个。

  ``` html
  <meta name="viewport" content="width=device-width,initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  ```

- 利用 WebKit 的新 css 函数 `constant()` 和 `env()` ，以及四个预定义的常量：`safe-area-inset-left` ，`safe-area-inset-right` ， `safe-area-inset-top` 和 `safe-area-inset-bottom`，来设置安全区域。

  先 `content()` 再 `env()` 兼容 IOS < 11.2 。

  ``` css
  body {
      padding-top: constant(safe-area-inset-top);
      padding-top: env(safe-area-inset-top);
      padding-left: constant(safe-area-inset-left);
      padding-left: env(safe-area-inset-left);
      padding-right: constant(safe-area-inset-right);
      padding-right: env(safe-area-inset-right);
      padding-bottom: constant(safe-area-inset-bottom);
      padding-bottom: env(safe-area-inset-bottom);
  }
  ```

- 为底部固定的元素增加适应 iPhoneX 系列手机的底部小黑条和圆角的底部高度（例如底部使用的是 fixed 布局，就需要考虑重叠问题，需要单独适配）。



### click 点击延迟和穿透

在 iOS 设备上，单击事件可能会有 300ms 的延迟，因为 Safari 浏览器需要在单击 300ms 后判断用户是否进行了第二次点击以实现双击缩放操作。此外，点击穿透问题也常见，如点击蒙层，蒙层消失后可能会触发蒙层下层元素的点击事件。

解决方案：

- 禁止缩放：通过设置 meta 标签的 `user-scalable=no` 来禁止用户缩放。

- 使用 touch 事件替代 click 事件：这可以消除延迟，因为 touch 事件没有 300ms 延迟。

  ``` javascript
  function tap(obj, callback) {
      let startTime = 0
      let flag = false
  
      obj.addEventListener('touchstart', function(e) {
          startTime = Date.now()
      })
  
      obj.addEventListener('touchmove', function(e) {
          flag = true
      });
  
      obj.addEventListener('touchend', function(e) {
          if (!flag && (Date.now() - startTime) < 150) {
              callback && callback()
          }
          flag = false
          startTime = 0
      })
  }
  ```

- 使用 FastClick 库：FastClick 库可以解决 click 延时和穿透问题。

- 使用 CSS 的 `pointer-events` 属性：通过设置 `pointer-events: none` ，可以让鼠标点击事件失效，从而解决点击穿透问题。

  ``` css
  .element {
      pointer-events: none;
  }
  ```



### 1px 问题

在 H5 页面中，可能需要设置边框宽度为 1px，但在 Retina 屏幕上，1px 可能会看起来比实际要粗。

原因：这是因为移动设备的物理像素密度与 CSS 像素的比例（设备像素比 dpr ）导致的。

解决方案：

- 利用伪元素和 scale 来实现 0.5px 的效果。

  ``` css
  .border-1px {
      position: relative;
  }
  
  .border-1px:after {
      content: "";
      position: absolute;
      left: 0;
      top: 0;
      width: 200%;
      height: 200%;
      border: 1px solid #000;
      transform: scale(0.5);
      transform-origin: left top;
      box-sizing: border-box;
  }
  ```

- 利用 `box-shadow` 来实现。

  ``` css
  box-shadow: 0  -1px 1px -1px #e5e5e5,   //上边线
              1px  0  1px -1px #e5e5e5,   //右边线
              0  1px  1px -1px #e5e5e5,   //下边线
              -1px 0  1px -1px #e5e5e5;   //左边线
  ```

  缺陷：其实就是用阴影代替边框，如果此时还需要阴影则无法同时使用。

- 或者项目设计上就是 viewport + rem ，使得 css 1px 就等于物理像素 1px 。



### sticky 的兼容性问题

在某些 Android 设备的原生浏览器中，使用 `position: sticky` 实现的元素不能正常吸顶。

原因：这是因为这些浏览器不支持 position: sticky。

解决方案：

- 使用 JS：通过自定义滚动事件的监听，根据 top 的改变来实现吸顶层 fixed 和 absolute 的转换。

  ``` html
  <div id="stickyElement">吸顶bar</div>
  <div id="content">这是主要内容</div>
  <script>
      window.addEventListener('scroll', function() {
        const stickyElement = document.getElementById('stickyElement')
        const stickyElementRect = stickyElement.getBoundingClientRect()
        if (stickyElementRect.top <= 0) {
            // 当元素到达顶部，将其定位方式改为固定
            stickyElement.style.position = 'fixed'
            stickyElement.style.top = '0'
          } else {
            // 当元素离开顶部，将其定位方式改回绝对
            stickyElement.style.position = 'absolute'
            stickyElement.style.top = 'initial'
          }
      })
  </script>
  ```

- Vue 项目使用 `vue-sticky` 组件。

  ``` shell
  npm i vue-sticky -S
  ```

  ``` vue
  // 设置自定义指令
  directives: {
    'sticky': VueSticky,
  }
  <ELEMENT v-sticky="{ zIndex: NUMBER, stickyTop: NUMBER, disabled: [true|false]}">
    <div> <!-- sticky wrapper, IMPORTANT -->
      CONTENT
    </div>
  </ELEMENT>
  ```

- React 项目使用 `react-sticky` 组件：通过计算 `<Sticky>` 组件相对于 `<StickyContainer>` 组件的位置进行工作。

  ``` shell
  npm i react-sticky -S
  ```

  ``` react
  <StickyContainer>
    <Sticky>{({ style }) => <h1 style={style}>Sticky element</h1>}</Sticky>
  </StickyContainer>
  ```



### 软键盘顶起页面，收起未回落

在 Android 设备上，点击 input 框弹出键盘时，可能会将页面顶起来，导致页面样式错乱。失去焦点时，键盘收起，键盘区域空白，未回落。

原因：键盘不能回落问题出现在 iOS 12+ 和 wechat 6.7.4+ 中，而在**微信 H5 开发**中是比较常见的 Bug。

兼容原理：1.判断版本类型 2.更改滚动的可视区域。

解决方案：

通过监听页面高度变化，强制恢复成弹出前的高度。

``` javascript
const originalHeight = document.documentElement.clientHeight || document.body.clientHeight

window.onresize = function() {
    const resizeHeight = document.documentElement.clientHeight || document.body.clientHeight

    if (resizeHeight < originalHeight) {
        document.documentElement.style.height = originalHeight + 'px';
        document.body.style.height = originalHeight + 'px';
    }
}
```



### line-height 实现文字垂直居中时文字偏上

实际这个Bug一直存在，没有好的解决方案，详情见 Android 浏览器下 line-height 垂直居中为什么会偏离 ？

解决方案：使用 flex 布局代替。



### border-radius 画出的圆在移动端有毛边

给元素添加 overflow: hidden 属性。

``` css
.elem {
  overflow: hidden;
}
```



### 滚动穿透

滚动穿透（scrolling through）是指在一个固定区域内滚动时，滚动事件透过该区域继续传递到其下方的元素，导致同时滚动两个区域的现象。滚动穿透可能会对用户体验产生负面影响，因为用户可能意外地滚动到不相关的内容。

原理：用户滚动时浏览器会去滚动一切它可以滚动的元素。一般分为两种，**目标 element 的滚动**和 **document 的滚动**。当目标 element 滚动到不可以再继续的时候，就会去滚动 document 。

这个问题一直很无解，只能hack去兼容。

解决方案：

1. 先锁住body

   ``` css
   body,.no-scroll {
     overflow: hidden;
     height: 100%;
   }
   ```

2. 还原 body 滚动区域

   ``` javascript
   / 获取滚动区域的容器元素
   const container = document.querySelector('.container');
   
   // 获取滚动区域的内容元素
   const content = document.querySelector('.content');
   
   // 记录滚动位置
   let scrollTop = 0
   
   // 禁止滚动穿透
   function disableScroll() {
     // 记录当前滚动位置
     scrollTop = window.pageYOffset || document.documentElement.scrollTop
     
     // 设置滚动区域容器的样式，将其高度设置为固定值，并设置滚动条样式
     container.style.height = '100%'
     container.style.overflow = 'hidden'
     
     // 阻止窗口滚动
     document.body.classList.add('no-scroll')
     document.body.style.top = `-${scrollTop}px`
   }
   
   // 启用滚动穿透
   function enableScroll() {
     // 恢复滚动区域容器的样式
     container.style.height = ''
     container.style.overflow = ''
   
     // 允许窗口滚动
     document.body.classList.remove('no-scroll')
     document.body.style.top = ''
   
     // 恢复滚动位置
     window.scrollTo(0, scrollTop)
   }
   
   // 示例使用，当某个事件触发时禁止滚动穿透
   function disableScrollEvent() {
     disableScroll()
   }
   
   // 示例使用，当某个事件触发时启用滚动穿透
   function enableScrollEvent() {
     enableScroll()
   }
   
   ```

   

### 软键盘挡住输入框

解决软键盘挡住输入框的问题。

https://github.com/alibaba/ChatUI/blob/master/src/components/Composer/riseInput.ts

``` typescript
const ua = navigator.userAgent
const iOS = /iPad|iPhone|iPod/.test(ua)

function uaIncludes(str: string) {
  return ua.indexOf(str) !== -1
}

function testScrollType() {
  if (iOS) {
    if (uaIncludes('Safari/') || /OS 11_[0-3]\D/.test(ua)) {
      /**
       * 不处理
       * - Safari
       * - iOS 11.0-11.3（`scrollTop`/`scrolIntoView` 有 bug）
       */
      return '0'
    }
    // 用 `scrollTop` 的方式
    return '1'
  }
  // 其它的用 `scrollIntoView` 的方式
  return '2'
}

export default function riseInput(input: HTMLElement, target: HTMLElement) {
  const scrollType = testScrollType()
  let scrollTimer: ReturnType<typeof setTimeout>

  if (!target) {
    // eslint-disable-next-line no-param-reassign
    target = input
  }

  const scrollIntoView = () => {
    const { top, height } = target.getBoundingClientRect()

    switch (scrollType) {
      case '0':
        // 有 bug
        break
      case '1': {
        /**
         * 正常会自动移至屏幕中，
         * 但是有可能会页面会自动滚动到顶部（即滚动高度为 0 ）。
         * 当页面滚动高度为 0 时，设置滚动高度为 el 距离顶部的高度。
         */
        const el = document.body.scrollTop > 0 ? document.body : document.documentElement

        // If the element is in the screen, return
        if (el.scrollTop > 0) return
        el.scrollTop = top
        break
      }
      default: {
        /**
         * 输入法会占用页面的 innerHeight（可视高度），即 innerHeight 变小。
         * 所以 el 的距离顶部高度 + el 本身高度小于 innerHeight 时，代表 el 没在屏幕中。
         * 此时使 el 滚动至可视范围内。
         */
        const diff = window.innerHeight - (top + height)
        // If the element is in the screen, return
        if (diff >= 0) return
        this.scrollIntoView(true)
      }
    }
  }

  input.addEventListener('focus', () => {
    setTimeout(scrollIntoView, 300)
    scrollTimer = setTimeout(scrollIntoView, 1000)
  })

  input.addEventListener('blur', () => {
    clearTimeout(scrollTimer)

    // 某些情况下收起键盘后输入框不收回，页面下面空白
    // 比如：闲鱼、大麦、乐动力、微信
    if (scrollType && iOS) {
      // 以免点击快捷短语无效
      setTimeout(() => {
        document.body.scrollIntoView()
      })
    }
  })
}
```

