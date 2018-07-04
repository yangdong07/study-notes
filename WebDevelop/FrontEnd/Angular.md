


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
- DOM

> The Document Object Model (DOM) is a programming interface for HTML and XML documents. It represents the page so that programs can change the document structure, style, and content. The DOM represents the document as nodes and objects. That way, programming languages can connect to the page.


## Getting Started

参考 <https://angular.io/guide/quickstart>

### ng commands

- `ng new <project-name>`， 创建新的 project
- `ng g` 是 `ng generate` 的简写
    - `ng g class shared/<class-name>`， 在 `src/app/shared`下创建一个 类文件。
    - `ng g module <module-name>`， 在 `src/app` 下创建一个module及目录，并且自动在 app.module里导入。
    - `ng g component <component-name>`， 在 `src/app` 下创建一个 component目录， 并且自动在 app.module里引入。
    - `ng g service services/<service-name>`， 在 `src/app/services` 下创建一个 service

### Project Structure

- `main.ts`, app module 的入口
- `index.html`, 主页， 包括 header 之类，通过 `<app-root></app-root>` 包含 app主体。
- `styles.scss`, 全局样式(scss)
- `app`
    - `app/app.module.ts`, app module
        - declarations, 各种 component的声明
        - imports, module的引入
        - providers, 各种 services，用于 Dependency Injection
        - entryComponents, 入口组件： 被调用的，例如 一些 弹框。
        - bootstrap: 插入 `<app-root>` 的 root component
    - `app/app.component.ts`， `<app-root>` 根组件
    - `app/app.component.html`， 根组件对应模板。


### AppModule/AppComponent

可以定义组件，使用独立模板和样式。 组件之间可以层级调用。 非常好的组织划分方式。

这是根组件。

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

- Component， 基础组件（装饰器?）
- selector， 选择器，主页元素。
- templateUrl， 模板位置
- styleUrls， 样式文件位置

也可以用 template， 直接定义 _inline_ 模板（如果模板不大，就几行的话），比如
```
template: `<h1> {{title} </h1>}`
```


### Directive

Directive 是 Angular 中的一个基本概念。 Component 也可以看成一个 带 Template 的 Directive。

#### Structural Directive

用于调整 DOM 结构。

- `ngIf`，  `<div *ngIf="selectDish">...</div>` ，如果有值，会插入到 DOM中。
- `ngFor`，  一种循环遍历方式，适用于列表，或者 `list` 元素
- `ngSwitch`，类似 switch 方法


#### Attribute Directive

参考 <https://angular.io/guide/attribute-directives>

它的主要作用是通过属性为元素添加一些特性（功能）。


### Angular Pipe

在模板文件中，可以 用 一些 `|` ，来对数据进行简单处理。例如 : `{{ name | uppercase }}`， 就是大写处理。



## Angular Material

Angular 与 Bootstrap 的 JQuery Component 还有一些冲突，所以避免使用 Bootstrap UI Framework。

取而代之，可以用： Angular Material Framework

参考 <https://material.angular.io/guide/getting-started>

1. `npm install --save @angular/material @angular/cdk`
2. `npm install --save @angular/animations` 动画效果
3. `npm install --save hammerjs`  手势

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

`fxLayout` 定义排列方式， 参考 <https://github.com/angular/flex-layout/wiki/fxLayout-API> ，定义排列方式，主要有 row、column 以及相应倒序，共四种。

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

数据流可以是单向的，从 Data数据库 流向 View，也可以是双向的： 一种交互：添加、编辑数据。

有很多概念提出来，就是为了降低 Data 与 View 之间的耦合度。 比如 reflux， 指明数据必须单向流动。

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

注意第二种写法向子组件传入数据，一定要注意在 子组件的 Component 定义中， 将需要给传入的数据变量添加 `Input()` 装饰器。


## Angular Service

### MVC 和 MVVM

两种很经典的 Design Pattern。 所谓 Pattern，就是套路。 遇到什么问题，按照什么套路解决就可以了。

MVC 有三个部分： Model、View、Controller，各司其职。 但MVC并不是最好的pattern， MVC三个部分之间的耦合度还是太高了： Model受 View 和Controller同时调用， 还向 View输出数据； View 从 Controller和 Model获取数据。。。等等。 单纯用 MVC反而不会降低程序设计的复杂度。

感觉上，程序员主要在做的一件事情，就是降低整个系统的复杂程度，提高其容错性、易用性和重用性。（简单来说，就是让世界变得简单。。。但实际上还是在越来越复杂，如果不加限制，就会自我毁灭）

