# Lint

## ESLint

### 配置

- 选择生成的配置文件格式最好是 `js` ，因为 `eslint` 查询配置文件有优先级 `js` > `yaml` > `json` 。

- 选择配置文件 module type 的时候根据自己项目的打包工具进行选择，`webpack` 一般是 `CommonJS` , `vite` 一般是 `Javascript modules(import / export)`

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



### 安装

``` bash 
npm install eslint -D
```



### config 代码提示

使用 `eslint-define-config` 给 eslint js 文件提供代码提示

``` bash
npm install eslint-define-config -D
```

``` js
const { defineConfig } = require('eslint-define-config')

module.exports = defineConfig({
  // ...
})
```



### 初始化

`.eslintrc.js`

``` js
module.exports = {
  root: true,
  env: {
    browser: true,
    es6: true,
    // 不添加 node ，eslint 会报错
    // 'module' is not defined
    node: true
  },
  extends: [
    'eslint:recommended',
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  rules: {}
}

```



### Vue 拓展

eslint 是不会解析 `.vue` 后缀文件，所以需要添加 extends 拓展。

``` bash
npm install eslint-plugin-vue vue-eslint-parser -D
```

``` js
{
  extends: {
    'eslint:recommended',
    'plugin:vue/vue3-recommended'
  }
}
```



### Ts 拓展

eslint 是不会解析 `.ts` 后缀文件，所以需要添加 extends 拓展。

``` bash
npm install @typescript-eslint/eslint-plugin @typescript-eslint/parser -D
```

``` js
{
  extends: {
    'eslint:recommended',
    'plugin:vue/vue3-recommended',
    'plugin:@typescript-eslint/recommended'
  },
  /**
   * 使用 'plugin:vue/vue3-recommended' extend 时就已经引入了 parser 'vue-eslint-parser',
   * 但是使用 'plugin:@typescript-eslint/recommended' extend 会再引入 parser '@typescript-eslint/parser' 覆盖掉 vue 的 parser，
   * 根据 eslint-plugin-vue 的官网可知需要将其他的 parser 放入 parserOptions 中。
   * 两个 parer 的区别在于：
   * 外面的 parser 用于解析 .vue 后缀文件，使得 eslint 可以解析 template 中的内容，而 paserOptions 中的 parser，即 @typescript-eslint/parser 用来解析 .vue 后缀文件中 scripte 中的内容。
   */
  parser: 'vue-eslint-parser',
  parserOptions: {
    ecmaVersion: 'latest',
    parser: '@typescript-eslint/parser',
    sourceType: 'module'
  }
}
```



### UnoCSS

``` bash
npm install -D @unocss/eslint-config
```

``` js
{
  extends: {
    '@unocss'
  }
}
```





### 兼容 Prettier

ESLint 与 Prettier 会在一些规则上有冲突，导致无法正常格式化，需要进行兼容。

``` bash
npm install eslint-plugin-prettier eslint-config-prettier -D 
```

``` js	
{
  extends: [
    // Prettier 必须放在最后
    'plugin:prettier/recommended'
  ]
}

// 引入后相当于下面的作用，引入 prettier 并屏蔽掉冲突的规则
{
 	extends: ['prettier'],
  plugins: ['prettier'],
  rules: {
    'prettier/prettier': 'error',
    'arrow-body-style': 'off',
    'prefer-arrow-callback': 'off',
  }
}
```



### 总结

- 安装

  ``` shell
  npm install eslint -D
  npm install eslint-define-config -D
  npm install eslint-plugin-vue vue-eslint-parser -D
  npm install @typescript-eslint/eslint-plugin @typescript-eslint/parser -D
  ```

