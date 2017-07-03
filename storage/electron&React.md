# 使用electron完成web桌面应用,组合React构建


## 创建electron应用
开发环境基础准备安装nodejs和npm

### 1. 官方简单事例

> electron是自带require的

#### 1.1 下载运行demo
在终端运行
````
git clone https://github.com/electron/electron-quick-start  
cd electron-quick-start  
npm install // 安装依赖包
npm run start // 运行项目
````

![electron-quick-start start view](https://raw.githubusercontent.com/Grifree/blog/master/storage/images/electron%26React_1.png)

#### 1.2 package.json
````json
{
  "name": "electron-quick-start",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  },
  "devDependencies": {
    "electron": "~1.6.2"
  }
}
````
#### 1.3 main.js

````javascript
const electron = require('electron');
const path = require('path');
const url = require('url');

// 控制应用生命周期的模块
const app = electron.app
// 创建本地浏览器窗口的模块
const BrowserWindow = electron.BrowserWindow

// 指向窗口对象的一个全局引用，如果没有这个引用，那么当该javascript对象被垃圾回收的
// 时候该窗口将会自动关闭
let win;

function createWindow() {
  // 创建一个新的浏览器窗口
  win = new BrowserWindow({width: 800, height: 600});

  // 并且装载应用的index.html页面
  win.loadURL(url.format({
    pathname: path.join(__dirname, 'index.html'),
    protocol: 'file:',
    slashes: true
  }))

  // 打开开发工具页面
  win.webContents.openDevTools();

  // 当窗口关闭时调用的方法
  win.on('closed', function () {
    // 解除窗口对象的引用，通常而言如果应用支持多个窗口的话，你会在一个数组里
    // 存放窗口对象，在窗口关闭的时候应当删除相应的元素。
    win = null;
  });
}

// 当Electron完成初始化并且已经创建了浏览器窗口，则该方法将会被调用。
// 有些API只能在该事件发生后才能被使用。
app.on('ready', createWindow);

// 当所有的窗口被关闭后退出应用
app.on('window-all-closed', function () {
  // 对于OS X系统，应用和相应的菜单栏会一直激活直到用户通过Cmd + Q显式退出
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', function () {
  // 对于OS X系统，当dock图标被点击后会重新创建一个app窗口，并且不会有其他
  // 窗口打开
  if (win === null) {
    createWindow();
  }
});

// 在这个文件后面你可以直接包含你应用特定的由主进程运行的代码。
// 也可以把这些代码放在另一个文件中然后在这里导入。

````


### 2. 整理目录结构优化

## 创建React项目

