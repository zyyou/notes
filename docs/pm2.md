## 安装
```
npm install -g pm2
```

## 服务器要安装git
参考 <a href="/#/centos?id=安装git">安装git</a>

## 配置文件
- 本地工程创建配置文件：pm2 ecosystem
- 编辑配置文件，示例：
```js
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
```
pm2 deploy dev121 setup
```

## 更新部署
```
pm2 deploy dev121
```