- `.eslintrc.js`

  ``` js
  const { defineConfig } = require('eslint-define-config')
  
  module.exports = defineConfig({
    root: true,
    env: {
      browser: true,
      es6: true,
      node: true
    },
    extends: [
      'eslint:recommended',
      'plugin:vue/vue3-recommended',
      'plugin:@typescript-eslint/recommended',
      'plugin:prettier/recommended'
    ],
    parser: 'vue-eslint-parser',
    parserOptions: {
      ecmaVersion: 'latest',
      parser: '@typescript-eslint/parser',
      sourceType: 'module'
    },
    rules: {
      // 没有内容的 HTML 标签要求自闭
      'vue/html-self-closing': ['error', {
        html: {
          normal: 'never',
          void: 'always'
        }
      }],
      // vue 文件名要求至少两个单词小驼峰格式
      'vue/multi-word-component-names': ['error', {
        // 下面文件名不会被校验
        ignores: [
          'index'
        ]
      }],
      // 可以使用 any type
      '@typescript-eslint/no-explicit-any': 'off',
      // 可以使用 @ts-ignore 等注释
      '@typescript-eslint/ban-ts-comment': 'off'
    }
  })
  ```

- `.eslintignore`

  ``` 
  # 忽略检查的文件和文件夹
  
  node_modules
  dist
  .github
  *-lock.yaml
  *-lock.json
  .husky
  ```

- `package.json`

  ``` json
  {
  	"script": {
      // . => 要检查的文件路径
      // --ext .vue,.js,.ts,.jsx,.tsx => 默认只检查 .js 文件，添加拓展
      // --fix => 检查后进行修复
      "lint:eslint": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts --fix"
    }
  }
  ```

> Tips：
>
> 可以使用 `<!-- eslint-disable-eslint 规则 -->` 在模版中关闭对应语句的 `eslint` 检查。



## Prettier

### 配置

``` js
// .prettierrc.js
module.exports = {
  // 最大行长。通常为 100 - 120，建议 80 。
  printWidth: 80,
  
	// 缩进使用多少个空格。
  tabWidth: 2,
  
  /**
   * true  - 使用 tab 缩进。
   * false - 使用空格。
   */
  useTabs: false,
  
  /**
   * true  - 结尾加分号。
   * false - 不加。
   */
  semi: true,
  
  /**
   * true  - 使用单引号代替双引号。
   * false - 不使用。
   */
  singleQuote: false,
  
  /**
   * 对象里的 key 是否需要使用引号。
   * as-needed  - 仅在需要时加。
   * consistent - 如果有一个 key 需要，则所有 key 添加。
   * preserve   - 输入什么就是什么。
   */
  quoteProps: 'as-needed',
  
  /**
   * true  - 在 JSX 使用单引号代替双引号。
   * false - 不使用。
   */
  jsxSingleQuote: false,
  
  /**
   * 多行情况下在最后一行末尾增加逗号
   * es5  - 在 es5 数据中加（object, arrays, etc.），Typescript 中的变量不加。
   * none - 所有都不添加。
   * all  - 所有能加的地方都加。
   */
  trailingComma: 'es5',
  
  /**
   * true  - 对象字面量加空格: { foo: bar }
   * false - 不加: {foo: bar}
   */
  bracketSpacing: true,
  
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
  
  // 同 bracketSameLine ，作用于 JSX 。
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



### 安装

``` bash
npm install prettier -D
```



### 总结

- `.prettierrc.js`

  ``` js
  module.exports = {
    printWidth: 100,
    semi: false,
    singleQuote: true,
    singleAttributePerLine: true,
    trailingComma: 'none',
    tabWidth: 2
  }
  ```

- `.prettierignore`

  ``` 
  node_modules
  dist
  .github
  .husky
  *-lock.yaml
  *-lock.json
  ```

- `package.json`

  ``` json
  {
    "script": {
      // 检查修复文件
      "lint:prettier": "prettier . --write"
    }
  }
  ```

  

## Stylelint

### 配置

常见配置

``` js
// .stylelintrc.js
module.exports = {
  // 配置要格式化的规则
  rules: {},
  
  // 拓展合并其它配置
  extends: [],
  
  // 用于支持非常规的 css 属性或者用例的插件
  plugins: [],
  
  // 用于支持将包括在不同文件中（e.g HTML、template）的中的预编译语言等样式转换为类 CSS 语言进行识别
  // 如果要支持多种，可以使用 overrides 属性
  customSyntax: ''
  
  // 指定要使用 customSyntax 识别的文件类型
  // e.g 使用 postcss-scss 识别 scss
  overrides: [
  	{
  		files: ['*.scss', '**/*.scss'],
      customSyntax: 'postcss-scss'
		}
  ],
    
  // 设置所有 rules 告警的默认严重程度
  // e.g warging
  defaultSeverity: 'warning',
    
  // 忽略的文件类型
  // e.g js
  ignoreFiles: ['**/*.js'],
    
  // 当 glob 模式匹配不到任何文件时不会报错
  allowEmptyInput: true,
    
  // 缓存结果，只处理有改动的部分
  cache: true,
    
  // 自动修复
  fix: true
}
```



### 安装

``` bash
npm install stylelint -D
```



### SCSS 拓展

- 添加 SCSS 标准

  ``` bash
  npm install stylelint-config-recommended-scss -D
  ```

- 兼容 Prettier

  ``` bash
  npm install stylelint-config-prettier-scss -D
  ```

- 解析 SCSS

  ``` bash
  npm install postcss-scss -D
  ```

``` js
module.exports = {
  extends: [
    'stylelint-config-recommended-scss',
    'stylelint-config-prettier-scss'
  ],
  overrides: [
    // 解析 SCSS
    {
      files: ['*.(scss|css|vue|html)', '**/*.(scss|css|vue|html)'],
      customSyntax: 'postcss-scss'
    }
  ],
  rules: [
    // 兼容 scss 语法
    'at-rule-no-unknown': [
      true,
      {
        'ignoreAtRules': ['at-root', 'mixin', 'include', 'extend']
      }
    ]
  ]
}
```



### Vue / HTML 拓展

- 添加 Vue 标准

  ``` bash
  npm install stylelint-config-recommended-vue -D
  ```

- 解析 Vue 和 HTML 中的类 HTML 结构

  ``` bash
  npm install postcss-html -D
  ```

``` js
module.exports = {
  extends: [
    'stylelint-config-recommended-vue'
  ],
  overrides: [
    {
      files: ['*.(html|vue)', '**/*.(html|vue)'],
      customSyntax: 'postcss-html'
    }
  ],
  rules: {
    // 解决在 style 标签中使用 v-bind 时要求参数一定要小写的问题
    'value-keyword-case': null
  }
}
```



### CSS 属性排序

CSS 属性按一下顺序排序：

- `position`
- `display` and box model
- `font`, leading, `color`, text
- `background` and `border`
- CSS3 properties like `border-radius` and `box-shadow`
- and a handful of other purely visual properties

``` bash
npm install stylelint-config-recess-order -D
```

``` js
module.exports = {
  extends: [
    'stylelint-config-recess-order'
  ]
}
```



### 适配 BEM 规则

``` bash
npm install stylelint-selector-bem-pattern -D
```

``` js
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



