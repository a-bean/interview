###   1. **事件循环？**
- **宏任务队列**（macrotask queue）：ajax、setTimeout、setInterval、DOM监听、UI Rendering等
- **微任务队列**（microtask queue）：Promise的then回调、 Mutation Observer API、queueMicrotask()等
#### 浏览器事件循环的过程
1. 从上至下执行所有的同步代码（编写的顶层script代码）；
2. 遇到宏任务，微任务将连个分别加到各自的事件队列中；
3. 同步代码执行完成后，优先执行==微任务队列的任务==。然后在执行宏任务；
4. 在执行任何一个宏任务之前（不是队列，是**一个宏任务**），都会先查看**微任务队列**中是否有任务需要执行
5. 也就是宏任务执行之前，必须保证微任务队列是空的（都会去清空微任务队列)
6. 如果不为空，那么就优先执行微任务队列中的任务（回调）；
#### nodejs的事件循环

- **nodejs宏任务事件队列如下**：
1. 定时器（Timers）：本阶段执行已经被 `setTimeout()` 和 `setInterval()` 的调度回调函数。
2. 待定回调（Pending Callback）：对某些系统操作（如TCP错误类型）执行回调，比如TCP连接时接收到ECONNREFUSED。
3. idle, prepare：仅系统内部使用。
4. 轮询（Poll）：检索新的 I/O 事件；执行与 I/O 相关的回调；
5. 检测：`setImmediate()` 回调函数在这里执行。
6. 关闭的回调函数：一些关闭的回调函数，如：`socket.on('close', ...)`。
- **微任务**（不是队列）（microtask queue）：Promise的then回调、 **process.nextTick()（优先级高于promise.then的回调）**
#### nodejs完整事件环：

1. 执行同步代码，将不同任务添加至相应的队列。
2. **所有同步代码执行后会去执行满足条件的微任务**
3. 所有微任务代码执行完成后会执行timer队列中满足的宏任务（**新版node，改为和浏览器一致，执行完一个宏任务就回去清空微任务**）
4. timer中所有的宏任务执行完成之后就会依次切换队列（按照上面的顺序）。**在完成队列切换之前会先清空微任务**。
```js
//第一个问题：
setTimeout(()=>{
	console.log("timeoout")
},0);
setImmediate(()=>{
    console.log("immediate")
});

//多次输出出现不同结果，原因是因为：setTimeout的延迟不准
//1.第一次
timeoout
immediate
//2.第n次
immediate
timeoout


//第二个问题
const fs = require('fs')
fs.readFile("./m.js",()=>{
    setTimeout(()=>{
        console.log("timeoout")
    },0);
    setImmediate(()=>{
        console.log("immediate")
    });
})
//输出结果都是如下，原因：fs.readFile是在Poll队列中执行，而Poll队列执行完成之后就会去执行check队列，然后在重新执行事件循环执行timer中的任务。
immediate
timeoout
```


### 2.this的绑定规则

#### 1. 默认绑定

+ 独立的函数调用我们可以理解成函数没有被绑定到某个对象上进行调用；

~~~js
//1.该函数直接被调用，并没有进行任何的对象关联；
//2.这种独立的函数调用会使用默认绑定，通常默认绑定时，函数中的this指向全局对象（window）

function foo() {
  console.log(this); 
}

foo();// window
~~~

#### 2. 隐式绑定

+ 另外一种比较常见的调用方式是通过某个对象进行调用的：也就是他的调用位置中，是通过某个对象发起的函数调用。

~~~js
//案例1
function foo() {
  console.log(this); 
}

var obj = {
  name: "why",
  foo: foo
}

obj.foo();// obj对象

//案例2
function foo() {
  console.log(this); 
}

var obj1 = {
  name: "obj1",
  foo: foo
}

var obj2 = {
  name: "obj2",
  obj1: obj1
}

obj2.obj1.foo();// obj1对象


//案例3：隐式丢失
function foo() {
  console.log(this);
}

var obj1 = {
  name: "obj1",
  foo: foo
}

// 将obj1的foo赋值给bar
var bar = obj1.foo;
bar(); //window
~~~

