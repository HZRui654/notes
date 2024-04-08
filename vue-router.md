# vue-router

[vue-router](https://router.vuejs.org/)

环境：vue3



## 安装引入

``` bash
npm i vue-router@4 -S
```

``` typescript
// router/index.ts
import { createMemoryHistory, createRouter } from 'vue-router'

import HomeView from './HomeView.vue'
import AboutView from './AboutView.vue'

const routes = [
  { path: '/', component: HomeView },
  { path: '/about', component: AboutView },
]

const router = createRouter({
  history: createMemoryHistory(),
  routes,
})

export default router
```

``` typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App)
  .use(router)
  .mount('#app')
```



## 获取路由

**`.vue` 文件中：**

``` vue
<script setup>
import { useRoute, useRouter } from 'vue-router'

const router = useRouter()
const route = useRoute()
</script>
```

- `router` ：路由对象，可以使用路由的 `push` ，`go` 等方法。
- `route` ：当前路由 reactive object ，可以获取当前路由的 `params` ，`query` ，`hash` 等信息。

> 在 template 中依旧可以直接使用 $route 和 $router 。



**其它文件中：**

像 `main.ts` 直接引入创建的 router instance 对象，可以使用 `push` 等函数。

``` typescript
import router from '@/router'

router.push(...)
```



## 动态路由

route 的 `params` 参数跟在 `:` 之后：

``` typescript
import User from './User.vue'

// these are passed to `createRouter`
const routes = [
  // dynamic segments start with a colon
  { path: '/users/:id', component: User },
]
```

> 使用 params 的动态路由用的是相同组件，路由 params 改变时会重用组件，不会重新加载渲染，即不会重新触发生命周期勾子，可以通过 watch 监听 params 的变化，或者导航守卫 `beforeRouteUpdate` 来执行操作。

``` vue
<script setup>
import { watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()

watch(() => route.params.id, (newId, oldId) => {
  // react to route changes...
})
</script>
```

在一个 route 中可以有多个 params ：

| pattern                        | matched path             | route.params                             |
| ------------------------------ | ------------------------ | ---------------------------------------- |
| /users/:username               | /users/eduardo           | `{ username: 'eduardo' }`                |
| /users/:username/posts/:postId | /users/eduardo/posts/123 | `{ username: 'eduardo', postId: '123' }` |



**添加 routes**

`router.addRoute()` 

只是添加新路由配置。

``` typescript
const router = createRouter({
  history: createWebHistory(),
  routes: [{ path: '/:articleName', component: Article }],
})

// About.vue
router.addRoute({ path: '/about', component: About })
```

如果在 `About.vue` 中添加新路由，此时展示的还是 `Article` 组件，需要手动在 `push` / `replace` 当前路由才能展示新的 `About` 组件。

> 可以用 `await router.replace()` 类似这种方式监听新页面是否加载完成。

添加嵌套路由：

``` typescript
router.addRoute({ name: 'admin', path: '/admin', component: Admin })

// 将父路由的 name 作为第一个参数传入
router.addRoute('admin', { path: 'settings', component: AdminSettings })

// 等同于
router.addRoute({
  name: 'admin',
  path: '/admin',
  component: Admin,
  children: [{ path: 'settings', component: AdminSettings }],
})
```



**删除 routes**

有几种方式删除路由：

- 添加一个名称有冲突的路由，这样子旧的路由会被删除，新的路由会被添加。

  ``` typescript
  router.addRoute({ path: '/about', name: 'about', component: About })
  
  // this will remove the previously added route because they have the same name and names are unique across all routes
  router.addRoute({ path: '/other', name: 'about', component: Other })
  ```

- `router.addRoute()` 会返回一个删除路由的函数，执行该函数可以删除添加的路由。

  ``` typescript
  const removeRoute = router.addRoute(routeRecord)
  
  // removes the route if it exists
  removeRoute()
  ```

- 使用 `router.removeRoute()` 根据 `name` 删除 route 。

  ``` typescript
  router.addRoute({ path: '/about', name: 'about', component: About })
  // remove the route
  router.removeRoute('about')
  ```



> Vue Router 提供了两个方式去查看已存在的路由：
>
> - `router.hasRoute()` ：检查路由是否已存在。
> - `router.getRoutes()` ：array ，包含当前已存在的所有路由记录。



## 路由正则

**区分不同 params ：**

在 params 后 `()` 中添加正则。

> 正则的格式是 javascript 的字符串格式，即需要对反斜杆 `\` 进行转义。
>
> e.g. `\d` => `\\d`

``` typescript
const routes = [
  // /:orderId -> matches only numbers
  { path: '/:orderId(\\d+)' },
  // /:productName -> matches anything else
  { path: '/:productName' },
]
```



**匹配多级路由：**

在路由的末尾直接添加 `*`（无限） 、`+`（ 1 或无限）来代表需要展开的级数。

``` typescript
const routes = [
  // /:chapters -> matches /one, /one/two, /one/two/three, etc
  { path: '/:chapters+' },
  // /:chapters -> matches /, /one, /one/two, /one/two/three, etc
  { path: '/:chapters*' },
]
```

``` typescript
// given { path: '/:chapters*', name: 'chapters' },
router.resolve({ name: 'chapters', params: { chapters: [] } }).href // produces/
router.resolve({ name: 'chapters', params: { chapters: ['a', 'b'] } }).href // produces/a/b

// given { path: '/:chapters+', name: 'chapters' },
router.resolve({ name: 'chapters', params: { chapters: [] } }).href // throws an Error because `chapters` is empty
```

`?`（ 0 或 1 ）代表 params 是可选的。

``` typescript
const routes = [
  // will match /users and /users/posva
  { path: '/users/:userId?' },
  // will match /users and /users/42
  { path: '/users/:userId(\\d+)?' },
]
```



严格模式：

默认情况下，所有路由不区分大小写，并且匹配带或不带尾部斜杆 `/` 的路由。

e.g. `/users` 匹配 `/users` 、`/users/` 、`Users/` 。

通过 `sensitive` 和 `strict` 开启严格模式。

``` typescript
const router = createRouter({
  history: createWebHistory(),
  routes: [
    // will match /users/posva but not:
    // - /users/posva/ because of strict: true
    // - /Users/posva because of sensitive: true
    { path: '/users/:id', sensitive: true },
    // will match /users, /Users, and /users/42 but not /users/ or /users/42/
    { path: '/users/:id?' },
  ],
  strict: true, // applies to all routes
})
```



> debug ：通过 [path ranker tool](https://paths.esm.dev/?p=AAMeJSyAwR4UbFDAFxAcAGAIJXMAAA..#) 可以测试路由的正则表达式 。



## 路由命名

给路由添加命名以后，就可以通过 `name` 而不是 `path` 来进行路由跳转。

> 每个路由的 name 都必须是唯一的。如果有相同 name 的多个 route ，只有最后一个会生效。

``` typescript
const routes = [
  {
    path: '/user/:username',
    name: 'profile', 
    component: User
  }
]
```

更推荐使用 name 来进行路由跳转。

``` vue
<template>
  <router-link :to="{ name: 'profile', params: { username: 'erina' } }">
    User profile
  </router-link>
</template>

<script setup>
import { useRoute, useRouter } from 'vue-router'

const router = useRouter()
router.push({ name: 'profile', params: { username: 'erina' } })
</script>
```



## 路由嵌套

子组件会渲染在父组件的 `RouterView` 中。

``` typescript
const routes = [
  {
    path: '/user/:id',
    component: User,
    children: [
      {
        // UserProfile will be rendered inside User's <router-view>
        // when /user/:id/profile is matched
        path: 'profile',
        component: UserProfile,
      },
      {
        // UserPosts will be rendered inside User's <router-view>
        // when /user/:id/posts is matched
        path: 'posts',
        component: UserPosts,
      },
    ],
  },
]
```



**省略父 `component` ：**

如果一组路由需要前缀相同，但不需要父组件，可以通过省略父路由的 `component` 选项来达到这个效果。

> 版本 4.1 以上有效。

``` typescript
const routes = [
  {
    path: '/admin',
    children: [
      { path: '', component: AdminOverview },
      { path: 'users', component: AdminUserList },
      { path: 'users/:id', component: AdminUserDetails },
    ]
  }
]
```



**命名比较特殊：**

``` typescript
const routes = [
  {
    path: '/user/:id',
    name: 'user-parent',
    component: User,
    children: [{ path: '', name: 'user', component: UserHome }],
  },
]
```

如果访问 path `/user/:id` ，那么等同于访问 name `user` ，父子组件都会渲染；但是给父路由添加 name 并访问，即访问 name `user-parent` ，则不会渲染子组件 `UserHome` 。 



## 路由导航

**跳转**

| Declarative               | Programmatic       |
| ------------------------- | ------------------ |
| `<router-link :to="...">` | `router.push(...)` |

``` typescript
// literal string path
router.push('/users/eduardo')

// object with path
router.push({ path: '/users/eduardo' })

// named route with params to let the router build the url
router.push({ name: 'user', params: { username: 'eduardo' } })

// with query, resulting in /register?plan=private
router.push({ path: '/register', query: { plan: 'private' } })

// with hash, resulting in /about#team
router.push({ path: '/about', hash: '#team' })
```

> - 如果提供了 path ，query 以外的 options 会被忽略。即使用 path ，就需要手动组合除 query 以外完整的 path 。
>
> - params 值的类型只能是 string 、number 或 array 。可以通过赋值 `""` 或 `null` 来删除 params 。
> - `.push` 等导航函数返回的都是 `Promise` ，可以监听到路由是否跳转成功。 



**替换**

替换当天路由，不会产生新的 history 。

| Declarative                       | Programmatic          |
| --------------------------------- | --------------------- |
| `<router-link :to="..." replace>` | `router.replace(...)` |

``` typescript
router.push({ path: '/home', replace: true })
// equivalent to
router.replace({ path: '/home' })
```



**history**

和 `window.history.go(n)` 类似。

``` typescript
// go forward by one record, the same as router.forward()
router.go(1)

// go back by one record, the same as router.back()
router.go(-1)

// go forward by 3 records
router.go(3)

// fails silently if there aren't that many records
router.go(-100)
router.go(100)
```



## 命名视图

> RouterView 没有 name 属性时默认 name 为 `default` 。

``` vue
<template>
  <router-view class="view left-sidebar" name="LeftSidebar" />
  <router-view class="view main-content" />
  <router-view class="view right-sidebar" name="RightSidebar" />
</template>
```

``` typescript
const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: '/',
      components: {
        default: Home,
        // short for LeftSidebar: LeftSidebar
        LeftSidebar,
        // they match the `name` attribute on `<router-view>`
        RightSidebar,
      }
    }
  ]
})
```



**嵌套路由的命名视图**

``` vue
<template>
  <!-- UserSettings.vue -->
  <div>
    <h1>User Settings</h1>
    <NavBar />
    <router-view />
    <router-view name="helper" />
  </div>
