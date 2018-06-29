


## Intro to Angular

### Library vs. Framework

参考 <https://medium.com/datafire-io/libraries-vs-frameworks-626cdde799a7> 写的很不错。

> In general, the differentiating feature is a matter of control — frameworks dictate how your project will be structured, whereas libraries are building blocks that can be used anywhere. If we use the metaphor of architecture, libraries are bricks, and frameworks are, fittingly, the frame.

Library 是砖块， Framework 就是框架

![Perception vs Cognition](images/library.vs.framework.png)



### Terms

- interpolation：  模板插入
- one-way data binding： 数据绑定
- property-binding， Component类属性可以对应到模板
- event-binding，模板事件可以调用 Component 类方法。



### Angular Material

参考 <https://material.angular.io/guide/getting-started>

1. `npm install --save @angular/material @angular/cdk`
2. `npm install --save @angular/animations` 动画效果
3. `npm install --save hammerjs`  手势


## Getting Started

参考 <https://angular.io/guide/quickstart>

Angular 与 Bootstrap 的 JQuery Component 还有一些冲突，所以避免使用 Bootstrap UI Framework。

取而代之，可以用： Angular Material Framework


### 创建新的 app ： `ng new`

文件作用：

- `main.ts`, app module 的入口
- `index.html`, 主页
- `styles.scss`, 全局样式(scss)
- `app`
    - `app/app.module.ts`, app module
    - `app/app.component.ts`, app component 定义

### app.component

可以定义组件，使用独立模板和样式。 组件之间可以层级调用。 非常好的组织划分方式。

```javascript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'app';
}
```

- Component， 基础组件（装饰器?）
- selector， 选择器，主页元素。
- templateUrl， 模板位置
- styleUrls， 样式文件位置

也可以用 template， 直接定义 _inline_ 模板（如果模板不大，就几行的话），比如
```
template: `<h1> {{title} </h1>}`
```


### Structural Directive

- `ngIf`，  `<div *ngIf="selectDish">...</div>` ，如果有值，会插入到 DOM中。
- `ngFor`，  一种循环遍历方式，适用于列表，或者 `list` 元素
- `ngSwitch`，类似 switch 方法


### Angular Pipe

在模板文件中，可以 用 一些 `|` ，来对数据进行简单处理。例如 : `{{ name | uppercase }}`， 就是大写处理。


## Angular Material

### Flex Layout

参考 <https://github.com/angular/flex-layout/wiki/Responsive-API>

![breakpoints](images/material_breakpoints.png)

| breakpoint | mediaQuery |
|--------|--------|
| xs    | 'screen and (max-width: 599px)'                         |
| sm    | 'screen and (min-width: 600px) and (max-width: 959px)'  |
| md    | 'screen and (min-width: 960px) and (max-width: 1279px)' |
| lg    | 'screen and (min-width: 1280px) and (max-width: 1919px)'|
| xl    | 'screen and (min-width: 1920px) and (max-width: 5000px)'|
|       |                                                         |
| lt-sm | 'screen and (max-width: 599px)'                         |
| lt-md | 'screen and (max-width: 959px)'                         |
| lt-lg | 'screen and (max-width: 1279px)'                        |
| lt-xl | 'screen and (max-width: 1919px)'                        |
|       |                                                         |
| gt-xs | 'screen and (min-width: 600px)'                         |
| gt-sm | 'screen and (min-width: 960px)'                         |
| gt-md | 'screen and (min-width: 1280px)'                        |
| gt-lg | 'screen and (min-width: 1920px)'                        |
<br/>


注意与 bootstrap的 breakpoints 还不一样。
这里 960px 以下都算小设备 （sm）。。。。 bootstrap 是 768px。

### fxLayout 和 fxLayoutAlign

`fxLayout` 定义排列方式， 参考 <https://github.com/angular/flex-layout/wiki/fxLayout-API> ，定义排列方式，主要有 row、column 以及相应倒序，共四种。

如果加上 `wrap`， 则表示可以换行。

`fxLayoutAlign` 这个是对齐方式， 包括 main axis 和 cross axis(optional)， 参考 <https://github.com/angular/flex-layout/wiki/fxLayoutAlign-API>

  举个例子：

```html
<div fxLayout="row" fxLayoutAlign="center center">
  <div>1. One</div> <div>2. Two</div> <div>3. Three</div> <div>4. Four</div>
</div>
```

主轴是横轴，交叉轴是纵轴。 对齐方式首先是 水平居中，然后是 垂直居中。


## Angular Data-Binding

这是 Data 与 View 之间必须存在的一个关系， 二者是强耦合的。

数据流可以是单向的，从 Data数据库 流向 View，也可以是双向的： 一种交互：添加、编辑数据。

有很多概念提出来，就是为了降低 Data 与 View 之间的耦合度。 比如 reflux， 指明数据必须单向流动。

Angular的 data binding包括以下几种：

![Angular Data-binding](./images/angular_databinding.png)

左边是 DOM，是 view；  右边是 Component，是数据源（可以通过api从backend取）。

在 Component 里定义  property；在模板中可以这样写：

| component | template | 意义    |
| :------ | :---- | :----|
| `property: type = value` | `{{ value }}`| Data ----> View，单向 |
| `property: type = value` | `<app-sub-component [sub-property] = "value" />`| Data ----> View，单向， 向子组件传入数据 |
| `handler` | `(event) = "handler" `|  Data <---- View， 单向 |
| `property` | `[(ng-model)] == "property"` | Data <----> View， 双向|

注意第二种写法向子组件传入数据，一定要注意在 子组件的 Component 定义中， 将需要给传入的数据变量添加 `Input()` 装饰器。


