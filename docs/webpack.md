## 开始
```shell
# 工程目录执行，初始化工程
npm init

# 安装
npm install -D webpack webpack-cli
```

## 工程目录结构参考
```shell
dist                # 打包输出目录
src                 # 源码目录
    js
        test.js     # test模块
        main.js     # 入口js

index.html          # 首页
package.json        # 描述文件
webpack.config.js   # webpack配置文件（非必须）
```

## 首页index.html
```html
<!DOCTYPE html>

<head>
    <title></title>
</head>

<body>
    这是首页 <button onclick="runTest();">run test</button>
    <hr />
    <div id='root'>
    </div>
    <script src="dist/main.bundle.js"></script>
</body>

</html>
```

## 配置文件参考 webpack.config.js
根目录 webpack.config.js 为webpack约定配置文件，自动加载
```javascript
const path = require('path');

module.exports = {
  mode: 'development', //development production
  entry: './src/js/main.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.bundle.js'
  }
};
```

## 入口 main.js
!> 注意对外公开的函数必须 window.***，否则webpack会压缩
```javascript
const test = require('./test.js');

window.runTest = () => {
    document.querySelector('#root').appendChild(test());
}
```

## 模块 test.js
```javascript
module.exports = function() {
    var test = document.createElement('div');
    test.textContent = '这是test输出的字符串';
    return test;
  };
```

## package.json 脚本
script 节点下增加以下配置
```javascript
"build": "./node_modules/.bin/webpack",
```

## 打包
```shell
npm run build
```