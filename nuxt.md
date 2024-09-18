# Nuxt

[Nuxt](https://nuxt.com/)

文档版本：`v3.13`

环境：**Node.js** > `v18.0.0`



## Installation

``` bash
pnpm dlx nuxi@latest init <project-name>

```



## Configuration

### Nuxt Configuration

`nuxt` 的默认配置已经涵盖了大部分可能情况，可以通过根目录 `nuxt.config.ts` 来拓展或者覆盖默认配置。

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  // My Nuxt config
})

```

> `defineNuxtConfig` 全局默认支持，不需要再单独引入。



#### Environment

可以根据不同环境提供或者覆盖配置：

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  $production: {
    routeRules: {
      '/**': { isr: true }
    }
  },
  $development: {
    // ...
  }
})

```



`runtimeConfig` Api 可以暴露自定义的环境变量，通常只有在 `server-side` 才可以访问这些变量。定义在 `runtimeConfig.public` 中的变量才可以在 `client-side` 中访问。

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  runtimeConfig: {
    // The private keys which are only available server-side
    apiSecret: '123',
 
    // Keys within public are also exposed client-side
    public: {
      apiBase: '/api'
    }
  }
})

```

通过 `useRuntimeConfig` 访问这些变量：

``` vue
<script setup lang="ts">
const runtimeConfig = useRuntimeConfig()
</script>

```



### App Configuration

根目录 `app.config.ts` 可以暴露在 `build` 时使用的自定义公共变量，与 `runtimeConfig` 不同的是，这些变量不能被环境变量覆盖。

``` typescript
// app.config.ts
export default defineAppConfig({
  title: 'Hello Nuxt',
  theme: {
    dark: true,
    colors: {
      primary: '#ff0000'
    }
  }
})

```

> `defineAppConfig` 全局默认支持，不需要再单独引入。

通过 `useAppConfig` 访问这些变量：

``` vue
<script setup lang="ts">
const appConfig = useAppConfig()
</script>

```



> 区分使用 `runtimeConfig` 和 `app.config`：
>
> `runtimeConfig` ：在构建以后，还需要根据不同 environment 进行变换的属性，例如 `token` ，`baseUrl` 等。
>
> `app.config` ：app 构建时所需要的一些不敏感的数据，例如：`theme variant` ，`title` 等。



## Views

- `app.vue` ：入口。

  ``` vue
  <template>
    <div>
      <NuxtLayout>
        <NuxtPage />
      </NuxtLayout>
    </div>
  </template>
  
  ```

- `components/` ：存放公共组件，使用时会自动引入，不需要手动。

- `pages/` ：其中每个文件代表了各自路由页面。新建 `pages/index.vue`（首页，会自动引入） 并且将 `<NuxtPage />` 组件添加到 `app.vue` 即可启用。

- `layouts/` ：部分页面的公共布局。`layouts/default.vue` 会被自动使用（如果只有一种 `layout` ，建议直接使用 `app.vue` ）。

  ``` vue
  <template>
    <div>
      <AppHeader />
      <slot />
      <AppFooter />
    </div>
  </template>
  
  ```

  



拓展 `HTML` 模版：

使用 `Nitro` 插件注册一个 hook 。`render:html` 允许你在 `HTML` 发至 `client-side` 之前进行修改。

``` typescript
// server/plugins/extend-html.ts
export default defineNitroPlugin((nitroApp) => {
  nitroApp.hooks.hook('render:html', (html, { event }) => {
    // This will be an object representation of the html template.
    console.log(html)
    html.head.push(`<meta name="description" content="My custom description" />`)
  })
  // You can also intercept the response here.
  nitroApp.hooks.hook('render:response', (response, { event }) => { console.log(response) })
})

```



## Assets

- `public/` ：会原封不动的在打包文件中，开发时可以直接通过 `/` 访问其中的文件。
- `assets/` ：存放需要经过打包工具处理的静态资源，例如 `scss` ，`font` ，`svg` 等。没有硬性要求用 `assets` 命名，只是一般都用这个。开发时可以通过 `~/`访问其中的文件。



## Styling

### 引入全局 style

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  app: { 
    head: {
      // 通过 link 引入
      // 也可以如 Views 提到使用 `Nitro` 插件为 Head 添加 link
      link: [{ rel: 'stylesheet', href: 'https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css' }]
    }
  },
  vite: {
    // css: ['~/assets/css/main.css']
    // css: ['animate.css'] // 第三方 UI 库
    css: {
      preprocessorOptions: {
        scss: {
          additionalData: '@use "~/assets/_colors.scss" as *;'
        }
      }
    }
  }
})

```



### 动态引入

使用 `useHead` ：

``` vue
<script setup lang="ts">
useHead({
  link: [{ rel: 'stylesheet', href: 'https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css' }]
})
</script>

```



### 使用 `SASS` 

``` bash
pnpm add -D sass

```



### 使用 `PostCss` 

``` typescript
export default defineNuxtConfig({
  postcss: {
    plugins: {
      'postcss-nested': {},
      'postcss-custom-media': {}
    }
  }
})

```

`Nuxt` 已经默认引入了部分 plugins：

- `postcss-import` : improves the `@import` rule
- `postcss-url` : transforms `url()` statements
- `autoprefixer` : automatically adds vendor prefixes
- `cssnano` : minification and purge



## Routing

`pages/` 下的每个文件都会根据其文件名生成对应的 `route` 。



### `<NuxtLink>`

用于页面跳转。

**如果页面有该标签，那么 `Nuxt` 会预加载对应目标页面的内容**。

``` vue
<template>
  <header>
    <nav>
      <ul>
        <li><NuxtLink to="/about">About</NuxtLink></li>
        <li><NuxtLink to="/posts/1">Post 1</NuxtLink></li>
        <li><NuxtLink to="/posts/2">Post 2</NuxtLink></li>
      </ul>
    </nav>
  </header>
</template>

```



### Route Parameters

通过 `useRoute` hook 获取。

``` vue
<script setup lang="ts">
const route = useRoute()

// When accessing /posts/1, route.params.id will be 1
console.log(route.params.id)
</script>

```



