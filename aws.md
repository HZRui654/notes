# AWS

## 部署 monorepo

### .npmrc

在 repo 根目录下的  `.npmrc` 文件，添加以下配置

``` 
node-linker=hoisted
```

关于 `.npmrc` 更多配置，可以看 [pnpm .npmrc](https://pnpm.io/next/npmrc) 。



### build settings

由于 Amplify 默认没有 pnpm ，所以需要在 Amplify 的 build settings 中配置，部署前先安装 pnpm 。

``` 
version: 1
applications:
  - frontend:
      phases:
        preBuild:
          commands:
            - npm install -g pnpm
```



### environment variable

需要在 Amplify 的 `environment variable` 中设置 `AMPLIFY_MONOREPO_APP_ROOT` 的值为部署的 app 相对于根目录的路径。

创建新项目时会根据你指定的根目录自动设置，而对于已经存在的旧项目则需手动设置。

比如现在有一个 monorepo 名为 `ExampleMonorepo` ，然后在根目录下的 `apps` 文件夹中包含了多个 apps ，`app1` ，`app2` 和  `app3` 。

``` 
ExampleMonorepo
  apps
    app1
    app2
    app3
```

此时 `app1` 下的  `AMPLIFY_MONOREPO_APP_ROOT` 的值应该为 `apps/app1` 。



### deploy flow

1. 登陆 AWS 管理后台的 Amplify console 。
2. 在右上角 **Host web app** 中选择 **New app** 。
3. 在 **Host your web app** 页面，选择 Git 供应商，然后选择 **Continue** 。
4. 在 **Add repository branch** 页面做以下事情：
   1. 选择要部署的 repo ；
   2. 选择要使用的分支；
   3. 选择 **Connecting a monorepo? Pick a folder** ；
   4. 输入要部署的 app 在 monorepo 中的路径，例如：`apps/app1` ；
   5. 选择 **Next** 。
5. 可以在 **Build settings** 页面修改或自定义 app 的打包配置；在 4.4 步骤以后，**Environment variables** 页面中的 `AMPLIFY_MONOREPO_APP_ROOT` 属性会自动设置值。
6. 选择 **Next** ；
7. 在 **Review** 页面，选择 **Save and deploy** 。