</template>
```

``` typescript
const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: '/settings',
      // You could also have named views at the top
      component: UserSettings,
      children: [{
        path: 'emails',
        component: UserEmailsSubscriptions
      }, {
        path: 'profile',
        components: {
          default: UserProfile,
          helper: UserProfilePreview
        }
      }]
    }
  ]
})
```



## 重定向 + 别名

**重定向**

``` typescript
const routes = [{ path: '/home', redirect: '/' }]
```

``` typescript
const routes = [{ path: '/home', redirect: { name: 'homepage' } }]
```

``` typescript
const routes = [
  {
    // /search/screens -> /search?q=screens
    path: '/search/:searchText',
    redirect: to => {
      // the function receives the target route as the argument
      // we return a redirect path/location here.
      return { path: '/search', query: { q: to.params.searchText } }
    },
  },
  {
    path: '/search',
    // ...
  },
]
```

> 定义了 `redirect` 的 route ：
>
> - 可以不提供 `component` ，因为该 route 不会被访问；
> - 路由守卫无法获取该 route ，路由守卫只会获取 redirect 的最终 route 。



**别名**

访问别名时，相当于访问别名对应的 route 。

```typescript
const routes = [{ path: '/', component: Homepage, alias: '/home' }]
```

```typescript
const routes = [
  {
    path: '/users',
    component: UsersLayout,
    children: [
      // this will render the UserList for these 3 URLs
      // - /users
      // - /users/list
      // - /people
      { path: '', component: UserList, alias: ['/people', 'list'] },
    ],
  },
]
```

> 如果 route 带有 params ，需要确保每个单独的别名也带 params 。

```typescript
const routes = [
  {
    path: '/users/:id',
    component: UsersByIdLayout,
    children: [
      // this will render the UserDetails for these 3 URLs
      // - /users/24
      // - /users/24/profile
      // - /24
      { path: 'profile', component: UserDetails, alias: ['/:id', ''] },
    ],
  },
]
```



## 传递 Props

将 route 的 params 作为 props 传入组件。

``` typescript
const routes = [
  { path: '/user/:id', component: User, props: true }
]
```



带有多命名视图的 routes ，必须定义每个命名视图的 props 。

``` typescript
const routes = [
  {
    path: '/user/:id',
    components: { default: User, sidebar: Sidebar },
    props: { default: true, sidebar: false }
  }
]
```



当 props 为 object 时，可以传入固定的 props 值。

``` typescript
const routes = [
  {
    path: '/promotion/from-newsletter',
    component: Promotion,
    props: { newsletterPopup: false }
  }
]
```



当 props 为也可以作为函数，返回 props 所需对象。可以用于将一些 route 的参数拼接后作为 prop 传递。

```typescript
const routes = [
  {
    path: '/search',
    component: SearchUser,
    props: route => ({ query: route.query.q })
  }
]
```



也可以在 `RouterView` 的 slot 中传递固定 prop ，但是这种方式下，所有组件都会被传入该 prop 。

``` vue
<template>
	<RouterView v-slot="{ Component }">
    <component
      :is="Component"
      view-prop="value"
     />
  </RouterView>
