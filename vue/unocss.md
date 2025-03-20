# UnoCSS

环境：Vite + Vue3



## 安装引入

``` bash
npm install -D unocss
```

``` typescript
// vite.config.ts
import UnoCSS from 'unocss/vite'
import { defineConfig } from 'vite'

export default defineConfig({
  plugins: [
    UnoCSS()
  ]
})
```

``` typescript
// uno.config.ts
import { defineConfig } from 'unocss'

export default defineConfig({
  // ...UnoCSS options
})
```

``` typescript
// main.ts
import 'virtual:uno.css'
```



## typescript

默认不检测 js, typescript ，需手动开启。

``` typescript
// uno.config.ts
export default defineConfig({
  content: {
    pipeline: {
      include: [
        // the default
        /\.(vue|svelte|[jt]sx|mdx?|astro|elm|php|phtml|html)($|\?)/,
        // include js/ts files
        'src/**/*.{js,ts}',
      ]
      // exclude files
      // exclude: []
    }
  }
})
```

或者添加 `@unocss-include` 注释：

``` typescript
// ./some-utils.ts

// since `.ts` files are not included by default,
// the following comment tells UnoCSS to force scan this file.
// @unocss-include
export const classes = {
  active: 'bg-primary text-white',
  inactive: 'bg-gray-200 text-gray-500'
}
```



## directives

支持 `@apply` , `@screen` , `theme()` 等指令。

> 目前还是实验性，可能未来会引入破坏性更改。



配置：

``` typescript
// uno.config.ts
import { defineConfig, transformerDirectives } from 'unocss'

export default defineConfig({
  //...
  transformers: [
    transformerDirectives()
  ]
})
```



### @unocss

占位符，会被替换成对应 layer 的 CSS 。

``` css
/* style.css */
@unocss preflights;
@unocss default;

/*
  Fallback layer. It's always recommended to include.
  Only unused layers will be injected here.
*/
@unocss;
```

> If you want to include all layers no matter whether they are previously included or not, you can use `@unocss all`. This is useful if you want to include generated CSS in multiple files.



### @apply

在 CSS 中应用 unocss 的 utilities 。

``` css
.custom-div {
  /* @apply text-center my-0 font-medium; */
  /* 推荐使用下面这种，否则编译器会警告找不到 @apply */
  --at-apply: text-center my-0 font-medium;
}

/* will be transformed to */
.custom-div {
  margin-top: 0rem;
  margin-bottom: 0rem;
  text-align: center;
  font-weight: 500;
}
```



### @screen

新增媒体查询。

``` css
.grid {
  @apply grid grid-cols-2;
}
@screen xs {
  .grid {
    @apply grid-cols-1;
  }
}
@screen sm {
  .grid {
    @apply grid-cols-3;
  }
}

/* will be transformed to */
.grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
}
@media (min-width: 320px) {
  .grid {
    grid-template-columns: repeat(1, minmax(0, 1fr));
  }
}
@media (min-width: 640px) {
  .grid {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}
```



### theme()

接收在 theme config 定义的值。

``` css
.btn-blue {
  background-color: theme('colors.blue.500');
}

/* will be transformed to */
.btn-blue {
  background-color: #3b82f6;
}
```



## configuration

``` typescript
// uno.config.ts
import { defineConfig } from 'unocss'

export default defineConfig({
  // ...UnoCSS options
})
```

> 建议使用 uno.config.ts ，对编译器的代码提示友好。



### rules

可以自定义 unocss utilities 。

- **static rules**

  ``` typescript
  rules: [
    ['m-1', { margin: '0.25rem' }]
  ]
  ```

  will be generated:

  ``` css
  .m-1 { margin: 0.25rem; }
  ```

  > 应该遵循 CSS 的属性命名格式。e.g. 使用 `font-weight` 而不是 `fontWeight` 。

- **dynamic rules**

  使用正则进行动态匹配。

  ``` typescript
  rules: [
    [/^m-(\d+)$/, ([, d]) => ({ margin: `${d / 4}rem` })],
    [/^p-(\d+)$/, match => ({ padding: `${match[1] / 4}rem` })]
  ]
  ```

  使用：

  ``` html
  <div class="m-100">
    <button class="m-3">
      <icon class="p-5" />
      My Button
    </button>
  </div>
  ```

  will be generated:

  ``` css
  .m-100 { margin: 25rem; }
  .m-3 { margin: 0.75rem; }
  .p-5 { padding: 1.25rem; }
  ```

- **order**

  unocss 尊重用户定义 rules 的顺序，写在后面的 rule 生成的 CSS 优先级更高。

  而 dynamic rules 可以匹配多个 rules ，默认会按字母排序。



### shortcuts

可以将多个 rules 合并成一个缩写。

普通映射：