### Route Middleware

路由中间键。可以将跳转到某个特殊路由前要**提前执行**的代码放到这里。

- `Anonymous (or inline) route middleware` ：匿名/內联中间键，直接在目标路由中定义；
- `Named route middleware` ：命名路由中间键，放在 `middleware/` 下，页面使用时会异步导入自动加载，使用 `kebab-case` 的命名格式；
- `Global route middleware` ：全局路由中间键，放在 `middleware/` 下，增加 `.global` 后缀，会在每个路由加载前自动执行。



为 `/dashbaord` 添加 `auth` middleware ：

``` typescript
// middleware/auth.ts
function isAuthenticated(): boolean { return false }
// ---cut---
export default defineNuxtRouteMiddleware((to, from) => {
  // isAuthenticated() is an example method verifying if a user is authenticated
  if (isAuthenticated() === false) {
    return navigateTo('/login')
  }
})

```

``` vue
<script setup lang="ts">
definePageMeta({
  middleware: 'auth'
})
</script>

<template>
  <h1>Welcome to your dashboard</h1>
</template>

```



### Route Validation

每个页面都可以通过 `definePageMeta` 中的 `validate` 属性进行验证。

`validate` 接收一个 `route` 参数，你可以通过返回 `boolean` 决定验证是否通过，是否渲染该页面。如果是 `false` ，并且没有其它匹配的页面可跳转，则会导致 `404` 错误。你可以返回一个 object 包含 `statusCode`/`statusMessage` 去立即响应错误（不会再去检查是否匹配其它错误）。

**如果你的使用场景很复杂，建议使用 `middleware` 代替。**

``` vue
<script setup lang="ts">
// pages/posts/[id].vue
definePageMeta({
  validate: async (route) => {
    // Check if the id is made up of digits
    return typeof route.params.id === 'string' && /^\d+$/.test(route.params.id)
  }
})
</script>

```



## SEO and Meta

通过 head 配置，组合式 hooks 以及组件来优化项目的 SEO。

`Nuxt` 已经提供了合理的默认值，如果有需要，可以进行覆盖。

`nuxt.config.ts` 中 `app.head` 可以自定义你整个 app 的 head ：

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  app: {
    head: {
      charset: 'utf-8',
      viewport: 'width=device-width, initial-scale=1',
    }
  }
})

```

> 但是使用 `app.head` 这种方式不能使用响应值。
>
> 所以更建议在 `app.vue` 中使用 `useHead` 。



### `useHead`

`app.vue` ：

``` vue
<script setup lang="ts">
useHead({
  title: 'My App',
  meta: [
    { name: 'description', content: 'My amazing site.' }
  ],
  bodyAttrs: {
    class: 'test'
  },
  script: [ { innerHTML: 'console.log(\'Hello world\')' } ]
})
</script>

```



**Title Template**

可以使用 `titleTemplate` 去提供一个动态的自定义标题。例如：可以为所有的页面提供一个标题。

`titleTemplate` 也可以是 string ，`%s` 为占位符。

如果你想要使用一个 function 完全控制标题，那就最好不要在 `nuxt.config.ts` ，而是在 `app.vue` 中使用 `useHead` 设置：

``` vue
<script setup lang="ts">
useHead({
  titleTemplate: (titleChunk) => {
    return titleChunk ? `${titleChunk} - Site Title` : 'Site Title';
  }
})
</script>

```

根据以上的例子，如果你在 `MyPage` 中设置标题 `My Page` ，那么 `MyPage` 最终得到的标题为 'My Page - Site Title' 。



**Body Tags**

可以通过 `tagPosition: 'bodyClose'` 选项让 tag 插入到 body tag 的最后。

``` vue
<script setup lang="ts">
useHead({
  script: [
    {
      src: 'https://third-party-script.com',
      // valid options are: 'head' | 'bodyClose' | 'bodyOpen'
      tagPosition: 'bodyClose'
    }
  ]
})
</script>

```



### `useSeoMeta`

可以直接通过一个 object 定义项目的 SEO meta tags，提供了 `Typescript` 支持，有效避免写错属性等一些常见错误。

`app.vue` ：

``` vue
<script setup lang="ts">
useSeoMeta({
  title: 'My Amazing Site',
  ogTitle: 'My Amazing Site',
  description: 'This is my amazing site, let me tell you all about it.',
  ogDescription: 'This is my amazing site, let me tell you all about it.',
  ogImage: 'https://example.com/image.png',
  twitterCard: 'summary_large_image',
})
</script>

```



### Components

`Nuxt` 提供了一组可以直接在组件模版中使用的组件： `<Title>` ，`<Base>` ，`<NoScript>` ，`<Style>` ，`<Meta>` ，`<Link>` ，`<Body>` ，`<Html>` 和 `<Head>` 。这些组件在模版中必须使用 `capitalized` 格式，不然会和原生元素冲突。

`app.vue` ：

``` vue
<script setup lang="ts">
const title = ref('Hello World')
</script>

<template>
  <div>
    <Head>
      <Title>{{ title }}</Title>
      <Meta name="description" :content="title" />
      <Style type="text/css" children="body { background-color: green; }" ></Style>
    </Head>

    <h1>{{ title }}</h1>
  </div>
</template>

```



### Head 中非响应式属性

以下是 `useHead` ，`app.head` 和组件中的非响应式属性的类型：

``` typescript
interface MetaObject {
  title?: string
  titleTemplate?: string | ((title?: string) => string)
  templateParams?: Record<string, string | Record<string, string>>
  base?: Base
  link?: Link[]
  meta?: Meta[]
  style?: Style[]
  script?: Script[]
  noscript?: Noscript[];
  htmlAttrs?: HtmlAttributes;
  bodyAttrs?: BodyAttributes;
}

```



## Transitions

`Nuxt` 使用 `Vue` 的 `<Transition>` 标签提供该功能。`page` 和 `layout` 的动画配置是分开的。

> `mode` 默认都是 `out-in` 。
>
> 将 `pageTransition`/`layoutTransition` 设置为 `false` 可以禁止动画效果。



### Page

在 `nuxt.config.ts` 为所有页面添加动画：

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  app: {
    pageTransition: { name: 'page', mode: 'out-in' }
  }
})

```

