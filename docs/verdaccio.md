## 为什么不用sinopia
sinopia已经停止维护，而且坑比较多，包括安装阶段，verdaccio是sinopia的分支，发稿前在维护中

## 安装与启动
搭建nodejs和npm环境，然后全局安装
```shell
sudo npm install -g verdaccio

# 启动，成功后会打印配置文件等信息
verdaccio

# 或者pm2守护进程启动
pm2 start verdaccio
```

!> Error: EACCES: permission denied, mkdir '/usr/local/lib/node_modules/verdaccio/node_modules 
    解决方案简单粗暴：chmod 777 /usr/local/lib/node_modules/verdaccio

## 默认配置信息
配置文件默认在用户目录的.config下，如：/root/.config/verdaccio
```shell
# 包存储目录
storage: ./storage
# 插件目录
plugins: ./plugins

web:
  # 禁用WEB访问
  #enable: false
  title: Verdaccio

auth:
  htpasswd:
    # 存储用户信息目录
    file: ./htpasswd
    # 允许注册的最大用户数，-1禁止注册
    #max_users: 1000

# 公有仓库
uplinks:
  npmjs:
    url: https://registry.npmjs.org/

# 角色： 所有 "$all", 匿名用户 "$anonymous", 注册用户 "$authenticated"
packages:
  '@*/*':
    # 私有仓
    # 什么角色能读取包
    access: $all
    # 什么角色可以发布包
    publish: $authenticated
    # 如果本地包不可用则向哪个代理发起请求，参考共有仓库配置：uplinks
    proxy: npmjs

  '**':
    access: $all
    publish: $authenticated
    proxy: npmjs


server:
  keepAliveTimeout: 60

# 是否启用 `npm audit`
# npm audit 为npm 6 新增指令,分析代码和漏洞
middlewares:
  audit:
    enabled: true

# 日志配置
logs:
  - {type: stdout, format: pretty, level: http}
  #- {type: file, path: verdaccio.log, level: info}

```

## 配置端口监听
配置文件中添加IP和端口配置，否则只能本机访问，默认端口4873
```shell
listen: 0.0.0.0:4873
```

## 配置钉钉Webhook
用于接收包发布通知
```shell
notify:
  method: POST
  headers: [{'Content-Type': 'application/json'}]
  endpoint: https://oapi.dingtalk.com/robot/send?access_token=***
  content: '{"msgtype":"text","text":{"content":"{{ publisher.name }} 发布新包: {{ name }}{{#each versions}} v{{version}}{{/each}}"}}'
```

## 使用nrm切换源

```shell
# 安装
npm install -g nrm

# 查看源，带星的为当前源
nrm ls

# 切换淘宝源
nrm use taobao

# 查看npm源是否已切换
npm config get registry

# 添加自己搭建的私有源
nrm add hynet http://私有源IP:端口

# 删除源
nrm del hynet

# 测试源速度
nrm test hynet

```

## 相关文档
- [配置](https://verdaccio.org/docs/zh-CN/configuration.html)
- [权限](https://verdaccio.org/docs/zh-CN/authentification)
- [通知](https://verdaccio.org/docs/zh-CN/notifications)
- [包访问控制](https://verdaccio.org/docs/zh-CN/packages)


