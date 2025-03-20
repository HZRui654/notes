# Vuetify

[Vuetify](https://vuetifyjs.com/)

环境：vue3



## 安装引入

``` bash
npm i vuetify -S

```

- 整体引入

  ``` typescript
  // main.ts
  import { createApp } from 'vue'
  import App from './App.vue'
  
  // Vuetify
  import 'vuetify/styles'
  import { createVuetify } from 'vuetify'
  import * as components from 'vuetify/components'
  import * as directives from 'vuetify/directives'
  
  const vuetify = createVuetify({
    components,
    directives,
  })
  
  createApp(App).use(vuetify).mount('#app')
  
  ```

- 按需引入

  ``` bash
  npm i vite-plugin-vuetify -D
  
  ```
  
  ``` typescript
  // vite.config.ts
  import vuetify from 'vite-plugin-vuetify'
  
  plugins: [
    vue(),
    vuetify({ 
      // Enabled by default
      // autoImport: true
      
      // 引入实验性组建
      // autoImport: {
      //   labs: true
      // }
      
      // 忽略组建或者指令
      // autoImport: {
      //   ignore: [
      // 			'VAlert',
      //      'Rippple'
      //   ]
      // }
      
      // 不引入所有样式
      // style: 'none'
      
      // 引入 style 配置文件
      style: {
        configFile: 'src/assets/scss/vuetify.scss'
      }
    })
  ]
  
  ```
  
  ``` scss
  // src/assets/scss/vuetify.scss
  @forward 'vuetify/settings' with (
    // 如果使用的 css reset 已经综合了 vuetify 官方指定的相关 reset
    // 不使用 vuetify 的样式重置
    $reset: false,
    
    // 会有很多用不上的颜色，导致打包文件变大，故不使用预设好的颜色样式
    $color-pack: false,
    
    // 已经使用了原子化的 css 库，不使用 vuetify 的工具类
    $utilities: false
  );
  
  ```
  
  ``` typescript
  // plugins/vuetify.ts
  import 'vuetify/styles'
  import { createVuetify } from 'vuetify'
  
  export default createVuetify()
  
  ```
  
  ``` typescript
  // main.ts
  import vuetify from '@/plugins/vuetify'
  
  createApp(App).use(vuetify).mount('#app')
  
  ```



## global config

``` typescript
// plugins/vuetify.ts
import 'vuetify/styles'
import { createVuetify } from 'vuetify'

export default createVuetify()

```



### icon

按需引入 icon ，优先推荐 `mdi` ，也可以自行引入其它。

``` bash
npm i @mdi/font -D

```

``` typescript
// plugins/vuetify.ts
import 'vuetify/styles'
import { createVuetify } from 'vuetify'
import { aliases, mdi } from 'vuetify/iconsets/mdi-svg'

export default createVuetify({
  icons: {
    defaultSet: 'mdi',
    aliases,
    sets: {
      mdi
    }
  }
})

```





### theme

默认提供了 `light` 和 `dark` 两种 theme 。

``` typescript
import 'vuetify/styles'
import { createVuetify } from 'vuetify'

import type { ThemeDefinition } from 'vuetify'

const myCustomLightTheme: ThemeDefinition = {
  dark: false,
  colors: {
    background: '#FFFFFF',
    surface: '#FFFFFF',
    primary: '#6200EE',
    'primary-darken-1': '#3700B3',
    secondary: '#03DAC6',
    'secondary-darken-1': '#018786',
    error: '#B00020',
    info: '#2196F3',
    success: '#4CAF50',
    warning: '#FB8C00',
    // 添加自定义颜色
    // We have omitted the standard color properties here to emphasize the custom one that we've added
    something: '#00ff00'
  }
}

export default createVuetify({
  theme: {
    defaultTheme: 'myCustomLightTheme',
    // 添加颜色变体
    variations: {
      colors: ['primary', 'secondary'],
      lighten: 1,
      darken: 2,
    },
    themes: {
      myCustomLightTheme,
    }
  }
})

```

``` vue
<template>
 	<div class="bg-something on-something">background color with appropriate text color contrast</div>
  <div class="text-something">text color</div>
  <div class="border-something">border color</div>

  <div class="text-primary-lighten-1">text color</div>
  <div class="text-primary-darken-1">text color</div>
  <div class="text-primary-darken-2">text color</div>
</template>

<style>
  .custom-class {
    background: rgb(var(--v-theme-something))
    color: rgba(var(--v-theme-on-something), 0.9)
  }
</style>
```



### defaults + aliases

给组件设置默认属性。

``` typescript
import { createVuetify } from 'vuetify'
import { VChip } from 'vuetify/components/VChip'

export default createVuetify({
  aliases: {
    VChipPrimary: VChip,
  },

  defaults: {
    // class 和 style 不能使用该属性设置
    global: {
      ripple: false,
    },
    
    VCard: {
      VBtn: { variant: 'outlined' },
    },
    // 可以通过在这配置类名和 style 来覆盖组件样式
    VChipPrimary: {
      class: 'v-chip--primary',
    },
    VChip: {
      class: 'v-chip--custom',
    },
  },
})
```





## i18n

集成 `vue-i18n`

``` typescript
// i18n/locales/en.ts
import { en } from 'vuetify/locale'

export default {
  $vuetify: {
    ...en,
    dataIterator: {
      rowsPerPageText: 'Items per page:',
      pageText: '{0}-{1} of {2}'
    }
  }
}

```

``` ts
// plugins/vuetify.ts
import { createVuetify } from 'vuetify'
import { createVueI18nAdapter } from 'vuetify/locale/adapters/vue-i18n'
import { useI18n } from 'vue-i18n'
import { i18n } from '../i18n'

export const vuetify = createVuetify({
  locale: {
    adapter: createVueI18nAdapter({ i18n, useI18n })
  }
})

```