#### 3. 显示绑定

+ 隐式绑定有一个前提条件：必须在调用的`对象内部`有一个对函数的引用（比如一个属性），如果没有这样的引用，在进行调用时，会报找不到该函数的错误；正是通过这个引用，间接的将this绑定到了这个对象上；
+ 如果我们不希望在 **对象内部** 包含这个函数的引用，同时又希望在这个对象上进行强制调用，我们应该使用==call,apply方法==，JavaScript所有的函数都可以使用call和apply方法
  + ==call和apply的区别==：call第二个参数需要把实参按照形参的个数传进去，apply则传入一个形参数组。

~~~js
function foo() {
  console.log(this);
}

foo.call(window); // window
foo.call({name: "why"}); // {name: "why"}
foo.call(123); // Number对象,存放时123
~~~

+ 使用**bind**函数，让一个函数总是显示的绑定到一个对象上

1. 手写bind（）

~~~js
function bind(func, obj){
    return function() {
        return func.apply(obj, arguments)
	}
}

function foo() {
  console.log(this);
}

var obj = {
  name: "why"
}

var bar = bind(foo, obj);
bar(); // obj对象
~~~

2. 使用 Function.prototype.bind

~~~js
function foo() {
  console.log(this);
}

var obj = {
  name: "why"
}

var bar = foo.bind(obj);

bar(); // obj对象
~~~

#### 4. 内置函数的this指向

~~~js
// 1. setTimeout内部是通过apply进行绑定的this对象，并且绑定的是全局对象
setTimeout(function() {
  console.log(this); // window
}, 1000);


// 2. 数组的forEach：默认情况下传入的函数是自动调用函数（默认绑定）
var names = ["abc", "cba", "nba"];
names.forEach(function(item) {
  console.log(this); // 三次window
});

var obj = {name: "why"};
names.forEach(function(item) {
  console.log(this); // 三次obj对象
}, obj);

// 3.dom事件
var box = document.querySelector(".box");
box.onclick = function() {
  console.log(this); // box对象:这是因为在发生点击时，执行传入的回调函数被调用时，会将box对象绑定到该函数中
}
~~~

#### 5. new绑定

+ 使用new关键字来调用函数时：会执行如下操作：

  1. 创建一个全新的对象；
  2. 这个新对象会被执行Prototype连接；
  3. 这个新对象会绑定到函数调用的this上（this的绑定在这个步骤完成）
  4. 如果函数没有返回其他对象，表达式会返回这个新对象

  ~~~js
  // 创建Person
  function Person(name) {
    console.log(this); 
    this.name = name; 
  }
  
  var p = new Person("why");
  console.log(p);// Person {name: "why"}
  ~~~

#### 6. 规则的优先级

+ **new绑定 > 显示绑定（bind）> 隐式绑定 > 默认绑定**

#### this规则之外的绑定

##### 1. 忽略显示绑定

+ 如果在显示绑定中，我们传入一个null或者undefined，那么这个显示绑定会被忽略，使用默认规则：

~~~js
function foo() {
  console.log(this);
}

var obj = {
  name: "why"
}

foo.call(obj); // obj对象
foo.call(null); // window
foo.call(undefined); // window

var bar = foo.bind(null);
bar(); // window
~~~

##### 2. 间接函数引用

+ 创建一个函数的**间接引用**，这种情况使用默认的绑定规则。

~~~js
var num1 = 100;
var num2 = 0;
var result = (num2 = num1);
console.log(result); // 100

function foo() {
  console.log(this);
}
var obj1 = {
  name: "obj1",
  foo: foo
}; 
var obj2 = {
  name: "obj2"
}
obj1.foo(); // obj1对象
(obj2.foo = obj1.foo)();  // window

~~~

##### 3. ES6箭头函数

+ 箭头函数没有自己的this对象，函数内部的this指向外层作用域的this


### 3.函数预编译过程
#### 四部曲
1. 创建一个AO（activation object:执行期上下文）对象；
2. 找形参和**变量声明**，将形参和变量声明作为AO对象的属性，值为undefined；
3. 将实参，形参相统一；
4. 找函数体内部的**函数声明**；

