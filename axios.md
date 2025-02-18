# axios

## 安装

`npm install axios -S`



## API

``` javascript
import axios from 'axios'

// axios(config)
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
})

// axios(url[, config]) 默认为 GET 请求
axios('/user/12345')

/** 所有支持的请求方法都提供了别名
 * 在使用别名方法时， url、method、data 这些属性都不必在配置中指定
 * axios.get(url[, config])
 * axios.post(url[, data[, config]])
 * ...
 */

```



## 实例

``` javascript
import axios from 'axios'

// axios.create([config])
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: { 'X-Custom-Header': 'foobar' }
})

/**
 * 实例方法
 * axios#request(config)
 * axios#get(url[, config])
 * axios#post(url[, data[, config]])
 * axios#getUri([config])
 * ...
 */
```



## config

只有 `url` 是必传的，没有指定 `method` 时，默认 `GET` 方法。

``` js
{
  // `url` 是用于请求的服务器 URL
  url: '/user',

  // `method` 是创建请求时使用的方法
  method: 'GET', // 默认值

  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // 自定义请求头
  headers: { 'X-Requested-With': 'XMLHttpRequest' },

  // `params` 是与请求一起发送的 URL 参数
  // 必须是一个简单对象或 URLSearchParams 对象
  params: {
    ID: 12345
  },
    
  // `data` 是作为请求体被发送的数据
  // 仅适用 'PUT', 'POST', 'DELETE 和 'PATCH' 请求方法
  // 在没有设置 `transformRequest` 时，则必须是以下类型之一:
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属: FormData, File, Blob
  // - Node 专属: Stream, Buffer
  data: {
    firstName: 'Fred'
  },
  
  // 发送请求体数据的可选语法
  // 请求方式 post
  // 只有 value 会被发送，key 则不会
  data: 'Country=Brasil&City=Belo Horizonte',
    
  // `timeout` 指定请求超时的毫秒数。
  // 如果请求时间超过 `timeout` 的值，则请求会被中断
  timeout: 1000, // 默认值是 `0` (永不超时)

  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 它只能用于 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 数组中最后一个函数必须返回一个字符串， 一个Buffer实例，ArrayBuffer，FormData，或 Stream
  // 你可以修改请求头。
  transformRequest: [function (data, headers) {
    // 对发送的 data 进行任意转换处理

    return data;
  }],

  // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对接收的 data 进行任意转换处理

    return data;
  }],

  // `paramsSerializer`是可选方法，主要用于序列化`params`
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function (params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // default

  // `adapter` 允许自定义处理请求，这使测试更加容易。
  // 返回一个 promise 并提供一个有效的响应 （参见 lib/adapters/README.md）。
  adapter: function (config) {
    /* ... */
  },

  // `auth` HTTP Basic Auth
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

  // `responseType` 表示浏览器将要响应的数据类型
  // 选项包括: 'arraybuffer', 'document', 'json', 'text', 'stream'
  // 浏览器专属：'blob'
  responseType: 'json', // 默认值

  // `responseEncoding` 表示用于解码响应的编码 (Node.js 专属)
  // 注意：忽略 `responseType` 的值为 'stream'，或者是客户端请求
  // Note: Ignored for `responseType` of 'stream' or client-side requests
  responseEncoding: 'utf8', // 默认值

  // `xsrfCookieName` 是 xsrf token 的值，被用作 cookie 的名称
  xsrfCookieName: 'XSRF-TOKEN', // 默认值

  // `xsrfHeaderName` 是带有 xsrf token 值的http 请求头名称
  xsrfHeaderName: 'X-XSRF-TOKEN', // 默认值

  // `onUploadProgress` 允许为上传处理进度事件
  // 浏览器专属
  onUploadProgress: function (progressEvent) {
    // 处理原生进度事件
  },

  // `onDownloadProgress` 允许为下载处理进度事件
  // 浏览器专属
  onDownloadProgress: function (progressEvent) {
    // 处理原生进度事件
  },

  // `maxContentLength` 定义了 node.js 中允许的 HTTP 响应内容的最大字节数
  maxContentLength: 2000,

  // `maxBodyLength`（仅Node）定义允许的 http 请求内容的最大字节数
  maxBodyLength: 2000,

  // `validateStatus` 定义了对于给定的 HTTP 状态码是 resolve 还是 reject promise。
  // 如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，
  // 则promise 将会 resolved，否则是 rejected。
  validateStatus: function (status) {
    return status >= 200 && status < 300; // 默认值
  },

  // `maxRedirects` 定义了在 node.js 中要遵循的最大重定向数。
  // 如果设置为 0，则不会进行重定向
  maxRedirects: 5, // 默认值

  // `socketPath` 定义了在 node.js 中使用的UNIX套接字。
  // e.g. '/var/run/docker.sock' 发送请求到 docker 守护进程。
  // 只能指定 `socketPath` 或 `proxy` 。
  // 若都指定，这使用 `socketPath` 。
  socketPath: null, // default

  // `httpAgent` and `httpsAgent` define a custom agent to be used when performing http
  // and https requests, respectively, in node.js. This allows options to be added like
  // `keepAlive` that are not enabled by default.
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // `proxy` 定义了代理服务器的主机名，端口和协议。
  // 您可以使用常规的 `http_proxy` 和 `https_proxy` 环境变量。
  // 使用 `false` 可以禁用代理功能，同时环境变量也会被忽略。
  // `auth`表示应使用 HTTP Basic auth 连接到代理，并且提供凭据。
  // 这将设置一个 `Proxy-Authorization` 请求头，它会覆盖 `headers` 中已存在的自定义 `Proxy-Authorization` 请求头。
  // 如果代理服务器使用 HTTPS，则必须设置 protocol 为`https`
  proxy: {
    protocol: 'https',
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // see https://axios-http.com/zh/docs/cancellation
  cancelToken: new CancelToken(function (cancel) {
    
  }),

  // `decompress` indicates whether or not the response body should be decompressed 
  // automatically. If set to `true` will also remove the 'content-encoding' header 
  // from the responses objects of all decompressed responses
  // - Node only (XHR cannot turn off decompression)
  decompress: true // 默认值
}

```



