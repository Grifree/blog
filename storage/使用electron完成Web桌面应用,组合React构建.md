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

## 仅用electron创建应用
开发环境基础准备安装nodejs和npm

### 1. 官方简单事例

> electron是自带require的

#### 1.1 下载运行demo
````
git clone https://github.com/electron/electron-quick-start  
cd electron-quick-start  
npm run start // 运行项目
````
#### 1.2 package.json
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
    pathname: path.join(__dirname, './build/index.html'),
    protocol: 'file:',
    slashes: true
  }));

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

## 结合React

## 打包
