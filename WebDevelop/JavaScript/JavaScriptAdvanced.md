

## JavaScript Advanced

一些技术专题，查漏补遗


## Async

2018-07-03

问题： 接触javascript 时间不长，又是一堆概念，各种callback。 最近又看了 promise 和 observable，更加迷惑： javascript的异步是怎么实现的？

1. [Concurrency model and Event Loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
2. [Understanding Javascript Function Executions — Call Stack, Event Loop , Tasks & more](https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec)


![Visual](https://developer.mozilla.org/files/4617/default.svg)

![Event Loop](./images/js_event_loop.png)


到目前为止我的理解：

1. 浏览器里面 V8 引擎相当于一个 Javascript Runtime。每个页面只有一个 Javascript Runtime。 Javascript的执行是 单线程的。
2. 所有非异步过程，比如定义函数、对象、变量、执行函数之类都是在  Heap中分配空间， 按Call Stack（调用栈）中的顺序执行。
3. 一些所谓的异步过程，比如 `setTimeout`， `AJAX` 实际上调用了 Browser Web API， 比如 DOM 中的 `onclick`。 DOM 是文档模型，不是 Javascript Runtime的一部分，对 DOM 的操作不过也是底层 API 调用而已
4. 这些交给别人干的事情，异步的事情，可以“注册” callback 方法，即回调方法。 而这些异步过程在完成之后会发给消息到 Callback Queue 里。 Javascript 的 Event Loop 会循环读取这些消息， 然后将对应的 Callback 函数压入 Call Stack，开始执行。
    - 如果 DOM 的事件没有 listener， 则不会发消息到 Queue中。
    - 有的文章里提到消息和对应的 Callback实际上 是一个 Event Table。 本着职责分离的原则， 异步过程如果有listener，也只是通知一下消息。 Event Loop 在收到消息之后找到对应的 Callback 函数开始执行。


不清楚的地方有：

1. 感觉 Event Loop 是在js 的 main做完之后，也就是所有 js的 非异步过程全部执行完之后， 才开始处理这些 事件的，不过没有在哪里明确看到这一点。
    - 文章 2 有提到这一点
    - event loop 与 runtime 平级的，还是只是 runtime的一部分？

2. 更多的细节。

https://blog.carbonfive.com/2013/10/27/the-javascript-event-loop-explained/

这篇文章很老了。大概从第一张示意图可以看出来， event loop 是在 call stack 为空后开始执行的。 所以我的推测是 event loop 也是 runtime的一部分， 只要 call stack 为空就一直循环检查消息

> “Concurrency in JS— One Thing at a Time, except not Really, Async Callbacks”

> Browser Web APIs- threads created by browser implemented in C++ to handle async events like DOM events, http request, setTimeout, etc.
