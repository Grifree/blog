# 使用electron完成web桌面应用,组合React构建
====================================

## 大纲

- 基础准备 node npm安装
- electron-quick-start 官方示例
    - 安装
    - 运行调试
- Create React App 官方文档
    - 热更新调试
- 配置细节,更改
    - package.json
    - window.require
- mac系统下打包
    - electron-packager
    - wine
    - XQuartz
    - homebrew
- 参考示例链接并简易标明介绍
    - electron/doc
    - https://github.com/facebookincubator/create-react-app
    - wine
    - XQuartz (解决打包速度慢)
    - homebrew

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
![electron-quick-start start view](/Users/grifree/Downloads/QQ20170701-082859.gif)

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

![create-react-app start view](/Users/grifree/Downloads/QQ20170701-080831.gif)

#### 1.2 explain

支持热跟新

## 整合 React + Electron

### 1. 修改运行
Create React App创建的项目是一个纯前端项目，整合React项目和Electron并且保留热调试和本地包引用需要以下几个简单的操作 .
可以直接在上一个示例 `my-app` 项目中进行一点小修改

1. 在项目根目录（不是src目录）下,创建Electron的启动文件 `main.js` 文件 , 把上面示例拷过来就行 .
2. 在 `package.json` 中添加main字段 , `"main": "main.js"`
````
main字段
npm run elec

````
3. 修改main.js 中的win.loadURL，改为
````javascript
win.loadURL(url.format({
   pathname: path.join(__dirname, './build/index.html'),
   protocol: 'file:',
   slashes: true
}))
````

#### explain
window.require
所有js只能在src目录下


## 打包