在 `app.vue` 中添加动画效果：

``` vue
<template>
  <NuxtPage />
</template>

<style>
.page-enter-active,
.page-leave-active {
  transition: all 0.4s;
}
.page-enter-from,
.page-leave-to {
  opacity: 0;
  filter: blur(1rem);
}
</style>

```



也可以使用 `definePageMeta` 中的 `pageTransition` 属性单独为页面指定不一样的动画：

``` vue
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'rotate'
  }
})
</script>

```



### Layout

`nuxt.config.ts` ：

``` typescript
export default defineNuxtConfig({
  app: {
    layoutTransition: { name: 'layout', mode: 'out-in' }
  }
})

```

同样是在 `app.vue` 中添加动画效果。



也同样单独在页面指定不同动画：

``` vue
<script setup lang="ts">
definePageMeta({
  layout: 'orange',
  layoutTransition: {
    name: 'slide-in'
  }
})
</script>

```



### Javascript Hooks

``` vue
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'custom-flip',
    mode: 'out-in',
    onBeforeEnter: (el) => {
      console.log('Before enter...')
    },
    onEnter: (el, done) => {},
    onAfterEnter: (el) => {}
  }
})
</script>

```



### Dynamic

可以在 `middleware` 中动态修改页面的 `pageTransition` 的 `name` ，从而在不同操作下使用不同动画：

``` vue
<script setup lang="ts">
// pages/[id].vue
definePageMeta({
  pageTransition: {
    name: 'slide-right',
    mode: 'out-in'
  },
  middleware (to, from) {
    if (to.meta.pageTransition && typeof to.meta.pageTransition !== 'boolean')
      to.meta.pageTransition.name = +to.params.id! > +from.params.id! ? 'slide-left' : 'slide-right'
  }
})
</script>

```



### With NuxtPage

当 `<NuxtPage>` 用于 `app.vue` 时， 也可以通过 `transition` 属性添加动画：

``` vue
<template>
  <div>
    <NuxtLayout>
      <NuxtPage :transition="{
        name: 'bounce',
        mode: 'out-in'
      }" />
    </NuxtLayout>
  </div>
</template>

```

> 注意：使用这种方式定义的动画属性，不能被页面的 `definePageMeta` 覆盖。



## Data Fetching

`Nuxt` 使用了两个组合式 hooks 和一个内置的库来解决在 client 和 server 两个环境下的数据请求问题。

- `useFetch` ：在 setup 中最直接常用的方式。
- `$fetch` ：在用户交互时需要请求数据时最好的方式，比如提交表单等。
- `useAsyncData` ：与 `$fetch` 相结合，可以对请求进行更细粒度的控制。

> 由于 `Nuxt` 在 client 和 server 两个环境下执行的同一套代码，如果直接使用 `$fetch` 会在两个端分别执行一次，即**执行两次**，所以需要使用组合式 hooks 确保只执行一次。
>
> 使用 hooks 在 server 端获取完数据后，可以在 client 端使用 `useNuxtApp().payload` 直接获取，避免重复请求获取一样的数据。

> hooks 需要 `setup`  下的 top level 调用，而 `$fetch` 在哪个位置都可以调用。



> `Nuxt` 使用 Vue 的异步组件 `<Suspense>` 来阻止在请求的数据可以被 client 端获取到前进行导航。可以利用这个特性进行一些操作，例如使用 `<NuxtLoadingIndicator>` 去添加一个加载进度条。



### `useFetch`

本质上是在 `useAsyncData` 和 `$fetch` 上包装了一层。

``` vue
<script setup lang="ts">
const { data: count } = await useFetch('/api/count')
</script>

<template>
  <p>Page visits: {{ count }}</p>
</template>

```

> 相当于 `useAsyncData(url, () => $fetch(url))` 。
>
> 有些情况下并不适合使用，比如我们在使用 CMS 或者某些第三方库获取 layer ，使用 `useAsyncData` 可以添加对返回数据的处理逻辑。

> 默认使用 url 作为 key ，也可以在最后一个参数 options 中传入自定义 key 覆盖。



### `$fetch`