``` typescript
shortcuts: {
  // shortcuts to multiple utilities
  'btn': 'py-2 px-4 font-semibold rounded-lg shadow-md',
  'btn-green': 'text-white bg-green-500 hover:bg-green-700',
  // single utility alias
  'red': 'text-red-100'
}
```

\+ 动态映射：

``` typescript
shortcuts: [
  // you could still have object style
  {
    btn: 'py-2 px-4 font-semibold rounded-lg shadow-md',
  },
  // dynamic shortcuts
  [/^btn-(.*)$/, ([, c]) => `bg-${c}-400 text-${c}-100 py-2 px-4 rounded-lg`]
]
```



### theme

可以像 `Tailwind CSS` 等一样配置自己的 theme 参数属性，配置完深度递归和默认配置合并。

``` typescript
theme: {
  // ...
  colors: {
    'veryCool': '#0000ff', // class="text-very-cool"
    'brand': {
      'primary': 'hsl(var(--hue, 217) 78% 51%)', //class="bg-brand-primary"
    }
  }
}
```



- 在 rules 中使用：

  ``` typescript
  rules: [
    [/^text-(.*)$/, ([, c], { theme }) => {
      if (theme.colors[c])
        return { color: theme.colors[c] }
    }]
  ]
  ```

- 在 variants 中使用：

  ``` typescript
  variants: [
    {
      name: 'variant-name',
      match(matcher, { theme }) {
        // ...
      }
    }
  ]
  ```

- 在 shortcuts 中使用：

  ``` typescript
  shortcuts: [
    [/^badge-(.*)$/, ([, c], { theme }) => {
      if (Object.keys(theme.colors).includes(c))
        return `bg-${c}4:10 text-${c}5 rounded`
    }]
  ]
  ```



设置 breakpoints ：

``` typescript
theme: {
  // ...
  breakpoints: {
    sm: '320px',
    // Because uno does not support comparison sorting of different unit sizes, please convert to the same unit.
    // md: '40rem',
    md: `${40 * 16}px`,
    lg: '960px'
  }
}
```

> breakpoints 不会进行合并，而是直接替换，比如上方的例子，定义后就只能使用定义的这 3 个 breakpoints 了。
>
> breakpoints 必须使用统一单位，如果要使用其它单位需进行换算。
>
> `verticalBreakpoints` 的规则同 breakpoints ，只不过是应用于 vertical layout 。



### variants

可以给已有 rules 添加一些变体，比如 `hover` 等。

``` typescript
variants: [
  // hover:
  (matcher) => {
    if (!matcher.startsWith('hover:'))
      return matcher
    return {
      // slice `hover:` prefix and passed to the next variants and rules
      matcher: matcher.slice(6),
      selector: s => `${s}:hover`
    }
  }
],
rules: [
  [/^m-(\d)$/, ([, d]) => ({ margin: `${d / 4}rem` })]
]
```

`controls` : 控制 variant 何时启用。如果返回一个 string ，会被作为匹配该 rules 的 selector ；

`selector` : 提供生成自定义 CSS 选择器的能力。



hook 匹配 `hover:m-2` 执行的流程：

- 从用户的使用中提取到了 `hover:m-2` ；
- 将 `hover:m-2` 与所有的 variants 进行匹配；
- `hover:m-2` 匹配成功并返回了 `m-2` ；
- `m-2` 会进入下一轮 variants 的匹配；
- 如果没有再被 variants 匹配上，就会与 rules 进行匹配；
- 匹配上 rule 后生成 `.m-2 { margin: 0.5rem }` ；
- 最后，将生成的 CSS 用 variants 的 transformation 进行转换。

在这个例子中，最终生成：

``` css
.hover\:m-2:hover { margin: 0.5rem; }
```



### extractors

提取器，在源码中提取使用的 utilities 。

``` typescript
// uno.config.ts
import { defineConfig } from 'unocss'

export default defineConfig({
  extractors: [
    // your extractors
  ]
})
```



### transformers

转换器，用于将源码转换成符合 UnoCSS 约定。

``` typescript
// my-transformer.ts
import { createFilter } from '@rollup/pluginutils'
import { SourceCodeTransformer } from 'unocss'

export default function myTransformers(options: MyOptions = {}): SourceCodeTransformer {
  return {
    name: 'my-transformer',
    enforce: 'pre', // enforce before other transformers
    idFilter(id) {
      // only transform .tsx and .jsx files
      return id.match(/\.[tj]sx$/)
    },
    async transform(code, id, { uno }) {
      // code is a MagicString instance
      code.appendRight(0, '/* my transformer */')
    }
  }
}
```



### preflights

You can inject raw CSS as preflights from the configuration. The resolved `theme` is available to customize the CSS.

``` typescript
preflights: [
  {
    getCSS: ({ theme }) => `
      * {
        color: ${theme.colors.gray?.[700] ?? '#333'};
        padding: 0;
        margin: 0;
      }
    `
  }
]
```