```js
function fn(a) {
    console.log(a) // f a(){}
    var a = 123
    console.log(a) // 123
    function a(){}
    console.log(a) // 123
    var b = function(){}
    console.log(b)  //f (){}
    function b(){}
 }
    
 fn(1)
```
#### 全局预编译过程

 + 少掉四部曲中的第三步，AO变成GO
 ```js
 console.log(test) //1
 function test(test){
    console.log(test)//2
    var test = 234
    console.log(test) // 3
    function test(){}
  }
  test(1)
  var test = 123
  
 // 1.  ƒ test(test){
          //console.log(test)
           //var test = 234
           //console.log(test)
           //function test(){}
        // }
// 2.  ƒ test(){}
// 3.  234
 ```

###  4. JavaScript继承

+ **原型链继承**
​	原型链继承存在的问题：

​		问题1：原型中包含的引用类型属性将被所有实例共享；

​		问题2：子类在实例化的时候不能给父类构造函数传参；

~~~js
function Animal() {
    this.colors = ['black', 'white']
}
Animal.prototype.getColor = function() {
    return this.colors
}
function Dog() {}
Dog.prototype =  new Animal()

let dog1 = new Dog()
dog1.colors.push('brown')
let dog2 = new Dog()
console.log(dog2.colors)  // ['black', 'white', 'brown']
~~~

+ **借用构造函数实现继承**

  借用构造函数实现继承解决了原型链继承的 2 个问题：引用类型共享问题以及传参问题。但是由于方法必须定义在构造函数中，所以会导致每次创建子类实例都会创建一遍方法

~~~js
function Animal(name) {
    this.name = name
    this.getName = function() {
        return this.name
    }
}
function Dog(name) {
    Animal.call(this, name)
}
Dog.prototype =  new Animal()
~~~

+ **组合继承**
  组合继承结合了原型链和盗用构造函数，将两者的优点集中了起来。基本的思路是==使用原型链继承原型上的属性和方法，而通过盗用构造函数继承实例属性==。这样既可以把方法定义在原型上以实现重用，又可以让每个实例都有自己的属性。
~~~js
function Animal(name) {
    this.name = name
    this.colors = ['black', 'white']
}
Animal.prototype.getName = function() {
    return this.name
}
function Dog(name, age) {
    Animal.call(this, name)
    this.age = age
}
Dog.prototype =  new Animal()
Dog.prototype.constructor = Dog

let dog1 = new Dog('奶昔', 2)
dog1.colors.push('brown')
let dog2 = new Dog('哈赤', 1)
console.log(dog2) 
// { name: "哈赤", colors: ["black", "white"], age: 1 }
~~~

+ **圣杯模式继承**
  组合继承已经相对完善了，但还是存在问题，它的问题就是调用了 2 次父类构造函数，第一次是在 new Animal()，第二次是在 Animal.call() 这里。
  所以解决方案就是不直接调用父类构造函数给子类原型赋值，而是通过创建空函数 F 获取父类原型的副本。
~~~js
// 1.第一种
function inherit(target, origin) {
    function F(){}
    F.prototype = origin.prototype
    target.prototype = new F()
    target.prototype.consctruct = target
    target.prototype.Uber = origin.prototype
}
// 重写第一种写法
var inherit = (function(){
    var F = function(){}
    return function(target, origin){
        F.prototype = origin.prototype
        target.prototype = new F()
        target.prototype.consctruct = target
        target.prototype.Uber = origin.prototype
    }
}())

~~~

+ **class继承**
~~~js
class Animal {
    constructor(name) {
        this.name = name
    } 
    getName() {
        return this.name
    }
}
class Dog extends Animal {
    constructor(name, age) {
        super(name)
        this.age = age
    }
}
~~~

### 5. 闭包

一个函数和对其周围状态（**lexical environment，词法环境**）的引用捆绑在一起（或者说函数被引用包围），这样的组合就是**闭包**（**closure**）。也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。[详细描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)

~~~js
function init() {
    var name = "Mozilla"; // name 是一个被 init 创建的局部变量
    function displayName() { // displayName() 是内部函数，一个闭包
        console.log(name); // 使用了父函数中声明的变量
    }
    displayName();
}
init();//Mozilla