### 总结

- `.stylelintrc.js`

  ``` js
  const nameRegExpString = '[a-z0-9]+(-[a-z0-9]+)*'
  
  module.exports = {
    extends: [
      'stylelint-config-recommended-scss',
      'stylelint-config-prettier-scss',
      'stylelint-config-recommended-vue',
      'stylelint-config-recess-order'
    ],
    plugins: ['stylelint-selector-bem-pattern'],
    overrides: [
      {
        files: ['*.(scss|css|vue|html)', '**/*.(scss|css|vue|html)'],
        customSyntax: 'postcss-scss'
      },
      {
        files: ['*.(html|vue)', '**/*.(html|vue)'],
        customSyntax: 'postcss-html'
      }
    ],
    rules: {
      'at-rule-no-unknown': [
        true,
        {
          'ignoreAtRules': ['at-root', 'mixin', 'include', 'extend']
        }
      ],
      'plugin/selector-bem-pattern': {
        preset: 'bem',
        componentName: `^${nameRegExpString}$`,
        componentSelectors: {
          initial: `^\\.{componentName}(?:__${nameRegExpString})?(?:--${nameRegExpString})?$`,
          combined:
            `^\\.{componentName}(((?:__${nameRegExpString})(?:--${nameRegExpString})?)|(?:--${nameRegExpString}))$`
        },
        utilitySelectors: '^\\.u-[a-zA-z]+$',
      },
      'selector-class-pattern': null,
      'value-keyword-case': null
    },
    cache: true
  }
  ```

- `.stylelintignore`

  ``` 
  # 默认忽略 node_modules
  dist
  *.js
  *.jsx
  *.ts
  *.tsx
  *.json
  *.md
  *.png
  *.svg
  *.eot
  *.ttf
  *.woff
  *.yaml
  ```

