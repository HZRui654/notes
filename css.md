# CSS

## reset

### [normalize](https://www.npmjs.com/package/@csstools/normalize.css)

``` bash
npm i @csstools/normalize.css
```

``` js
import '@csstools/normalize.css';
```

``` css
/* normalize.css */

/* Document
 * ========================================================================== */

/**
 * 1. Correct the line height in all browsers.
 * 2. Prevent adjustments of font size after orientation changes in iOS.
 */
:where(html) {
  line-height: 1.15; /* 1 */
  -webkit-text-size-adjust: 100%; /* 2 */
  text-size-adjust: 100%; /* 2 */
}

/* Sections
 * ========================================================================== */

/**
 * Correct the font size and margin on `h1` elements within `section` and
 * `article` contexts in Chrome, Edge, Firefox, and Safari.
 */
:where(h1) {
  font-size: 2em;
  margin-block-end: 0.67em;
  margin-block-start: 0.67em;
}

/* Grouping content
 * ========================================================================== */

/**
 * Remove the margin on nested lists in Chrome, Edge, and Safari.
 */
:where(dl, ol, ul) :where(dl, ol, ul) {
  margin-block-end: 0;
  margin-block-start: 0;
}

/**
 * 1. Add the correct box sizing in Firefox.
 * 2. Correct the inheritance of border color in Firefox.
 */
:where(hr) {
  box-sizing: content-box; /* 1 */
  color: inherit; /* 2 */
  height: 0; /* 1 */
}

/* Text-level semantics
 * ========================================================================== */

/**
 * Add the correct text decoration in Safari.
 */
:where(abbr[title]) {
  text-decoration: underline;
  text-decoration: underline dotted;
}

/**
 * Add the correct font weight in Chrome, Edge, and Safari.
 */
:where(b, strong) {
  font-weight: bolder;
}

/**
 * 1. Correct the inheritance and scaling of font size in all browsers.
 * 2. Correct the odd `em` font sizing in all browsers.
 */
:where(code, kbd, pre, samp) {
  font-family: monospace, monospace; /* 1 */
  font-size: 1em; /* 2 */
}

/**
 * Add the correct font size in all browsers.
 */
:where(small) {
  font-size: 80%;
}

/* Tabular data
 * ========================================================================== */

/**
 * 1. Correct table border color in Chrome, Edge, and Safari.
 * 2. Remove text indentation from table contents in Chrome, Edge, and Safari.
 */
:where(table) {
  border-color: currentColor; /* 1 */
  text-indent: 0; /* 2 */
}

/* Forms
 * ========================================================================== */

/**
 * Remove the margin on controls in Safari.
 */
:where(button, input, select) {
  margin: 0;
}

/**
 * Remove the inheritance of text transform in Firefox.
 */
:where(button) {
  text-transform: none;
}

/**
 * Correct the inability to style buttons in iOS and Safari.
 */
:where(button, input:is([type="button" i], [type="reset" i], [type="submit" i])) {
  -webkit-appearance: button;
}

/**
 * Add the correct vertical alignment in Chrome, Edge, and Firefox.
 */
:where(progress) {
  vertical-align: baseline;
}

/**
 * Remove the inheritance of text transform in Firefox.
 */
:where(select) {
  text-transform: none;
}

/**
 * Remove the margin in Firefox and Safari.
 */
:where(textarea) {
  margin: 0;
}

/**
 * 1. Correct the odd appearance in Chrome, Edge, and Safari.
 * 2. Correct the outline style in Safari.
 */
:where(input[type="search" i]) {
  -webkit-appearance: textfield; /* 1 */
  outline-offset: -2px; /* 2 */
}

/**
 * Correct the cursor style of increment and decrement buttons in Safari.
 */
::-webkit-inner-spin-button,
::-webkit-outer-spin-button {
  height: auto;
}

/**
 * Correct the text style of placeholders in Chrome, Edge, and Safari.
 */
::-webkit-input-placeholder {
  color: inherit;
  opacity: 0.54;
}

/**
 * Remove the inner padding in Chrome, Edge, and Safari on macOS.
 */
::-webkit-search-decoration {
  -webkit-appearance: none;
}

/**
 * 1. Correct the inability to style upload buttons in iOS and Safari.
 * 2. Change font properties to `inherit` in Safari.
 */
::-webkit-file-upload-button {
  -webkit-appearance: button; /* 1 */
  font: inherit; /* 2 */
}

/**
 * Remove the inner border and padding of focus outlines in Firefox.
 */
:where(button, input:is([type="button" i], [type="color" i], [type="reset" i], [type="submit" i]))::-moz-focus-inner {
  border-style: none;
  padding: 0;
}

/**
 * Restore the focus outline styles unset by the previous rule in Firefox.
 */
:where(button, input:is([type="button" i], [type="color" i], [type="reset" i], [type="submit" i]))::-moz-focusring {
  outline: 1px dotted ButtonText;
}

/**
 * Remove the additional :invalid styles in Firefox.
 */
:where(:-moz-ui-invalid) {
  box-shadow: none;
}

/* Interactive
 * ========================================================================== */

/*
 * Add the correct styles in Safari.
 */
:where(dialog) {
  background-color: white;
  border: solid;
  color: black;
  height: -moz-fit-content;
  height: fit-content;
  left: 0;
  margin: auto;
  padding: 1em;
  position: absolute;
  right: 0;
  width: -moz-fit-content;
  width: fit-content;
}

:where(dialog:not([open])) {
  display: none;
}

/*
 * Add the correct display in all browsers.
 */
:where(summary) {
  display: list-item;
}
```