`Nuxt` 内置了 [ofetch](https://github.com/unjs/ofetch) 库，使用别名 `$fetch` 。

``` vue
<script setup lang="ts">
async function addTodo() {
  const todo = await $fetch('/api/todos', {
    method: 'POST',
    body: {
      // My todo data
    }
  })
}
</script>

```

> 直接使用 `$fetch` 将不会有上面提到的**防止请求两次**和**请求完成前阻止导航**的功能，所以建议在用户交互时使用或者在初始化数据时和 `useAsyncData` 结合使用。



### `useAsyncData`

负责包装异步逻辑并且在获取结果后进行返回。

``` vue
<script setup lang="ts">
const { data, error } = await useAsyncData('users', () => myGetFunction('users'))

// This is also possible:
const { data, error } = await useAsyncData(() => myGetFunction('users'))
</script>

```

第一个参数是唯一 key ，第二个参数是请求数据的函数。

key 用于缓存请求函数的返回值，如果直接传入请求函数，那么会自动生成 key ，但是自动生成时仅考虑当前文件，所以不一定全局唯一，建议还是**调用时自己传入唯一 key **。

> 唯一 key 对于在组件中使用 `useNuxtData` 共享数据和重新或者某些特定数据时非常有用。



和 `$fetch` 搭配使用很方便。

``` vue
<script setup lang="ts">
const { data: discounts, status } = await useAsyncData('cart-discount', async () => {
  const [coupons, offers] = await Promise.all([
    $fetch('/cart/coupons'),
    $fetch('/cart/offers')
  ])

  return { coupons, offers }
})
// discounts.value.coupons
// discounts.value.offers
</script>

```



### Return Values

`useFetch` 和 `useAsyncFetch` 有一样的返回值：

- `data` ：请求函数返回的数据。

- `refresh` / `execute` ：可以用于手动重新执行请求。一般情况下，`refresh` 要在请求完成后才可再次执行。

  `execute` 本质上是和 `refresh` 相同的，但是在 `not immediate` 的情景下更语义化。

  ``` vue
  <script setup lang="ts">
  const { data, error, execute, refresh } = await useFetch('/api/users')
  </script>
  
  <template>
    <div>
      <p>{{ data }}</p>
      <button @click="() => refresh()">Refresh data</button>
    </div>
  </template>
  
  ```

  > `refreshNuxtData(key)` 是一样的效果。

- `clear` 可以将缓存的数据清空，`data` => `undefined` ，`error` => `null` ，`status` => `idle` ，标记当前正在 `pending` 的请求为 `cancle` 。

  ``` vue
  <script setup lang="ts">
  const { data, clear } = await useFetch('/api/users')
  
  const route = useRoute()
  watch(() => route.path, (path) => {
    if (path === '/') clear()
  })
  </script>
  
  ```

  > `clearNuxtData(key)` 是一样的效果。

- `error` ：请求失败时的错误。

- `status` ：表明当前请求的状态（`idle` ，`pending` ，`success` ，`error`）。

> `data` ，`error` 和 `status` 都是 Vue 的响应式变量，即 `ref` 。



### Options

`useFetch` 和 `useAsyncFetch` 的最后一个入参都是一个 options ，包含了一系列可选值，方便我们更好的控制组合式 hooks 请求的行为，例如停止导航，缓存和处理错误。

- `Lazy` ：默认情况下会在请求完数据再进行导航，但是在 client 环境可以通过 `lazy` 选项忽略该功能，不过就需要自己通过 Return Values 的 `status`手动控制 loading 的状态了。

  > 也可以直接使用 `useLazyFetch` 和 `useLazyAsyncData` 代替传入 `lazy` 选项的方式。

  ``` vue
  <script setup lang="ts">
  const { status, data: posts } = useFetch('/api/posts', {
    lazy: true
  })
  </script>
  
  <template>
    <!-- you will need to handle a loading state -->
    <div v-if="status === 'pending'">
      Loading ...
    </div>
    <div v-else>
      <div v-for="post in posts">
        <!-- do something -->
      </div>
    </div>
  </template>
  
  ```

- `Serve` ：默认情况下数据请求在 serve 和 client 端都会执行，如果传入 `serve: false` ，那么在服务端生成页面的时候不会请求数据，但是生成页面以后，还是会等待数据加载完成导航加载该页面。

  > 与 `lazy` 选项配合，就可以让页面在加载后再去获取数据。适用于这些数据对 SEO 没有影响的情况下。

- `Pick` ：通过指定要保存的属性，来减少储存在你 HTML document 的数据。

  ``` vue
  <script setup lang="ts">
  /* only pick the fields used in your template */
  const { data: mountain } = await useFetch('/api/mountains/everest', {
    pick: ['title', 'description']
  })
  </script>
  
  <template>
    <h1>{{ mountain.title }}</h1>
    <p>{{ mountain.description }}</p>
  </template>
  
  ```

- `Transform` ：如果 `Pick` 属性还无法满足需求，需要对多个 object 控制和映射，可以使用该选项。

  ``` vue
  <script setup lang="ts">
  const { data: mountains } = await useFetch('/api/mountains', {
    transform: (mountains) => {
      return mountains.map(mountain => ({ title: mountain.title, description: mountain.description }))
    }
  })
  </script>
  
  ```

- `Watch` ：通过侦听响应式变量，如果变量改变了就重新获取数据。

  ``` vue
  <script setup lang="ts">
  const id = ref(1)
  
  const { data, error, refresh } = await useFetch(`/api/users/${id.value}`, {
    watch: [id]
  })
  </script>
  
  ```

  > 要注意的是，在以上例子中，重新获取数据时请求的 url 还和第一次一样，并不会跟着模版字符串改变。除非使用 `Not immediate` 选项。

- `Query` ：如果 `Watch` 无法满足需求，你需要在 url 变量变的时候重新请求，那么可以将变量作为 query 值传入。

  ``` vue
  <script setup lang="ts">
  const id = ref(null)
  
  const { data, status } = useLazyFetch('/api/user', {
    query: {
      user_id: id
    }
  })
  </script>
  
  ```

- `Immediate` ：默认情况下 hooks 都会立即执行请求，可以通过传入 `immediate: false` 来手动选择执行请求的时机。

  ``` vue
  <script setup lang="ts">
  const { data, error, execute, status } = await useLazyFetch('/api/comments', {
    immediate: false
  })
  </script>
  
  <template>
    <div v-if="status === 'idle'">
      <button @click="execute">Get data</button>
    </div>
  
    <div v-else-if="status === 'pending'">
      Loading comments...
    </div>
  
    <div v-else>
      {{ data }}
    </div>
  </template>
  
  ```



### Headers 和 cookies

在 browser 的请求默认会带上 cookies ，但是在 server 端的不会。

可以使用 `useRequestHeaders` 使两端都带上同样的 headers ，即在 server 端也可以在 client 发送的 headers 中获取到对应的值：

``` vue
<script setup lang="ts">
const headers = useRequestHeaders(['cookie'])

const { data } = await useFetch('/api/me', { headers })
</script>

```

> 注意：如果调用的第三方 api ，headers 中的有些属性可能会导致安全风险，所以最好指定只添加有需要的 header 属性。

如果不想从 client ，而是从其它地方获取 cookie 值再通过 SSR 返回到 client 端，就需要自己处理：

``` typescript
// composables/fetch.ts
import { appendResponseHeader } from 'h3'
import type { H3Event } from 'h3'

export const fetchWithCookie = async (event: H3Event, url: string) => {
  /* Get the response from the server endpoint */
  const res = await $fetch.raw(url)
  /* Get the cookies from the response */
  const cookies = res.headers.getSetCookie()
  /* Attach each cookie to our incoming Request */
  for (const cookie of cookies) {
    appendResponseHeader(event, 'set-cookie', cookie)
  }
  /* Return the data of the response */
  return res._data
}

```

``` vue
<script setup lang="ts">
// This composable will automatically pass cookies to the client
const event = useRequestEvent()

const { data: result } = await useAsyncData(() => fetchWithCookie(event!, '/api/with-cookie'))

onMounted(() => console.log(document.cookie))
</script>

```



## State Management

`Nuxt` 提供了 `useState` 作为 `ref` 的替代品，使用 `useState` 在整个 SSR 过程中都会保留，并且所有组件都能通过唯一 key 访问对应的值。

> `useState` 中的值会被序列化为 JSON ，所以不要存放无法被序列话为 JSON 的值，例如 classes，functions 或者 symbols 。

``` vue
<script setup lang="ts">
const counter = useState('counter', () => Math.round(Math.random() * 1000))
</script>

<template>
  <div>
    Counter: {{ counter }}
    <button @click="counter++">
      +
    </button>
    <button @click="counter--">
      -
    </button>
  </div>
</template>

```



**最佳实践**

不要在 `setup` 以外的地方使用 `ref` ，可能会导致 server 端的 request 共享状态或者导致内存泄漏。

可以使用 `const useX = () => useState('x')` 代替。



### Initializing State

我们经常需要使用异步获取回来的数据初始化 state ，可以在 `app.vue` 中使用 `callOnce` 工具去实现。

``` vue
<script setup lang="ts">
const websiteConfig = useState('config')

await callOnce(async () => {
  websiteConfig.value = await $fetch('https://my-cms.com/api/website-config')
})
</script>

```



### Pinia

1. 安装

   ``` bash
   npx nuxi@latest module add pinia
   
   ```

2. 在配置中添加 pinia module ：

   ``` typescript
   // nuxt.config.js
   export default defineNuxtConfig({
     // ... other options
     modules: [
       // ...
       '@pinia/nuxt',
     ],
   })
   
   ```

3. 使用：

   ``` typescript
   // stores/website.ts
   export const useWebsiteStore = defineStore('websiteStore', {
     state: () => ({
       name: '',
       description: ''
     }),
     actions: {
       async fetch() {
         const infos = await $fetch('https://api.nuxt.com/modules/pinia')
   
         this.name = infos.name
         this.description = infos.description
       }
     }
   })
   
   ```

   ``` vue
   <script setup lang="ts">
   const website = useWebsiteStore()
   
   await callOnce(website.fetch)
   </script>
   
   <template>
     <main>
       <h1>{{ website.name }}</h1>
       <p>{{ website.description }}</p>
     </main>
   </template>
   
   ```

   

## Error Handling

`Nuxt` 是一个全栈框架，意味着在不同的上下文时都可能发生无法预防的错误：

- 在 Vue 的生命周期中出现的错误（SSR 和 CSR）
- server 和 client 端启动错误（SSR + CSR）
- `Nitro` server 生命周期中出现的错误（server / directory）
- 下载 JS chunks 时出现错误



### Vue Errors

可以使用 `onErrorCaptured` hook 捕捉 Vue errors 。

另外，`Nuxt` 也提供了 `vue:error` hook ，当任何 Vue errors 出现在 top level 时都会被调用。

>  `vue:error` hook 也是基于 `onErrorCaptured`  实现。

如果你使用了一个处理错误的框架，可以通过 `vueApp.config.errorHandle` 提供一个 global handle 。它会接受所有 Vue errors ，即便错误已经被处理。

``` typescript
// plugins/error-handler.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.config.errorHandler = (error, instance, info) => {
    // handle error, e.g. report to a service
  }

  // Also possible
  nuxtApp.hook('vue:error', (error, instance, info) => {
    // handle error, e.g. report to a service
  })
})

```



### Startup Errors

`Nuxt` 也提供了 `app:error` hook 用于捕捉任何启动 app 时出现的错误。包括：

- 运行 `Nuxt plugins` 时；
- 执行 `app:created` ，`app:beforeMount`和 `app:mounted` hooks 时；
- 将 Vue app 渲染成 HTML 时（整个 SSR 阶段）；
- 在 client side mounting app 时，即便你需要在 `onErrorCaptured` 或者 `vue:error` 中处理该错误。



### Nitro Server Errors

你无法处理这些错误，但是可以渲染一个 error page 。



### Errors with JS Chunks

下载 JS Chunks 错误，有可能是因为用户的网络问题，或者是由于新的发布。`Nuxt` 会在路由导航期间出现该错误时强制刷新页面，重新获取资源。

你可以通过设置 `experimental.emitRouteChunkError` 为 false 关闭该功能。

>  如果想要手动控制，需要查看插件文档。



### Error Page

当 `Nuxt` 遇到致命的错误（在 server 端无法处理，或者在 client 端标记 `fatal: true` 的错误）时，会渲染一个 JSON response（当请求头存在 `Accept: application/json`存在）或者返回一个全屏的错误页面。

在 server 端整个生命周期中可能触发的错误的时间点：

-  处理 `Nuxt` plugins 时；
- 将 Vue app 渲染为 HTML ；
- 一个 server API route 抛出错误。

在 client 端可能触发的错误的时间点：

- 处理 `Nuxt` plugins 时；
- 挂载 application 前（触发 `app:beforeMount` hook 时）；
- 挂载 application 时没有被 `onErrorCaptured` 和 `vue:error` hook 所处理的错误；
- Vue app 初始化和挂载在 brower 上（触发 `app:mounted` hook 时）。



在根目录下新建 `error.vue` 可以自定义错误页面：

`~/error.vue`

``` vue
<script setup lang="ts">
import type { NuxtError } from '#app'

const props = defineProps({
  error: Object as () => NuxtError
})

const handleError = () => clearError({ redirect: '/' })
</script>

<template>
  <div>
    <h2>{{ error.statusCode }}</h2>
    <button @click="handleError">Clear errors</button>
  </div>
</template>

```

> 当你觉得可以离开错误页面时，可以使用 error util  `clearError` 函数，传入 route 跳转到对应页面。



建议使用在页面 `setup` 中使用 `onErrorCaptured` 组合式函数或者在插件中使用 `vue:error` hook 自定义处理错误。

``` typescript
// plugins/error-handler.ts
export default defineNuxtPlugin(nuxtApp => {
  nuxtApp.hook('vue:error', (err) => {
    //
  })
})

```



> 渲染 error page 是一个完全独立的页面加载，意味着所有的 `middleware` 都会被再执行一次，建议在 `middleware` 中使用 error util `useError` 避免重复处理错误。



 ### Error Utils

- `useError` ： 会返回全局所有已经被处理过的错误。

  ``` typescript
  function useError (): Ref<Error | { url, statusCode, statusMessage, message, description, data }>
  
  ```

- `createError` ：新建一个带有额外自定义信息的 error object 。可以传入一个 `message` string ，或者一个包含 error 相关属性的 object 。创建后应该使用 `throw` 抛出。

  - 在 server 端，它会触发生成一个 error page ；
  - 在 client 端，会生成一个可处理的非致命错误。如果你不想处理，想直接触发跳转到 error page ，可以设置 `fatal: true` 。

  ``` typescript
  function createError (err: string | { cause, data, message, name, stack, statusCode, statusMessage, fatal }): Error
  
  ```

  ``` vue
  <script setup lang="ts">
  // pages/movies/[slug].vue
  const route = useRoute()
  const { data } = await useFetch(`/api/movies/${route.params.slug}`)
  
  if (!data.value) {
    throw createError({
      statusCode: 404,
      statusMessage: 'Page Not Found'
    })
  }
  </script>
  
  ```

- `showError` ：你可以在 client 或者 server 端的 `middleware` ，plugin 或者页面 `setup` 等任何地方直接调用该函数，触发跳转至错误页。

  ``` typescript
  function showError (err: string | Error | { statusCode, statusMessage }): Error
  
  ```

- ` clearError` ：清除 `Nuxt` 当前正在处理的错误，并且可以传入 route 跳转到对应页面。

  ``` typescript
  function clearError (options?: { redirect?: string }): Promise<void>
  
  ```

  

### Render Error in Component

`Nuxt` 提供了 `<NuxtErrorBoundary>` 组件，可以处理 client 端错误，而不用直接跳转到错误页。

这个组件会处理在其 default slot 中的错误，在 client 端可以避免错误冒泡到 top level ，出现错误时会渲染它的 `#error` slot 。

`#error` slot 接受一个 `error` 参数，如果将 `error` 设置为 `null` ，则会重新渲染 default slot 的内容。所以要确保已经将错误处理好，否则重新渲染完也会再次触发 `#error` slot 。

``` vue
<template>
  <!-- some content -->
  <NuxtErrorBoundary @error="someErrorLogger">
    <!-- You use the default slot to render your content -->
    <template #error="{ error, clearError }">
      You can display the error locally here: {{ error }}
      <button @click="clearError">
        This will clear the error.
      </button>
    </template>
  </NuxtErrorBoundary>
</template>

```



## Prerendering

`Nuxt` 允许在构建时静态渲染页面，以提高某些性能或 SEO 指标。

`Nuxt` 可以在构建时先渲染后选中的页面。在被请求时直接提供预构建的页面而不是动态生成。



### 基于爬虫的 Prerendering

使用 `pnpm dlx nuxi generate ` 进行打包，会将所有页面（ pages 中自动生成对应 routes 的页面）都打包成静态页面，可以部署在任何静态服务器上。



### 选择性 Prerendering

可以手动置顶要预渲染的页面：

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    prerender: {
      routes: ["/user/1", "/user/2"],
      ignore: ["/dynamic"]
    }
  }
})