如何降低这种复杂性？就是去除系统之间部件的关联性，这种关联性就是耦合度。

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

所谓 Single Page Application，实际上是指 一个app 所有页面实际都在一个页面（single page）里面。 加载之后，导航切换，虽然地址看上去变了，但实际只是切换了某个组件。


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
    1. `import AppRoutingModule`
    2. 在主页模板里面，使用 `<router-outlet></router-outlet>`， 表示由routes规则定义的可替换的组件。
4. 在导航链接里使用  `routeLink`。


附： `routerLinkActive` 可以用来指明 active时添加的类，用于改样式。 参考 <https://angular.io/api/router/RouterLinkActive>


### Single Page Application: SPA

![Traditional vs. SPA](images/spa_lifecycle.png)

参考： https://en.wikipedia.org/wiki/Single-page_application

SPA 与传统网页的主要区别在于： 传统网页应用，无论 POST还是点击导航链接， 都刷新新的页面。 而 SPA 则只需要在最初把网页应用 download下来， 之后与服务器的交互仅通过 ajax 请求， 单纯获取数据（JSON）即可。

SPA 的挑战（Challenge）， 需要解决的问题：

1. Search Engine Optimization， 传说中的 SEO
2. Partitioning the responsibility between client and server. 划分客户端与服务器端职责。
3. Maintaining History
4. Analytics
5. Speeding up the initial page load. 加速初始页面的加载。 这个很重要。 如果应用很大， 加载速度太慢会影响用户体验。 虽然之后的切换很流畅。

总体来说， SPA更像是一个客户端应用，但是在第一次使用时，要从服务器下载所有资源 （assets）。之后就是请求数据了。



## Angular Forms

### Template Driven Forms

参考 <https://angular.io/guide/forms>

使用 模板 + Two-Way Data-binding 的方式

```html
<form novalidate #loginForm="ngForm" (ngSubmit)="onSubmit(loginForm)">
  <div class="form-group">
    <label for="name">Name</label>
    <input type="text" class="form-control" id="name"
           required
           [(ngModel)]="model.name" name="name">
  </div>
  <button type="submit" [disabled]="loginForm.form.invalid" >Login</button>
</form>
```

- 注意使用了 `[(ngModel)]="model.name"` 将 `model.name` 与这个input进行 “Two-way binding”
- `#loginForm` 是一个 Template Reference Variable， 下面有提到。
- `#loginForm="ngForm"`， 有点像为这个变量加一些属性和方法。
- `(ngSubmit)="onSubmit(loginForm)"`， 就是为 `ngSubmit` 事件添加 handler。注意 handler的参数 `loginForm`，就是 `#loginForm` 模板引用变量。 可以获取这个form的所有信息。
- 在最后提交的 button中，用到了 `loginForm.form.invalid` 这个信息来决定 disable 这个button。

> The native `<form>` element doesn't have a form property. But the NgForm directive does

#### Template Reference Variable

https://angular.io/guide/template-syntax#template-reference-variables--var-

可以在 template 里面引用 DOM 元素， 比如取某个 input 的 value。

```html
<input #phone placeholder="phone number">

<!-- lots of other elements -->

<!-- phone refers to the input element; pass its `value` to an event handler -->
<button (click)="callPhone(phone.value)">Call</button>
```

可以用于调试，例如

```html
<input type="text" class="form-control" id="name"
  required
  [(ngModel)]="model.name" name="name"
  #spy>
<br>TODO: remove this: {{spy.className}}
```

可以观察某个元素的类的变化。


#### Template Driven Validation

参考 <https://angular.io/guide/form-validation#template-driven-validation>

```html
<div class="form-group">
  <label for="name">Name</label>
  <input type="text" class="form-control" id="name"
         required
         [(ngModel)]="model.name" name="name"
         #name="ngModel">
  <div [hidden]="name.valid || name.pristine"
       class="alert alert-danger">
    Name is required
  </div>
</div>
```

注意里面 `#name="ngModel"` ， 可以给 `#name` 这个 DOM 引用，添加状态， 便于使用。

> #name="ngModel" exports NgModel into a local variable called name. NgModel mirrors many of the properties of its underlying FormControl instance, so you can use this in the template to check for control states such as valid and dirty.


### Reactive Forms

 参考 https://angular.io/guide/reactive-forms

如果认为 Template Driven Form 是自底向上构建一个Form表单，那么 Reactive Form 是自顶向下构建，也可以看成 Data Driven Form：先有数据需求，然后构建Form。

