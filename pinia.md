### 特点

- 完整的 ts 的支持；
- 足够轻量，压缩后的体积只有 1kb 左右；
- 去除 mutations ，只有 state ，getters ，actions；
- actions 支持同步和异步；
- 代码扁平化没有模块嵌套，只有 store 的概念，store 之间可以自由使用，每一个 store 都是独立的，甚至可以拥有 store 的循环依赖关系；
- 无需手动添加 store ，store 一旦创建便会自动添加；
- 支持 Vue3 和 Vue2。



### 使用

安装： `npm install pinia -S`

- vue3

  ``` javascript
  import { createApp } from 'vue'
  import App from './App.vue'
  import { createPinia } from 'pinia'
  
  const store = createPinia()
  let app = createApp(App)
  
  app.use(store)
  
  app.mount('#app')
  ```

- vue2

  ``` javascript
  import { createPinia, PiniaVuePlugin } from 'pinia'
  
  const pinia = createPinia()
  
  Vue.use(PiniaVuePlugin)
  
  new Vue({
    el: '#app',
    pinia
  })
  ```

- 定义 store：

  ``` typescript
  // /store/index.ts
  import { defineStore } from 'pinia'
  
  const getData = (): Promise<number> => {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve(666)
      }, 2000)
    })
  }
  
  // store 的名称（ id ）可以定义在专门定义常量的 js 文件中，方便多处引用
  // id 是必要的，Pinia 使用它链接 devtools ，返回的函数名为 use... 是跨可组合项的约定
  export const useTestStore = defineStore('Test', {
    state: () => ({
      current: 1000
    }),
    
    // computed 修饰一些值
    getters: {
      // getter 可以通过 this 访问整个 store 实例，但是在 ts 需要定义返回类型
      // 使用 state 访问则会自动推断类型
      // getters 中的值可以相互调用
      newCurrent(state): string {
        return `$-${this.current}`
      },
      // 可以返回一个函数以接受参数
      newCurrentFn: state => ((num: number) => {
        state.current = number
      })
    },
    
    // methods 同步异步都可以，负责提交 state
    // 调用 action 的时候，一切都会自动推断
    actions: {
      // 同步
      setCurrent(num: number) {
        this.current = num
      },
      // 异步
      async setCurrentAsync() {
        const result = await getData()
        this.current = result
        // actions 中的函数可以相互调用
        // this.setCurrent(777)
      }
    }
  })
  ```

- 获取 store 内容：

  App.vue

  ``` vue
  <template>
  	<div>{{ Test.current }}</div>
  	<div>{{ current }}</div>
  	<button @click="change">change</button>
  </template>
  
  <script setup lang="ts">
    // vue3
  	import { useTestStore } from './store'
    import { storeToRefs } from 'pinia'
    
    const Test = useTestStore()
    // store 是一个 reactive 包裹的对象，解构失去响应式。需要使用 storeToRefs 将结构属性变成 ref 对象，保持响应性
    const { current } = storeToRefs(Test)
    
    const change = () => {
      /**
       * 修改值的几种方式，
       * 1. Test.current++
       * 2. Test.$patch({ current: 888 })
       * 3. 支持第三种，可以在函数中处理逻辑
       * 		Test.$patch(state => { state.current = 999 }) 
       * 4. 不支持，因为会覆盖整个 state 对象，所以必须传入所有值
       * 		Test.$state = { current: 2000 }
       * 5. 直接更改 pinia 实例的 state ，在 SSR for hydration 期间使用
       * 		pinia.state.value = {}
       * 6. 通过 actions 定义的函数修改
       * 		Test.setCurrent(567)
       */
      current.value++
    }
  </script> 
  
  <script>
    // vue2
  	import { useTestStore } from './store'
    import { mapState, mapActions, mapWritableState } from 'pinia'
    
    export default {
      computed: {
        // mapState 只可以访问属性，不能修改
        ...mapState(useTestStore, {
          // 可以 this.myCount 访问 store.count
          myCurrent: 'current',
          // 也可以是访问 store 的一个函数
          double： store => store.current * 2,
          magicValue(store) {
        		// 可以正常访问 this 中的值，但是无法正常写入
        		return store.current + this....
      		}
        }),
          
        // 允许访问组件内的 current 并且设置它
        ...mapWritableState(useTestStore, ['currrent']),
        ...mapWritableState(useTestStore, {
          myCurrent: 'current',
        })
      },
      methods: {
        ...mapActions(useTestStore, 'setCurrent'),
        ...mapActions(useTestStore, { toSetCurrent: 'setCurrentAsync' }),
      }
    }
  </script>
  ```

- store 实例上除 `$patch` 以外其他 api

  - `$reset` ：把 state 上的值重置到初始值，直接调用即可

  - `$subscribe` ：监听 state ，只要 state 的值变化就会触发，具体看官方文档
  - `$onAction` ：监听 actions ，只要 actions 调用之前触发，或者使用 `after` 参数在调用之后触发，具体看官方文档



### Pinia 持久化插件

pinia 和 vuex 一样都有一个通病，就是刷新页面状态会丢失，所以写一个插件自动将值保存在 storage  值中并获取。

``` typescript
// main.ts
import { toRaw } from 'vue'
import { createPinia } from 'pinia'
import piniaPlugin from './plugins/pinia-plugin.ts'

const store = createPinia()

store.use(piniaPlugin)
```

``` typescript
// pinia-plugin.ts
// 简易版，具体使用再扩展
import { toRaw } from 'vue'
import type { PiniaPluginContext } from 'pinia'

const _piniaValueKey_ = 'lqd_localv_'

export default (ctx: PiniaPluginContext) => {
  if (!window.sessionStorage) return {}
  const { store } = ctx
  const key = `${_piniaValueKey_}-${store.$id}`
  const data = sessionStorage.getItem(key)
    ? JSON.parse(sessionStorage.getItem(key) as string)
    : {}
  store.$subscribe(() => {
    sessionStorage.setItem(key, JSON.stringify(toRaw(store.$state)))
  })
  return {
    ...data
  }
}
```