</template>
```



## Active links

通常项目中都会有一个 navigation component 渲染了一组 `RouterLink` ，包含已有的 routes 。一般当前 active 的 route 的样式需要不同于其它。代表 active route 的 `RouterLink` 会被添加两个类：`router-link-active` 、`router-link-exact-active` 。

如何判断 `RouterLink` 是否被 active ：

- 配置的 route 于当前的 location 一致；
- 拥有和当前 location 一致的 params 。



精确匹配：

``` typescript
const routes = [
  {
    path: '/user/:username',
    component: User,
    children: [
      {
        path: 'role/:roleId',
        component: Role,
      }
    ]
  }
]
```

``` vue
<template>
  <RouterLink to="/user/erina">
    User
  </RouterLink>
  <RouterLink to="/user/erina/role/admin">
    Role
  </RouterLink>
</template>
```

当 location 为 `/user/erina/role/admin` 时：

`router-link-active` ：两个 `RouterLink` 都会被添加该类；

`router-link-exact-active` ：精确匹配的第二个 `RouterLink` 才会被添加该类。



配置 active 的 classes ：

在 `RouterLink` 上配置：

``` vue
<template>
  <RouterLink
    activeClass="border-indigo-500"
    exactActiveClass="border-indigo-700"
    ...
  >