### layers

CSS 的顺序会影响优先级，虽然 CSS 会按配置 rules 的顺序，但是有些时候你会想将一些 utilities 分类显示地控制优先级。

不像 Tailwind CSS 只提供三个固定的 layers ，UnoCSS 允许你自己定义你想要的 layers 。可以通过在 rules 中传入第三个参数启用。

``` typescript
rules: [
  [/^m-(\d)$/, ([, d]) => ({ margin: `${d / 4}rem` }), { layer: 'utilities' }],
  // when you omit the layer, it will be `default`
  ['btn', { padding: '4px' }]
]
```

will generate : 

``` css
/* layer: default */
.btn { padding: 4px; }
/* layer: utilities */
.m-2 { margin: 0.5rem; }
```



也可以在 preflight 中使用: 

``` typescript
preflights: [
  {
    layer: 'my-layer',
    getCSS: async () => (await fetch('my-style.css')).text()
  }
]
```



ordering:

``` typescript
layers: {
  'components': -1,
  'default': 1,
  'utilities': 2,
  'my-layer': 3
}
```

如果不配置的话，会按字母排序。



在 layer 之间引入自定义 CSS 。

``` typescript
// 'uno:[layer-name].css'
import 'uno:components.css'

// layers that are not 'components' and 'utilities' will fallback to here
import 'uno.css'

// your own CSS
import './my-custom.css'

// "utilities" layer will have the highest priority
import 'uno:utilities.css'
```



级联 layers :

开启 `outputToCssLayers: true` ；

然后修改 layers name ：

``` typescript
outputToCssLayers: (layer) => {
  // The default layer will be output to the "utilities" CSS layer.
  if (layer === 'default')
    return 'utilities'

  // The shortcuts layer will be output to the "shortcuts" sublayer the of "utilities" CSS layer.
  if (layer === 'shortcuts')
    return 'utilities.shortcuts'

  // All other layer will just use their name as the CSS layer name.
}
```



### autocomplete

可以为 VS Code 等的 UnoCSS 插件等提供我们自定义的自动补全代码提示。



### presets

预设部分配置，会合并到主配置中。

[official presets](https://unocss.dev/presets/)



## style reset

``` bash
npm install @unocss/reset
```

在 `main` 文件中引入。

可以使用自己 reset ，具体看 CSS 笔记。



## my config

``` typescript
import { defineConfig, presetUno, presetAttributify } from 'unocss'

export default defineConfig({
  presets: [
    // default 的配置已经兼容了 tailwind 、wind 等
    presetUno(),
    // unocss 默认集成了 presetAttributify 
    // 即可以使用属性的方式定义样式 bg="blue-400 hover:blue-500 dark:blue-500 dark:hover:blue-600"
    // 可能会与一些 UI 框架的组件属性冲突，所以使用强制使用 presetAttributify 必须添加前缀 'un-'
    presetAttributify({
      // 这个是默认配置
      // prefix: 'un-',
      prefixedOnly: true
    })
  ],
  content: {
    // 需要支持 .js .ts 时添加
    // pipeline: {
    //   include: [/\.(vue|svelte|[jt]sx|mdx?|astro|elm|php|phtml|html)($|\?)/, 'src/**/*.{js,ts}']
    // }
  }
})

```



## 忽略/单独扫描文件

如果不想扫描所有 *.{js,ts} 文件，可以通过加 comment `@unocss-include` 让 unocss 扫描对应文件：

``` js
// ./some-utils.js

// since `.js` files are not included by default,
// the following comment tells UnoCSS to force scan this file.
// @unocss-include
export const classes = {
  active: 'bg-primary text-white',
  inactive: 'bg-gray-200 text-gray-500',
}

```

添加 comment `@unocss-ignore` 可以让 unocss 忽略整个文件。

添加 comment `@unocss-skip-start` `@unocss-skip-end` 只单独扫描某一块。

``` html
<p class="text-green text-xl">
  Green Large
</p>

<!-- @unocss-skip-start -->
<!-- `text-red` will not be extracted -->
<p class="text-red">
  Red
</p>
<!-- @unocss-skip-end -->

```



## 合并配置

例如在 `monorepo` 中，根目录添加公共配置：

``` typescript
// uno.config.ts
/**
 * Base uno config and for @unocss/eslint-config
 */
import { defineConfig, presetUno, presetAttributify, transformerDirectives } from 'unocss'

export default defineConfig({
  presets: [
    presetUno(),
    presetAttributify({
      prefixedOnly: true
    })
  ],
  transformers: [
    transformerDirectives()
  ]
})

```

在子项目中合并文件：

``` typescript
// 子项目 uno.config.ts
import { mergeConfigs } from '@unocss/core'
import config from '../../uno.config'

export default mergeConfigs([config, {
  // your overrides
}])

```

> 注意：两个文件必须都在 tsconfig include 中。