```

可以使用 `crawlLinks` 选项预构建一些无法被自动发现的页面（大概可以指不在 pages 中的页面或者 非 vue 文件）：

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  nitro: {
    prerender: {
     	crawlLinks: true,
      routes: ["/sitemap.xml", "/robots.txt"]
    }
  }
})

```

最后，也可以通过指定 `routeRules` 来进行预渲染：

``` typescript
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    // Set prerender to true to configure it to be prerendered
    "/rss.xml": { prerender: true },
    
    // Set it to false to configure it to be skipped for prerendering
    "/this-DOES-NOT-get-prerendered": { prerender: false },
    
    // Everything under /blog gets prerendered as long as it
    // is linked to from another page
    "/blog/**": { prerender: true }
  }
})

```

上面的配置也可以直接在页面中通过 `defineRouteRules` 定义：

``` vue
<script setup>
// Or set at the page level
defineRouteRules({
  prerender: true,
});
</script>

<template>
  <div>
    <h1>Homepage</h1>
    <p>Pre-rendered at build time</p>
  </div>
</template>

```

> 使用 `defineRouteRules` 需要确保 `nuxt.config.ts` 中的 `experimental.inlineRouteRules` 是 `true` 。



### 运行时 Prerendering

可以在页面中使用 `prerenderRoutes` 指定其它页面进行预加载。