二者做的事情一样，但是视角不一样。 内部细节也有不同。

Reactive Form 三大件: FormControl / FormGroup / FormBuilder

#### ReactiveFormsModule

在使用 Reactive Form 之前，要先导入 ReactiveFormsModule 到  AppModule 里。

#### FormControl

```javascript
import { FormControl } from '@angular/forms';

export class HeroDetailComponent {
  name = new FormControl();
}
```

```html
<input class="form-control" [formControl]="name">
```

这就是最简单的一个 form。 name 作为一个 FormControl 实例 通过属性绑定 成为 input的一个属性。

#### FormGroup

```javascript
import { FormControl, FormGroup } from '@angular/forms';

export class HeroDetailComponent {
  heroForm = new FormGroup ({
    name: new FormControl()
  });
}
```

```html
<form [formGroup]="heroForm">
  <div class="form-group">
    <label class="center-block">Name:
      <input class="form-control" formControlName="name">
    </label>
  </div>
</form>
```

通常 form 包含很多 form control。 通过 `FormGroup` 组织起来


#### FormBuilder

可以用 FormBuilder 创建 FormGroup， 简化创建形式。

```javascript
import { FormBuilder, FormGroup } from '@angular/forms';
export class HeroDetailComponent3{
  heroForm: FormGroup; // <--- heroForm is of type FormGroup

  constructor(private fb: FormBuilder) { // <--- inject FormBuilder
    this.createForm();
  }

  createForm() {
    this.heroForm = this.fb.group({
      name: '', // <--- the FormControl called "name"
    });
  }
}
```

 `name: ''` 表示 FormControl 名称为 `name`， 初始值是 `''` 空字符串。


#### Validators

```javascript
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

...
  this.heroForm = this.fb.group({
    name: ['', Validators.required ],
  });
...
```



## Component Interaction

 参考 https://angular.io/guide/component-interaction

Component 之间的交互。主要是层级关系中的 Parent 和 Child 组件交互。 如果是 Sibling 关系，总是可以通过 父组件 间接交互。

### \@Input + setter

数据单向传输： 从父组件到子组件， 通过属性或者数据。

- 子组件： `@Input() iii` + setter
- 父组件模板： `[iii]="property/value"`

### Parent listens for child event

- 子组件： `@Output() ooo = new EventEmitter<boolean>()` 类似一个事件发生器， 事件类型是 boolean 型。也可以是其他类。
- 在父组件模板中 ， 使用 `(ooo)="handler($event)"` 用 handler 处理。

### Parent interacts with child via local variable

- 子组件： 随意
- 父组件模板： `<child-component #child></child-component>`

就是之前的 Template Reference Variable，在父组件中就可以用 `child` 变量访问 子组件的所有属性和方法。

这样做的问题是：只能在Template中调用子组件， 在父组件类中，干瞪眼。

### Parent calls an _@ViewChild()_

ViewChild 也是一个 Angular 常用的概念

> You can use ViewChild to get the first element or the directive matching the selector from the view DOM. If the view DOM changes, and a new child matches the selector, the property will be updated.

意思就是找到第一个 match 的 DOM 元素，然后可以在父组件类中使用。

- 子组件类： `XyzComponent`
- 父组件使用： `@ViewChild(XyzComponent) child: XyzComponent`

然后可以用 child 调用子组件的所有属性和方法。 这里注意另一个概念： AfterViewInit lifecycle hook.

注意 child 对应一个实际的 DOM元素， 一定是等所有 DOM 元素： 父组件、子组件都创建完成时候， 也就是 **AfterViewInit** 及之后， 才能访问到。


关于 ViewChild，有一些参考：

