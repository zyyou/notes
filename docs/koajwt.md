## 安装
```shell
npm install koa-jwt -S
```
## app.js配置
```javascript
const koajwt = require('koa-jwt');
const jwtRefresh = require('./lib/middlewares/jwt-refresh');

//中间件引用放在最顶端，具体查koa洋葱模型
// unless按http method、扩展名、路径忽略验证，支持正则
app.use(koajwt({
  secret: appConfig.tokenKey
}).unless({
  method: [],
  ext: ['.css', '.js', '.png', '.jpg'],
  path: ['/',
    /\/index/, /\/test/, /\/ttt/, /\/jwttest\/login/]
}));


//刷新中间件
app.use(jwtRefresh);
```

## 刷新中间件 jwt-refresh
```javascript

/**
 * 自动刷新 jwt token
 * request header中 token-refresh=true 时刷新
 * 刷新后在 response header auth_token 中
 * 
 */


const debug = require('debug')('jwt-refresh');

const jwt = require('jsonwebtoken');
const koajwt = require('koa-jwt');


module.exports = async (ctx, next) => {
    await next();

    var token = ctx.headers['authorization'];
    if (token) {
        if (ctx.headers['token-refresh']) {
            let user = ctx.state.user;
            delete user.iat;
            delete user.exp;

            token = jwt.sign(user, ctx._appConfig.tokenKey, { expiresIn: ctx._appConfig.tokenExpires });

            debug('延长 %s 新token %s', ctx._appConfig.tokenExpires, token);
        }
        ctx.res.setHeader('auth_token', token);
    }
}
```

## 示例 jwttest
```javascript
'use strict';
const router = require('koa-router')();
const jwt = require('jsonwebtoken');
const moment = require('moment');

const controller = require('../lib/controller');
const message = require('../lib/message');

controller.init(router, module);

//模拟登陆
controller.jsonPOST('/login', function (ctx) {

    let data = ctx.request.body;
    if (!data.name || !data.password) {
        return ctx.body = message(true, '用户名或密码错误', {}, 10000);
    }

    let token = jwt.sign({
        name: data.name,
        tm: moment().format('YYYY-MM-DD HH:mm:ss'),
        msg: 'JWT test'
    }, ctx._appConfig.tokenKey, { expiresIn: ctx._appConfig.tokenExpires });


    ctx.res.setHeader('auth_token', token);

    return message(false, 'ok');
});

//测试GET响应JSON
controller.jsonPOST('/getjson', function (ctx) {
    var token = ctx.headers['authorization'];
    return {
        title: 'jwt get json',
        jwtdata: ctx.state.user,
        reqbody: ctx.request.body,
        reqquery: ctx.request.query
    };
});



module.exports = router;

```

## 测试脚本
```
// jwt test REST Client 测试脚本

@hostname = 127.0.0.1
@port = 3001
@host = {{hostname}}:{{port}}
@contentType = application/json


### 模拟登陆
# @name login
POST http://{{host}}/jwttest/login?a=b&b=1 HTTP/1.1
 Content-Type: {{contentType}}

{
    "name": "zyy",
    "password": "abc"
}

### 模拟获取登陆后数据，不刷新token
@token = {{login.response.headers.auth_token}}
POST http://{{host}}/jwttest/getjson/ HTTP/1.1
Authorization:Bearer {{token}}
Content-Type: {{contentType}}

{
    "id":123
}

### 模拟获取登陆后数据，刷新token
# @name refresh
@token = {{login.response.headers.auth_token}}
POST http://{{host}}/jwttest/getjson/ HTTP/1.1
token-refresh:true
Authorization:Bearer {{token}}
Content-Type: {{contentType}}

{
    "id":123
}

### 模拟获取登陆后数据，使用刷新后token，不刷新token
@rtoken = {{refresh.response.headers.auth_token}}
POST http://{{host}}/jwttest/getjson/ HTTP/1.1
Authorization:Bearer {{rtoken}}
Content-Type: {{contentType}}

{
    "id":123
}

```

## 完整示例工程
[点击前往GitHub koa-mvc](https://github.com/zyyou/koa-mvc)

!> 注意：网站启用https防止token被截获，并且保存好秘钥防止泄露