``` vue
<script setup>
prerenderRoutes(["/some/other/url"]);
</script>

<template>
  <div>
    <h1>This will register other routes for prerendering when prerendered</h1>
  </div>
</template>

```



### 相关 hooks

`prerender:routes` hook 会在有额外页面添加进预加载列表时触发：

``` typescript
// nitro.config.ts
export default defineNuxtConfig({
  hooks: {
    async "prerender:routes"(ctx) {
      const { pages } = await fetch("https://api.some-cms.com/pages").then(
        (res) => res.json()
      )
      for (const page of pages) {
        ctx.routes.add(`/${page.name}`)
      }
    }
  }
})

```



`prerender:generate` 会在每个 route 进行 prerender  时触发：

``` typescript
// nitro.config.ts
export default defineNuxtConfig({
  nitro: {
    hooks: {
      "prerender:generate"(route) {
        if (route.route?.includes("private")) {
          route.skip = true
        }
      }
    }
  }
})

```



## Deployment

看 [AWS](https://nuxt.com/deploy/aws-amplify) 配置。



## i18n

### Setup

`npm install @nuxtjs/i18n`

``` javascript
// nuxt.config.js
[
  module: [
		'@nuxtjs/i18n'
  ],
  i18n: {
    /* module options */
    baseUrl: ''
    langDir: 'i18n/'
    locales: [
      { code: 'en', iso: 'en-US', file: 'en.js' }
    ],
    defaultLocale: 'en',
    lazy: true,
    vuex: false
  }
]
```



### Basic Usage

#### $t

> 在 template 中使用 $t 。
>
> ``` vue
> <nuxt-link :to="localePath('index')">{{ $t('home') }}</nuxt-link>
> ```



#### nuxt-link

> - `localePath` - 生成当前 locale 对应的路由。
>
>   第一个参数可以是 route 的 name 或者 path 或者 object
>
>   第二个参数可以传特定的 locale code，如果有需要的话。
>
>   `<nuxt-link :to="localePath('index')">{{ $t('home') }}</nuxt-link>`
>
> - `switchLocalePath` - 生成本页面的另一种 locale 的 route，但是跳转后不会更新 cookie 。
>
>   `<nuxt-link :to="switchLocalePath('en')">English</nuxt-link>`
>
> 这些函数在 app 上下文中也可以使用：
>
> ``` javascript
> export default ({ app }) => {
>   // Get localized path for homepage
>   const localePath = app.localePath('index')
>   // Get path to switch current route to French
>   const switchLocalePath = app.switchLocalePath('fr')
> }
> ```
>
> 
>
> - `localeRoute` - 返回当前页面的路由对象。
>
>   `const { fullPath } = localeRoute({ name: 'index', params: { foo: '1' } })`
>
> - localeLocation - 返回当前页面的 `Locale` 对象。
>
>   `<a href="#" @click="$router.push(localeLocation({ name: 'index', params: { foo: '1' } }))">Navigate</a>`



#### $i18n

> 可以在 `this.$i18n` 中获取 i18n 的相关配置（eg: `this.$i18n.locales`）
>
> - **setLocale(locale)** - 会更新 cookie 并且跳转至对应路由。
>
>   ``` vue
>   <a
>     href="#"
>     v-for="locale in availableLocales"
>     :key="locale.code"
>     @click.prevent.stop="$i18n.setLocale(locale.code)">{{ locale.name }}</a>
>   ```
>
> - setLocaleCookie(locale) - 只更新 cookie 。
>
> - getLocaleCookie() - 获取 cookie 保存的值。
>
> - **localeProperties** - 当前的 locale 对象



### Options（may use）

#### baseUrl

> default: ' '
>
> 用于生成带 `hreflang` tag 的备用 URL 的基本前缀。
>
> 使用生产域名，有利于 SEO。



 #### locales

> type: `array`
>
> default: `[]`
>
> App 所支持的多语言列表
>
> ``` javascript
> [
> { code: 'en', iso: 'en-US', file: 'en.json', dir: 'ltr' },
> { code: 'fr', iso: 'fr-FR', file: 'fr.json' ,
> ]
> ```
>
> - `code`(**required**) - 语言的唯一标识。
>
> - `iso`(使用 `SEO` 特性时 **required**) - 设置语言的 ISO 代码。
>
> - `file` - 文件名，会在 `langDir` 的文件路径后找对应文件。
>
> - `dir` - 指定元素和内容的方向（文字？可能有些地区语言阅读方向不同），`'rtl'` `'ltl'` `'auto' `。
>
> - `domain`(使用 `differentDomains` 时 **required**) - 使用此语言时想要使用的 domain 。
>
> - `...`  - 任何其它的属性都可以在 `runtime` 中暴露，例如我们可以设定 `name` 之类的属性在语言选择器中使用。



#### langDir

> default: `null`
>
> 包含翻译文件的文件夹路径（eg: `'i18n/'`）。



#### defaultDirection

> default: `ltr`
>
> locales options 对象中没有配置 `dir` 属性时默认使用这个属性。



#### defaultLocale

> default: `null`
>
> App 的默认语言，需要与 `locales` 配置列表的其中一种语言 `code` 相匹配。
>
> 使用 `prefix_except_default` **strategy** 时，此处定义的语言将不会添加路由前缀。
>
> 但是不管使用什么策略，都推荐配置该属性，因为如果路由中的语言前缀匹配不到相应语言时，会回退到该语言。



#### strategy

> default: `'prefix_except_default'`
>
> 路由生成策略，确定已经设置了 `defaultLocale`。如果 Nuxt 版本低于 2.10.2 ，需要确保 `defaultLocale` 在 locales array 中最后一个。
>
> - `'no_prefix'` - 路由不带 locale 前缀，设置后需要靠 browser 和 cookie 分辨 locale ，并且只能通过 i18n Api 切换 locale 。
> - `'prefix_except_default'` - 除了 `defaultLocale` ，其他 locale 路由都带前缀。
> - `'prefix'` - 所有 locale 路由都带前缀。
> - `'prefix_and_default'` - 所有 locale 路由都带前缀，但是 `defaultLocale` 不带前缀的路由也保留。



#### lazy

> type: object | boolean
>
> default: `false`
>
> locale file 是否延迟加载。
>
> 当值为 `true` 时，可以选择使用 object 配置一些属性：
>
> - `skipNuxtState`(defalut: `true`) - default locale 的 message 会在 serve-side 时就打包进了 Nuxt ‘state’ 中，并在 client-size 进行使用，意味着可以减少对 default locale message 文件的网络请求。
>
> 必须配置 langDir 。



#### detectBrowserLanguage

> default:
>
> ``` javascript
> {
> alwaysRedirect: false,
> fallbackLocale: '',
> redirectOn: 'root',
> useCookie: true,
> cookieAge: 365,
> cookieCrossOrigin: false,
> cookieDomain: null,
> cookieKey: 'i18n_redirected',
> cookieSecure: false,
> }
> ```
>
> 允许使用浏览器语言检测，可以在用户首次访问网站时自动跳转到他们浏览器的首要语言。
>
> 通过 `navigator` 或者 `accept-language` HTTP header 匹配 locales array 中 object 的 `iso` 或者 `code` 属性。会先按完全匹配，如果没有匹配上，再按 `-` 之前的字符进行匹配。
>
> **对 SEO 最有利的是设置 `redirectOn: 'root'`**  。
>
> - `alwaysRedirect`(default: `false`) - 每次都进行重定向，并保留 cookie。
> - `fallbackLocale`(default: `null`) - 如果没有可以匹配用户浏览器首要语言的，回退到该语言。
> - `redirectOn`(default: `root`) 
>   - `all` - 所有路由都检测浏览器语言。
>   - `root` - 只在网站根路径 `/` 下才检测。只有 `stategy` 设置 `no_prefix`  以外的值才有效。
>   - `no prefix` - 比 `root` 更宽松一点，不止根路径，只要不到 locale 前缀的路由都检测（例：`/foo`）。
> - `useCookie`(default: `true`) - 使用 cookie 保存用户第一次访问时重定向的 locale 值，防止后续每次访问都重定向；可以设置 false ，每次都进行重定向。
> - `cookieAge` (default: `365`) - 设置 cookie 的有效天数。
> - `cookieKey` (default: `'i18n_redirected'`) - 设置 cookie 的属性名。
> - `cookieDomain` (default: `null`)  - 重写 cookie 的默认域名（网站 host 的地址）。
> - `cookieCrossOrigin` (default: `false`) - 设置为 true 时，设置 flags `SameSite=None; Secure` 允许不同域名下使用改 cookie ，如果 app 被使用 iframe 集成时需要开启。
> - `cookieSecure` (default: `false`) - 设置 cookie 的 `Secure` flag。



#### rootRedirect

> default: `null`
>
> 设置用户访问根路径时（`/`）需要跳转的路由。可以接受一个 string 或者带有 `statusCode` 和 `path` 的 object 。
>
> ``` javascript
> {
> statusCode: 301,
> path: 'about-us'
> }
> ```



#### vuex

> type: `object` or `false`
>
> default: `{ moduleName: 'i18n', syncRouteParams: true }`
>
> 注册一个 i8n vuex module。
>
> - `moduleName`(default: `i18n`) - module namespace 。
> - syncRouteParams(default: `true`) - 启用 `setRouteParams` mutation , 为了在动态路由（dynamic routes）中自定义路由名称（custom route names）。



#### vueI18n

> default: `{}`
>
> 添加 `vue-i18n` 配置。



#### vueI18nLoader

> default: `false`
>
> 如果为 true ， 将会添加 vue-i18n-loader 到 Nuxt 的 Webpack config 中去，可以单独定义每个页面的 i18n 块。



#### onBeforeLanguageSwitch

> default: `(oldLocale, newLocale, isInitialSetup, context)=> {}`
>
> locale 切换前的回调函数，可以改变准备要设置的语言 newLocale。



#### onLanguageSwitched

> default: `(oldLocale, newLocale) => {}`
>
> locale 切换后的回调函数。



#### skipSettingLocaleOnNavigate

> default: `false`
>
> 当切换去新语言（切换语言会跳转路由）时，页面如果有添加动画，那么在页面动画执行期间不会切换语言。
>
> 如果为 true ，搭配 beforeEnter ，可以使得页面动画在语言切换后再进行。
>
> ``` javascript
> export default {
> plugins: ['~/plugins/router'],
> 
> i18n: {
>  // ... your other options
>  skipSettingLocaleOnNavigate: true,
> }
> }
> ```
>
> ``` javascript
> export default ({ app }) => {
> app.nuxt.defaultTransition.beforeEnter = () => {
>  app.i18n.finalizePendingLocaleChange()
> }
> 
> // Optional: wait for locale before scrolling for a smoother transition
> app.router.options.scrollBehavior = async (to, from, savedPosition) => {
>  // Make sure the route has changed
>  if (to.name !== from.name) {
>    await app.i18n.waitForPendingLocaleChange()
>  }
>  return savedPosition || { x: 0, y: 0 }
> }
> }
> ```
>
> 单一 page
>
> ``` javascript
> export default {
> transition: {
>  beforeEnter() {
>    this.$i18n.finalizePendingLocaleChange()
>  }
> }
> }
> ```
>
> 



### Guide

#### ignoring localized routes

> 让一些页面只能够适配部分 locale 。
>
> ``` javascript
> export default {
> nuxtI18n: {
>  locales: ['fr', 'es']
> }
> }
> ```
>
> ``` javascript
> // 禁止切换语言
> export default {
> nuxtI18n: false
> }
> ```



#### SEO

> 提供了 `$nuxtI18nHead` 函数生成 SEO metadata ，用来针对搜索引擎优化 app 的语言相关方面。
>
> - `<html>` 标签的 `lang` 属性。
> - 生成 `hreflang` 备用 link 。
> - 生成 OpenGraph locale tag 。
> - 生成规范链接（canonical link）。
>
> 
>
> 要求：
>
> - 设置 locales array 中 object 的 iso 属性为 locale 对应的 ISO 代码。
>
> - 设置 `baseURL` 为生产环境域名，才可以生成合格的备用 link 。
>
>   ``` javascript
>   i18n: {
>     baseUrl: 'https://my-nuxt-app.com'
>   }
>   ```
>
> 
>
> 使用：
>
> 在 `head()` 中调用 `$nuxtI18nHead` 函数，可以在 nuxt.config.js 或者 page 中单独配置。
>
> ``` javascript
> export default {
>   // ...other Nuxt options...
>   head () {
>     return this.$nuxtI18nHead({ addSeoAttributes: true })
>   }
> }
> ```
>
> 合并其它 head
>
> ``` javascript
> export default {
>   // ...other Nuxt options...
>   head () {
>     const i18nHead = this.$nuxtI18nHead({ addSeoAttributes: true })
>     return {
>       htmlAttrs: {
>         myAttribute: 'My Value',
>         ...i18nHead.htmlAttrs
>       },
>       meta: [
>         {
>           hid: 'description',
>           name: 'description',
>           content: 'My Custom Description'
>         },
>         ...i18nHead.meta
>       ],
>       link: [
>         {
>           hid: 'apple-touch-icon',
>           rel: 'apple-touch-icon',
>           sizes: '180x180',
>           href: '/apple-touch-icon.png'
>         },
>         ...i18nHead.link
>      ]
>     }
>   }
> }
> ```
>
> 注意：
>
> 在 nuxt generation 时由于有可能不在 vue component 的环境下执行该函数导致崩溃，所以最好在执行前进行判空。

