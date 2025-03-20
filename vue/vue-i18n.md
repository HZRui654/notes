

# vue-i18n

[vue-i18n](https://vue-i18n.intlify.dev/)

环境：`vue3` + `typescript`

> 以下都为 global scope ，local scope 基本不用，有需求再看文档。



## 安装

``` bash
npm i -S vue-i18n@9
```

基础使用：

``` typescript
// i18n/index.ts
import { createI18n } from 'vue-i18n'

const i18n = createI18n({})
export default i18n
```

``` typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import i18n from './i18n'

const app = createApp(App)

app.use(i18n).mount('#app')
```

``` vue
<script setup>
  import { useI18n } from 'vue-i18n'
  
  // 必须在 setup 的顶部引用 useI18n
  const { t, d, n } = useI18n()
</script>
```



## 插值（ Interpolations ）

``` typescript
const messages = {
  en: {
    message: {
      hello: '{msg} world',
      hello2: '{0} world' // 列表插值
    }
  }
}
```

``` vue
<template>
  <p>{{ t('message.hello', { msg: 'hello' }) }}</p>
  <p>{{ t('message.hello2', ['hello']) }}</p>
	<p>hello world</p>
</template>
```

> 特殊字符，不能直接使用：
>
> `{` , `}` , `@` , `$` , `|`
>
> `address: "draven{'@'}domain.com"`



## 引用值（ Linked Message ）

> `@:key` , `@.modifier:key`

``` typescript
const messages = {
  en: {
    message: {
      the_world: 'the world',
      dio: 'DIO:',
      linked: '@:message.dio @:message.the_world !!!!',
      
      /**
       * 修饰符 @.modifier:key
       * upper: 全部大写
       * lower: 全部小写
       * capitalize: 首字母大写
       */
      homeAddress: 'Home address',
      missingHomeAddress: 'Please provide @.lower:message.homeAddress'
    }
  }
}
```

``` vue
<template>
  <p>{{ t('message.linked') }}</p>
	<p>DIO: the world !!!!</p>

  <label>{{ $t('message.homeAddress') }}</label>
	<label>Home address</label>

	<p class="error">{{ $t('message.missingHomeAddress') }}</p>
	<p class="error">Please provide home address</p>
</template>
```

自定义修饰符（ Custom Modifiers ）

``` typescript
const i18n = createI18n({
  locale: 'en',
  messages: {
    en: {
      message: {
        snake: 'snake case',
        custom_modifier: "custom modifiers example: @.snakeCase:{'message.snake'}"
      }
    }
  },
  // set custom modifiers at `modifiers` option
  modifiers: {
    snakeCase: (str) => str.split(' ').join('_')
  }
})
```



## 复数化（ Pluralization ）

> `|` ，可以自定义 format 方式。
>
>  使用 `useI18n` 时直接使用 `t` 即可。

``` typescript
const messages = {
  en: {
    car: 'car | cars',
    apple: 'no apples | one apple | {count} apples'
  }
}
```

``` vue
<template>
  <p>{{ $tc('car', 1) }}</p>
  <p>car</p>

  <p>{{ $tc('car', 2) }}</p>
  <p>cars</p>

  <p>{{ $tc('apple', 0) }}</p>
  <p>no apples</p>

  <p>{{ $tc('apple', 1) }}</p>
  <p>one apple</p>

  <p>{{ $tc('apple', 10, { count: 10 }) }}</p>
  <p>10 apples</p>

  <p>{{ $tc('apple', 10) }}</p>
  <p>10 apples</p>
</template>
```



## 时间格式化（ Datetime Formatting ）

> use the options : [Intl.DateTimeFormat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/DateTimeFormat) .

``` typescript
// i18n/index.ts

const datetimeFormats = {
  'en-US': {
    short: {
      year: 'numeric', month: 'short', day: 'numeric'
    },
    long: {
      year: 'numeric', month: 'short', day: 'numeric',
      weekday: 'short', hour: 'numeric', minute: 'numeric'
    }
  },
  'ja-JP': {
    short: {
      year: 'numeric', month: 'short', day: 'numeric'
    },
    long: {
      year: 'numeric', month: 'short', day: 'numeric',
      weekday: 'short', hour: 'numeric', minute: 'numeric', hour12: true
    }
  }
}

const i18n = createI18n({
  datetimeFormats
})
```

``` html
<template>
  <!-- currency locale: en-US -->
  
  <p>{{ d(new Date(), 'short') }}</p>
  <p>Apr 19, 2017</p>

  <p>{{ d(new Date(), 'long', 'ja-JP') }}</p>
  <p>2017年4月19日(水) 午前2:19</p>
</template>
```



## 数字格式化（ Number Formatting）

> use the options :  [Intl.NumberFormat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat/NumberFormat) .

``` typescript
// i18n/index.ts

const numberFormats = {
  'en-US': {
    currency: {
      style: 'currency', currency: 'USD', notation: 'standard'
    },
    decimal: {
      style: 'decimal', minimumFractionDigits: 2, maximumFractionDigits: 2
    },
    percent: {
      style: 'percent', useGrouping: false
    }
  },
  'ja-JP': {
    currency: {
      style: 'currency', currency: 'JPY', useGrouping: true, currencyDisplay: 'symbol'
    },
    decimal: {
      style: 'decimal', minimumSignificantDigits: 3, maximumSignificantDigits: 5
    },
    percent: {
      style: 'percent', useGrouping: false
    }
  }
}

const i18n = createI18n({
  numberFormats
})
```

``` html
<!-- 
	value: number
	key: string
	locale: Locale
	args: {
		[key: string]: string
	}
	options: NumberOptions
-->

<!-- currency locale: en-US -->

<!-- n(value, key) -->
<p>{{ n(10000, 'currency') }}</p>
<p>$10,000.00</p>

<!-- n(value, key, locale) -->
<p>{{ n(10000, 'currency', 'ja-JP') }}</p>
<p>￥10,000</p>

<!-- n(value, args) -->
<p>{{ n(10000, 'currency', 'ja-JP', { useGrouping: false }) }}</p>
<p>￥10000</p>

<!-- n(value, key, args) -->
<p>{{ n(987654321, 'currency', { notation: 'compact' }) }}</p>
<p>$988M</p>


<p>{{ n(0.99123, 'percent') }}</p>
<p>99%</p>

<p>{{ n(0.99123, 'percent', { minimumFractionDigits: 2 }) }}</p>
<p>99.12%</p>

<p>{{ n(12.11612345, 'decimal') }}</p>
<p>12.12</p>

<p>{{ n(12145281111, 'decimal', 'ja-JP') }}</p>
<p>12,145,000,000</p>
```



> 时间和数字格式化都可以 Custom Format 自定义格式化后的数据，比如小数点前后使用不同的样式，但是感觉用处不大，有需要再查文档。



## 回退（ Fallbacking ）

- 隐式回退

  默认会自动隐性回退。
  比如 `de-DE-bavarian` ，会按一下顺序找 locale 回退：

  1. `de-DE-bavarian`
  2. `de-DE`
  3. `de`

  > 如果不想启用回退，在后面添加 `!` ，e.g. `de-DE!` 。

- 显示回退

  在 `createI18n` 中配置 `fallbackLocale` 。

  ``` typescript
  const i18n = createI18n({
    locale: 'en',
    fallbackLocale: 'en', // ['fr', 'en']
    messages
  })
  ```

  配置后，如果获取找不到当前语言的翻译值，就会回退到指定 locale 的翻译值。

  

>  在开发环境下会有 warning ，可以通过配置 `fallbackWarn: false` 取消。



## 组件/标签插值（ Component Interpolation ）

场景：在一句话中，可能部分语句需要额外用其它标签包裹，比如 `a` 包裹变成链接，或者 `span` 包裹添加样式。在这种情况，可能一句话就得拆成前中后 3 段翻译，为了解决这个问题，所以有了`Translation component` (` i18n-t` )  。

- 列表插值：

  ``` vue
  <template>
  	<!--
  		tag: 生成的根标签，没有设置的话，默认 fragment
  		keypath: 使用的翻译的 key
  		scope="global": 如果不加这个，会有 Not found parent scope. use the global scope 的警告
  	-->
    <i18n-t keypath="term" tag="label" for="tos" scope="global">
      <a href="/term" target="_blank">{{ $t('tos') }}</a>
    </i18n-t>
  
  	<!-- result -->
  	<label for="tos">
      I accept xxx <a href="/term" target="_blank">Term of Service</a>.
    </label>
  </template>
  ```

  ``` typescript
  const messages = {
    en: {
      tos: 'Term of Service',
      term: 'I accept xxx {0}.'
    }
  }
  ```

- slot

  ``` vue
  <template>
  	<i18n-t keypath="info" tag="p">
      <template #limit>
        <span>15</span>
      </template>
      <template #action>
        <a href="/change">{{ $t('change') }}</a>
      </template>
    </i18n-t>
  
  	<!-- result -->
  	<p>
      You can <a href="/change">change your flight</a> until <span>15</span> minutes from departure.
    </p>
  </template>
  ```

  ``` typescript
  const messages = {
    en: {
      info: 'You can {action} until {limit} minutes from departure.',
      change: 'change your flight'
    }
  }
  ```

  > 在 `i18n-t` component 下，slots props 不支持。

> 还有复数模式支持，支持 scoped ，不常用，查看文档。



## TypeScript Support

要给每个翻译的字段写类型，太麻烦了。

``` typescript
// i18n.d.ts
// global scope

/**
 * you need to import the some interfaces
 */
import {
  DefineLocaleMessage,
  DefineDateTimeFormat,
  DefineNumberFormat
} from 'vue-i18n'

declare module 'vue-i18n' {
  // define the locale messages schema
  export interface DefineLocaleMessage {
    hello: string
    menu: {
      login: string
    }
    errors: string[]
  }

  // define the datetime format schema
  export interface DefineDateTimeFormat {
    short: {
      hour: 'numeric'
      minute: 'numeric'
      second: 'numeric'
      timeZoneName: 'short'
      timezone: string
    }
  }

  // define the number format schema
  export interface DefineNumberFormat {
    currency: {
      style: 'currency'
      currencyDisplay: 'symbol'
      currency: string
    }
  }
}
```



## 懒加载（ Lazy Loading ）

``` typescript
// i18n/index.ts

import { nextTick } from 'vue'
import { createI18n } from 'vue-i18n'
import en from './locales/en'

import type { I18n, I18nOptions } from 'vue-i18n'

const SUPPORT_LOCALES = ['en', 'zh'] as const
export type SupportLocales = (typeof SUPPORT_LOCALES)[number]

const options: I18nOptions = {
  legacy: false, // you must set `false`, to use Composition API
  locale: 'en', // set locale
  fallbackLocale: 'en', // set fallback locale
  messages: { en } // set locale messages
}
export let i18n: I18n<
  Record<string, unknown>,
  Record<string, unknown>,
  Record<string, unknown>,
  SupportLocales,
  false
>
export function initI18n() {
  /**
   * setup vue-i18n with i18n resources with global type definition.
   * if you define the i18n resource schema in your `*.d.ts`, these is checked with typeScript.
   *
   * - The first type parameter: If false, the type is a Composer instance for the Composition API, if true, the type is a VueI18n instance for the legacy API.
   * - The second type parameter: specifies a type hint for options.
   */
  i18n = createI18n<false, typeof options>(options)
  setI18nLanguage('en')
  return i18n
}

function setI18nLanguage(locale: SupportLocales) {
  i18n.global.locale.value = locale

  /**
   * NOTE:
   * If you need to specify the language setting for headers, such as the `fetch` API, set it here.
   * The following is an example for axios.
   *
   * axios.defaults.headers.common['Accept-Language'] = locale
   */
  document.querySelector('html')?.setAttribute('lang', locale)
}

async function loadLocaleMessages(locale: SupportLocales) {
  // load locale messages with dynamic import
  const messages = await import(`./locales/${locale}.ts`)

  // set locale and locale message
  i18n.global.setLocaleMessage(locale, messages.default)

  return nextTick()
}

export async function changeLocale(locale: SupportLocales) {
  if (i18n.global.locale.value === locale) return
  if (!i18n.global.availableLocales.includes(locale)) {
    await loadLocaleMessages(locale)
  }
  setI18nLanguage(locale)
}
```

``` typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import { initI18n } from './i18n'

const app = createApp(App)

app.use(initI18n()).mount('#app')
```