</template>
```

在创建 router instance 时就配置：

``` typescript
const router = createRouter({
  linkActiveClass: 'border-indigo-500',
  linkExactActiveClass: 'border-indigo-700',
  // ...
})
```



## Extending RouterLink

AppLink :

``` vue
<script setup>
import { computed } from 'vue'
import { RouterLink } from 'vue-router'

defineOptions({
  inheritAttrs: false,
})

const props = defineProps({
  // add @ts-ignore if using TypeScript
  ...RouterLink.props,
  inactiveClass: String,
})

const isExternalLink = computed(() => {
  return typeof props.to === 'string' && props.to.startsWith('http')
})
</script>

<template>
  <a v-if="isExternalLink" v-bind="$attrs" :href="to" target="_blank">
    <slot />
  </a>
  <router-link
    v-else
    v-bind="$props"
    custom
    v-slot="{ isActive, href, navigate }"
  >
    <a
      v-bind="$attrs"
      :href="href"
      @click="navigate"
      :class="isActive ? activeClass : inactiveClass"
    >
      <slot />
    </a>
  </router-link>
</template>
```

使用（ e.g. Tailwind CSS ）：

``` vue
<template>
  <AppLink
    v-bind="$attrs"
    class="inline-flex items-center px-1 pt-1 border-b-2 border-transparent text-sm font-medium leading-5 text-gray-500 hover:text-gray-700 hover:border-gray-300 focus:outline-none focus:text-gray-700 focus:border-gray-300 transition duration-150 ease-in-out"
    active-class="border-indigo-500 text-gray-900 focus:border-indigo-700"
    inactive-class="text-gray-500 hover:text-gray-700 hover:border-gray-300 focus:text-gray-700 focus:border-gray-300"
  >
    <slot />
  </AppLink>