## Angular Service

### MVC 和 MVVM

两种很经典的 Design Pattern。 所谓 Pattern，就是套路。 遇到什么问题，按照什么套路解决就可以了。

MVC 有三个部分： Model、View、Controller，各司其职。 但MVC并不是最好的pattern， MVC三个部分之间的耦合度还是太高了： Model受 View 和Controller同时调用， 还向 View输出数据； View 从 Controller和 Model获取数据。。。等等。 单纯用 MVC反而不会降低程序设计的复杂度。

感觉上，程序员主要在做的一件事情，就是降低整个系统的复杂程度，提高其容错性、易用性和重用性。（简单来说，就是让世界变得简单。。。但实际上还是在越来越复杂，如果不加限制，就会自我毁灭）

如何降低这种复杂性？就是去除系统之间部件的关联性，这种关联性就是耦合度。

MVVM 是 MVC的进一步简化： Model View View-Model， 去掉了Controller， 用 View-Model 替代。

![MVVM](./images/MVVMPattern.png)

这里 View 只与 ViewModel 完成数据绑定（data-binding）。 ViewModel 与 Model产生数据交互。


### Dependency Injection

> In software engineering, dependency injection is a technique whereby one object (or static method) supplies the dependencies of another object. A dependency is an object that can be used (a service). An injection is the passing of a dependency to a dependent object (a client) that would use it. The service is made part of the client's state.[1] Passing the service to the client, rather than allowing a client to build or find the service, is the fundamental requirement of the pattern.

主要是这句话： **Passing the service to the client, rather than allowing a client to build or find the service, is the fundamental requirement of the pattern.**  就是不要让client去找 server， 而是把 server 注入到 client 中。

如果一个对象 A 依赖另一个对象 B， 常规思路是什么？就是在 A 需要B的时候创建 B，然后使用 B。 这种做法被诟病的地方是： **这种依赖关系被 A 隐藏了。**

所谓依赖注入是指， 在创建A时（或者创建A之后），将 B 传递给 A。 A 只需要知道接口即可， 就可以任意使用。 B成为 A的一部分。_The service is made part of the client's state._ 这种好处就是 依赖关系外部可见：我创建了 A、B， 我知道 A 需要依赖 B。把 B 传给 A好了。 **主要将创建B的职责从 A 中剥离了**

个人感觉这种 依赖注入 还是面向接口编程的一种。名字取得高大上，实际上做的事情非常简单。


参考

- <https://en.wikipedia.org/wiki/Dependency_injection>



### Angular Service

Angular Service主要就是实现了这种依赖注入的概念。定义 Service， 然后将 Service 注入到 组件（Component）中。

基础使用：

- `ng generate service services/dish`， 会创建一个 Angular Service
- 在 services/dish.ts 里面可以写一些接口方法。
- 在 app.moudle 里 添加 DishService， 即可以在所有组件中注入。
- 在 Component 里 “注入” 这个 DishService 注入方式就是在 constructor里面 添加一个 `private dishService: DishService` ，然后就可以通过 dishService 调用方法，获取数据。

表面上看，其实跟直接调用也没啥区别。 感觉只是一种形式上的要求。

## Router and Single Page Application

以前我在用 React 的时候搞过一段时间， 这些概念还是了解一点的。

所谓 Single Page Application，实际上是指 一个app 所有页面实际都在一个页面（single page）里面。 加载之后，导航切换，虽然地址看上去变了，但实际只是切换了某个组件。


### Html5 History API

参考 <https://developer.mozilla.org/en-US/docs/Web/API/History_API>

 除了可以调用 API，back 或者 forward 页面， 还可以 “Adding and modifying history entries”， 就是为历史页面添加状态。



### Router

参考 <https://angular.io/guide/router>

 可以全部在 `app.module` 里定义，但是这里建议 在单独 module中定义 router。

1. 新建module： `ng g module app-routing`
2. 在 module文件里，
    1. `import { RouterModule, Routes } from '@angular/router';`
    2. 定义 各种路径 `routes: Routes[]`。 或者单独文件定义。
    3. 除了默认导出的 `AppRoutingModule` ，还要准备一个 ： `RouterModule.forRoot(routes)`
3. 在 app.module 里：
    1. `import AppRoutingModule`
    2. 在主页模板里面，使用 `<router-outlet></router-outlet>`， 表示由routes规则定义的可替换的组件。
4. 在导航链接里使用  `routeLink`。


附： `routerLinkActive` 可以用来指明 active时添加的类，用于改样式。 参考 <https://angular.io/api/router/RouterLinkActive>


### Single Page Application: SPA

![Traditional vs. SPA](images/spa_lifecycle.png)

参考： https://en.wikipedia.org/wiki/Single-page_application

SPA 与传统网页的主要区别在于： 传统网页应用，无论 POST还是点击导航链接， 都刷新新的页面。 而 SPA 则只需要在最初把网页应用 download下来， 之后与服务器的交互仅通过 ajax 请求， 单纯获取数据（JSON）即可。

SPA 的挑战（Challenge）， 需要解决的问题：

1. Search Engine Optimization， 传说中的 SEO
2. Partitioning the responsibility between client and server. 划分客户端与服务器端职责。
3. Maintaining History
4. Analytics
5. Speeding up the initial page load. 加速初始页面的加载。 这个很重要。 如果应用很大， 加载速度太慢会影响用户体验。 虽然之后的切换很流畅。

总体来说， SPA更像是一个客户端应用，但是在第一次使用时，要从服务器下载所有资源 （assets）。之后就是请求数据了。















## Angular Services, Routing and Single Page Applications
