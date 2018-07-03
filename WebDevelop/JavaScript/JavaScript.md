

## JavaScript

https://javascript.info/


## Intro

- JavaScript was initially created as a browser-only language, but now it is used in many other environments as well.
- At this moment, JavaScript has a unique position as the most widely-adopted browser language with full integration with HTML/CSS.
- There are many languages that get “transpiled” to JavaScript and provide certain features. It is recommended to take a look at them, at least briefly, after mastering JavaScript.

### What can in-browser JavaScript do?

- Add new HTML to the page, change the existing content, modify styles.
- React to user actions, run on mouse clicks, pointer movements, key presses.
- Send requests over the network to remote servers, download and upload files (so-called AJAX and COMET technologies).
- Get and set cookies, ask questions to the visitor, show messages.
- Remember the data on the client-side (“local storage”).

### What CAN’T in-browser JavaScript do?

简单说来就是：

1. 不允许访问系统资源，除非得到许可（permission）
2. 不允许跨域访问： 为了保护用户信息安全，防止 js脚本 将用户信息发送到其他域。
3. 不存在 浏览器 tab 页面直接通信。 间接通信的方法有，比如用 cookie 或者通过服务器。

### What makes JavaScript unique?

- Full integration with HTML/CSS.
- Simple things done simply.
- Supported by all major browsers and enabled by default.


## JavaScript Fundamentals

### The modern mode, "use strict"

1. **The `"use strict"` directive switches the engine to the “modern” mode, changing the behavior of some built-in features.**
2. **The strict mode is enabled by `"use strict"` at the top. Also there are several language features like “classes” and “modules” that enable strict mode automatically.**
3. The strict mode is supported by all modern browsers.
4. It’s always recommended to start scripts with "use strict". All examples in this tutorial assume so, unless (very rarely) specified otherwise.

尽量都使用 "use strict"， 利用一些更完善的特性

### Variable

- `let` – is a modern variable declaration. The code must be in strict mode to use let in Chrome (V8).
- `const` – is like let, but the value of the variable can’t be changed.
- `var` – is an old-school variable declaration.

这里有一个类比： 变量是一个盒子， 给变量赋值就是把值装进盒子。 没装值的时候 变量是 `undefined`。

大部分语言中(例如python)， 变量都只是一个引用。每次对一个值进行引用，引用计数+1。 如果对一个值的引用没有了，这个值会被清理掉。

### Data Types

There are 7 basic types in JavaScript.

- `number` for numbers of any kind: integer or floating-point.
- `string` for strings. A string may have one or more characters, there’s no separate single-character type.
- `boolean` for true/false.
- `null` for unknown values – a standalone type that has a single value null.
- `undefined` for unassigned values – a standalone type that has a single value undefined.
- `object` for more complex data structures.
- `symbol` for unique identifiers.

The typeof operator allows us to see which type is stored in the variable.

- Two forms: `typeof x` or `typeof(x)`.
- Returns a string with the name of the type, like "string".
- For `null` returns "object" – that’s an error in the language, it’s not an object in fact.

### Type Conversion

There are three most widely used type conversions: to string, to number and to boolean.

#### ToString

Occurs when we output something, can be performed with `String(value)`. The conversion to string is usually obvious for primitive values.

#### ToNumber

Occurs in math operations, can be performed with `Number(value)`.

The conversion follows the rules:

| Value |  Becomes... |
|-------|-------------|
|`undefined`|`NaN`|
|`null`|`0`|
|<code>true&nbsp;/&nbsp;false</code> | `1 / 0` |
| `string` | The string is read "as is", whitespaces from both sides are ignored. An empty string becomes `0`. An error gives `NaN`. |

#### ToBoolean

Occurs in logical operations, or can be performed with `Boolean(value)`.

Follows the rules:

| Value |  Becomes... |
|-------|-------------|
|`0`, `null`, `undefined`, `NaN`, `""` |`false`|
|any other value| `true` |


Most of these rules are easy to understand and memorize. The notable exceptions where people usually make mistakes are:

- `undefined` is `NaN` as a number, not `0`.
- `"0"` and space-only strings like `"   "` are true as a boolean.


### Operators

基本操作符和操作符优先级就不抄了。需要的时候上网找，用多了就熟了。

几个不常见但有用的，或者 js 特有的：