//闭包的例子
function makeFunc() {
    var name = "Mozilla";
    function displayName() {
        console.log(name);
    }
    return displayName;
}

var myFunc = makeFunc();
myFunc();//Mozilla
~~~

上面两个函数执行效果一样的原因在于，JavaScript中的函数会形成了闭包。 *闭包*是由函数以及声明该函数的`词法环境`组合而成的。该环境包含了这个闭包创建时作用域内的任何局部变量。在本例子中，`myFunc` 是执行 `makeFunc` 时创建的 `displayName` 函数实例的引用。`displayName` 的实例维持了一个对它的词法环境（变量 `name` 存在于其中）的引用。因此，当 `myFunc` 被调用时，变量 `name` 仍然可用，其值 `Mozilla` 就被传递到`console.log`中。

+ 作用：
  + 实现共有变量
  + 可以做缓存
  + 可以实现封装，属性私有化
  + 模块化开发，防止污染全局变量
+ 危害：闭包会导致作用域链不释放，造成内存泄漏
+ 性能考量
  + 如果不是某些特定任务需要使用闭包，在其它函数中创建函数是不明智的，因为闭包在处理速度和内存消耗方面对脚本性能具有负面影响。
  + 例如，在创建新的对象或者类时，方法通常应该关联于对象的原型，而不是定义到对象的构造器中。原因是这将导致每次构造器被调用时，方法都会被重新赋值一次（也就是说，对于每个对象的创建，方法都会被重新赋值）。

### 6. 模块化

#### 1. CommonJs规范

- Node是CommonJS在服务器端一个具有代表性的实现；
- Browserify是CommonJS在浏览器中的一种实现；
- webpack打包工具具备对CommonJS的支持和转换（后面我会讲到）；

所以，Node中对CommonJS进行了支持和实现，让我们在开发node的过程中可以方便的进行模块化开发：

- 在Node中每一个js文件都是**一个单独的模块**；
- 这个模块中包括CommonJS规范的核心变量：exports、module.exports、require；
- 我们可以使用这些变量来方便的进行模块化开发；

前面我们提到过模块化的核心是导出和导入，Node中对其进行了实现：

- exports和module.exports可以负责对模块中的内容进行导出；
- require函数可以帮助我们导入其他模块（自定义模块、系统模块、第三方库模块）中的内容；

##### 1.1 exports导出

**强调：exports是一个对象，我们可以在这个对象中添加很多个属性，添加的属性会导出**

bar.js中导出内容：

```js
exports.name = name;
exports.age = age;
exports.sayHello = sayHello;
```

main.js中导入内容：

```js
const bar = require('./bar'); //意味着bar等于bar.js中导出的exports
```

本质：bar对象是exports对象的浅拷贝；浅拷贝本质一种引用的赋值而已；

![exports.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28e605723e2d42ffa60b8c24d4c9b667~tplv-k3u1fbpfcp-watermark.image?)

![exports2.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/372824a025864736b75f7c465b0328f5~tplv-k3u1fbpfcp-watermark.image?)

##### 1.2 module.exports

但是Node中我们经常导出东西的时候，又是通过module.exports导出的：

- module.exports和exports有什么关系或者区别呢？

我们追根溯源，通过维基百科中对CommonJS规范的解析：

- CommonJS中是没有module.exports的概念的；
- 但是为了实现模块的导出，Node中使用的是Module的类，每一个模块都是Module的一个实例，也就是module；
- 所以在Node中真正用于导出的其实根本不是exports，而是module.exports；
- 因为module才是导出的真正实现者；

但是，为什么exports也可以导出呢？

- ==这是因为module对象的exports属性是exports对象的一个引用==；
- 也就是说 `module.exports = exports = main中的bar`；

注意：真正导出的模块内容的核心其实是module.exports，只是为了实现CommonJS的规范，刚好module.exports对exports对象有一个引用而已；

##### 1.3 require细节

我们现在已经知道，require是一个函数，可以帮助我们引入一个文件（模块）中导入的对象。

