# CSS

## reset

``` css
*, *::before, *::after {
  box-sizing: border-box;
}

* {
  margin: 0;
}

html,
body {
  height: 100%;
}

body {
  /* -apple-system: use macOs default font first. */
  /* macOs: 'Helvetica Neue', 'PingFang SC' */
  /* windows: 'Microsoft Yahei' */
  /* both macOs and windows: Tahoma, Arial */
  /* linux: 'WenQuanYi Micro Hei' */
  /* callback: sans-serif */
  font-family: -apple-system, 'Helvetica Neue', Tahoma, Arial, 'PingFang SC', 'Microsoft Yahei', 'WenQuanYi Micro Hei', sans-serif;
  line-height: 1.5;
	
  /* 渲染字体是否使用抗锯齿。antialiased: 在深色背景时，渲染浅色文字使用抗锯齿，使其看起来更亮。 */
  -webkit-font-smoothing: antialiased;

  /* 阻止浏览器自动调整字体大小 */
  -webkit-text-size-adjust: 100%;
  
  /* for h5 */
  padding-top: env(safe-area-inset-top);
  /* 提前声明滚动容器的 `padding-right` 为滚动条宽度，可以防止滚动条的出现导致屏幕抖动 */
  padding-right: calc(100vw - 100% - env(safe-area-inset-right));
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
  
  /* 微信 h5 上下滑动卡顿 */
  -webkit-overflow-scrolling: touch;
}

img, picture, video, canvas, svg {
  display: block;
  max-width: 100%;
}

input, button, textarea, select {
  font: inherit;
}

p, h1, h2, h3, h4, h5, h6 {
  overflow-wrap: break-word;
}

#app {
  /* create a new stacking context without needing to set a z-index. */
  isolation: isolate;
}
```



## 属性排序（ Order ）

- `position`
- `display` and box model
- `font`, leading, `color`, text
- `background` and `border`
- CSS3 properties like `border-radius` and `box-shadow`
- and a handful of other purely visual properties



## 滚动条

- **Webkit**

  > - `::-webkit-scrollbar` ：整个滚动条。
  > - `::-webkit-scrollbar-track` ：滚动条的滚动区域（轨道）。
  > - `::-webkit-scrollbar-thumb` ：滚动条的可拖拽部分（滑块）。
  > - `::-webkit-scrollbar-button` ：滚动条两端的上下/（或左右）按钮。
  > - `::-webkit-scrollbar-track-piece` ：滚动条轨道未被滑块覆盖的部分。
  > - `::-webkit-scrollbar-corner` ：垂直滚动条和水平滚动条交汇的部分。

- **自定滚动条**

  >流行开源库：
  >
  >- [SimpleBar](https://github.com/Grsmto/simplebar)
  >- [Overlay Scrollbars](https://github.com/KingSora/OverlayScrollbars)



## 文字过长省略

父元素需要设置宽度。

```css
/* 单行文字省略 */
p {
  width: 100%;
  overflow: hidden;
  text-overflow: ellipsis;
}


/* 超过 2 行文字后省略 */
p {
  width: 100%;
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
-webkit-text-size-adjust: none;
```



## 清除 select 元素默认样式

``` css
select {
  appearance: none;
}
```



## 移动端刘海、边缘和底部适配

`meta` 标签的 `content` 添加 `viewport-fit=cover`

``` html
<meta name='viewport' content='initial-scale=1, viewport-fit=cover'>
```

使用 CSS 的 `env`

``` scss
body {
  padding-top: env(safe-area-inset-top);
  /* 提前声明滚动容器的 `padding-right` 为滚动条宽度，可以防止滚动条的出现导致屏幕抖动 */
  padding-right: calc(100vw - 100% - env(safe-area-inset-right));
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
}
```