### [ress](https://github.com/filipelinhares/ress/blob/master/ress.css)

在 `normalize` 上封装，`Vuetify` 基于这个 `reset` 。

``` bash
npm i -D ress
```

``` css
/* resset.dev • v5.0.2 */

/* # =================================================================
   # Global selectors
   # ================================================================= */

html {
  box-sizing: border-box;
  -webkit-text-size-adjust: 100%; /* Prevent adjustments of font size after orientation changes in iOS */
  word-break: normal;
  -moz-tab-size: 4;
  tab-size: 4;
}

*,
::before,
::after {
  background-repeat: no-repeat; /* Set `background-repeat: no-repeat` to all elements and pseudo elements */
  box-sizing: inherit;
}

::before,
::after {
  text-decoration: inherit; /* Inherit text-decoration and vertical align to ::before and ::after pseudo elements */
  vertical-align: inherit;
}

* {
  padding: 0; /* Reset `padding` and `margin` of all elements */
  margin: 0;
}

/* # =================================================================
   # General elements
   # ================================================================= */

hr {
  overflow: visible; /* Show the overflow in Edge and IE */
  height: 0; /* Add the correct box sizing in Firefox */
  color: inherit; /* Correct border color in Firefox. */
}

details,
main {
  display: block; /* Render the `main` element consistently in IE. */
}

summary {
  display: list-item; /* Add the correct display in all browsers */
}

small {
  font-size: 80%; /* Set font-size to 80% in `small` elements */
}

[hidden] {
  display: none; /* Add the correct display in IE */
}

abbr[title] {
  border-bottom: none; /* Remove the bottom border in Chrome 57 */
  /* Add the correct text decoration in Chrome, Edge, IE, Opera, and Safari */
  text-decoration: underline;
  text-decoration: underline dotted;
}

a {
  background-color: transparent; /* Remove the gray background on active links in IE 10 */
}

a:active,
a:hover {
  outline-width: 0; /* Remove the outline when hovering in all browsers */
}

code,
kbd,
pre,
samp {
  font-family: monospace, monospace; /* Specify the font family of code elements */
}

pre {
  font-size: 1em; /* Correct the odd `em` font sizing in all browsers */
}

b,
strong {
  font-weight: bolder; /* Add the correct font weight in Chrome, Edge, and Safari */
}

/* https://gist.github.com/unruthless/413930 */
sub,
sup {
  font-size: 75%;
  line-height: 0;
  position: relative;
  vertical-align: baseline;
}

sub {
  bottom: -0.25em;
}

sup {
  top: -0.5em;
}

table {
  border-color: inherit; /* Correct border color in all Chrome, Edge, and Safari. */
  text-indent: 0; /* Remove text indentation in Chrome, Edge, and Safari */
}

iframe {
  border-style: none;
}

/* # =================================================================
   # Forms
   # ================================================================= */

input {
  border-radius: 0;
}

[type='number']::-webkit-inner-spin-button,
[type='number']::-webkit-outer-spin-button {
  height: auto; /* Correct the cursor style of increment and decrement buttons in Chrome */
}

[type='search'] {
  -webkit-appearance: textfield; /* Correct the odd appearance in Chrome and Safari */
  outline-offset: -2px; /* Correct the outline style in Safari */
}

[type='search']::-webkit-search-decoration {
  -webkit-appearance: none; /* Remove the inner padding in Chrome and Safari on macOS */
}

textarea {
  overflow: auto; /* Internet Explorer 11+ */
  resize: vertical; /* Specify textarea resizability */
}

button,
input,
optgroup,
select,
textarea {
  font: inherit; /* Specify font inheritance of form elements */
}

optgroup {
  font-weight: bold; /* Restore the font weight unset by the previous rule */
}

button {
  overflow: visible; /* Address `overflow` set to `hidden` in IE 8/9/10/11 */
}

button,
select {
  text-transform: none; /* Firefox 40+, Internet Explorer 11- */
}

/* Apply cursor pointer to button elements */
button,
[type='button'],
[type='reset'],
[type='submit'],
[role='button'] {
  cursor: pointer;
}

/* Remove inner padding and border in Firefox 4+ */
button::-moz-focus-inner,
[type='button']::-moz-focus-inner,
[type='reset']::-moz-focus-inner,
[type='submit']::-moz-focus-inner {
  border-style: none;
  padding: 0;
}

/* Replace focus style removed in the border reset above */
button:-moz-focusring,
[type='button']::-moz-focus-inner,
[type='reset']::-moz-focus-inner,
[type='submit']::-moz-focus-inner {
  outline: 1px dotted ButtonText;
}

button,
html [type='button'], /* Prevent a WebKit bug where (2) destroys native `audio` and `video`controls in Android 4 */
[type='reset'],
[type='submit'] {
  -webkit-appearance: button; /* Correct the inability to style clickable types in iOS */
}

/* Remove the default button styling in all browsers */
button,
input,
select,
textarea {
  background-color: transparent;
  border-style: none;
}

a:focus,
button:focus,
input:focus,
select:focus,
textarea:focus {
  outline-width: 0;
}

/* Style select like a standard input */
select {
  -moz-appearance: none; /* Firefox 36+ */
  -webkit-appearance: none; /* Chrome 41+ */
}

select::-ms-expand {
  display: none; /* Internet Explorer 11+ */
}

select::-ms-value {
  color: currentColor; /* Internet Explorer 11+ */
}

legend {
  border: 0; /* Correct `color` not being inherited in IE 8/9/10/11 */
  color: inherit; /* Correct the color inheritance from `fieldset` elements in IE */
  display: table; /* Correct the text wrapping in Edge and IE */
  max-width: 100%; /* Correct the text wrapping in Edge and IE */
  white-space: normal; /* Correct the text wrapping in Edge and IE */
  max-width: 100%; /* Correct the text wrapping in Edge 18- and IE */
}

::-webkit-file-upload-button {
  /* Correct the inability to style clickable types in iOS and Safari */
  -webkit-appearance: button;
  color: inherit;
  font: inherit; /* Change font properties to `inherit` in Chrome and Safari */
}

/* Replace pointer cursor in disabled elements */
[disabled] {
  cursor: default;
}

/* # =================================================================
   # Specify media element style
   # ================================================================= */

img {
  border-style: none; /* Remove border when inside `a` element in IE 8/9/10 */
}

/* Add the correct vertical alignment in Chrome, Firefox, and Opera */
progress {
  vertical-align: baseline;
}

/* # =================================================================
   # Accessibility
   # ================================================================= */

/* Specify the progress cursor of updating elements */
[aria-busy='true'] {
  cursor: progress;
}

/* Specify the pointer cursor of trigger elements */
[aria-controls] {
  cursor: pointer;
}

/* Specify the unstyled cursor of disabled, not-editable, or otherwise inoperable elements */
[aria-disabled='true'] {
  cursor: default;
}
```