那么，require的查找规则是怎么样的呢？

- https://nodejs.org/dist/latest-v14.x/docs/api/modules.html#modules_all_together

**这里我总结比较常见的查找规则：**

导入格式如下：require(X)

- 情况一：X是一个核心模块，比如path、http

- - 直接返回核心模块，并且停止查找

- 情况二：X是以 `./` 或 `../` 或 `/`（根目录）开头的

- - 查找目录下面的index文件
  - 1> 查找X/index.js文件
  - 2> 查找X/index.json文件
  - 3> 查找X/index.node文件
  - 1.如果有后缀名，按照后缀名的格式查找对应的文件
  - 2.如果没有后缀名，会按照如下顺序：
  - 1> 直接查找文件X
  - 2> 查找X.js文件
  - 3> 查找X.json文件
  - 4> 查找X.node文件
  - 第一步：将X当做一个文件在对应的目录下查找；
  - 第二步：没有找到对应的文件，将X作为一个目录
  - 如果没有找到，那么报错：`not found`

- 情况三：直接是一个X（没有路径），并且X不是一个核心模块

- - 比如 `/Users/coderwhy/Desktop/Node/TestCode/04_learn_node/05_javascript-module/02_commonjs/main.js`中编写 `require('why')`
  - ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeccY2Bics4UStoH4FzQrWyibQ4JPiaQayWNXn7ph55CpDPSNmeVXb0Jxyg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)查找顺序
  - 如果上面的路径中都没有找到，那么报错：`not found`

##### 1.4 模块的加载顺序

1. **模块在被第一次引入时，模块中的js代码会被运行一次**

2. **模块被多次引入时，会缓存，最终只加载（运行）一次**

    为什么只会加载运行一次呢

   - 这是因为每个模块对象module都有一个属性：loaded。
   - 为false表示还没有加载，为true表示已经加载

3. **循环引入：Node使用深度优先算法**

   main -> aaa -> ccc -> ddd -> eee ->bbb 

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeicyjvLkriaru3ojNlKRmvS2JxaSFMSc2FBKKwYarUJo4yg4gswJiaR3pA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### 2. ES Module

[详细描述](https://es6.ruanyifeng.com/#docs/module)

#### 3. ES Module 和CommonJs的区别

**CommonJS模块加载js文件的过程是运行时加载的，并且是同步的：**

- 运行时加载意味着是js引擎在执行js代码的过程中加载 模块；
- 同步的就意味着一个文件没有加载结束之前，后面的代码都不会执行；

```
console.log("main代码执行");

const flag = true;
if (flag) {
  // 同步加载foo文件，并且执行一次内部的代码
  const foo = require('./foo');
  console.log("if语句继续执行");
}
```

**CommonJS通过module.exports导出的是一个对象：**

- 导出的是一个对象意味着可以将这个对象的引用在其他模块中赋值给其他变量；
- 但是最终他们指向的都是同一个对象，那么一个变量修改了对象的属性，所有的地方都会被修改；

**ES Module加载js文件的过程是编译（解析）时加载的，并且是异步的：**

- 编译时（解析）时加载，意味着import不能和运行时相关的内容放在一起使用：

- - 比如from后面的路径需要动态获取；
  - 比如不能将import放到if等语句的代码块中；
  - 所以我们有时候也称ES Module是静态解析的，而不是动态或者运行时解析的；

- 异步的意味着：JS引擎在遇到`import`时会去获取这个js文件，但是这个获取的过程是异步的，并不会阻塞主线程继续执行；

- - 也就是说设置了 `type=module` 的代码，相当于在script标签上也加上了 `defer` 属性；
  - 如果我们后面有普通的script标签以及对应的代码，那么ES Module对应的js文件和代码不会阻塞它们的执行；

```html
<script src="main.js" type="module"></script>
<!-- 这个js文件的代码不会被阻塞执行 -->
<script src="index.js"></script>
```

**ES Module通过export导出的是变量本身的引用：**

- export在导出一个变量时，js引擎会解析这个语法，并且创建**模块环境记录**（module environment record）；
- **模块环境记录**会和变量进行 `绑定`（binding），并且这个绑定是实时的；
- 而在导入的地方，我们是可以实时的获取到绑定的最新值的；

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojeezXibRWtBBwGrUqbiceysIwmIBaWxjhEc1QAOOr0MSjCbOJTkKhP5dfw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)export和import绑定的过程