</template>
```



## History modes

``` typescript
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    //...
  ],
})
```



**Hash mode**

`createWebHashHistory()` 

在内部转换时会在实际 URL 时前添加 hash 字符 `#` 。这部分的 URL 不会被传到服务端，不要求服务端进行任务特殊处理。但是对 SEO 不友好，如果这个有影响，请使用 HTML5 mode 。



**HTML5 mode**

`createWebHistory()`

> 推荐使用该模式。

会生成正常的 URL ，e.g. `https://example.com/user/id` 。

但是有个问题，当 app 是 single page app 时，没有正确的服务端配置会导致用户在浏览器访问时返回 404 。

解决这个问题很简单，在服务器加一个 fallback route ：如果访问的 route 无法匹配任何静态资源（ `static assets` ），则重定向到 `index.html` 。



**Memory mode**

会假设不在浏览器环境下，所以不需要与 URL 做交互，也不会触发初始导航。在 Node 环境和 SSR 下好用。要求在 `app.use(router)` 后 `push` 初始导航。

> 不推荐使用该模式。在浏览器下使用这个模式，不会用 history ，即不能够 go back 或 forword 。



## 导航守卫

`Navination guards`



**导航解析流程**

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫（2.2+）。
5. 调用路由配置里的 `beforeEnter` 。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter` 。
8. 调用全局的 `beforeResolve` 守卫（2.5+）。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。



**Global Guards**

Vue 3.3 以后，可以在全局守卫中通过 `inject()` 获取 `app.provide()` 依赖注入的数据，例如获取 pinia stores 。

``` typescript
// main.ts
const app = createApp(App)
app.provide('global', 'hello injections')

// router.ts or main.ts
router.beforeEach((to, from) => {
  const global = inject('global') // 'hello injections'
  // a pinia store
  const userStore = useAuthStore()
  // ...
})
```



`beforeEach`

``` typescript
const router = createRouter({ ... })

/**
 * 导航被触发后调用
 * to: 跳转的目标 route
 * from: 当前 route
 */
router.beforeEach(async (to, from) => {
  try {
    // canUserAccess() returns `true` or `false`
    const canAccess = await canUserAccess(to)

    /**
     * return
     * false: cacel the navigation
     * route: e.g. '/login' | { name: 'login' }
     */
    if (!canAccess) return '/login'
  } catch (error) {
    // nexpected error, cancel the navigation and pass the error to the global handler
    throw error
  }
})