### [custom](https://www.joshwcomeau.com/css/custom-css-reset/)

简易的自定义 reset 。

``` css
/*
  1. Use a more-intuitive box-sizing model.
*/
*, *::before, *::after {
  box-sizing: border-box;
}

/*
  2. Remove default margin
*/
* {
  margin: 0;
}

/*
  Typographic tweaks!
  3. Add accessible line-height
  4. Improve text rendering
*/
body {
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

/*
  5. Improve media defaults
*/
img, picture, video, canvas, svg {
  display: block;
  max-width: 100%;
}

/*
  6. Remove built-in form typography styles
*/
input, button, textarea, select {
  font: inherit;
}

/*
  7. Avoid text overflows
*/
p, h1, h2, h3, h4, h5, h6 {
  overflow-wrap: break-word;
}

/*
  8. Create a root stacking context
*/
#root, #__next {
  isolation: isolate;
}
```



### my custom

根据上个自定义 reset ，自己添加一些常用 reset 。

``` css
html,
body {
  height: 100%;
}

html {
  box-sizing: border-box;

  /* 阻止浏览器自动调整字体大小 */
  -webkit-text-size-adjust: 100%;
}

body {
  /* for h5 */
  padding-top: env(safe-area-inset-top);
  /* 提前声明滚动容器的 `padding-right` 为滚动条宽度，可以防止滚动条的出现导致屏幕抖动 */
  padding-right: calc(100vw - 100% - env(safe-area-inset-right));
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);

  /* -apple-system: use macOs default font first. */
  /* macOs: 'Helvetica Neue', 'PingFang SC' */
  /* windows: 'Microsoft Yahei' */
  /* both macOs and windows: Tahoma, Arial */
  /* linux: 'WenQuanYi Micro Hei' */
  /* callback: sans-serif */
  font-family: -apple-system, 'Helvetica Neue', Tahoma, Arial, 'PingFang SC', 'Microsoft Yahei',
    'WenQuanYi Micro Hei', sans-serif;

  line-height: 1.5;

  /* 渲染字体是否使用抗锯齿。antialiased: 在深色背景时，渲染浅色文字使用抗锯齿，使其看起来更亮。 */
  -webkit-font-smoothing: antialiased;

  /* 微信 h5 上下滑动卡顿 */
  -webkit-overflow-scrolling: touch;
}

*,
*::before,
*::after {
  box-sizing: inherit;
  /* set `background-repeat: no-repeat` to all elements and pseudo elements */
  background-repeat: no-repeat;
}

* {
  /* reset `padding` and `margin` of all elements */
  padding: 0;
  margin: 0;
}

::before,
::after {
  /* inherit text-decoration and vertical align to ::before and ::after pseudo elements */
  text-decoration: inherit;
  vertical-align: inherit;
}

img,
picture,
video,
canvas,
svg {
  display: block;
  max-width: 100%;
}

p,
h1,
h2,
h3,
h4,
h5,
h6 {
  overflow-wrap: break-word;
}

#app {
  /* create a new stacking context without needing to set a z-index. */
  isolation: isolate;
}

```



