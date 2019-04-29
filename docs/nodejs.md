## https服务
- 生成证书，开发版证书生成（pem私钥 csr证书签名 crt证书文件）

```shell
mkdir cert
cd cert
# 生成私钥
openssl genrsa -out privatekey.pem 1024
# 生成签名，按提示输入主体信息
openssl req -new -key privatekey.pem -out sign.csr
# 生成证书
openssl x509 -req -in sign.csr -signkey privatekey.pem -out cert.crt
```

- 启动https服务，app为koa2的应用入口文件，其他随意

```javascript
const fs = require('fs');
const http = require('https');

let server = http.createServer({
  key: fs.readFileSync('./cert/privatekey.pem', 'utf8'),
  cert: fs.readFileSync('./cert/cert.crt', 'utf8')
}, app.callback());
```

## 常用开发包
```shell
# 静态服务器 serve
serve .dist


```

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

