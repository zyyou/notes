## CERT_UNTRUSTED 错误
安装模块报错
```
npm http GET https://registry.npmjs.org/n 
npm ERR! Error: CERT_UNTRUSTED
```
解决方案：
```
npm config set registry http://registry.npmjs.org/  
```

## npm 发布包
- 注册账号
    [前往npmjs](https://www.npmjs.com/signup)

- 控制台登陆
```shell
npm login
```

- 创建包目录并在目录中执行初始化
```shell
npm init
```

- 发布包
在包目录执行
```shell
npm publish
```

