# Volta

JS 工具管理器。可用于管理 node 版本。



## 安装

mac 安装：

``` bash
curl https://get.volta.sh | bash
```



## 锁定版本

可以在项目里锁定 `node` 或 包管理器版本，锁定后每次打开项目都会自动切换到对应版本：

``` bash
volta pin node@18.19
volta pin yarn@1.19
```

或者在 `package.json` 中指定：

``` json
{
  "volta": {
    "node": "18.19.1"
  }
}

```