**所以我们下面的代码是成立的：**

bar.js文件中修改

```js
let name = 'coderwhy';

setTimeout(() => {
  name = "湖人总冠军";
}, 1000);

setTimeout(() => {
  console.log(name);
}, 2000);

export {
  name
}
```

main.js文件中获取

```js
import { name } from './modules/bar.js';

console.log(name);

// bar中修改, main中验证
setTimeout(() => {
  console.log(name);
}, 2000);
```

但是，下面的代码是不成立的：main.js中修改

```js
import { name } from './modules/bar.js';

console.log(name);

// main中修改, bar中验证
setTimeout(() => {
  name = 'kobe';
}, 1000);
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/O8xWXzAqXuseDpSr345APaEno34kibojelXdJWbQtsL9dkFfVickXCPiclf8icow980G4sPwpITsbzuEYaZS1VPIoA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)导入的变量不可以被修改

思考：如果bar.js中导出的是一个对象，那么main.js中是否可以修改对象中的属性呢？

- 答案是可以的，因为他们指向同一块内存空间；（自己编写代码验证，这里不再给出）

### 7. Promise

```js
const PENDING = 'pending'; //等待
const FULFILLED = 'fulfilled'; //失败
const REJECTED = 'rejected'; //成功

class MyPromise {
  constructor(executor) {
    try {
      executor(this.resolve, this.reject);
    } catch (error) {
      //捕获执行器中的错误
      this.reject(error);
    }
  }
  status = PENDING; //默认等待
  value = undefined; //成功之后的值
  reason = undefined; //失败之后的原因
  successCallback = []; //成功回调
  failCallback = []; //失败回调

  //定义成箭头函数为了保证this指向Promise本身
  resolve = (value) => {
    if (this.status !== PENDING) return; //如果状态不是pending，阻止程序向下执行
    this.status = FULFILLED; //将状态改为成功
    this.value = value; //保存成功之后的值

    // 判断成功回调是否存在，存在的话就调用(处理异步的情况)
    // this.successCallback && this.successCallback(this.value);
    while (this.successCallback.length) this.successCallback.shift()();
  };

  reject = (reason) => {
    if (this.status !== PENDING) return; //如果状态不是pending，阻止程序向下执行
    this.status = REJECTED; //将状态改为失败
    this.reason = reason; //保存失败的原因

    // 判断失败回调是否存在，存在的话就调用(处理异步的情况)
    // this.failCallback && this.failCallback(this.reason);
    while (this.failCallback.length) this.failCallback.shift()();
  };

  then(successCallback, failCallback) {
    //处理参数不传的情况(参数可选的情况)
    successCallback = successCallback ? successCallback : (value) => value;
    failCallback = failCallback
      ? failCallback
      : (reason) => {
          throw reason;
        };

    let promise2 = new MyPromise((resolve, reject) => {
      // 判断状态
      if (this.status === FULFILLED) {
        setTimeout(() => {
          //让这块代码变成异步的，然后才能拿到promise2
          try {
            let x = successCallback(this.value);
            resolvePromise(promise2, x, resolve, reject); //将then的返回值传递给下一个then
          } catch (error) {
            //捕获then中的错误然后传递给下一个rejecte
            reject(error);
          }
        }, 0);
      } else if (this.status === REJECTED) {
        setTimeout(() => {
          //让这块代码变成异步的，然后才能拿到promise2
          try {
            let x = failCallback(this.reason);
            resolvePromise(promise2, x, resolve, reject); //将then的返回值传递给下一个then
          } catch (error) {
            //捕获then中的错误然后传递给下一个rejecte
            reject(error);
          }
        }, 0);
      } else {
        //等待(处理异步的情况)
        // 将成功和失败的回调保存起来
        this.successCallback.push(() => {
          setTimeout(() => {
            //让这块代码变成异步的，然后才能拿到promise2
            try {
              let x = successCallback(this.value);
              resolvePromise(promise2, x, resolve, reject); //将then的返回值传递给下一个then
            } catch (error) {
              //捕获then中的错误然后传递给下一个rejecte
              reject(error);
            }
          }, 0);
        });
        this.failCallback.push(() => {
          setTimeout(() => {
            //让这块代码变成异步的，然后才能拿到promise2
            try {
              let x = failCallback(this.reason);
              resolvePromise(promise2, x, resolve, reject); //将then的返回值传递给下一个then
            } catch (error) {
              //捕获then中的错误然后传递给下一个rejecte
              reject(error);
            }
          }, 0);
        });
      }
    });
    return promise2;
  }

