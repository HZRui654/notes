# Vue3

## 创建项目

``` bash
npm create vue@latest
```



## 自动引入组件

[`unplugin-vue-components`](https://www.npmjs.com/package/unplugin-vue-components) ：可以自动引入组件，并且已经集成了市面上很多常见的组件库。

``` bash
npm install unplugin-vue-components -D

```

``` typescript
// vite.config.ts
import { defineConfig } from 'vite'
import Components from 'unplugin-vue-components/vite'
import { Vuetify3Resolver } from 'unplugin-vue-components/resolvers'

export default defineConfig(() => {
  plugins: [
    Components({
      // 生成 components 的全局声明
      // false / true / path
      // 默认 false ，检测到使用了 Ts 后默认为 true
      // 可以传入全局声明的路径名 path ，默认为根目录下的 components.d.ts
      dts: 'types/components.d.ts',
      // 自动引入第三方组件库的组件
    	resolvers: [
        Vuetify3Resolver()
      ]
    }),
  ]
})
```

> monorepo 的项目中，需要把第三方 UI 库装在根目录，否则会出现自动引入的第三方 UI 组件类型为 unknown 的情况。



## SCSS 全局变量

`vite.config.ts`

``` tsx
import { defineConfig } from 'vite'

export default defineConfig(() => {
  css: {
    preprocessorOptions: {
      scss: {
        // 会在每个文件中引入
        additionalData: '@import "./src/assets/scss/bem.scss";'
      }
    }
  }
})
```



## 打包优化

`npm install terse -D`

`vite.config.ts`

``` tsx
import { defineConfig } from 'vite'

export default defineConfig(() => {
  build: {
    chunkSizeWarningLimit: 2048, // 文件体积大于 2000 再进行 warn 报警
    cssCodeSplit: true, // CSS 拆分
    sourcemap: false, // 不生成 sourcemap
    minify: 'terser', // 最小化混淆，'esbuild' 打包速度最快，'terser' 打包体积最小， false 禁用
    assetsInlineLimit: 4096 // 小于该值的图片将打包成 Base64
  }
})

```