```



`beforeResolve`

获取数据或者执行一些你认为用户无法进入页面就需要避免执行的操作。

``` typescript
/**
 * 导航确认后调用，即 beforeEach、组件导航守卫都调用完毕以及异步组件加载完毕以后。
 * 用法类似于 beforeEach 。
 */
router.beforeResolve(async to => {
  if (to.meta.requiresCamera) {
    try {
      await askForCameraPermission()
    } catch (error) {
      if (error instanceof NotAllowedError) {
        // ... handle the error and then cancel the navigation
        return false
      } else {
        // unexpected error, cancel the navigation and pass the error to the global handler
        throw error
      }
    }
  }
})
```



`afterEach`

无法影响导航。

可以在这里执行 analytics 、更换页面 title 等操作。

``` typescript
router.afterEach((to, from, failure) => {
  // 可以在这里捕获全局的路由跳转错误 Navigation Failure 。
  if (!failure) sendToAnalytics(to.fullPath)
})
```



**Per-Route Guards**

在路由配置中添加守卫。

 `params` 、`query` 或 `hash` 等改变不会被触发。

在嵌套路由中，当导航守卫在父路由，那么子路由的切换父路由的守卫不会触发。

可以传入一个数组，这个可以用于复用守卫。

``` typescript
function removeQueryParams(to) {
  if (Object.keys(to.query).length)
    return { path: to.path, query: {}, hash: to.hash }
}

function removeHash(to) {
  if (to.hash) return { path: to.path, query: to.query, hash: '' }
}

const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // reject the navigation
      return false
    },
  },
  {
    path: '/about',
    component: UserDetails,
    beforeEnter: [removeQueryParams, removeQueryParams],
  }
]
```



**In-Component Guards**

直接在组件中添加守卫。

> 在 `RouterView` 渲染的组件中可使用。

``` vue
<script setup>
import { onBeforeRouteEnter, onBeforeRouteUpdate, onBeforeRouteLeave } from 'vue-router'
  
onBeforeRouteEnter(to, from) {
  // called before the route that renders this component is confirmed.
  // does NOT have access to `this` component instance,
  // because it has not been created yet when this guard is called!
  // 只有 enter 有 next 的能力，可以阻止或者重定向导航。

  next(vm => {
    // access to component public instance via `vm`
  })
}

onBeforeRouteUpdate(to, from) {
  // called when the route that renders this component has changed, but this component is reused in the new route.
  // For example, given a route with params `/users/:id`, when we navigate between `/users/1` and `/users/2`,
  // the same `UserDetails` component instance will be reused, and this hook will be called when that happens.
  // Because the component is mounted while this happens, the navigation guard has access to `this` component instance.
}

onBeforeRouteLeave(to, from) {
  // called when the route that renders this component is about to be navigated away from.
  // As with `beforeRouteUpdate`, it has access to `this` component instance.

  // 阻止本次导航
  return false
}
</script>
```



## Meta Fields

给路由附加自定义信息。

``` typescript
const routes = [
  {
    path: '/posts',
    component: PostsLayout,
    children: [
      {
        path: 'new',
        component: PostsNew,
        // only authenticated users can create posts
        meta: { requiresAuth: true },
      },
      {
        path: ':id',
        component: PostsDetail,
        // anybody can read a post
        meta: { requiresAuth: false },
      },
    ],
  },
]
```

在 route object 中：

- `route.matched` ：array ，包含匹配当前 location 的所有父子路由，可以通过遍历这个数组获取所有 meta 。
-  `route.meta` ：object ，vue-router 已经递归合并了所有匹配当前 location 的路由的 meta 。



**定义 meta 类型：**

``` typescript
// This can be directly added to any of your `.ts` files like `router.ts`
// It can also be added to a `.d.ts` file. Make sure it's included in
// project's tsconfig.json "files"
import 'vue-router'

// To ensure it is treated as a module, add at least one `export` statement
export {}