  finally(callback) {
    return this.then(
      (value) => {
        return MyPromise.resolve(callback()).then(() => value);
      },
      (reason) => {
        return MyPromise.resolve(callback()).then(() => {
          throw reason;
        });
      }
    );
  }

  catch(failCallback) {
    return this.then(undefined, failCallback);
  }

  static all(array) {
    let result = [];
    let index = 0;
    return new MyPromise((resolve, reject) => {
      function addData(key, value) {
        result[key] = value;
        index++;
        if (index === array.length) {
          //保证array中的每一项都执行完
          resolve(result);
        }
      }
      for (let i = 0; i < array.length; i++) {
        let current = array[i];
        if (current instanceof MyPromise) {
          current.then(
            (value) => addData(i, value),
            (reason) => reject(reason)
          );
        } else {
          addData(i, array[i]);
        }
      }
    });
  }

  static resolve(value) {
    if (value instanceof MyPromise) return value;
    return new MyPromise((resolve) => resolve(value));
  }

  static reject(reason) {
    return new MyPromise((resolve, reject) => reject(reason));
  }

  static race(array) {
    return new MyPromise((resolve, reject) => {
      array.forEach((p) => {
        MyPromise.resolve(p).then(
          (value) => {
            resolve(value);
          },
          (reason) => {
            reject(reason);
          }
        );
      });
    });
  }

  static allSettled(array) {
    let result = [];
    return new MyPromise((resolve, reject) => {
      array.forEach((p, i) => {
        MyPromise.resolve(p).then(
          (value) => {
            result.push({
              status: 'fulfilled',
              value: value,
            });
            if (result.length === array.length) {
              resolve(result);
            }
          },
          (reason) => {
            result.push({
              status: 'rejected',
              reason: reason,
            });
            if (result.length === array.length) {
              resolve(result);
            }
          }
        );
      });
    });
  }

  static any(array) {
    let index = 0;
    return new MyPromise((resolve, reject) => {
      if (array.length === 0) return;
      array.forEach((p, i) => {
        MyPromise.resolve(p).then(
          (value) => {
            resolve(value);
          },
          (reason) => {
            index++;
            if (index === array.length) {
              reject(new AggregateError('All promises were rejected'));
            }
          }
        );
      });
    });
  }
}

