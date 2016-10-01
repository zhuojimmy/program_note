# 回顾Node.js

* Node.js结构
* 单线程系统
* 事件循环和无阻塞IO模型
***

## Node.js结构
### 事件驱动IO-libuv

## libuv——Node.js的核心
libuv为IO操作分配进程，并且向调用者返回回调函数。所以，Node.js内部创建IO操作的进程，但是，他给程序员使用单运行线程。
当调用HTTP请求，Node.js创建libuv线程并且做好准备接收另外一个请求。当时间被libuv激活，他向用户返回response。

**libuv包提供一下重要核心特性**
* 完全特定的事件循环
* 异步文件系统操作
* 线程池
* 线程和同步基础
* 异步TCP和UDP sockets
* 子进程
* 信号处理

libuv内部使用libeio，负责线程和异步IO事件。libev，高性能事件循环。

***

## 多线程VS单线程

##### 多线程的缺点
* 很难处理并发和死锁

libuv包含**可移植操作系统接口系统**

多线程阻塞IO直到特定的现成完成了他的操作在所有较慢的平台上。

***

## 事件循环和无阻塞IO模型
如何通知特定的进程，任务完成或者发生错误。

### 事件循环的重要性
Node.js本质上是异步的，需要用异步的方式编程。除非清晰理解事件循环，否则很难做到。

### 事件循环的机制
Node.js运行系统有执行栈，压入想要执行的任务。操作系统从栈里取任务，管理运行必要的操作。
为了运行异步代码，这种方法将无效。Libuv包采用队列存储每个异步操作的回调函数。时间循环在特定的时间间隔运行，并且检查栈，这在Node.js的术语中叫*tick*。如果栈空，将回调函数从队列中压进栈等待执行。

```javascript
console.log("i am first");

setTimeout(function timeout() {
     console.log("i am second");
}, 5000);

console.log("i am third");
```
输出：
```
i am first
i am third
i am second
```
另一种情况：
```javascript
console.log("i am first");

setTimeout(function timeout() {
 	console.log("i am second");
}, 0);

console.log("i am third");
```
输出：
```
i am first
i am third
i am
```
继续测试：
```javascript
console.log("i am 1");

setTimeout(function timeout() {
 	console.log("i am 2");
}, 10);

setTimeout(function timeout() {
 	console.log("i am 3");
},0);

console.log("i am 4");
```
输出：
```
i am 1
i am 4
i am 3
i am 2
```

***

# 第二章 Node.js网络服务

* 网络服务的执行
* HTTP操作和他的作用
* 使用HTTP模块开发网络服务
* 使用Express开发网络服务
***

## 网络服务的执行

用户输入URL（统一资源定位），内部发生了：
1. 输入URL，向name server发送一个请求，任务是将域名转换为IP地址。
2. 浏览器发送一个HTTP的GET请求到目标IP地址的80端口。
3. 网络服务器接收到GET请求，发送文件（通常是HTML）到浏览器，浏览器渲染视图。这发生在你发送请求的每个时刻。

所有的操作通过HTTP和TCP/IP协议执行，TCP/IP负责网络层任务，HTTP主要处理服务请求和响应。

## HTTP操作和用法

* CREAT——POST
* READ——GET
* UPDATA——PUT     *PUT和POST的区别？？？？*
* DELETE——DELETE

## 使用HTTP模块开发网络服务器

## 路由和中间件

#### 路由存在的问题：
我们需要为每个路由执行一条代码。

#### 中间件
中间件可以看做软件结构注入代码，每次都执行。

***

#介绍Sails.js和MVC概念
***
Sails.js是一个网络框架，帮助开发者快速搭建可扩展的（scalable）生产就绪的（production-ready）网络应用。他遵循注明的MVC模式，参考了Active Server Pages（ASP），Ruby on Rails等。因为内置数据流的支持，和API generation，Sails.js 是开发网络服务的第一选择之一，特别是实时应用，像聊天系统或者多人在线游戏。

* MVC概念入门
* 安装Sails.js
* 理解Sails.js项目的目录结构
* 增加数据库支持
*通过JSHint配置Grunt任务运行文件 
***

## MVC概念入门
MVC是一种Smalltalk工程师创造的软件结构模式，MVC把应用分为三个内部部分，数据通过每一个部分。这种划分提供了良好的软件复用性和松耦合。

#### Model
Model是MVC模型最重要的组件，Model代表知识，他有可能是单一对象或者对象的嵌入结构。Model直接管理数据，逻辑，和应用逻辑。

#### View
View是model的视觉表现。View从Model取得数据显示在浏览器或者命令行上。View跟随着Model改变。一个理想的MVC应用必须有一个系统去通知其他组件对于这个变化。

#### Controller
顾名思义，Controller的任务是接收输入，将其转换成model或者view的命令。管理model做状态转换，或者管理view做信息更新。

## 安装Sails.js

## 理解Sails.js项目目录结构

#### assets
包含静态文件，图像，HTML，JS文件。在Express.js中，我们需要使用`express.static`定义静态路径