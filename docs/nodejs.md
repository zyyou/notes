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
