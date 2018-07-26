

问题：

- Node.js 是什么？ 是一个 JavaScript run-time environment。
- Node.js 如何工作的？
    - 猜测是类似于浏览器的 Javascript engine。 这里用到 V8。



## Node.js

参考 <https://en.wikipedia.org/wiki/Node.js>

> Node.js is an open-source, cross-platform JavaScript run-time environment that executes JavaScript code server-side. Historically

> **JavaScript was used primarily for client-side scripting**, in which scripts written in JavaScript are embedded in a webpage's HTML and run client-side by a JavaScript engine in the user's web browser.

> Node.js lets developers use JavaScript for server-side scripting—running scripts server-side to produce dynamic web page content before the page is sent to the user's web browser.

> Consequently, Node.js represents a **"JavaScript everywhere"** paradigm,[5] unifying web application development around a single programming language, rather than different languages for server side and client side scripts.

大意：

- Node.js 是一个 javascript run-time environment。
- 过去 javascript 运行在客户端浏览器上，由 JavaScript egine运行。
- 主要为了在服务器端执行，并且返回动态网页内容。统一客户端和服务器端编程语言。。

如果拿 node.js 与 python 做比较， 二者有点相似； npm 也可以类比成 pip。

### npm (node package manager)

可以安装 package


### Node Modules

Node 的一个概念

参考 ：

- https://nodejs.org/api/modules.html
- https://www.w3schools.com/nodejs/nodejs_modules.asp
- https://medium.freecodecamp.org/requiring-modules-in-node-js-everything-you-need-to-know-e7fbd119be8

注意几点：

- 每个文件是一个 module（ Each file in Node is its own module）
-


### Event Loops

Nodejs 是如何工作的？ 一种 non-blocking I/O 模型， 使用 callback。

参考 <https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick>

```
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

- 这是一个 event loop。 每个 box 对应一个 event loop 的一个 phase。
- Each phase has a FIFO queue of callbacks to execute. 每个 phase 有一个 FIFO 的 callbacks 队列。
- 每个phase中 同步执行队列中的 callbacks， 直到耗尽队列的所有 callback或者执行callbacks数量达到上限。

注意所有的异步调用，比如  `setTimeout` `setInterval` 或者 网络请求、文件读取 都是一个异步过程（参考 JavaScript），在结束后， callbacks 就绪，进入对应的 callback 队列。

#### Phases Overview

- **timers**: this phase executes callbacks scheduled by setTimeout() and setInterval().
- **pending callbacks**: executes I/O callbacks deferred to the next loop iteration.
- **idle**, prepare: only used internally.
- **poll**: retrieve new I/O events; execute I/O related callbacks (almost all with the exception of close callbacks, the ones scheduled by timers, and setImmediate()); node will block here when appropriate.
- **check**: setImmediate() callbacks are invoked here.
- **close callbacks**: some close callbacks, e.g. socket.on('close', ...).

Between each run of the event loop, Node.js checks if it is waiting for any asynchronous I/O or timers and shuts down cleanly if there are not any.

注意大部分callbacks 都是在 poll 阶段执行的。