- Numeric conversion, unary `+`： 用于将字符串转成数字。
- Strict equality operator `===`： **Checks the equality without type conversion.**

#### Comparison with null and undefined

两个变量在做比较时，会对值做 Type Conversion。

类型定义多了一个 `undefined`， 就带来一堆问题。。。


##### null and undefined
```js
alert( null === undefined ); // false
alert( null == undefined ); // true,  
```

Rules:

1. `null` 和 `undefined` 这俩货彼此相等（`==`）， 与其他值都不相等。
2. **For maths and other comparisons `< > <= >=`,  Values `null/undefined` are converted to a number:**
    - `null` becomes 0
    - `undefined` becomes NaN.

##### examples

```js
alert( null > 0 );  // false   rule2: 0 > 0
alert( null == 0 ); // false   rule1: null != 0
alert( null >= 0 ); // true    rule1: 0 >= 0

alert( undefined > 0 ); // false    rule2
alert( undefined < 0 ); // false    rule2
alert( undefined == 0 ); // false   rule1
```

#### Ternary operator ‘?’

三元操作符， 常规用法是一种根据条件取值的简写。例如：

```js
let age = prompt('age?', 18);

let message = (age < 3) ? 'Hi, baby!' :
  (age < 18) ? 'Hello!' :
  (age < 100) ? 'Greetings!' :
  'What an unusual age!';
alert( message );
```

 用 if 的写法是：

```js
if (age < 3) {
  message = 'Hi, baby!';
} else if (age < 18) {
  message = 'Hello!';
} else if (age < 100) {
  message = 'Greetings!';
} else {
  message = 'What an unusual age!';
}
```

注意最好 **不要在 `?` 中写执行语句**，可读性不强。例如：

```js
(company == 'Netscape') ?
   alert('Right!') : alert('Wrong.');
```

#### Logical Operators

- `||`(OR)
- `&&`(AND)
- `!` (NOT)

注意一件事情就行了，  

- `||` 的运算如果遇到 True 就会终止， 返回 True，后面的表达式就不会计算了
- `&&` 的运算如果遇到 False 就会终止， 返回 False。

这与 python 很像。

#### Summary

- Comparison operators return a logical value.
- Strings are compared letter-by-letter in the “dictionary” order.
- **When values of different types are compared, they get converted to numbers (with the exclusion of a strict equality check `===`).**
- **Values `null` and `undefined` equal `==` each other and do not equal any other value.**
- Be careful when using comparisons like > or < with variables that can occasionally be null/undefined. Making a separate check for null/undefined is a good idea.

最后一套是建议先检查 null/undefined 然后再比较。


### Loops/ Switch


#### while /for
- `while` – The condition is checked before each iteration.
- `do..while` – The condition is checked after each iteration.
- `for (;;)` – The condition is checked before each iteration, additional settings available.

`break/continue` support labels before the loop. A label is the only way for `break/continue` to escape the nesting and go to the outer loop.

#### switch

### Functions


1. Function Declaration

```js
function sum(a, b) {
  let result = a + b;

  return result;
}
```

2. Function Expression

```js
let sum = function(a, b) {
  let result = a + b;

  return result;
}
```

3. Arrow Functions

```js
// expression at the right side
let sum = (a, b) => a + b;

// or multi-line syntax with { ... }, need return here:
let sum = (a, b) => {
  // ...
  return a + b;
}

// without arguments
let sayHi = () => alert("Hello");

// with a single argument
let double = n => n * 2;
```


4. Functions may have local variables: those declared inside its body. Such variables are only visible inside the function.
5. Parameters can have default values: `function sum(a = 1, b = 2) {...}`.
6. Functions always return something. If there’s no return statement, then the result is undefined.


#### Function naming

prefix:

- `create...`， 创建对象
- `show`
- `get`， 取一个值
- `check`， 检查，返回 boolean



## JavaScript Development

### Interaction: `alert`, `prompt`, `confirm`

基础交互。

- `alert`, shows a message.
- `prompt`, shows a message asking the user to input text. It returns the text or, if CANCEL or Esc is clicked, all browsers return null.
- `confirm`, shows a message and waits for the user to press “OK” or “CANCEL”. It returns true for OK and false for CANCEL/Esc.

### Chrome Debugging

https://javascript.info/debugging-chrome


### Code style

