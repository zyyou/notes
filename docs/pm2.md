## 安装
```shell
npm install -g pm2
```

## 服务器要安装git
参考 <a href="/#/centos?id=安装git">安装git</a>

## 配置文件
- 本地工程创建配置文件：pm2 ecosystem
- 编辑配置文件，示例：
```javascript
module.exports = {
  apps : [{
    //项目名称
    name: 'koademo',
    //应用入口
    script: 'bin/www',
    // 传递给脚本的参数
    args: [''],
    //studout的文件路径（每行都附加到该文件中）,不记录设置 /dev/null
    output:'~/logs/koademo_out.log',
    //stderr的文件路径（每行都附加到此文件中）,不记录设置 /dev/null
    error:'~/logs/koademo_out.err',
    //禁用所有日志存储
    disable_logs:false,
    //设置执行模式，可能的值为：fork|cluster
    exec_mode:'cluster',
    //在群集模式下启动的实例数, 数字 或 max=按CPU核数启动
    instances: 'max',
    //在群集模式下，将每种类型的日志合并到一个文件中（而不是每个群集都单独一个）
    merge_logs:true,
    //进程失败后自重启
    autorestart: true,
    //启用或禁用观察模式（文件变动重启）
    watch: false,
    //观察模式要忽略的路径列表（正则表达式）
    ignore_watch:['logs','public','views'],
    //如果超出内存量，重新启动应用
    max_memory_restart: '1G',
    //将环境名称附加到应用名称
    append_env_to_name:true,
    //指定要注入的环境变量
    env: {
      NODE_ENV: 'local',
      port: 3000
    },
    env_dev121: {
      NODE_ENV: 'dev121',
      port:3001
    },
    env_production: {
      NODE_ENV: 'production',
      port:3001
    }
  }],
  deploy : {
    dev121 : {
      //服务器用户名
      user : 'root',
      //服务器IP
      host : '******',
      //仓库分支
      ref  : 'origin/master',
      //仓库地址
      repo : '******',
      //应用部署到服务器的目录
      path : '/home/app/koademo',
      'post-setup': "ls -la",
      // 获取版本后在服务器上执行的脚本
      'post-deploy' : 'npm install && pm2 reload ecosystem.config.js --env dev121'
    }
  }
}
```

## 初始化服务器
首次部署，工程目录执行（ecosystem.config.js所在目录）
```shell
pm2 deploy dev121 setup
```

## 更新部署
```shell
pm2 deploy dev121

# 回滚1
pm2 deploy dev121 revert 1

# 远程执行指令
pm2 deploy dev121 exec "npm install"
```

## 随系统启动
必须root执行

```shell
# 保存当前启动的应用
pm2 save

# 随系统启动
pm2 startup

# 以其它账号启动，先在git账号下save，然后root执行
pm2 startup -u git --hp /home/git
```

## 模块相关命令
```shell
# 安装pm2-webhooks模块
pm2 install pm2-webhooks

# 查看pm2-webhooks模块配置
pm2 conf pm2-webhooks

# 卸载模块
pm2 uninstall pm2-webhooks

# 修改模块配置项，会自动重启模块（存储在 ~/.pm2/module_conf.json）
pm2 set <module-name>:<attr> <val>

# 查看模块配置
pm2 conf [module-name]

# 创建演示模块
pm2 module:generate pm2-module-demo

# 发布模块 (Inc Semver + Git push + NPM publish)
pm2 publish

```

## 模块配置文件package.json

```javascript
{
  "name": "sample-module",
  "version": "1.0.0",
  "description": "PM2 Sample Module",
  "main": "app.js",
  "dependencies": {
    "pmx": "beta"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/keymetrics/pmx.git"
  },
  "config": { //模块配置项，可通过pm2 set修改
    "conf_var_1": true,
    "conf_var_2": "myvalue"
  },
  "apps": [ //模块运行行为配置
    {
      "autorestart": true,
      "exec_mode": "fork",
      "merge_logs": true,
      "max_memory_restart": "200M",
      "script": "app.js"
    }
  ],
  "author": "Keymetrics Inc.",
  "license": "MIT"
}

```