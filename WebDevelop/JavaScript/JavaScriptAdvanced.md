

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


## Promises


## Observable

说实话 Promise 好理解。

但是 Observable 比较复杂，内部机制也没有明确说明。 只能通过各种文章，片面的了解 Observable。


### Learning Observable By Building Observable

终于找到了这篇文章， RxJS Lead 写的很透彻！ <https://medium.com/@benlesh/learning-observable-by-building-observable-d5da57405d87>

没看这篇文章之前，总觉得 Observable 里面各种魔法，异步同步满天飞。 如果 observer 订阅了，会怎么怎么样，没有订阅会怎么怎么样。 肯定有很神奇的机制吧。

看了这篇文章之后， 才发现 Observable 真的是没那么复杂。

> When we talk about RxJS or Reactive programming, generally **observables** get top billing. But **the observer implementation is actually the workhorse of this type of reactive programming**.

看上去 Observable 概念高大上，吸引了所有人的目光。但是实际上工作的还是 observer，正如通常的观察者模式。

#### Observable is just a function

```js
function myObservable(observer) {
    const datasource = new DataSource();
    datasource.ondata = (e) => observer.next(e);
    datasource.onerror = (err) => observer.error(err);
    datasource.oncomplete = () => observer.complete();
    return () => {
        datasource.destroy();
    };
}

const observable = myObservable({
  next(x) { console.log(x); },
  error(err) { console.error(err); },
  complete() { console.log('done')}
});
```

观察上面的代码，

- `datasource` 是一个数据源/事件源，可以是同步的，也可以是异步的。 为 `ondata` 添加一个方法 ： `（e) => observer.next(e)` 每次数据到来时处理数据。 可以看出，其实就是 observer 在干活。
- `myObservable` 这个 function 主要做了这几件事情：
    1. 创建数据源
    2. 注册方法
    3. 返回一个销毁数据源的函数


#### Safe Observers: Make Observers Great Again

当然，写 observer 需要遵守一些规定，比如必须有 `next` `error` `complete` 处理函数。 用户懒得写怎么办？  那就用 `safeObserver` 封装一下好了。

```js
function myObservable(observer) {
  const safeObserver = new SafeObserver(observer);
  const datasource = new DataSource();
  datasource.ondata = (e) => safeObserver.next(e);
  datasource.onerror = (err) => safeObserver.error(err);
  datasource.oncomplete = () => safeObserver.complete();

  safeObserver.unsub = () => {
    datasource.destroy();
  };

  return safeObserver.unsubscribe.bind(safeObserver);
}
```

这里 `SafeObserver` 实现方法








### Promises vs. Observables

大部分文章会拿 Observable 与 Promise 作比较。 二者在表现形式上很相似， 但是内部机制完全是两个概念。

解读：  [Promises vs Observables]( https://medium.com/@mpodlasin/promises-vs-observables-4c123c51fe13)

注： 这篇文章是先看的，没有了解 Observable内部机制之前看的。 其实还是流于表面了。


#### Single value vs multiple values

每个 Promise 只有一个 result/value

每个 Observable 会出现很多 value， 像一个 **stream**， emit values。

```js
const numberObservable = new Observable((observer) => {
    observer.next(5);  // emit 5
    observer.next(10); // emit 10
});

numberObservable.subscribe(value => console.log(value));
```

所以：

Promise 适用于只有一个结果的 异步过程， 例如 HTTP request， `setTimeout`

但是有很多异步事件是可能有多个结果的，例如：

- `setInterval`
- webSockets
- DOM events (mouse clicks etc.)
- any kind of events, for that matter (also in Node.js).

解释一下 鼠标点击， 最常规的做法就是写 `click` 回调。

但有没有一种防止用户频繁点击或者刷新的 `click` 方法？ 采用常规做法就是 `click` 回调中设置 `timeout` 之类的。 会很麻烦。

如果把事件源想象成一个 Observable， click事件就是用户点击 button， piupiupiu发出的若干事件， 可以用 Observable 的一些 Operator（RxJS）， 来过滤、或者限流（throttle）这些事件。 写起来会很方便。

#### Eager vs lazy

promise 的 executor 是立即执行的： 比如设置个定时器，定时器也是立刻开始的。异步过程结束之后会改变 promise对象 的状态和结果。

```js
const promise = new Promise((resolve) => {       
    let i = 0;
    setInterval(() => {
        resolve(i++); // 只返回 0 这个结果
    }, 1000);
});
```

observable 如果没有调用 subscribe， 定时器不会开始执行。 lazy模式。
```js
const observable = new Observable((observer) => {       
    let i = 0;
    setInterval(() => {
        observer.next(i++);
    }, 1000);
});
observable.subscribe(value => console.log(value));
```












###
