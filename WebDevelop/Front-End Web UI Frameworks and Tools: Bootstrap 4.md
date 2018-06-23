



## Full Stack Web Development

开始进入全栈 web 开发的世界。

分成三层：

- Presentation Layer， 主要是 Html/CSS/javascript
- Business Logic Layer， 主要是 php/python/jsp 之类的，CGI或者 API
- Data Access Layer， DBMS。


现在的技术倾向于用一种语言处理所有问题。 这个任务落在了 javascript 头上。。

- Presentation Layer：  js框架： angular/react
- Business Logic Layer: nodejs，express， 提供 JSON 格式的API接口
- Data Access Layer， Mongodb，json文档


参考资料：

### What is a Full Stack developer?

https://www.laurencegellert.com/2012/08/what-is-a-full-stack-developer/

自底向上：

1. Server, Network, and Hosting Environment.
2. Data Modeling
3. Business Logic（业务逻辑）
4. API layer / Action Layer / MVC
5. User Interface
6. User Experience
7. Understanding what the customer and the business need.

### Multitier architecture

https://en.wikipedia.org/wiki/Multitier_architecture

> In software engineering, multitier architecture (often referred to as n-tier architecture) or multilayered architecture is a client–server architecture in which presentation, application processing, and data management functions are physically separated. The most widespread use of multitier architecture is the three-tier architecture.

### What is the 3-Tier Architecture?

http://www.tonymarston.net/php-mysql/3-tier-architecture.html

很大的一篇文章。2012年的文章。

3层架构与 MVC 有一些区别。 感觉上 3层 架构更清晰一些。 区分展示层、业务逻辑层和数据访问层，职责分明，并且所用技术也可以区分。


## Node.js

参考 <https://en.wikipedia.org/wiki/Node.js>

> Node.js is an open-source, cross-platform JavaScript run-time environment that executes JavaScript code server-side. Historically, JavaScript was used primarily for client-side scripting, in which scripts written in JavaScript are embedded in a webpage's HTML and run client-side by a JavaScript engine in the user's web browser. Node.js lets developers use JavaScript for server-side scripting—running scripts server-side to produce dynamic web page content before the page is sent to the user's web browser. Consequently, Node.js represents a "JavaScript everywhere" paradigm,[5] unifying web application development around a single programming language, rather than different languages for server side and client side scripts.

大意：

- Node.js 是一个 javascript run-time environment。
- 过去 javascript 运行在客户端浏览器上，由 JavaScript egine运行。
- 主要为了在服务器端执行，并且返回动态网页内容。统一客户端和服务器端编程语言。。

如果拿 node.js 与 python 做比较， 二者有点相似； npm 也可以类比成 pip。

### npm (node package manager)

可以安装 package

### package.json

这个文件主要列出了 项目依赖的 packages。 JSON 格式

- `npm init`， 初始化 文件
- `npm install`， 安装 pacakge 到 node_modules 里。


## Bootstrap 4

Bootstrap 可以算是最流行的 UI framework。

同样的还有  Material UI（google）。 https://www.keycdn.com/blog/front-end-frameworks/ 这里有10大流行的前端框架。

借助 UI Framework， 可以快速搭建页面元素，进行前端开发，提高效率，非常方便。

### Responsive Design

响应式设计： 主要是为了适应移动设备不同屏幕大小。

Adapt to user's "viewport"

Mobile-First Design： 先考虑移动设备， 最后过渡到桌面浏览器。

Foundation of Responsive Design

- Grid System
- Fluid Images
- Media Queries： CSS 语法： 根据 ”媒介” 决定显示样式。

### Bootstrap Grid System

最新的版本基于  CSS Flexbox layout。 更加规范和灵活。

#### container/row/col

- contrainer
- row
- 12 col

#### size
| size | screen size | max container width |
| :--- | :---------- | :---|
| default | extra small < 576px | None, auto |
| _sm_ | small (576px ~ 768px) | 540px
| _md_ | medium (768px ~ 992px) | 720px
| _lg_ | large (992px ~ 1200px) | 960px
| _xl_ | extra large (1200px ~) | 1140px

比如 `col-sm-*`， 表示当设备尺寸大于 576px时，都是以正常比例按列在一行内分布。 否则就不按比例（被挤下来）。

column 之间间隙（gutter） 30px， 一边 15px。

#### order

- `order-sm-first`
- `order-sm-last`

注意： 只在符合设备尺寸（`sm` 表示大于 576px）时有效。 挤成两行就不算了。

#### alignment

- `row align-items-center`  每列垂直 置中（vertical centered）
- `row justify-content-center` 每列水平居中（horizontal-alignment）
- `col align-self-center` 用在列中，独自垂直居中


#### offset

- `offset-sm-*` 表示偏移几列。


#### nesting

可以在col中嵌套若干行。


#### display

参考 <https://getbootstrap.com/docs/4.0/utilities/display/>