## 配置默认值

``` javascript
import axios from 'axios'

// 全局 axios 默认值
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'

// 自定义实例 instance 默认值
// 创建实例时配置默认值
const instance = axios.create({
  baseURL: 'https://api.example.com'
})

// 创建实例后修改默认值
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN
```

配置将会按优先级进行合并。`default` -> `instance default` -> `config` 后面的优先级要高于前面的。



## 取消请求

从 `v0.22.0` 开始，Axios 支持以 fetch API 方式—— [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) 取消请求：

``` javascript
const controller = new AbortController()

axios.get('/foo/bar', {
   signal: controller.signal
}).then(function(response) {
   //...
})

// 取消请求
controller.abort()
```



## 封装（TS）

希望以 `const [error, result] = await api.getUserInfo(id)` 的方式调用；支持「请求重试」、「重复请求过滤」以及「请求取消功能」。



### 流程思路

1. 调用请求函数，处理请求参数
2. 请求拦截
   - 修改请求头
   - 配置用户标识等
3. 发起请求
4. 响应拦截
   - 网络错误处理
   - 授权错误处理
   - 普通错误处理
   - 代码异常处理
5. 请求完成，返回参数处理

> 1，5 的`参数处理`又分为「针对所有接口统一处理」和「针对单个接口处理」。既理论上应该可以传入回调函数处理数据。



### 目录结构

``` 
├── api      
⎪   ├── path
⎪   ⎪    ├── user.ts
⎪   ⎪    ├── ...
⎪   ├── index.ts
⎪   ├── server.ts
⎪   ├── tools.ts
├── ...
```



### 代码

`utils/request.ts`

``` typescript
import axios from 'axios'
import type { AxiosRequestConfig, AxiosInstance } from 'axios'

// 以 Vue3 为例
const baseURL = import.meta.env.BASE_URL

type Result<T> = {
  code: number
  message: string
  data: T
  errorData?: unknown
}

export class Request {
  instance: AxiosInstance
  baseConfig: AxiosRequestConfig = { baseURL }

  constructor(config: AxiosRequestConfig = {}) {
    this.instance = axios.create(Object.assign(this.baseConfig, config))

    // 请求拦截器
    this.instance.interceptors.request.use((config) => {
      // handle header. eg: add token
      // config.header['Authorization'] = 'your token'

      return config.data
    })

    // 响应拦截器
    this.instance.interceptors.response.use(
      (response) => {
        const { code } = response.data
        switch (code) {
          case 0:
            return response.data
          case 401:
            // 登录失败逻辑
            return response.data || {}
          default:
            return response.data || {}
        }
      },
      (error) => {
        // 接口请求报错
        // 不使用 Promise.reject() ，而是也返回对象，这样使用 async / await 的时候不需要加 try / catch
        // 假设 code 为 0 为请求正常，则不为 0 表示异常，使用 message 提示
        return { message: onErrorReason(error.message), errorData: error.response.data }
      }
    )
  }

  request<T = any>(config: AxiosRequestConfig): Promise<Result<T>> {
    return this.instance.request(config)
  }

  get<T = any>(url: string, config?: AxiosRequestConfig): Promise<Result<T>> {
    return this.instance.get(url, config)
  }

  post<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<Result<T>> {
    return this.instance.post(url, data, config)
  }
}

// 解析 http 层面请求异常的原因
const onErrorReason = (message: string): string => {
  if (message.includes('Network Error')) {
    return '网络异常，请检查网络情况！'
  }
  if (message.includes('timeout')) {
    return '请求超时，请重试！'
  }
  return '服务异常，请重试！'
}

export default new Request()

```



### 使用

types.ts

``` typescript
// 登陆接口参数
export interface ILoginParams {
  username: string
  password: string
}

// 登陆接口响应
export interface ILoginData {
  token: string
}
```

index.ts

``` typescript
import { request } from '@/utils/request'
import { ILoginParams, ILoginData } from './types.ts'

export const loginApi = (params: ILoginParams) => {
  return request.post<ILoginData>('/login', params)
}
```

use

``` typescript
const onLogin = async () => {
  const res = await loginApi(params)
  if (res.code === 0) {
    // 处理正常登陆逻辑
  } else {
    // 错误提示也可以在封装时统一添加
    alert(res.message)
  }
}
```