declare module 'vue-router' {
  interface RouteMeta {
    // is optional
    isAdmin?: boolean
    // must be declared by every route
    requiresAuth: boolean
  }
}
```



## RouterView slot

可以给被渲染组件**添加父组件**、**添加 ref** 或者**传入自定义属性**等。

``` vue
<template>
  <router-view v-slot="{ Component, route }">
    <transition>
      <keep-alive>
        <component :is="Component" ref="mainContent" some-prop="a value" />
      </keep-alive>
    </transition>
  </router-view>
</template>
```



## Scroll Behavior

`scrollBehavior`

控制进入路由时对应页面的滚动位置。

> 浏览器支持 `history.pushState` 才有效。

``` typescript
const router = createRouter({
  history: createWebHashHistory(),
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // return desired position
           
    // always scroll to top
    return { top: 0 }
                            
    // always scroll 10px above the element #main
    return {
      // could also be
      // el: document.getElementById('main'),
      el: '#main',
      // 10px above the element
      top: 10,
    }
		
		/**
	 	 * savedPosition 只有在点击浏览器前进/后退按钮时才有效。
	 	 */
		if (savedPosition) {
      return savedPosition
    } else {
      return { top: 0 }
    }

		// 滚动到锚点。
		if (to.hash) {
      return {
        el: to.hash,
        // 让滚动更顺滑自然
        behavior: 'smooth'
      }
    }
	
		// delay
		return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve({ left: 0, top: 0 })
      }, 500)
    })
  }
})
```



## 懒加载

`component` / `components` 可以接收一个返回 Promise 的函数。

懒加载的 route components 会被分割成单独的 chunks ，在 route 被访问时才加载。

``` typescript
// replace
// import UserDetails from './views/UserDetails'
// with
const UserDetails = () => import('./views/UserDetails.vue')

const router = createRouter({
  // ...
  routes: [
    { path: '/users/:id', component: UserDetails }
    // or use it directly in the route definition
    { path: '/users/:id', component: () => import('./views/UserDetails.vue') }
  ]
})
```

> 不要在 route component 中使用异步组件，异步组件可以在路由组件中使用，但是路由组件



可以将部分组件打包到一个 chunk 中，而不是分开：

``` typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      // https://rollupjs.org/guide/en/#outputmanualchunks
      output: {
        manualChunks: {
          'group-user': [
            './src/UserDetails',
            './src/UserDashboard',
            './src/UserProfileEdit',
          ]
        }
      }
    }
  }
})
```



## Navigation Failures

Navigation Failures 是一个带有一些额外属性的 `Error` instance。

``` vue
<script setup>
import { NavigationFailureType, isNavigationFailure } from 'vue-router'

// trying to leave the editing page of an article without saving
const failure = await router.push('/articles/2')

if (isNavigationFailure(failure, NavigationFailureType.aborted)) {
  // show a small notification to the user
  showToast('You have unsaved changes, discard and leave anyway?')
}
</script>
```

> isNavigationFailure 如果第二个参数不传，那么就只会判断 failure 是否为 Navigation Failure 。



**NavigationFailureType**

- `aborted` ：导航时返回了 false 阻止。
- `cancelled` ：导航未完成时又有了新的导航，旧的导航会被取消。
- `duplicated` ：导航被停止因为目前已经在目标 location 上。



**failure**

包含 `to` 和 `from` 属性，同 route 。

``` typescript
// trying to access the admin page
router.push('/admin').then(failure => {
  if (isNavigationFailure(failure, NavigationFailureType.aborted)) {
    failure.to.path // '/admin'
    failure.from.path // '/'
  }
})
```



**发现 Redirections**

在导航守卫中，返回一个新的 route 可以进行 redirect 。不同于其它返回值，redirect 不会停止当前导航，而是建一个新的导航替代，因此可以通过读取 `redirectedFrom` 来判断。

``` typescript
await router.push('/my-profile')
if (router.currentRoute.value.redirectedFrom) {
  // redirectedFrom is resolved route location like to and from in navigation guards
}
```