- [非常详细的官方解释](https://blog.angular-university.io/angular-viewchild/)
- <https://alligator.io/angular/viewchild-access-component/>


### TODO: Parent and children communicate via a service


## Observables & RxJS

关于 Observable 的概念见 [JavaScript 专题](../JavaScript/JavaScriptAdvanced.md#observable)

跳出那堆复杂说明，从表现上看 Observable 就是在 publishers（data source） 和 subscribers（observers）之间传递消息。 Observable 通过 subscribe 函数将二者联系起来。

### Observables

Observables provide support for passing messages between **publishers** and **subscribers** in your application. Observables offer significant benefits over other techniques for event handling, asynchronous programming, and handling multiple values.

- `Observable.of(...items)` — Returns an Observable instance that synchronously delivers the values provided as arguments.
- `Observable.from(iterable)` — Converts its argument to an Observable instance. This method is commonly used to convert an array to an observable.


如果理解 Observable 的主要作用，就可以定义任意数据源的 Observable，例如：

```js
function fromEvent(target, eventName) {
  return new Observable((observer) => {
    const handler = (e) => observer.next(e);

    // Add the event handler to the target
    target.addEventListener(eventName, handler);

    return () => {
      // Detach the event handler from the target
      target.removeEventListener(eventName, handler);
    };
  });
}
```
这个函数创建一个 Observable 对象，其 subscribe 函数将 target 事件 与 observer 联系起来。

#### Multicasting

广播

参考 <https://angular.io/guide/observables#multicasting>

其主要原理是用一种 Observable 封装若干个 observer 变成一个 observer。这个observer 在被调用 `next` 的时候， 遍历调用队列中的 `observer.next`

### RxJS Library

Reactive Programming, Observable, RxJS

ReactiveX 官网： http://reactivex.io/

> ReactiveX is a combination of the best ideas from
the Observer pattern, the Iterator pattern, and functional programming

ReactiveX 可以看做是 ： 观察者模式、 迭代器模式 和函数式编程 的一种组合。


#### Observable creation functions

1. Create an observable from a promise
```js
import { fromPromise } from 'rxjs';

// Create an Observable out of a promise
const data = fromPromise(fetch('/api/endpoint'));
// Subscribe to begin listening for async result
data.subscribe({
 next(response) { console.log(response); },
 error(err) { console.error('Error: ' + err); },
 complete() { console.log('Completed'); }
});
```

2. Create an observable from a counter

```js
import { interval } from 'rxjs';

// Create an Observable that will publish a value on an interval
const secondsCounter = interval(1000);
// Subscribe to begin publishing values
secondsCounter.subscribe(n =>
  console.log(`It's been ${n} seconds since subscribing!`));
```

3. Create an observable from an event

```js
import { fromEvent } from 'rxjs';

const el = document.getElementById('my-element');

// Create an Observable that will publish mouse movements
const mouseMoves = fromEvent(el, 'mousemove');

// Subscribe to start listening for mouse-move events
const subscription = mouseMoves.subscribe((evt: MouseEvent) => {
  // Log coords of mouse movements
  console.log(`Coords: ${evt.clientX} X ${evt.clientY}`);

  // When the mouse is over the upper-left of the screen,
  // unsubscribe to stop listening for mouse movements
  if (evt.clientX < 40 && evt.clientY < 40) {
    subscription.unsubscribe();
  }
});
```

4. Create an observable that creates an AJAX request

```js
import { ajax } from 'rxjs/ajax';

// Create an Observable that will create an AJAX request
const apiData = ajax('/api/data');
// Subscribe to create the request
apiData.subscribe(res => console.log(res.status, res.response));
```

注意上面的 subscribe 参数并不是 observer。 这是为了方便省事， 只写了一个 next 函数。 完整的形式是 ： `subscribe(next, error, complete)`

#### Operators

1. `pipe`

pipe 主要作用是把 一些 Operator 给串起来。

```js
import { filter, map } from 'rxjs/operators';

const squareOdd = of(1, 2, 3, 4, 5)
  .pipe(
    filter(n => n % 2 !== 0),
    map(n => n * n)
  );

// Subscribe to get values
squareOdd.subscribe(x => console.log(x));
```

2. common operators

| AREA | 	OPERATORS |
| ---- |  --------- |
| Creation | 	from , fromPromise , fromEvent , of |
| Combination | 	combineLatest , concat , merge , startWith , withLatestFrom , zip |
| Filtering | 	debounceTime , distinctUntilChanged , filter , take , takeUntil |
| Transformation | 	bufferTime , concatMap , map , mergeMap , scan , switchMap |
| Utility | 	tap |
| Multicasting | 	share |

3. `catchError`

RxJS provides the `catchError` operator that lets you handle known errors in the observable recipe.

```js
import { ajax } from 'rxjs/ajax';
import { map, catchError } from 'rxjs/operators';
// Return "response" from the API. If an error happens,
// return an empty array.
const apiData = ajax('/api/data').pipe(
  map(res => {
    if (!res.response) {
      throw new Error('Value expected!');
    }
    return res.response;
  }),
  catchError(err => of([]))   // {***}
);
```

注意在 `pipe` 里， 第一个operator 是map； 如果出错抛出异常； 第二个operator 是 catchError，如果出现异常，会捕获并返回一个空的 array。

记住 Operator 的主要作用： 在subscribe 方法中新建一个中间层的observer，这个observer可以处理数据然后传给下一个 observer。

catchError 稍微做了点改变， 这个中间层的observer ，如果捕获到异常，就传个空的结果给下一层 observer。

4. `retry`

Use the retry operator before the catchError operator. It **resubscribes to the original source observable**, which can then re-run the full sequence of actions that resulted in the error. If this includes an HTTP request, it will retry that HTTP request.

就是重新订阅。

```js
import { ajax } from 'rxjs/ajax';
import { map, retry, catchError } from 'rxjs/operators';
const apiData = ajax('/api/data').pipe(
  retry(3), // Retry up to 3 times before failing
  map(res => {
    if (!res.response) {
      throw new Error('Value expected!');
    }
    return res.response;
  }),
  catchError(err => of([]))
);
```

### Observables in Angular

#### Naming conventions for observables

没想到 Angular 对 Observable/RxJS 这么支持。。。。 连命名都有规范。 通常一个 observable 变量用 `$` 后缀。

```js
@Component({
  selector: 'app-stopwatch',
  templateUrl: './stopwatch.component.html'
})
export class StopwatchComponent {
  stopwatchValue: number;
  stopwatchValue$: Observable<number>;
  ...
}
```

#### EventEmitter

Angular provides an EventEmitter class that is used when publishing values from a component through the `@Output()` decorator. EventEmitter extends Observable, adding an `emit()` method so it can send arbitrary values. When you call `emit()`, it passes the emitted value to the `next()` method of any subscribed observer.

#### HTTP

HttpClient 相比 Promise 主要的优点是：

- Observables 不会改变 response 值； 用Promise可能会？？
- 可以取消
- 写 retry 代码很方便。

#### Async pipe

 这是定义在 template 中的 pipe：

```js
@Component({
  selector: 'async-observable-pipe',
  template: `<div><code>observable|async</code>:
       Time: {{ time | async }}</div>`
})
export class AsyncObservablePipeComponent {
  time = new Observable(observer =>
    setInterval(() => observer.next(new Date().toString()), 1000)
  );
}
```
注意 `time` 是一个 Observable（也可以是Promise）对象。 用 `| async` 这种pipe， 就像是 subscribe 这个 Observable 一样。 写起来非常方便。

#### Router

`Router.events` provides events as observables. You can use the filter() operator from RxJS to look for events of interest, and subscribe to them in order to make decisions based on the sequence of events in the navigation process. Here's an example:

The `ActivatedRoute` is an injected router service that makes use of observables to get information about a route path and parameters. For example, ActivateRoute.url contains an observable that reports the route path or paths.

```js
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-routable',
  templateUrl: './routable.component.html',
  styleUrls: ['./routable.component.css']
})
export class Routable2Component implements OnInit {
  constructor(private activatedRoute: ActivatedRoute) {}