function resolvePromise(promise2, x, resolve, reject) {
  if (promise2 === x) return reject(new TypeError('Chaining cycle detected for promise #<Promise>'));
  /*
    1.判断x的值是普通值还是promise对象
    2.如果是普通值，直接调用resolve
    3.如果是promise对象，查看promise对象返回的结果
    4.再根据promise对象返回的结果，决定是调用resolve，还是reject
    */
  if (x instanceof MyPromise) {
    //promise对象
    //x.then(value => resolve(value), reason => reject(reason))
    x.then(resolve, reject);
  } else {
    //普通值
    resolve(x);
  }
}
```

### 8. js基本数据类型有哪些及它们的区别

JavaScript共有八种数据类型，分别是 Undefined、Null、Boolean、Number、String、Object、Symbol、BigInt。

其中 Symbol 和 BigInt 是ES6 中新增的数据类型：

- Symbol 代表创建后独一无二且不可变的数据类型，它主要是为了解决可能出现的全局变量冲突的问题。
- BigInt 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这个数已经超出了 Number 能够表示的安全整数范围。

这些数据可以分为原始数据类型和引用数据类型：

- 栈：原始数据类型（Undefined、Null、Boolean、Number、String、Symbol、BigInt）
- 堆：引用数据类型（对象、数组和函数）

两种类型的区别在于**存储位置的不同：**

- 原始数据类型直接存储在栈（stack）中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；
- 引用数据类型存储在堆（heap）中的对象，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。

堆和栈的概念存在于数据结构和操作系统内存中，在数据结构中：

- 在数据结构中，栈中数据的存取方式为先进后出。
- 堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。

在操作系统中，内存被分为栈区和堆区：

- 栈区内存由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
- 堆区内存一般由开发着分配释放，若开发者不释放，程序结束时可能由垃圾回收机制回收。

### 9.原型和原型链

#### 1. 对原型、原型链的理解

在JavaScript中是使用构造函数来新建一个对象的，每一个构造函数的内部都有一个 prototype 属性，它的属性值是一个对象，这个对象包含了可以由该构造函数的所有实例共享的属性和方法。当使用构造函数新建一个对象后，在这个对象的内部将包含一个指针，这个指针指向构造函数的 prototype 属性对应的值，在 ES5 中这个指针被称为对象的原型。一般来说不应该能够获取到这个值的，但是现在浏览器中都实现了 **proto** 属性来访问这个属性，但是最好不要使用这个属性，因为它不是规范中规定的。ES5 中新增了一个 Object.getPrototypeOf() 方法，可以通过这个方法来获取对象的原型。

当访问一个对象的属性时，如果这个对象内部不存在这个属性，那么它就会去它的原型对象里找这个属性，这个原型对象又会有自己的原型，于是就这样一直找下去，也就是原型链的概念。原型链的尽头一般来说都是 Object.prototype 所以这就是新建的对象为什么能够使用 toString() 等方法的原因。

**特点：** JavaScript 对象是通过引用来传递的，创建的每个新对象实体中并没有一份属于自己的原型副本。当修改原型时，与之相关的对象也会继承这一改变。

#### 2. 原型修改、重写

```javascript
function Person(name) {
    this.name = name
}
// 修改原型
Person.prototype.getName = function() {}
var p = new Person('hello')
console.log(p.__proto__ === Person.prototype) // true
console.log(p.__proto__ === p.constructor.prototype) // true
// 重写原型
Person.prototype = {
    getName: function() {}
}
var p = new Person('hello')
console.log(p.__proto__ === Person.prototype)        // true
console.log(p.__proto__ === p.constructor.prototype) // false
```

可以看到修改原型的时候p的构造函数不是指向Person了，因为直接给Person的原型对象直接用对象赋值时，它的构造函数指向的了根构造函数Object，所以这时候`p.constructor === Object` ，而不是`p.constructor === Person`。要想成立，就要用constructor指回来：

```js
Person.prototype = {
    getName: function() {}
}
var p = new Person('hello')
p.constructor = Person
console.log(p.__proto__ === Person.prototype)        // true
console.log(p.__proto__ === p.constructor.prototype) // true
```

#### 3. 原型链指向

```javascript
p.__proto__  // Person.prototype
Person.prototype.__proto__  // Object.prototype
p.__proto__.__proto__ //Object.prototype
p.__proto__.constructor.prototype.__proto__ // Object.prototype
Person.prototype.constructor.prototype.__proto__ // Object.prototype
p1.__proto__.constructor // Person
Person.prototype.constructor  // Person
```

#### 4. 原型链的终点是什么？如何打印出原型链的终点？

由于`Object`是构造函数，原型链终点是`Object.prototype.__proto__`，而`Object.prototype.__proto__=== null // true`，所以，原型链的终点是`null`。原型链上的所有原型都是对象，所有的对象最终都是由`Object`构造的，而`Object.prototype`的下一级是`Object.prototype.__proto__`。

#### 5. 如何获得对象非原型链上的属性？

使用后`hasOwnProperty()`方法来判断属性是否属于原型链的属性：

```js
function iterate(obj){
   var res=[];
   for(var key in obj){
        if(obj.hasOwnProperty(key))
           res.push(key+': '+obj[key]);
   }
   return res;
} 
```