[Create React App 示例](https://github.com/facebookincubator/create-react-app)

命令行运行
````
npm install -g create-react-app
create-react-app my-app
cd my-app/
npm run start // 运行项目
````

![create-react-app start view](https://raw.githubusercontent.com/Grifree/blog/master/storage/images/electron%26React_2.png)

#### 1.2 explain

支持热更新

## 整合 React + Electron

### 1. config
Create React App创建的项目是一个纯前端项目，整合React项目和Electron并且保留热调试和本地包引用需要以下几个简单的操作 .
可以直接在上一个示例 `my-app` 项目中进行一点小修改

1. 在项目根目录（不是src目录）下,创建Electron的启动文件 `main.js` 文件 , 把上面示例拷过来就行 .
2. 在 `package.json` 中添加一些字段
````json
{
    "main":"main.js",
    "homepage": ".",
    "scripts": {
        "elec": "electron .",
    }
}
````
> - "homepage":"."
> > 默认情况下，homepage是 http://localhost:3000 ,build后，所有资源文件路径都是／static，而Electron调用的入口是以file：协议，/staitc就会定位到系统的根目录去，所以找不到静态文件。在package.json文件中添加homepage字段并设置为"."后，静态文件的路径就变成了相对路径，就能正确的找到了。
> - "main":"main.js"
> > 设定启动文件为根目录下的`main.js`文件
> - "elec": "electron ."
> > 终端运行npm run elec可以快速启动桌面应用程序
3. 修改main.js 中的win.loadURL，改为
````javascript
win.loadURL(url.format({
    pathname: path.join(__dirname, './build/index.html'),
    protocol: 'file:',
    slashes: true
}))
````

### 2. 运行
在终端运行
````
cd my-app
npm install path --save // 安装main.js中所需模块
npm install url --save // 安装main.js中所需模块
npm run build // 构建应用程序
npm run elec // 运行(已构建完成)项目
````
到目前为止，已经可以启动React创建的桌面应用了。
![electron & React Web Desktop](https://raw.githubusercontent.com/Grifree/blog/master/storage/images/electron%26React_3.png)

#### notice
- 还留有几个小问题
    1. 每次都要 npm run build 后才能看到修改项
    2. 无法获得系统包
- 解决小步骤
    1. main.js启动页修改为 http://localhost:3000/
        ````javascript
        win.loadURL('http://localhost:3000/')
        ````
        开发时，启动npm start 后，在另一个终端启动 npm run elec 这样在electron中就可以热调试了<br/>
        不过编译后需要改回从build/index.html启动，这样很麻烦，可以在main.js中定义个 DEV字段，设置为 true/false, 以后运行 npm run elec 之前，根据需要修改DEV为true/false就行了
        ````javascript
        const DEV = true
        //判断是否是开发模式
        if(DEV){
            win.loadURL("http://localhost:3000/")
        }else{
            win.loadURL(url.format({
                pathname: path.join(__dirname, './build/index.html'),
                protocol: 'file:',
                slashes: true
            }))
        }
        ````
    2. 无论是运行build中的index.html还是http://localhost:3000 都无法通过require调用本地包的，需要使用window.require , 示例:APP.js
        ````javascript
        import React, { Component } from 'react';
        import './App.css';

        var os = window.require('os') // not be : var os = require('os')
        console.log("got:",os.cpus())

        class App extends Component {
          render() {
            return (
              <div className="App">
                 CPU count: {os.cpus().length}
              </div>
            );
          }
        }
        export default App;
        ````
- 其他注意点        
    - 所有js只能在src目录下


## 打包
[electron-packager 官方文档](https://github.com/electron-userland/electron-packager)

### 1. 安装
````
npm install electron-packager --save-dev // for use in npm scripts
npm install electron-packager -g // for use from cli
````

###  2. 基本用法
````
electron-packager <sourcedir> <appname> --platform=<platform> --arch=<arch>
--electron-version=1.4.4 --out <packagedir> --overwrite [optional flags...]
````
在 package.json 中添加打包所需字段 , 方便以后调用
````json
{
  "scripts": {
        "packager_win": "electron-packager . HelloWorld --platform=win32 --arch=all --electron-version=1.4.4 --out ./OutApp --overwrite --ignore=node_modules --ignore=src --ignore=electron --ignore=logs --ignore=public --ignore=typings",
        "packager_mac":"electron-packager . HelloWorld --platform=mas --arch=all --electron-version=1.4.4 --out ./OutApp --overwrite --ignore=node_modules --ignore=src --ignore=electron --ignore=logs --ignore=public --ignore=typings",
        "packager": "npm run packager_win && npm run packager_mac"
    }
}
````
终端运行
````
npm run packager
````
第一次会很慢 , 他需要下载Electron安装包 , 以后就会很快了

### 3. mac下运行 `npm run packager_win` 会遇到的问题
- 问题
    - 打包时 , 报错缺少 wine
    ![缺少 wine](https://raw.githubusercontent.com/Grifree/blog/master/storage/images/electron%26React_4.png)
- 解决步骤
    - 安装 homebrew , 在终端运行
        ````
        /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
        ````
    - 安装 wine , 在终端运行
        ````
        brew install wine
        ````
    - 下载安装 XQuartz
        - [官网下载链接 XQuartz for mac](https://www.xquartz.org/)

现在mac下可以正常打包了

## 附录:
- 所有参考文档链接
    - https://electron.atom.io/docs/
    - http://www.libs.org.cn/index.php?m=content&c=index&a=show&catid=79&id=168
    - https://zhuanlan.zhihu.com/p/20225295?columnSlug=FrontendMagazine
    - http://www.tuicool.com/articles/biEVbau
    - http://www.jianshu.com/p/785ed0ac08ee
    - https://github.com/facebookincubator/create-react-app
    - https://github.com/electron-userland/electron-packager
    - http://blog.csdn.net/arvin0/article/details/52690023
    - https://wiki.winehq.org/MacOS
    - https://www.xquartz.org/