- https://javascript.info/coding-style
- https://google.github.io/styleguide/javascriptguide.xml


#### Ninja Code

参考： https://javascript.info/ninja-code， 都是些不好的代码习惯。

All “pieces of advice” above are from the real code… Sometimes, written by experienced developers. Maybe even more experienced than you are ;)

- Follow some of them, and your code will become full of surprises.
- Follow many of them, and your code will become truly yours, no one would want to change it.
- Follow all, and your code will become a valuable lesson for young developers looking for enlightenment.

如果这样写，这种代码以后只有自己懂。。。

### Automated testing with mocha

#### Behavior Driven Development (BDD)

BDD is three things in one: **tests** AND **documentation** AND **examples**.

BDD 核心思想就是：在 （需求 + 测试 + 文档 + 用例 + ...） 与 代码之间 快速迭代。


#### mocha

spec:

```js
describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function() {
      assert.equal([1,2,3].indexOf(4), -1);
    });
  });
});
```

- **Tests** guarantee that the code works correctly.
- **Docs** – the titles of `describe` and `it` tell what the function does.
- **Examples** – the tests are actually working examples showing how a function can be used.

就相当于 把 文档、测试、用例写在一起了。



## JavaScript Object

### Object

两种方式创建 Object

```js
let user = new Object(); // "object constructor" syntax
let user = {};  // "object literal" syntax
```

We can imagine an object as a cabinet with signed files. Every piece of data is stored in its file by the key. It’s easy to find a file by its name or add/remove a file.

- 对象是一个 cabinet
- 每个数据是一个文件，用键值对表示。

```js
let user = {     // an object
  name: "John",  // by key "name" store value "John"
  age: 30        // by key "age" store value 30
};
delete user.age;  // 删除一个文件
```


#### Computed properties

有时候 key 是一个变量的值， 称为 computed properties

```js
let fruit = prompt("Which fruit to buy?", "apple");
let bag = {
  [fruit]: 5, // the name of the property is taken from the variable fruit
};
alert( bag.apple ); // 5 if fruit="apple"
```

我被唬住了。。。其实就是：

```js
let fruit = prompt("Which fruit to buy?", "apple");
let bag = {};

bag[fruit] = 5;
```

#### reference

**A variable stores not the object itself, but its “address in memory”, in other words “a reference” to it.**

给变量赋值一个 object， 实际上是将 object 地址赋给这个变量，使 object 多了一个引用。

### Garbage collection

https://javascript.info/garbage-collection 解释的很好。

核心概念是： **Reachability**。 直白说，就是根据能否被访问到，决定是否清理某个对象（内存）。

算法的思路是： 从顶部（树状）遍历所有对象及其引用的对象， 并标记（mark）。 对于没有mark的对象，就是 无法访问到的对象，回收内存，清理掉。


### Symbol

`Symbol` is a primitive type for unique identifiers.

```js
let id1 = Symbol("id");
let id2 = Symbol("id");

alert(id1 == id2); // false
```
Symbols are created with `Symbol()` call with an optional description.

`Symbol` 有两个特性：

1. **Symbols are always different values**， 类似于产生一个 identifier
2. Symbolic properties do not participate in `for..in` loop. 遍历对象属性时访问不到。

`Symbol` 可以用来 "隐藏" 属性，防止被别的脚本使用。 这是因为： 1. 别的脚本无法通过创建同名的 `Symbol`来访问属性； 2. 无法通过`for..in` 看到。


### Object Method, this

- Methods can reference the object as `this`.
- The value of `this` is defined at run-time.
- When a function is declared, it may use this, but that this has no value until the function is called.
- That function can be copied between objects.
- When a function is called in the “method” syntax: `object.method()`, the value of this during the call is object.

- Please note that arrow functions are special: they have no this. When this is accessed inside an arrow function, it is taken from outside.


JavaScript 的 Method 中的 this 与 python 的 self 不一样。

1. `this` 是 unbound的： **不与对象绑定**， 在 run-time 才计算 this
2. function不是对象，没有 `this`


### Object to primitive conversion

主要用于 打印输出alert之类的， 类似 python的 `__str__` 和 `__repr__`

The object-to-primitive conversion is called automatically by many built-in functions and operators that expect a primitive as a value.

hint 是根据 context 判断得到的。有三种类型。

