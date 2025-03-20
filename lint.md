**实际上任何前端项目都可以按照这样的方法实现代码风格的规范化**，`monorepo` 也可用。



## ESLint

1. `ESLint` 如何理解代码？

   `parser` 和 `parserOptions` 选项与 `ESLint` 如何理解我们的代码相关。分析器负责解析对应语言，将代码转化为 AST 语法树，便于进行分析。而 `parserOptions` 可以对解析器的能力进行详细设置。

2. `ESLint` 如何判断代码是否符合规范？

   `ESLint` 提供了 [自定义规则](https://eslint.org/docs/latest/extend/custom-rule-tutorial) 的接口，开发者需要遵照接口，根据分析器的 AST 产物，实现规范检查逻辑，再将实现的多条规范聚合为 `plugin` 插件的形式。`plugin` 字段指定了 `ESLint` 应用什么规则集，具有理解哪些规范的能力。

3. 规则的启用与禁用：

   有了规则集，能够理解规范，不代表 `ESLint` 就要对不规范的内容做出响应，还需要进一步在 `rules` 字段中对这些规则进行开启或者关闭的声明，只有开启的规则才会生效。

4. 继承已有配置：

   面对琳琅满目的规则集，我们完全在项目中配置是不可取的。因此社区逐渐演进出了许多配置预设，让我们可以一键继承，从而减少绝大多数手动配置的工作量。

5. 配置重写：

   如果我们希望某些文件应用一些独特的配置，可以使用 `overrides` 字段实现。`overrides` 的每个成员对象都需要指定目标文件，除了应用所有父级配置之外，还要应用成员对象中声明的独有配置。`ESLint` 支持文件级别的重写。



相关配置：

- **`env`** 节点：告诉 `eslint` 代码是运行在哪些环境中（因为一般不允许使用**未在页面内声明的成员**）

  ``` js
  "env": {
    // 浏览器环境，可以使用 window / document 等
    "browser": true
  }
  ```

- **`globals`** 节点：定义全局变量，语法检查的时候不会报错。

  ``` js
  "glabals": {
    // true / false 控制 readOnly ，为 false 时表示 readOnly
    "$": true
  }
  ```

- **`extends`** 节点：检查时使用的规范，可以配置使用内置或者第三方规范。

  ``` js
  "extends": [
    // 下载的第三方 eslint-config-standard , 可以将前面的省略
    "standard"
  ]
  ```

- **`parserOptions`** 节点：eslint 解析器解析代码时指定的 js 版本。

  ``` js
  "parserOptions": {
    "ecmaVersion": 12
  }
  ```

- **`rules`** 节点：不使用 `extends` 节点中的规范，而是自己设计。或者覆盖 `extends` 中的部分规则。



### 规则集选型

`ESLint` 已经有了很成熟的规则集，常用：

1. **Airbnb 规则集：`eslint-config-airbnb`**

   最严格，强制使用分号，单引号，尾随逗号，适合需要高代码质量和一致性的项目。

2. **Google 规则集：`eslint-config-google`**

   较严格，强制使用分号，双引号，不推荐尾随逗号，适合需要清晰可读代码的项目。

3. **Standard 规则集：`eslint-config-standard`**

   最宽松，不使用分号，单引号，不推荐尾随逗号，适合需要简洁快速开发的项目。

这里选择使用 **Standard 规则集**。



### 依赖安装

首先安装核心工具 `ESLint` ：

```bash
pnpm i -wD eslint

```

编写配置文件时提供类型支持：

``` bash
pnpm i -wD eslint-define-config

```

Standard 规则集：

``` bash
pnpm i -wD eslint-config-standard

```

解析 `Typescript` 的能力：

```bash
pnpm i -wD @typescript-eslint/eslint-plugin @typescript-eslint/parser

```

解析 `Vue` 的能力：

``` bash
pnpm i -wD eslint-plugin-vue vue-eslint-parser

```

解析 `Unocss` 的能力：

``` bash
pnpm i -wD @unocss/eslint-config

```

需要在根目录提供 `uno.config.ts` 文件，否则会报错。



### 配置

根目录新建 `.eslintrc.js` 文件，作为 `ESLint` 的配置文件：

> - 选择生成的配置文件格式最好是 `js` ，因为 `eslint` 查询配置文件有优先级 `js` > `yaml` > `json` 。
>
> - 选择配置文件 module type 的时候根据自己项目的打包工具进行选择，`webpack` 一般是 `CommonJS` , `vite` 一般是 `Javascript modules(import / export)`

``` js
// .eslintrc.js
const { defineConfig } = require('eslint-define-config') // eslint-disable-line

module.exports = defineConfig({
  // 将浏览器 API、ES API 和 Node API 看做全局变量，不会被特定的规则(如 no-undef)限制。
  env: {
    browser: true,
    es2022: true,
    node: true
  },
  // 设置自定义全局变量，不会被特定的规则(如 no-undef)限制。
  globals: {
    // 假如我们希望 jquery 的全局变量不被限制，就按照如下方式声明。
    // $: 'readonly',
  },
  extends: [
    'standard',
    '@unocss',
    'plugin:vue/vue3-recommended',
    'plugin:@typescript-eslint/recommended'
  ],
  // 虽然 extends 插件提供额外的规则和功能，为了确保 ESLint 能够识别和使用这些规则，在 plugins 中显式声明插件。
  plugins: [
    '@typescript-eslint',
    'vue'
  ],
  /**
   * 指定 vue 解析器
   * 使用 'plugin:vue/vue3-recommended' extend 时就已经引入了 parser 'vue-eslint-parser',
   * 但是使用 'plugin:@typescript-eslint/recommended' extend 会再引入 parser '@typescript-eslint/parser' 覆盖掉 vue 的 parser，
   * 根据 eslint-plugin-vue 的官网可知需要将其他的 parser 放入 parserOptions 中。
   * 两个 parer 的区别在于：
   * 外面的 parser 用于解析 .vue 后缀文件，使得 eslint 可以解析 template 中的内容，而 paserOptions 中的 parser，即 @typescript-eslint/parser 用来解析 .vue 后缀文件中 scripte 中的内容。
   */
  parser: 'vue-eslint-parser',
  parserOptions: {
    // 配置 TypeScript 解析器
    parser: '@typescript-eslint/parser',
    // 支持的 ecmaVersion 版本
    ecmaVersion: 'latest',
    // 我们主要使用 esm，设置为 module
    sourceType: 'module',
    // TypeScript 解析器需要一个 tsconfig 文件来确认解析范围
    project: ['./tsconfig.eslint.json'],
    // 告诉 ESLint 解析器哪些文件扩展名是额外的文件类型, TypeScript 解析器需要这个配置
    extraFileExtensions: ['.vue']
  },
  rules: {
    // Typescript 已有类型检查，所以将这个关掉。
    'no-undef': 'off',
    // function 的括号前不要用空格
    'space-before-function-paren': ['error', 'never'],
    // 没有内容的 HTML 标签要求自闭。
    'vue/html-self-closing': ['error', {
        html: {
          normal: 'never',
          void: 'always'
        }
      }
    ],
    // vue 文件名要求至少两个单词小驼峰格式。
    'vue/multi-word-component-names': ['error', {
        ignores: ['index']
      }
    ],
    'vue/max-attributes-per-line': ['error', {
        singleline: 3, // 在单行元素中每行最多 3 个属性
        multiline: {
          max: 1 // 在多行元素中每行最多 1 个属性，默认为 1 
        }
        // multiline: 1 也可以使用这种格式
      }
    ],
    // 可以使用 any type。
    // '@typescript-eslint/no-explicit-any': 'off',
    // 可以使用 @ts-ignore 等注释。
    '@typescript-eslint/ban-ts-comment': 'off',
    // 当使用的类在 unocss 黑名单中时进行警告
    // "@unocss/<rule-name>": "warn", // or "error"
  }
})

```

`tsconfig.eslint.json` 用于 `Typescript` 解析器解析：

``` json
{
  "extends": "./tsconfig.base.json",
  "compilerOptions": {
    "noEmit": true
  },
  "include": ["**/*", "**/*", "**/*.*.*"],
  "exclude": ["**/node_modules", "**/dist"]
}

```

`package.json` 中添加 script ：

``` json
{
  // ...
  "script": {
    // ...
    // . => 要检查的文件路径
    // --ext .js,.jsx,.ts,.tsx,.vue => 默认只检查 .js 文件，添加拓展
    // --fix => 检查后进行修复
    "lint:eslint": "eslint . --ext .js,.jsx,.ts,.tsx,.vue --fix"
  }
}
```

`.eslintignore` 忽略文件：

``` 
node_modules
dist

.github
.husky
*-lock.yaml
*-lock.json
!.eslintrc.js
!.stylelintrc.js
!.prettierrc.js
!.lintstagedrc.js
!.commitlintrc.js

```

> **`ESLint` 默认忽略对 `.` 开头文件的检查**，所以对于这些文件都需要用 `!` 反向声明忽略。



可以通过以下命令输出此时 `ESLint` 配置所检查的文件列表：

``` bash
pnpm eslint . --ext .js,.jsx,.ts,.tsx,.vue --format json > eslint-file-list.json

```



### 忽略规则

如果需要单独关闭对某个位置的检查：

- `<!-- eslint-disable-eslint 规则 -->` ：在模版中关闭对应 eslint 规则的检查。
- `// eslint-disable-line`：放在某一行之后，禁用对该行检查。
- `// eslint-disable-next-line` ：放在某一行之上，禁用对该行检查。
- `/* eslint-disable 规则 */` ：放在 `.js` 文件中的任何代码之前，这将禁用整个文件的对应规则。
- `/* eslint-disable */` ：置于文件顶部来禁用所有 ESLint 规则。



## Stylelint

与 `ESLint` 一致，不过是 `CSS` 领域的。



### `CSS` 属性排序

- `position`
- `display` and box model
- `font`, leading, `color`, text
- `background` and `border`
- CSS3 properties like `border-radius` and `box-shadow`
- and a handful of other purely visual properties



### 适配 BEM 规则

``` json
const nameRegExpString = '[a-z0-9]+(-[a-z0-9]+)*'

module.exports = {
  rules: [
    // 使用注释指定 block 名称
    'plugin/selector-bem-pattern': {
      // 'suit' / 'bem', 不管哪种都需要手动指定，因为该插件未给源插件默认指定
      preset: 'bem',
			
    	// 默认 '^[-_a-zA-Z0-9]+$'
    	componentName: `^${nameRegExpString}$`,
    
    	/**
       * 自定义模式规则
       * 指定组合的选择器检查规则，其实就是指定class名称规则
       * 支持正则字符串、返回正则的函数、包含2个选项参数的对象等写法
       */
    	componentSelectors: {
        // 只初始的选择器规则（可以理解为外层的class规则）
        initial: `^\\.{componentName}(?:__${nameRegExpString})?(?:--${nameRegExpString})?$`,
        // 可以理解为外层以内的选择器的规则
        // 如果不指定，则跟 initial 同样的规则
        combined: `^\\.{componentName}(((?:__${nameRegExpString})(?:--${nameRegExpString})?)|(?:--${nameRegExpString}))$`
    	},
 
      // utils 类名
      utilitySelectors: '^\\.u-[a-zA-z]+$',
    
			// 忽略的选择器
      ignoreSelectors: ['^\\.van'],
  
  		// 忽略的自定义属性
      ignoreCustomProperties: []
    },

    // 解决和 'plugin/selector-bem-pattern' 类名检测的冲突
  	// 否则可能会一直提示类名需要使用 kebab-case 
    'selector-class-pattern': null
  ]
}
```

> Tips：
>
> 如果是链式选择器，如 `.A.B` ，需要写的正则支持，否则也会视为不符合规范发出警告。
>
> 如果伪类写在选择器最后，会被忽略，不影响使用。写在中间则会发出警告。



**使用**：

一个文件一个模块：

``` scss
/** @define FancyComponent */
:root {
  --FancyComponent-property: value;
}

.FancyComponent {}

.FancyComponent-other {}
```

一个文件多个模块：

``` scss
/** @define Foo */
.Foo {}
/** @end */

.something-something-something {}
```

Utilities ：

``` scss
/** @define utilities */
.u-sizeFill {}
```

> Tips：
>
> 可以添加该注释 `/* postcss-bem-linter: ignore */` 来忽略对某块样式的校验。



### 依赖安装

首先安装核心工具 `stylelint` ：

```bash
pnpm i -wD stylelint

```

`SCSS` 标准以及解析 `SCSS` 的能力：

``` bash
pnpm i -wD stylelint-config-recommended-scss postcss-scss

```

`Vue` 标准以及解析 `Vue` 的能力：

```bash
pnpm i -wD stylelint-config-recommended-vue postcss-html

```

`CSS` 属性排序：

``` bash
pnpm i -wD stylelint-config-recess-order

```

适配 `BEM` ：

``` bash
pnpm i -wD stylelint-selector-bem-pattern

```



### 配置

根目录新建 `.stylelintrc.js` 文件，作为 `stylelint` 的配置文件：

``` js
const nameRegExpString = '[a-z0-9]+(-[a-z0-9]+)*'

module.exports = {
  // 拓展合并其它配置
  extends: [
    'stylelint-config-recommended-scss',
    'stylelint-config-recommended-vue',
    'stylelint-config-recess-order'
  ],
  // 用于支持非常规的 css 属性或者用例的插件
  plugins: ['stylelint-selector-bem-pattern'],
  // 指定要使用 customSyntax 识别的文件类型
  // e.g 使用 postcss-scss 识别 scss
  overrides: [
    // 解析 SCSS
    {
      files: ['*.(scss|css|vue|html)', '**/*.(scss|css|vue|html)'],
      customSyntax: 'postcss-scss'
    },
    // 解析 Vue/HTML 文本
    {
      files: ['*.(html|vue)', '**/*.(html|vue)'],
      customSyntax: 'postcss-html'
    }
  ],
  // 配置要格式化的规则
  rules: {
    // 兼容 scss 语法
    'at-rule-no-unknown': [
      true,
      {
        // 添加 apply 兼容 unocss ，另外需要在 IDE 中关闭 scss 对 unknown at rule 的警告
        'ignoreAtRules': ['at-root', 'mixin', 'include', 'extend', 'use', 'apply']
      }
    ],
    // 使用注释指定 block 名称
    'plugin/selector-bem-pattern': {
      // 'suit' / 'bem', 不管哪种都需要手动指定，因为该插件未给源插件默认指定
      preset: 'bem',
      // 默认 '^[-_a-zA-Z0-9]+$'
      componentName: `^${nameRegExpString}$`,
      /**
       * 自定义模式规则
       * 指定组合的选择器检查规则，其实就是指定class名称规则
       * 支持正则字符串、返回正则的函数、包含2个选项参数的对象等写法
       */
      componentSelectors: {
        // 只初始的选择器规则（可以理解为外层的class规则）
        initial: `^\\.{componentName}(?:__${nameRegExpString})?(?:--${nameRegExpString})?$`,
        // 可以理解为外层以内的选择器的规则
        // 如果不指定，则跟 initial 同样的规则
        combined:
          `^\\.{componentName}(((?:__${nameRegExpString})(?:--${nameRegExpString})?)|(?:--${nameRegExpString}))$`
      },
      // utils 类名
      utilitySelectors: '^\\.u-[a-zA-z]+$'
    },
    // 解决和 'plugin/selector-bem-pattern' 类名检测的冲突
  	// 否则可能会一直提示类名需要使用 kebab-case 
    'selector-class-pattern': null,
    // 解决在 style 标签中使用 v-bind 时要求参数一定要小写的问题
    'value-keyword-case': null
  },
  // 缓存结果，只处理有改动的部分
  cache: true
}
```

`package.json` 中添加 script ：

``` json
{
  // ...
  "script": {
    // ...
    "lint:style": "stylelint . --fix"
  }
}
```

`.stylelintignore` 忽略文件：

``` 
node_modules
dist
public

*.js
*.cjs
*.jsx
*.ts
*.tsx
*.json
*.md
*.svg
*.png
*.eot
*.ttf
*.woff
*.yaml

```



### 忽略规则

如果需要单独关闭对某个位置的检查：

- 使用**未限定范围**的禁用注释去关闭所有/单个规则（需要显示地再用注释启用）：

  ``` scss
  /* stylelint-disable */
  a {}
  /* stylelint-enable */
  
  /* stylelint-disable selector-max-id, declaration-no-important */
  #id {
    color: pink !important;
  }
  /* stylelint-enable selector-max-id, declaration-no-important */
  
  ```

- 关闭**单行**的规则，不需要再显示启用：

  ``` scss
  #id { /* stylelint-disable-line */
    color: pink !important; /* stylelint-disable-line declaration-no-important */
  }
  
  #id {
    /* 关闭下一行 */
    /* stylelint-disable-next-line declaration-no-important */
    color: pink !important;
  }
  
  ```

- 支持复杂、重叠的禁用和启用：

  ``` scss
  /* stylelint-disable */
  /* stylelint-enable foo */
  /* stylelint-disable foo */
  /* stylelint-enable */
  /* stylelint-disable foo, bar */
  /* stylelint-disable baz */
  /* stylelint-enable baz, bar */
  /* stylelint-enable foo */
  
  ```

- 可以添加描述：

  ``` scss
  /* stylelint-disable -- Reason for disabling Stylelint. */
  /* stylelint-disable foo -- Reason for disabling the foo rule. */
  /* stylelint-disable foo, bar -- Reason for disabling the foo and bar rules. */
  
  ```

  

## Prettier

> `Prettier` 是一款固执己见的格式化工具。
>
> - 对所有语言一视同仁，无法对规则进行更细粒度的控制。
> - 与 `Lint` 结合使用的时候，所有错误都会被统一标记为 `prettier/prettier` ，没法进一步细分错误。

我们使用 `Prettier` 来格式化 `ESLint` 和 `Stylelint` 不支持的文件，例如： `Markdown` 、`json` 等。

``` json
// .prettierrc.js
module.exports = {
  // 最大行长。通常为 100 - 120，建议 80 。
  printWidth: 100,
  
	// 缩进使用多少个空格。
  tabWidth: 2,
  
  /**
   * true  - 结尾加分号。
   * false - 不加。
   */
  semi: false,
  
  /**
   * true  - 使用单引号代替双引号。
   * false - 不使用。
   */
  singleQuote: true,
  
  /**
   * 多行情况下在最后一行末尾增加逗号
   * es5  - 在 es5 数据中加（object, arrays, etc.），Typescript 中的变量不加。
   * none - 所有都不添加。
   * all  - 所有能加的地方都加。
   */
  trailingComma: 'none',
  
  /**
   * true  - 对象字面量加空格: { foo: bar }
   * false - 不加: {foo: bar}
   */
  bracketSpacing: true,
  
  /**
   * true  - 使用 tab 缩进。
   * false - 使用空格。
   */
  useTabs: false,
  
  /**
   * 对象里的 key 是否需要使用引号。
   * as-needed  - 仅在需要时加。
   * consistent - 如果有一个 key 需要，则所有 key 添加。
   * preserve   - 输入什么就是什么。
   */
  quoteProps: 'as-needed',
  
  /**
   * true  - HTML 多行元素的最后 '>' 不另起一行。
   * false - 另起一行。
   *
   * example:
   * 	if true
   * 	<button
   * 		class="prettier-class"
   * 		id="prettier-id">
   * 	</button>
   */
  bracketSameLine: false,
  
  /**
   * true  - 在 JSX 使用单引号代替双引号。
   * false - 不使用。
   */
  jsxSingleQuote: false,
  
  // 同 jsxSingleQuote ，作用于 JSX 。
  jsxBracketSameLine: false,
  
  /**
   * always - 箭头函数单参数使用括号包裹：(x) => x 。
   * avoid  - 不使用：x => x 。
   */
  arrowParens: 'always',
  
  // 格式化代码的范围，可以选择只格式化文件的一部分，默认整个文件。不能和 cursorOffset 一起使用。
  rangeStart: 0,
  rangeEnd: Infinity,
  
  /**
   * prettier 可以限制自己只格式化有在顶端添加注释的文件
   * @prettier 或者 @format
   * 
   * true  - 限制。
   * false - 不限制。
   */
  requirePragma: false,
  
  /**
   * prettier 可以通过在文件顶端添加注释 @format 标记文件已经被格式化
   * 
   * true  - 自动在已格式化的文件顶部添加注释。
   * false - 不添加。
   */
  insertPragma: false,
  
  /**
   * 默认情况下，prettier 不会修改 markdown 文件中的换行，
   * 但是有些渲染器对换行比较敏感（例如：github 等）。
   *
   * always   - 超过设置的 printWidth 属性就换行。
   * never    - 从不换行。
   * preserve - 什么都不做，按代码原本的来。
   */
  proseWrap: 'preserve',
  
  /**
   * 解决行内元素换行会被当成空格的问题
   * example:
   * 	<span>
   * 		123
   * 	</span>
   * 等同于 <span> 123 </span> ，多了两个空格
   *
   * css    - 按照元素的默认 CSS display 值进行换行。
   * strict - 全部都按行内元素换行方式格式化。
   * ignore - 全部都按块级元素换行方式格式化。
   *
   * example:
   * 	行内元素：与前后 <> 相连代表没有换行
   * 	<span
   * 		>123</span
   *	>
   * 	块级元素：可以通过添加注释告诉 prettier 把行内元素当块级元素处理
   *	<!-- display: block; -->
   * 	<span>
   * 		123
   *	</span>
   */
  htmlWhitespaceSensitivity: 'css',
  
  /**
   * true  - vue script 和 style 标签中进行缩进。
   * false - 不进行缩进。
   */
  vueIndentScriptAndStyle: false,
  
  /**
   * 行尾换行符格式
   * lf   - \n 。
   * crlf - \r\n 。
   * cr   - \r 。
   * auto - 各种格式都可以，会按第一行的格式进行格式化。
   */
  endOfLine: 'lf',
  
  /**
   * auto - 格式化被引号包裹的代码（如果 prettier 能识别）。
   * off  - 不格式化。
   */
  embeddedLanguageFormatting: 'off',
  
  /**
   * true  - 强制 HTML、Vue 和 JSX 每个属性为一行。
   * false - 不强制。
   */
  singleAttributePerLine: false
}
```



### 依赖安装

``` bash
pnpm i -wD prettier

```



### 配置

根目录新建 `.prettierrc.js` 文件，作为 `prettier` 的配置文件：

``` js
module.exports = {
  printWidth: 100,
  tabWidth: 2,
  semi: false,
  singleQuote: true,
  trailingComma: 'none'
}

```

`.prettierignore` 忽略文件：

``` 
node_modules
dist
.github
.husky
*-lock.yaml
*-lock.json

```



## 与 IDE 结合

在 `VSCode` 分别安装 `ESLint` 、`Stylelint` 和 `Prettier` 三款插件。

在 `.vscode/extensions.json` 中新增：

``` json
{
  "recommendations": [
    // ...
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "stylelint.vscode-stylelint"
  ]
}

```

在 `.vscode/settings.json` 中新增：

``` json
{
  // 设置默认格式化工具为 Prettier
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  
  // 默认禁用自动格式化(手动格式化快捷键：Shift + Alt + F)
  "editor.formatOnSave": false,
  "editor.formatOnPaste": false,
  
  // 避免使用 prettier 处理 vue 文件的格式化
  "[vue]": {
    "editor.defaultFormatter": null
  },
  
  // json、yaml 等配置文件保存时自动格式化
  "[json]": {
    "editor.formatOnSave": true
  },
  "[yaml]": {
    "editor.formatOnSave": true
  },
  "[html]": {
    "editor.formatOnSave": true
  },
  "[markdown]": {
    "editor.formatOnSave": true
  },
  
  // 启用 ESLint 和 Stylelint 保存自动格式化
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.fixAll.stylelint": "explicit"
  },
  
  // 启用 eslint 的格式化能力
  "eslint.format.enable": true,
  
	// 指定 eslint 该检测的文件类型(尤其是 vue 文件)。
  "eslint.probe": ["javascript", "typescript", "vue"],
  
  // 指定 stylelint 检测的文件类型(尤其是 vue 文件)。
  "stylelint.validate": ["css", "scss", "postcss", "vue"],
  
   // 行尾默认为 LF 换行符而非 CRLF
  "files.eol": "\n",

  // 键入 Tab 时插入空格而非 \t
  "editor.insertSpaces": true,

  // 缩进大小：2
  "editor.tabSize": 2
}

```

`editor.codeActionsOnSave` 的相关配置，让 `ESLint` 和 `Stylelint` 的**自动修复功能在保存文件时触发。** 当然，部分复杂的错误无法自动修复，需要人工检视。

将默认的格式化工具设为 `Prettier`，但是**禁用自动格式化，避免格式化与自动修复之间的冲突。自动格式化只对非 `ESLint` 和 `Stylelint` 目标的文件开启，** 例如 `json`、`yaml`。



## lint-staged

对 git staged 状态的文件进行格式化。



### 依赖安装

``` bash
pnpm i -wD lint-staged

```



### 配置

根目录新建 `.lintstagedrc.js` 文件，作为 `lint-staged` 的配置文件：

``` js
module.exports = {
  // 对于 js、ts 脚本文件，应用 eslint
  '*.{js,jsx,ts,tsx}': ['eslint --ext .js,.jsx,.ts,.tsx --fix'],
  // 对于 css scss 文件，应用 stylelint
  '*.{scss,css}': ['stylelint --fix'],
   // Vue 文件由于同时包含模板、样式、脚本，因此 eslint、stylelint 都要使用
  '*.vue': ['eslint --ext .vue --fix', 'stylelint --fix'],
  '*.{html,json,md}': ['prettier --write']
}

```



## commitlint

检查 git 提交信息。

``` js
const Configuration = {
  // 拓展其它配置
  extends: ['@commitlint/config-conventional'],

  // commit msg 的解析器
  parserPreset: '',

  // 用不同的格式输出检测结果
  formatter: '@commitlint/format',
  
	// 检测的规则
  rules: {
    'type-enum': [2, 'always', ['foo']],
  },
  /*
   * Array of functions that return true if commitlint should ignore the given message.
   * Given array is merged with predefined functions, which consist of matchers like:
   *
   * - 'Merge pull request', 'Merge X into Y' or 'Merge branch X'
   * - 'Revert X'
   * - 'v1.2.3' (ie semver matcher)
   * - 'Automatic merge X' or 'Auto-merged X into Y'
   *
   * To see full list, check https://github.com/conventional-changelog/commitlint/blob/master/%40commitlint/is-ignored/src/defaults.ts.
   * To disable those ignores and run rules always, set `defaultIgnores: false` as shown below.
   */
  ignores: [(commit) => commit === ''],
  /*
   * Whether commitlint uses the default ignore rules, see the description above.
   */
  defaultIgnores: true,
  /*
   * Custom URL to show upon failure
   */
  helpUrl:
    'https://github.com/conventional-changelog/commitlint/#what-is-commitlint',
  
  // 配置命令行的交互，和 @commitlint/cz-commitlint 一起使用
  prompt: {
    messages: {},
    questions: {
      type: {
        description: 'please input type:',
      },
    },
  },
};

export default Configuration
```



### Commit 规范

一般采用 Angular.js 规范：

详细参考：[https://feflowjs.com/zh/guide/rule-git-commit.html](https://feflowjs.com/zh/guide/rule-git-commit.html)

```javascript
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
       
```

其中，header 是必需的，body 和 footer 可以省略。
不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。这是为了避免自动换行影响美观。

可以使用 [commitlint](https://commitlint.js.org/guides/getting-started.html) + [@commitlint/cz-commitlint](https://www.npmjs.com/package/@commitlint/cz-commitlint) 。



- **Header**

  Header 部分只有一行，包括三个字段：`type`（required）、`scope`（optional）、`subject`（optional）。

  - type：用于说明 commit 的类别，只允许使用下面几个标识。

    | 类型     | 描述                                              |
    | -------- | ------------------------------------------------- |
    | build    | 发布版本                                          |
    | chore    | 改变构建流程、或者增加依赖库、工具等              |
    | ci       | 持续集成修改                                      |
    | docs     | 文档修改                                          |
    | feat     | 新功能（feature）                                 |
    | fix      | 修补 bug                                          |
    | perf     | 优化相关，比如提升性能、体验                      |
    | refactor | 重构（既不是新增功能，也不是修改 bug 的代码改动） |
    | revert   | 回滚到上一个版本                                  |
    | style    | 格式（不影响代码运行的变动）                      |
    | test     | 测试用例修改                                      |

  - scope：用于说明 commit 影响的范围，每个项目对这个范围的定义可能不同。

    如果你的修改影响了不止一个 `scope` ，你可以使用 `*` 代替。

  - subject：commit 目的的简短描述，不超过 50 个字符。

    - 以动词开头，使用第一人称现在时，比如 change ，而不是 changed 或 changes
    - 第一个字母小写
    - 结尾不加句号（.）

- **Body**

  Body 部分是对本次 commit 的详细描述，可以分成多行。下面是一个范例。

  ```bash
  More detailed explanatory text, if necessary.  Wrap it to 
  about 72 characters or so. 
  
  Further paragraphs come after blank lines.
  
   - Bullet points are okay, too
   - Use a hanging indent
   
  ```

  > Tips：
  >
  > 1. 使用第一人称现在时，比如 change ，而不是 changed 或 changes ；
  > 2. 与 Header 和 Footer 之间有
  > 3. 应该说明代码变动的动机，以及与以前行为的对比。

- **Footer**

  Footer 部分只用于两种情况：

  1. 不兼容变动

    如果当前代码与上一个版本不兼容，则 Footer 部分以 `BREAKING CHANGE` 开头，后面是对变动的描述、以及变动理由和迁移方法。

    ```bash
  BREAKING CHANGE: isolate scope bindings definition has changed.
    
    To migrate the code follow the example below:
  
    Before:
    
    scope: {
      myAttr: 'attribute',
    }
    
    After:
    
    scope: {
      myAttr: '@',
    }
    
    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
    
    ```

  2. 关闭 Issue

    如果当前 commit 针对某个 issue，那么可以在 Footer 部分关闭这个 issue。

    ``` bash
  Closes #234
  
    ```

    也可以一次性关闭多个 issue 。

    ``` bash
  Close #123, #245, #992
  
    ```


### 依赖安装

``` bash
pnpm i -wD @commitlint/config-conventional @commitlint/cli

```



### 配置

根目录新建 `.commitlintrc.js` 文件，作为 `commitlint` 的配置文件：

``` js
module.exports = {
  extends: ['@commitlint/config-conventional']
}

```



## husky

通过 git hook 在 git 操作之前执行一些自定义操作。



### 配置

`package.json` 添加 `prepare` 配置。添加后 install 的时候会先执行该 script 。

``` json
{
  "script": {
    "prepare": "husky install"
  }
}

```

如果当前已经 install 过，可以单独手动执行一次生成 `.husky` 目录

``` bash
pnpm prepare

```

> Tips：
>
> 使用 GUI + node 版本管理器（ e.g. sourceTree + Volta ），在 commit 的时候可能会出现 `command not found` 的报错，这种情况下是 GUI 无法正确加载到 node 版本管理器下 node 的路径。
>
> 可以在 `~/.config/husky/init.sh` 中把 node 版本管理器的路径配好，husky 执行命令前都会先执行这个文件。
>
> ``` bash
> export PATH="/Users/username/.volta/bin:$PATH"
> ```
>
> 路径可以通过以下命令查询：
>
> ``` bash
> which volta
> ```
>
> husky > v9.0



- **pre-commit**

  添加 `pre-commit` 钩子在 commit 前执行命令。

  在 git commit 之前执行 lint-staged ：

  在 `.husky` 文件夹下新建 `pre-commit` 文件。

  ``` 
  # pre-commit
  pnpm exec lint-staged
  
  ```

  > 如果不想真的提交，而是进行测试，可以在 `pre-commit` 文件最后一行加上 `exit 1` 。


- commit-msg

  检测 commit message 。

  使用 `commitlint` 校验 commit message ：

  在 `.husky` 文件夹下新建 `commit-msg` 文件。

  ``` 
  # commit-msg
  pnpm exec commitlint --edit
  
  ```







