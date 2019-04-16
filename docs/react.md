## 不使用脚手架从0搭建React工程

- 创建工程

```shell
mkdir reacttest
cd reacttest
npm init -y
npm install -S react react-dom
npm install -D babel-loader babel-core babel-preset-env babel-preset-react webpack webpack-cli
```

- 增加编译脚本
package.json scripts节点下增加
```javascript
"build": "./node_modules/.bin/webpack",
```

- 根目录建.babelrc文件，内容如下

```javascript
{
    "presets":[
        "env","react"
    ]
}
```

- 根目录建webpack.config.js配置文件

```javascript
const path = require('path');

module.exports = {
  mode: 'development', //development production
  entry: {
    app: './src/index.js'
  },
  output: {
    filename: 'js/[name].js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [{
      test: /\.(js|jsx)$/,
      use: ['babel-loader'],
      include: path.resolve(__dirname, './src')
    }
    ]
  }
};
```

- 建源码文件，目录结构

```
public
    index.html
src
    App.js
    index.js
.babelrc
package.json
webpack.config.js
```

- index.html

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8" />
    <title>test</title>
</head>
<body>
    <div id="root"></div>
    <script src="../dist/js/app.js"></script>
</body>
</html>
```

- App.js

```javascript
import React from 'react';

class App extends React.Component {
    render(){
        return (
            <div>
                Hello zyy测试
            </div>
        )
    }
}

export default App;

```

- index.js

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(<App/>,document.getElementById('root'));
```

- 编译

```shell
npm run build
```
!> 此时可能出现以下错误信息，意思就是版本不兼容，按提示安装就好了
Error: Cannot find module '@babel/core'
 babel-loader@8 requires Babel 7.x (the package '@babel/core'). If you'd like to use Babel 6.x ('babel-core'), you should install 'babel-loader@7'.

 - 测试
 可以使用serve作为开发测试
 ```shell
 cd ..
 serve reacttest
 ```