  ngOnInit() {
    this.activatedRoute.url
      .subscribe(url => console.log('The URL changed to: ' + url));
  }
}
```

#### Reactive forms

Reactive forms have properties that use observables to monitor form control values. The FormControl properties **`valueChanges`** and **`statusChanges`** contain observables that raise change events. Subscribing to an observable form-control property is a way of triggering application logic within the component class. For example:

```js
import { FormGroup } from '@angular/forms';

@Component({
  selector: 'my-component',
  template: 'MyComponent Template'
})
export class MyComponent implements OnInit {
  nameChangeLog: string[] = [];
  heroForm: FormGroup;

  ngOnInit() {
    this.logNameChange();
  }
  logNameChange() {
    const nameControl = this.heroForm.get('name');
    nameControl.valueChanges.forEach(
      (value: string) => this.nameChangeLog.push(value)
    );
  }
}
```

这个 Observable 概念在 Angular里面用的是真广泛。 不知道在其他框架（React和Vue）是什么样子？


### Observables vs. Promises

主要的差异：

- Observables are declarative; computation does not start until subscription. Promises execute immediately on creation. This makes observables useful for defining recipes that can be run whenever you need the result.

- Observables provide many values. Promises provide one. This makes observables useful for getting multiple values over time.

- Observables differentiate between chaining and subscription. Promises only have .then() clauses. This makes observables useful for creating complex transformation recipes to be used by other part of the system, without causing the work to be executed.

- Observables subscribe() is responsible for handling errors. Promises push errors to the child promises. This makes observables useful for centralized and predictable error handling.



参考 https://angular.io/guide/comparing-observables， 还有与 Array 的比较，总结的很好。就不抄了。

Observable 用了古老的技艺，做了一些很优雅的事情。

## Angular Forms, Angular and Reactive JavaScript