- "string" (for alert and other string conversions)
- "number" (for maths)
- "default" (few operators)

The specification describes explicitly which operator uses which hint. There are very few operators that “don’t know what to expect” and use the "default" hint. Usually for built-in objects "default" hint is handled the same way as "number", so in practice the last two are often merged together.

The conversion algorithm is:

1. Call `obj[Symbol.toPrimitive](hint)` if the method exists,
2. Otherwise if hint is "string"
    - try `obj.toString()` and `obj.valueOf()`, whatever exists.
3. Otherwise if hint is "number" or "default"
    - try `obj.valueOf()` and `obj.toString()`, whatever exists.

一般定义一个 `toString()` 方法就够了。


### Constructor, operator "new"

Constructor functions technically are regular functions. There are two conventions though:

- They are named with capital letter first. 首字母大写
- They should be executed only with "new" operator. 只能使用 "new" 操作符

```js
function User(name) {
  // this = {};  (implicitly)

  // add properties to this
  this.name = name;
  this.isAdmin = false;

  // return this;  (implicitly)
}

let user = new User("jack");
```


## Data Types


### Methods of primitives

JavaScript allows us to work with **primitives (strings, numbers etc)** as if they were objects.

Here’s the paradox faced by the creator of JavaScript:

1. 对 primitive 有很多操作（比如 uppercase， split）等等
2. 但是 primitive 最好非常快和轻量（lightweight），意思是不要变成 object 那么“复杂”。

解决方法：
1. Primitives are still primitive. A single value, as desired.
2. The language allows access to methods and properties of strings, numbers, booleans and symbols.
3. When this happens, a special “object wrapper” is created that provides the extra functionality, and then is destroyed.

简单说来就四个字： 过河拆桥。当需要要用一些特殊方法时，先用一个 “object wrapper” 把它变成一个临时对象，用完之后 destroy。


### Number

All numbers in JavaScript are stored in 64-bit format IEEE-754, also known as “double precision”.

所有数字都是双精度浮点， 64bit。

### Strings

In JavaScript, the textual data is stored as strings. There is no separate type for a single character.  单字符也是 string

The internal format for strings is always **UTF-16**, it is not tied to the page encoding.

 注意 python3 字符都是  **unicode**。

- There are 3 types of quotes. Backticks` `` ` allow a string to span multiple lines and embed expressions.
- Strings in JavaScript are encoded using **UTF-16**.
- To get a character, use: [].
- To get a substring, use: slice or substring.
- To lowercase/uppercase a string, use: `toLowerCase/toUpperCase`.
- To look for a substring, use: `indexOf`, or `includes/startsWith/endsWith` for simple checks.
- To compare strings according to the language, use: localeCompare, otherwise they are compared by character codes.



### Array

The declaration:

```js
// square brackets (usual)
let arr = [item1, item2...];
```

- The call to `new Array(number)` creates an array with the given length, but without elements.

- The length property is the array length or, to be precise, its last numeric index plus one. It is auto-adjusted by array methods.
- If we shorten length manually, the array is truncated.

To loop over the elements of the array:

- `for (let i=0; i<arr.length; i++)` – works fastest, old-browser-compatible.
- `for (let item of arr)` – the modern syntax for items only,
- `for (let i in arr)` – 有一些问题， 不要用这个遍历数组。


### Array Methods

A cheatsheet of array methods:

- To add/remove elements:
  - `push(...items)` -- adds items to the end,
  - `pop()` -- extracts an item from the end,
  - `shift()` -- extracts an item from the beginning,
  - `unshift(...items)` -- adds items to the beginning.
  - `splice(pos, deleteCount, ...items)` -- at index `pos` delete `deleteCount` elements and insert `items`.
  - `slice(start, end)` -- creates a new array, copies elements from position `start` till `end` (not inclusive) into it.
  - `concat(...items)` -- returns a new array: copies all members of the current one and adds `items` to it. If any of `items` is an array, then its elements are taken.

- To search among elements:
  - `indexOf/lastIndexOf(item, pos)` -- look for `item` starting from position `pos`, return the index or `-1` if not found.
  - `includes(value)` -- returns `true` if the array has `value`, otherwise `false`.
  - `find/filter(func)` -- filter elements through the function, return first/all values that make it return `true`.
  - `findIndex` is like `find`, but returns the index instead of a value.