- `package.json`

  ``` json
  {
    "script": {
      // 检查修复文件
      "lint:stylelint": "stylelint . --fix"
    }
  }
  ```



## lint-staged

对 git staged 状态的文件进行格式化。

- 安装

  ``` bash
  npm install lint-staged -D
  ```

- `.lintstagedrc.js`

  ``` js
  module.exports = {
    '*.{js,jsx}': [
      'prettier --write',
      'eslint --ext .js,.jsx --fix'
    ],
    '*.{ts,tsx}': [
      'prettier --write',
      'eslint --ext .ts,.tsx --fix'
    ],
    '*.vue': [
      'prettier --write',
      'eslint --ext .vue --fix',
      'stylelint --fix'
    ],
    '*.{scss,html}': [
      'prettier --write',
      'stylelint --fix'
    ],
    'package.json': [
      'prettier --write'
    ],
    '*.md': [
      'prettier --write'
    ]
  }
  ```

- 执行

  ``` bash
  pnpm exec lint-staged
  ```



## commitlint

检查 git 提交信息。



### 配置

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



### msg 模版

使用 `@commitlint/cz-commitlint` 可以在 commit 的时候提供 msg 模版供选择。

- 安装

  ``` bash
  npm install --save-dev @commitlint/cz-commitlint commitizen inquirer@9
  ```

- 配置 `package.json`

  ``` json
  {
    "scripts": {
      "cz": "git-cz"
    },
    "config": {
      "commitizen": {
        "path": "@commitlint/cz-commitlint"
      }
    }
  }
  ```

- 使用一下命令进行 commit

  ``` bash
  npm run cz
  ```

  

### 总结

- 安装

  ``` bash
  npm install --save-dev @commitlint/{cli,config-conventional}
  npm install --save-dev @commitlint/cz-commitlint commitizen inquirer@9
  ```

- 添加 `commitlint.config.js`

  ``` js
  module.exports = {
    extends: ['@commitlint/config-conventional']
  }
  ```

-  配置 `package.json`

  ``` json
  {
    "scripts": {
      "cz": "git-cz"
    },
    "config": {
      "commitizen": {
        "path": "@commitlint/cz-commitlint"
      }
    }
  }
  ```

- commit

  ``` bash
  npm run cz
  ```

- 执行

  ``` bash
  npx commitlint --edit
  ```

> Tips:
>
> 需要 commitlint 以及其拓展的配置版本 > **12.1.2** 。



## husky

通过 git hook 在 git 操作之前执行一些自定义操作。



### 安装

``` bash
npm install husky -D
```



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
npm prepare
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



### pre-commit

添加 `pre-commit` 钩子在 commit 前执行命令。

在 git commit 之前执行 lint-staged ：

在 `.husky` 文件夹下新建 `pre-commit` 文件。

``` 
# pre-commit
npx lint-staged
```



> Tips:
>
> 如果不想真的提交，而是进行测试，可以在 `pre-commit` 文件最后一行加上 `exit 1` 。



### commit-msg

检测 commit message 。

使用 `commitlint` 校验 commit message ：

在 `.husky` 文件夹下新建 `commit-msg` 文件。

``` 
# commit-msg
npx commitlint --edit
```



## VSCode

针对以上 lint ，VSCode 编译器的相关配套插件和设置。



### 插件

`ESLint`

`Prettier`

`Stylelint`

`Vue-official`



### 配置文件

`.vscode/*`

- 在 vscode 打开项目时提示要安装插件

  `extensions.json`

  ``` json
  {
    "recommendations": [
      "vue.volar",
      "dbaeumer.vscode-eslint",
      "esbenp.prettier-vscode",
      "stylelint.vscode-stylelint"
    ]
  }
  ```

- 保存时自动格式化代码

  `settings.json`

  ``` json
  {
    "editor.codeActionsOnSave": {
      "source.fixAll": "never",
      "source.fixAll.eslint": "explicit",
      "source.fixAll.stylelint": "explicit"
    },
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "[vue]": {
      "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "stylelint.validate": ["css", "scss", "vue", "html"],
    "editor.formatOnSave": true
  }
  ```

  