### 总结

综合以上 4 个 reset 。

``` bash
npm i -D npm i @csstools/normalize.css
```

``` css
@import '@csstools/normalize.css';

html,
body {
  height: 100%;
}

html {
  box-sizing: border-box;
  -moz-tab-size: 4;
  tab-size: 4;
}

body {
  /* for h5 */
  padding-top: env(safe-area-inset-top);
  /* 提前声明滚动容器的 `padding-right` 为滚动条宽度，可以防止滚动条的出现导致屏幕抖动 */
  padding-right: calc(100vw - 100% - env(safe-area-inset-right));
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);

  /* -apple-system: use macOs default font first. */
  /* macOs: 'Helvetica Neue', 'PingFang SC' */
  /* windows: 'Microsoft Yahei' */
  /* both macOs and windows: Tahoma, Arial */
  /* linux: 'WenQuanYi Micro Hei' */
  /* callback: sans-serif */
  font-family: -apple-system, 'Helvetica Neue', Tahoma, Arial, 'PingFang SC', 'Microsoft Yahei',
    'WenQuanYi Micro Hei', sans-serif;
  
  line-height: 1.5;

  /* 渲染字体是否使用抗锯齿。antialiased: 在深色背景时，渲染浅色文字使用抗锯齿，使其看起来更亮。 */
  -webkit-font-smoothing: antialiased;

  /* 微信 h5 上下滑动卡顿 */
  -webkit-overflow-scrolling: touch;
}

*,
::before,
::after {
  box-sizing: inherit;
  /* set `background-repeat: no-repeat` to all elements and pseudo elements */
  background-repeat: no-repeat;
}

* {
  /* reset `padding` and `margin` of all elements */
  padding: 0;
  margin: 0;
}

::before,
::after {
  /* inherit text-decoration and vertical align to ::before and ::after pseudo elements */
  text-decoration: inherit;
  vertical-align: inherit;
}

img,
picture,
video,
canvas,
svg {
  display: block;
  max-width: 100%;
}

p,
h1,
h2,
h3,
h4,
h5,
h6 {
  overflow-wrap: break-word;
}

a:active,
a:hover {
  /* remove the outline when hovering in all browsers */
  outline-width: 0;
}

code,
kbd,
pre,
samp {
  /* specify the font family of code elements */
  font-family: monospace;
}

/* form elements */

/* remove the default button styling in all browsers */
button,
input,
select,
textarea {
  background-color: transparent;
  border-style: none;
}

/* specify font inheritance of form elements */
button,
input,
optgroup,
select,
textarea {
  font: inherit;
}

input {
  border-radius: 0;
}

textarea {
  /* internet Explorer 11+ */
  overflow: auto;

  /* specify textarea resizability */
  resize: vertical;
}

/* style select like a standard input */
select {
  /* Firefox 36+ */
  -moz-appearance: none;

  /* Chrome 41+ */
  -webkit-appearance: none;

  appearance: none;
}

/* apply cursor pointer to button elements */
button,
[type='button'],
[type='reset'],
[type='submit'],
[role='button'] {
  cursor: pointer;
}

[hidden] {
  /* add the correct display in IE */
  display: none;
}

/* accessibility */

/* specify the progress cursor of updating elements */
[aria-busy='true'] {
  cursor: progress;
}

/* specify the pointer cursor of trigger elements */
[aria-controls] {
  cursor: pointer;
}

/* specify the unstyled cursor of disabled, not-editable, or otherwise inoperable elements */
[aria-disabled='true'] {
  cursor: default;
}

#app {
  /* create a new stacking context without needing to set a z-index. */
  isolation: isolate;
}

```



