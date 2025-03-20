# Mock

## Vue2

1.  安装 mockjs

2.  定义 mockjs 文件（具体 mock 数据语法可查看官方文档）

    ```javascript
    const Mock = require('mockjs')

    const baseUrl = process.env.VUE_APP_BASE_URL

    function mockJson(data) {
        return {
            code: 200,
            msg: 'success',
            data
        }
    }

    module.exports = function (middlewares, devServer) {
        if (!devServer) {
            throw new Error('webpack-dev-server is not defined');
        }

        const { app } = devServer

        app.post(baseUrl + 'your api', function (req, res) {
            res.send(Mock.mock(mockJson({
                token: '@guid'
            })))
        })

        return middlewares
    }
    ```

3.  在 vue.config.js 文件中引入（通过 webpack 的 setupMiddlewares 属性进行拦截请求）

    ```javascript
    devServer: {
        setupMiddlewares: require('./src/api/mock'),
    }
    ```
    
    

## Vue3

1.  安装 mockjs

    ```shell
    npm i mockjs vite-plugin-mock -D
    ```

2.  定义 mockjs 文件

    ```typescript
    // /mock/index.ts
    import { MockMethod } from 'vite-plugin-mock'

    export default [
      {
        url: '/api/get',
        method: 'get',
        response: ({ query }) => {
          return {
            code: 0,
            data: {
              name: 'vben'
            }
          }
        }
      },
      {
        url: '/api/post',
        method: 'post',
        timeout: 2000,
        response: {
          code: 0,
          data: {
            name: 'vben'
          }
        }
      },
      {
        url: '/api/text',
        method: 'post',
        rawResponse: async (req, res) => {
          let reqbody = ''
          await new Promise((resolve) => {
            req.on('data', (chunk) => {
              reqbody += chunk
            })
            req.on('end', () => resolve(undefined))
          })
          res.setHeader('Content-Type', 'text/plain')
          res.statusCode = 200
          res.end(`hello, ${reqbody}`)
        }
      }
    ] as MockMethod[]
    ```

3.  在 `vite.config.ts` 中配置 plugin

    ```typescript
    import { defineConfig, loadEnv } from 'vite'
    import vue from '@vitejs/plugin-vue'
    import { viteMockServe } from 'vite-plugin-mock'
    
    export default defineConfig(({ mode, command }) => {
      const env = loadEnv(mode, process.cwd(), '')
      return {
        plugins: [
            vue(),
            viteMockServe({
              // default
              // 有需要其他 serve 配置再查文档
              // https://github.com/vbenjs/vite-plugin-mock/blob/HEAD/README.zh_CN.md
              mockPath: 'mock',
              // enable: command === 'serve'
              enable: JSON.parse(env.VITE_USE_MOCK)
            })
        ]
      }
    })
    ```