- To transform the array:
  - `map(func)` -- creates a new array from results of calling `func` for every element.
  - `sort(func)` -- sorts the array in-place, then returns it.
  - `reverse()` -- reverses the array in-place, then returns it.
  - `split/join` -- convert a string to array and back.
  - `reduce(func, initial)` -- calculate a single value over the array by calling `func` for each element and passing an intermediate result between the calls.

- To iterate over elements:
  - `forEach(func)` -- calls `func` for every element, does not return anything.

- Additionally:
  - `Array.isArray(arr)` checks `arr` for being an array.

Please note that methods `sort`, `reverse` and `splice` modify the array itself.

These methods are the most used ones, they cover 99% of use cases. But there are few others:

- [arr.some(fn)]()/[arr.every(fn)]() checks the array.

  The function `fn` is called on each element of the array similar to `map`. If any/all results are `true`, returns `true`, otherwise `false`.

- [arr.fill(value, start, end)]() -- fills the array with repeating `value` from index `start` to `end`.

- [arr.copyWithin(target, start, end)]() -- copies its elements from position `start` till position `end` into *itself*, at position `target` (overwrites existing).

For the full list, see the [manual](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array).


### Iterables

Iterable objects is a generalization of arrays. That’s a concept that allows to make any object useable in a `for..of` loop.

#### Symbol.iterator

类似于 Symbol.toPrimitive， 可以用来定义迭代方法，类似python的 `__iter__`

```js
let range = {
  from: 1,
  to: 5
};

// 1. call to for..of initially calls this
range[Symbol.iterator] = function() {

  // 2. ...it returns the iterator:
  return {
    current: this.from,
    last: this.to,

    // 3. next() is called on each iteration by the for..of loop
    next() {
      // 4. it should return the value as an object {done:.., value :...}
      if (this.current <= this.last) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
};

// now it works!
for (let num of range) {
  alert(num); // 1, then 2, 3, 4, 5
}
```


### Map, Set, WeakMap and WeakSet

- `Map` -- is a collection of keyed values.

    The differences from a regular `Object`:

    - Any keys, objects can be keys.
    - Iterates in the insertion order.
    - Additional convenient methods, the `size` property.

- `Set` -- is a collection of unique values.

    - Unlike an array, does not allow to reorder elements.
    - Keeps the insertion order.

- `WeakMap` -- a variant of `Map` that allows only objects as keys and removes them once they become inaccessible by other means.

    - It does not support operations on the structure as a whole: no `size`, no `clear()`, no iterations.

- `WeakSet` -- is a variant of `Set` that only stores objects and removes them once they become inaccessible by other means.

    - Also does not support `size/clear()` and iterations.

`WeakMap` and `WeakSet` are used as "secondary" data structures in addition to the "main" object storage. Once the object is removed from the main storage, so it only stays in `WeakMap/WeakSet`, they clean up automatically.


### Object.keys, values, entries

They are supported for:
- Map
- Set
- Array (except arr.values())

### Destructuring assignment 

解释一下， 这是 “解构”， 就是把一个数据结构拆分。

- Destructuring assignment allows for instantly mapping an object or array onto many variables.
- The object syntax:
    ```js
    let {prop : varName = default, ...} = object
    ```

    This means that property `prop` should go into the variable `varName` and, if no such property exists, then `default` value should be used.

- The array syntax:

    ```js
    let [item1 = default, item2, ...rest] = array
    ```

    The first item goes to `item1`, the second goes into `item2`, all the rest makes the array `rest`.

- For more complex cases, the left side must have the same structure as the right one.


### Date and time

- Date and time in JavaScript are represented with the [Date]() object. We can't create "only date" or "only time": `Date` objects always carry both.

- Months are counted from zero (yes, January is a zero month).

- Days of week in `getDay()` are also counted from zero (that's Sunday).

- `Date` auto-corrects itself when out-of-range components are set. Good for adding/subtracting days/months/hours.

- Dates can be subtracted, giving their difference in milliseconds. That's because a `Date` becomes the timestamp when converted to a number.

- Use `Date.now()` to get the current timestamp fast.


### JSON methods, toJSON

- `JSON.stringify` to convert objects into JSON.
- `JSON.parse` to convert JSON back into an object.












## Callback, Async, EventLoop
