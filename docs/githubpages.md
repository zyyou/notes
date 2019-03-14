## 安装docsify-cli
```
npm i docsify-cli -g
```

## 初始化项目
```
docsify init ./notes    //当前目录下生成 notes 工程
cd notes
npm init    //生成package.json
```
编辑scripts，用于启动本地预览，参考
```js
{
  "name": "notes",
  "version": "1.0.0",
  "description": "学习笔记",
  "scripts": {
    "dev": "docsify serve docs --port 8888"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/zyyou/notes.git"
  },
  "author": "zyy",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/zyyou/notes/issues"
  },
  "homepage": "https://github.com/zyyou/notes#readme"
}

```

## 启动预览
命令行进入目录后执行
```
npm run dev
```

## 配置
参考
```js
window.$docsify = {
      name: '学习笔记', //文档标题，显示在侧边栏顶部
      nameLink: '/',  //点击文档标题后跳转的链接地址
      repo: 'zyyou/notes',  //仓库地址
      maxLevel: 4, //默认情况下会抓取文档中所有标题渲染成目录，可配置最大支持渲染的标题层级
      loadNavbar: '_navbar.md', //自定义导航
      loadSidebar: '_sidebar.md',  //自定义边栏
      subMaxLevel: 2, //自定义侧边栏后默认不会再生成目录，你也可以通过设置生成目录的最大层级开启这个功能。
      auto2top: true, //切换页面后自动切换到页面顶部
      //logo: '/_media/icon.svg', //侧边栏LOGO
      paths: 'auto',
      //coverpage: true,
      executeScript: true,
      search: {
        paths: 'auto',
        depth:4,
        maxAge: 600000, // 索引过期时间，单位毫秒，默认一天
        noData: {
          '/': '没有结果!',
          '/en/': 'No results!'
        },
        placeholder: {
          '/': '搜索',
          '/en': 'Search'
        }
      },
      //homepage:'',  //自定义首页
      // coverpage: { //封面页
      //   '/': 'cover.md',
      //   '/zh-cn/': 'cover.md'
      // }
    }
```

## docsify完整文档
https://docsify.js.org/#/zh-cn/quickstart

## 配置 GitHub Pages
- 创建仓库
- 设置仓库，找到GitHub Pages项
- Source 选 master branch /docs folder
至此已完成，可使用GitHub域名访问，如： https://zyyou.github.io/notes/

## 配置自定义域名访问
- 解析域名 CNAME 到 自己在 GitHub的地址，如：note 到 zyyou.github.io
- Custom domain 配置自己的域名 如 note.bianchengke.com
- 选中 Enforce HTTPS 

## 解决无法启用https
解析A记录到以下任意IP
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

## 解决不显示左侧菜单
在docs目录下新建空文件关闭主题，文件名：.nojekyll