- 显示用 `d-block`
- 隐藏用 `d-none`
- 只在 xs 隐藏： `d-none d-sm-block`， 就是从 sm开始显示。
- 只在 xs 显示： `d-block d-sm-none`， 就是从 sm开始隐藏。
- 其他可以以此类推。

### Navigation

#### Navigation

- `nav` ，tag
  - `navbar`， bootstrap class
  - `navbar-expand-sm`， 当大于等于sm的时候，扩展。（也就是在 xs的时候收起来）
  - `fixed-top`，固定在顶部
- `ul li` 列表 tag，用于 导航链接
  - `ul.navbar-nav`
  - `li.nav-item`， 导航项
  - `li.active` ，当前项
  - `li>a.nav-link`， 导航链接
- `collapse`
  - `div.collapse`
  - `div.navbar-collapse` 展开
- 其他
  - `navbar-brand` 这个是品牌logo位置
  - `mr-auto`， 标识向左靠，类似于 `float-left`，右边的margin 自动。

#### Breadcrumbs 面包屑

Indicator of the current page's location within a navigational hierarchy.

- Path based: set of steps， 步骤
- Location based: hierarchy， 层级结构（树）
- Attribute based: set of choices， 选项

应用：

- `ol.breadcrumb`， 将一列作为 “面包屑”
- `li.breadcrumb-item`， 路径。

### Icon Font

类似于 Emoji。 图标可以用来替代文字。

- Font Awesome， css文件和font files
- Bootstrap Social，  social 图标样式， css文件。

#### Font Awesome

参考 <https://fontawesome.com/> 主要是一些图标。

- `npm install font-awesome`
- `font-awesome.min.css`

#### Bootstrap Social

参考 <https://lipis.github.io/bootstrap-social/> 主要是图标样式。 一些 Social 的样式。

- `npm install bootstrap-social`
- `bootstrap-social.css`


### User Input: Buttons and Forms

用户交互部分

Button 就是按钮，也可以用 `<a>` 标签样式化 为 button样式。

Forms 主要包括：

- `input`
- `button`
- `textarea` 文本区
- `select` 选项

#### Buttons

定义 button group：

```
<div class="btn-group" role="group">
    <a role="button" class="btn btn-primary" href="tel:+85212345678"><i class="fa fa-phone"></i> Call</a>
    <a role="button" class="btn btn-info"><i class="fa fa-skype"></i> Skype</a>
    <a role="button" class="btn btn-success" href="mailto:confusion@food.net"><i class="fa fa-envelope-o"></i> Email</a>
</div>
```

注意里面的 role

一些button样式 class

- `btn`
- `btn-primary`， 蓝色的
- `btn-info`，
- `btn-success`， 绿色的


#### Forms







### 参考资料

- Responsive Design
  - [Beginner’s Guide to Responsive Web Design](http://blog.teamtreehouse.com/beginners-guide-to-responsive-web-design)
  - [The 2014 Guide to Responsive Web Design](http://blog.teamtreehouse.com/modern-field-guide-responsive-web-design)
- Bootstrap Grid System and Flexbox
  - [Bootstrap Grid System](https://getbootstrap.com/docs/4.0/layout/grid/)
  - [W3C. CSS Flexible Box Layout Module Level 1](https://www.w3.org/TR/css-flexbox/)
  - 👍 [A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
  - 👍 [A Visual Guide to CSS3 Flexbox Properties](https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties)

- Information Architecture ，主要关于 页面层级架构（信息结构）
  - [Information Architecture 101: Techniques and Best Practices](https://www.webpagefx.com/blog/web-design/information-architecture-101-techniques-and-best-practices/)
  - [Web Site Information Architecture models](http://webdesignfromscratch.com/website-architecture/ia-models/) 模型非常简单的图示说明

- Navigation Bar
  - [Designing A Winning Navigation Menu: Ideas and Inspirations](https://www.hongkiat.com/blog/navigation-design-ideas-inspiration/) 好的案例
  - [Are You Making These Common Website Navigation Mistakes?](https://blog.kissmetrics.com/common-website-navigation-mistakes/) 不好的案例。
  - [3 Reasons We Should Stop Using Navigation Bars](http://www.webdesignerdepot.com/2014/01/3-reasons-we-should-stop-using-navigation-bars/) 为啥不用 导航栏。

- Breadcrumbs
  - [Breadcrumb Navigation Examined: Best Practices & Examples](http://www.hongkiat.com/blog/breadcrumb-navigation-examined-best-practices-examples/) 好的案例
  - [](http://www.hongkiat.com/blog/breadcrumb-navigation-examined-best-practices-examples/) 最佳实践（图显示不出来）。

- Icon Fonts
  - 👍  [Icon Fonts are Awesome](https://css-tricks.com/examples/IconFont/)  为什么用 Icon Fonts，非常好的解释。
  - [The Final Nail in the Icon Fonts Coffin?](http://www.sitepoint.com/final-nail-icon-fonts-coffin/) 为什么不用 Icon Fonts ？
  - [Using SVGs](http://gomakethings.com/using-svgs/) Icon Fonts的替代品
