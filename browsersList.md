# .browserslistrc

可以在 [Browserlist](https://browsersl.ist/) 查询配置对应的浏览器覆盖率。



在根目录下新增 `.browserslistrc` ：

``` 
# Browsers that we support

> 0.2%, defaults, fully supports es6-module

```



默认：对于大多数用户合理的配置，可覆盖全球 97.7%

``` 
>0.2%, defaults, fully supports es6-module

# 如果有需要在 node 环境执行
# maintained node versions
```



自定义：如果想要自定义配置，务必加上以下配置

``` 
> 0.2%
not dead
last 2 version
```



一些比较远古但是可能有人使用的版本

``` 
# Browsers that we support

> 0.2%
not dead
last 2 version
Chrome > 31
Android >= 4.4
IOS >= 8
Safari >= 8
ff > 31
Opera >= 12
```