## 属性排序（ Order ）

影响页面布局的放在前面，不影响的放在后面。

- `position`
- `display` and box model
- `font`, leading, `color`, text
- `background` and `border`
- CSS3 properties like `border-radius` and `box-shadow`
- and a handful of other purely visual properties



## BEM

**Block, Elements and Modifiers**

**BEM** 是一个简单又非常有用的命名约定。让你的前端代码更容易阅读和理解，更容易协作，更容易控制，更加健壮和明确，而且更加严密。

可以获得更多的描述和更加清晰的结构，从其名字可以知道某个标记的含义。通过查看 HTML 代码中的 class 属性，就能知道元素之间的关联。



### 结构

`.Block__Elements--Modifiers`

- Block - 块级元素。
- Element - 元素。
- Modifiers - 修饰符。
- -（中划线）- 仅作为连字符使用。
- __（双下划线） - 连接块和其子元素。
- --（双中划线） - 连接修饰符，表示状态。



### 使用

- 名称可以包含小写拉丁字母、数字、中划线（ - ），长名称中空格用中划线代替。
- `Modifiers` 可以使用短划线区分 key 和 value（ e.g `.Block__Elements--size-s` ）。

- 使用 BEM 不应该再使用 **CSS 标签选择器** 和 **ID 选择器** 。
- 模块可以嵌套。
- 避免 .Block__ el1 __el2 。**这有悖BEM命名规范，BEM的命名中只包含三个部分，元素名只占其中一部分，所以不能出现多个元素名的情况**。在深层次嵌套的 DOM 结构下，**应避免过长的样式名称定义**，如果过长，说明可以拆分成更小的模块。



## font weight

推荐使用字重描述符而不是数字：

- `font-weight` 不是告诉浏览器用多高的字重来绘制文字，而是让浏览器在 `font-family` 字符集中匹配符合字重要求的字体；
- 通常情况下，一个字体只会包含少数可用的字重，如果存在的字重没有可以直接匹配 `font-weight` 的，则会通过算法规则匹配邻近的可用字重。



- 100 - Thin
- 200 - Extra Light (Ultra Light)
- 300 - Light
- 400 - Normal
- 500 - Medium
- 600 - Semi Bold (Demi Bold)
- 700 - Bold
- 800 - Extra Bold (Ultra Bold)
- 900 - Black (Heavy)



**总结**：

中文网站极少使用 `normal` / `bold` 以外的字重，实在没有必要写数字。

使用其它 font-weight 值，如果字体包不支持，则会跟预期效果有所偏差。



> `bolder` 、 `lighter` 表示其字重值是基于从父元素继承的字重计算所得，与 `normal` 、`bold` 所代表的字重无关系。



## 滚动条

- **Webkit**

  - `::-webkit-scrollbar` ：整个滚动条。

  - `::-webkit-scrollbar-track` ：滚动条的滚动区域（轨道）。

  - `::-webkit-scrollbar-thumb` ：滚动条的可拖拽部分（滑块）。

  - `::-webkit-scrollbar-button` ：滚动条两端的上下/（或左右）按钮。

  - `::-webkit-scrollbar-track-piece` ：滚动条轨道未被滑块覆盖的部分。

  - `::-webkit-scrollbar-corner` ：垂直滚动条和水平滚动条交汇的部分。

- **自定滚动条**

  流行开源库：
  
  - [SimpleBar](https://github.com/Grsmto/simplebar)
  - [Overlay Scrollbars](https://github.com/KingSora/OverlayScrollbars)



## 文字过长省略

父元素需要设置宽度。

```css
/* 单行文字省略 */
p {
  white-space: nowrap
  overflow: hidden;
  text-overflow: ellipsis;
}


/* 超过 2 行文字后省略 */
p {
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
```



## 移动端字体自动变大

```css
/* 移动端内容过多时字体会自动变大 */
/* 
  这是webkit内核移动浏览器特性导致的，这个特性被称做「Text Autosizer」，又称「Font Boosting」、「Font Inflation」，是 Webkit 给移动端浏览器提供的一个特性：当我们在手机上浏览网页时，很可能因为原始页面宽度较大，在手机屏幕上缩小后就看不清其中的文字了。而 Font Boosting 特性在这时会自动将其中的文字字体变大，保证在即不需要左右滑动屏幕，也不需要双击放大屏幕内容的前提下，也可以让人们方便的阅读页面中的文本。 */
/* 设置该属性可以解决 */
html {
	-webkit-text-size-adjust: none; 
}
```



## 清除 select 元素默认样式

``` css
select {
  /* Firefox 36+ */
  -moz-appearance: none;
  
  /* Chrome 41+ */
  -webkit-appearance: none;
  
  appearance: none;
}
```



## background 简写

``` css
<final-bg-layer> =
    <\'background-color'> ||
    <bg-image> ||
    <bg-position> [ / <bg-size> ]? ||
    <repeat-style> ||
    <attachment> ||
    <box> ||
    <box>
```

- `background-image`: 设置背景图像, 可以是真实的图片路径, 也可以是创建的渐变背景;
- `background-color`: 指定背景颜色；
- `background-position`: 设置背景图像的位置;
- `background-size`: 设置背景图像的大小;
- `background-repeat`: 指定背景图像的铺排方式;
- `background-attachment`: 指定背景图像是滚动还是固定;
- `background-origin`: 设置背景图像显示的原点（ `background-position`相对定位的原点 ）;
- `background-clip`: 设置背景图像向外剪裁的区域。

> Tips:
>
> - `background-position` 和 `background-size` 属性，之间需使用 `/ `分隔，且 `background-position` 值在前，`background-size` 值在后。
> - 如果同时使用 `background-origin` 和 `background-clip` 属性, `origin` 属性值需在 `clip` 属性值之前, 如果 `origin` 与 `clip` 属性值相同, 则可只设置一个值。



## animation 简写

``` css
<single-animation> =
    <time> ||
    <timing-function> ||
    <time> ||
    <single-animation-iteration-count> ||
    <single-animation-direction> ||
    <single-animation-fill-mode> ||
    <single-animation-play-state> ||
    [ none | <keyframes-name> ]
```

