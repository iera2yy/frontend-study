# frontend_study v3.3
#  JavaScript
## 语法

- 数据类型

  - 基本数据类型：string、boolean、number、null、undefined、symbol、bigint
  - 引用数据类型：object

- **this**，4种绑定规则分别是：默认绑定、隐式绑定、显示绑定、new 绑定。优先级从低到高。

  - 默认绑定，即没有其他绑定规则存在时的默认规则。

    ```js
    function func() {
        const a = 2;
        console.log(this.a);
    }
    // ---------------------
    const a = 1;
    func();	// undefined
    // ---------------------
    var a = 1;
    func();	// 理论上应该是1，但实际是undefined
    // ---------------------
    a = 1;
    func();	// 1
    ```

  - 隐式绑定，即调用位置上存在上下文对象。

    ```js
    function foo() { 
        console.log(this.a);
    }
    var a = 2;
    var obj = { 
        a: 3,
        foo: foo 
    };
    // 隐式绑定
    obj.foo(); // 3
    
    // 隐式丢失(函数别名)
    // 通过bar找到foo函数，进行调用。整个调用过程并没有obj的参数，所以是默认绑定，全局属性a。
    var bar = obj.foo;
    bar(); // 2
    
    // 隐式丢失(回调函数)
    // 虽然参传是obj.foo，因为是引用关系，所以传参实际上传的就是foo对象本身的引用。
    setTimeout(obj.foo, 100); // 2
    ```

  - 显式绑定，即通过改变对象的prototype关联对象。

    ```js
    function foo() { 
        console.log(this.a);
    }
    var a = 2;
    var obj1 = { a: 3 };
    var obj2 = { a: 4 };
    foo.call(obj1);  // 3
    foo.apply(obj2); // 4
    // 注：绑定null或undefined，则显示绑定不生效。
    ```

  - new绑定

    ```js
    function foo(a) { 
        this.a = a;
    }
    var a = 2;
    var bar1 = new foo(3);
    console.log(bar1.a); // 3
    var bar2 = new foo(4);
    console.log(bar2.a); // 4
    ```

- **new**

  - 创建一个新对象
  - 将构造函数的作用域赋值给新对象（也就是构造函数中的this指向新对象）
  - 执行构造函数中的代码（为这个新对象添加属性）
  - 返回新对象

- **call**、**apply**和**bind**

  - `call()`和`apply()`
    - 这两个方法都是函数对象的方法，需要通过函数对象来调用
    - 对函数调用`call()`和`apply()`都会调用函数执行
    - 在调用`call()`和`apply(`)可以将一个对象指定为第一个参数，此时这个对象将会成为函数执行时的this
    - `call()`方法可以将实参在对象之后一次传入，`fun.call(obj, 2, 3)`
    - `apply()`方法需要将实参封装到一个数组中统一传递，`fun.apply(obj, [2, 3])`

  ```javascript
  Function.prototype.call = function(context) {
      context.fn = this;
      context.fn();
      delete context.fn;
  }
  ```

  - bind()
    - bind方法和call类似，第一个参数是this的指向，后面传入一个参数列表(但是这个参数列表可以分多次传入，call则必须一次性传入所有参数)
    - 改变this的指向不会立即执行，而是返回一个永久改变this指向的函数，返回原函数的拷贝
    - call和apply临时改变一次this指向并立即执行

  ```javascript
  Function.prototype.bind = function(context) {
      var _this = this;
      var args = Array.prototype.slice.call(arguments, 1);
  
      return function() {
          return _this.apply(context, args);
      }
  }
  ```

- **let**和**var**

  - let定义块级作用域变量，var定义函数级作用域变量
  - 配合for循环独特应用`setTimeout`
  - 全局声明时，var会成为`window`对象的属性，let则不会
  - let没有变量提升与暂时性死区
  - let变量不能重复声明

- 变量提升：**函数提升优先级比变量提升要高，且不会被变量声明覆盖，但是会被变量赋值覆盖。**

- **instanceof**和**typeof**

  - typeof用来判断数据类型，返回值为8个字符串，分别为
    - 正常情况：undefined、boolean、string、number、symbol、bigint
    - 特殊情况：function、object(包括null)

- instanceof用来判断对象实例，返回值为布尔型

  - `A instanceof B`查看对象B的prototype指向的对象是否在对象A的`[[prototype]](__proto__)`链上，当对象B的prototype为null时将报错(类似于空指针异常)


  ```javascript
function func() {}

function instanceOf(f, func) {
    let proto = f.__proto__;
    while (proto !== null) {
        if (proto === func.prototype) {
            return true;
        }
        proto = proto.__proto__;
    }
    return false;
}

console.log(instanceOf(new func(), func)); // true
  ```

  - reduce

```javascript
// arr.reduce(callback, [initialValue])
// previousValue 上一次调用回调返回的值，或者是提供的初始值
// currentValue 数组中当前被处理的元素
// index 当前元素在数组中的索引
// array 调用reduce的数组
var arr = [1, 2, 3, 4];
var sum = arr.reduce(function(prev, cur, idx, arr) {
    console.log(prev, cur, idx);
    return prev + cur;    
});
console.log(arr, sum);
// 1 2 1
// 3 3 2
// 6 4 3
// [ 1, 2, 3, 4 ] 10

var arr = [1, 2, 3, 4];
var sum = arr.reduce(function(prev, cur, idx, arr) {
    console.log(prev, cur, idx);
    return prev + cur;    
}, 0);
console.log(arr, sum);
// 0 1 0
// 1 2 1
// 3 3 2
// 6 4 3
// [ 1, 2, 3, 4 ] 10

// 不指定初始值时，idx从1开始，第一次prev的值是数组的第一个值；设置初始值，则prev的值为初始值，idx从0开始。
```

- ==和===

  - == equality 等同，=== identity 恒等。
  - ==， 两边值类型不同的时候，要先进行类型转换，再比较。 
  - ==，不做类型转换，类型不同的一定不等。 
  - 区别： 
    - 如果两个值类型相同，进行 === 比较： 
      - 如果类型不同，就[不相等] 
      - 如果两个都是数值，并且是同一个值，那么[相等]
      - 如果两个都是字符串，每个位置的字符都一样，那么[相等]；否则[不相等]
      - 如果两个值都是true，或者都是false，那么[相等]
      - 如果两个值都引用同一个对象或函数，那么[相等]；否则[不相等]
      - 如果两个值都是null，或者都是undefined，那么[相等]
    - 如果两个值类型不同，他们可能相等，进行类型转换再==比较： 
      - 如果一个是null、一个是undefined，那么[相等]。 
      - 如果一个是字符串，一个是数值，把字符串转换成数值再进行比较。 
      - 如果任一值是 true，把它转换成 1 再比较；如果任一值是 false，把它转换成 0 再比较。 
      - 任何其他组合，都[不相等]。

- undefined和void 0

  > The void operator evaluates the given expression and then returns undefined. 

  - undefined 在 ES5 中已经是全局对象的一个只读（read-only）属性了，它不能被重写。但是在局部作用域中，还是可以被重写的。

  ```js
  (function() {
    var undefined = 10;
    // 10 -- chrome
    alert(undefined);
  })();
   
  (function() {
    undefined = 10;
    // undefined -- chrome
    alert(undefined);
  })();
  ```

  - void 运算符能对给定的表达式进行求值，然后返回undefined。也就是说，void 后面你随便跟上一个表达式，返回的都是 undefined，如 void (2), void ('hello')。而void 0是表达式中最短的。

- ?=和?:和?!

```javascript
// 前瞻：
exp1(?=exp2) 查找exp2前面的exp1
// 后顾：
(?<=exp2)exp1 查找exp2后面的exp1
// 负前瞻：
exp1(?!exp2) 查找后面不是exp2的exp1
// 负后顾：
(?<!exp2)exp1 查找前面不是exp2的exp1

()表示捕获分组，()会把每个分组里的匹配的值保存起来，使用$n(n是一个数字，表示第n个捕获组的内容)
(?:)表示非捕获分组，和捕获分组唯一的区别在于，非捕获分组匹配的值不会保存起来

var num = 212354645623;
console.log(num.toString().replace(/(\d)(?=(\d{3})+$)/g, ($1) => $1 + ',')); // 212,354,645,623
```

- 箭头函数

  - 箭头函数没有prototype(原型)，所以箭头函数本身没有this，也就不能用call()、apply()、bind()这些方法去改变this的指向。

  ```javascript
  let a = () => {}; 
  console.log(a.prototype); // undefined
  ```

  - 箭头函数的this指向在定义的时候继承自外层第一个普通函数的this。如果箭头函数外层没有普通函数，它的this会指向window(全局对象)。

  ```javascript
  function test() {
    this.type = 'cat'
    this.obj = {
      name: 'tom',
      getType: () => {
        return this.name + ',' + this.type
      },
      getName: function () {
        return this.name + ',' + this.type
      }
    }
  }
  let t = new test()
  console.log(t.obj.getType())
  console.log(t.obj.getName())
  // "undefined, cat"
  // "tom, undefined"
  ```

  - 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

  ```javascript
  function foo() {
    setTimeout(() => {
      console.log('id:', this.id);
    }, 100);
  }
  
  var id = 21;
  
  foo.call({ id: 42 });
  // id: 42
  ```

  - 箭头函数没有constructor，使用new调用箭头函数都会报错。

  ```javascript
  let a = () => {};
  let b = new a(); // a is not a constructor
  ```

  - 箭头函数的arguments

    - 箭头函数的this指向全局对象，会报arguments未声明的错误。

    - 箭头函数的this如果指向普通函数,它的argumens继承于该普通函数。

    ```javascript
    let foo = () => {
      console.log(arguments);
    };
    foo(1, 2, 3, 4); // Uncaught ReferenceError: arguments is not defined
    
    function foo() {
      setTimeout(() => {
        console.log('args:', arguments);
      }, 100);
    }
    
    foo(2, 4, 6, 8)
    // args: [2, 4, 6, 8]
    
    //  可以使用rest参数（扩展运算符...）来获取函数的不定数量参数
    let foo = (first, ...rest) => {
      console.log(first,  rest) // 1 [2, 3, 4]
    };
    foo(1, 2, 3, 4)
    ```

  - 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。

## 严格模式

- 严格模式下无法隐式创建全局变量
- 严格模式会使引起静默失败（silently fail，注：不报错也没有任何效果）的赋值操做抛出异常
- 在严格模式下，试图删除不可删除的属性时，会抛出异常（之前这种操作不会产生任何效果）
- 在严格模式下，不允许重名属性
- 严格模式要求函数参数名唯一
- 禁止八进制数字语法
- 禁止设置原始类型（primitive）值的属性
- 禁用`with`
- 严格模式下，`eval()`创建变量不能被调用
- 严格模式禁止删除声明变量
- 不能使用`eval` 和 `arguments`字符串
- 严格模式下，函数的 arguments 对象会保存函数被调用时的原始参数。arguments[i] 的值不会随与之相应的参数的值的改变而变化，同名参数的值也不会随与之相应的 arguments[i] 的值的改变而变化。
- 不再支持`arguments.callee`
- 保留部分关键字，这些字符包括`implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`和`yield`。在严格模式下，你不能再用这些名字作为变量名或形参名。
- 禁止`this`指向全局对象，当`this`指向全局对象时，自动转为`undefined`

## 原型

- 原型prototype
  - 对于一个对象parent而言`parent.__proto__ === parent.constructor.prototype`
  - 我们所创建的每一个函数，解析器都会向函数中添加一个熟悉prototype，这个属性对应着一个对象，即原型对象
  - 如果函数作为普通函数调用，prototype没有任何作用
  - 当函数以构造函数的形式调用时。它所创建的对象中都有一个隐含的属性，指向该构造函数的原型对象，可以通过`__proto__`来访问该属性
  - 原型对象就相当于一个公共的区域，所以同一个类的实例都可以访问到这个原型对象，我们可以将对象中共有的部分，统一设置到原型对象中
  - 当我们访问对象的一个属性或方法时，它会先在对象自身中寻找，如果有则直接使用，如果没有则会去原型对象中寻找，如果找到则直接使用
  - 创建对象时，可以将这些对象共有的属性和方法，统一添加到构造函数的原型对象中，这样不用分别为每一个对象添加，也不会影响到全局作用域，就可以使每个对象具有这些属性和方法了
  - 使用in检查对象中是否含有某个属性时，如果对象中没有但是原型中有，也会返回true；使用hasOwnProperty()来检查对象自身中是否含有该属性
  - 原型对象也是对象，所以它也有原型。当我们使用一个对象的属性或方法时，会先在自身中寻找，自身中有则直接使用；如果没有则取原型对象中寻找，如果原型对象中有，则使用；如果没有则去原型的原型中寻找, 直到找到Object对象的原型，Object对象的原型没有原型，如果Object对象中依然没有找到，则返回null

```javascript
function func() {}
let f = new func();
console.log(f.__proto__ === func.prototype); // true
```

- 链式调用

```javascript
function promise() {
  function myPromise() {}
  
  myPromise.then = function() {
    console.log('then');
    return this;
  }
  
  myPromise.where = function() {
    console.log('where');
    return this;
  }
  return myPromise;
}

promise().then().where().then().then();
```

## 继承

> ES5的继承实质是先创造子类的实例对象`this`，再将父类的方法添加到`this`上(`Parent.apply(this)` )。ES6继承机制的实质是先创造父类的实例对象this(所以必须先调用`super`方法)，再用子类的构造函数修改`this`。

- 原型链继承

  - 缺点：引用类型的属性被所有实例共享
  - 缺点：在创建Child的实例，不能向Parent传参

  ```javascript
  function Parent () {
      this.names = ['kevin', 'daisy'];
  }
  
  function Child () {}
  Child.prototype = new Parent();
  
  var child1 = new Child();
  child1.names.push('yayu');
  console.log(child1.names); // ["kevin", "daisy", "yayu"]
  
  var child2 = new Child();
  console.log(child2.names); // ["kevin", "daisy", "yayu"]
  ```

- 借用构造函数(经典继承)

  - 优点：避免了引用类型的属性被所有实例共享
  - 优点：可以在Child中向Parent传参
  - 缺点：方法都在构造函数中定义，每次创建实例都会创建一遍方法

  ```javascript
  function Parent (name) {
      this.name = name;
  }
  
  function Child (name) {
      Parent.call(this, name);
  }
  
  var child1 = new Child('kevin');
  console.log(child1.name); // kevin
  
  var child2 = new Child('daisy');
  console.log(child2.name); // daisy
  ```

- 组合继承

  - 优点：融合原型链继承和构造函数继承的优点，是JavaScript中最常用的继承模式
  - 缺点：调用两次父构造函数

  ```javascript
  function Parent (name) {
      this.name = name;
      this.colors = ['red', 'blue', 'green'];
  }
  
  Parent.prototype.getName = function () {
      console.log(this.name)
  }
  
  function Child (name, age) {
      Parent.call(this, name);
      this.age = age;
  }
  
  Child.prototype = new Parent();
  Child.prototype.constructor = Child;
  
  var child1 = new Child('kevin', '18');
  
  child1.colors.push('black');
  
  console.log(child1.name); // kevin
  console.log(child1.age); // 18
  console.log(child1.colors); // ["red", "blue", "green", "black"]
  
  var child2 = new Child('daisy', '20');
  
  console.log(child2.name); // daisy
  console.log(child2.age); // 20
  console.log(child2.colors); // ["red", "blue", "green"]
  ```

- 原型式继承

  - 缺点：包含引用类型的属性始终都会共享相应的值，这点跟原型链继承一样

  ```javascript
  // 将传入的对象作为创建的对象的原型，ES5中Object.create的模拟实现
  function createObj (o) {
      function F(){}
      F.prototype = o;
      return new F();
  }
  
  var person = {
      name: 'kevin',
      friends: ['daisy', 'kelly']
  }
  
  var person1 = createObj(person);
  var person2 = createObj(person);
  
  person1.name = 'person1';
  console.log(person2.name); // kevin
  
  person1.friends.push('taylor');
  console.log(person2.friends); // ["daisy", "kelly", "taylor"]
  // 注意：修改person1.name的值，person2.name的值并未发生改变，并不是因为person1和person2有独立的 name 值，而是因为person1.name = 'person1'，给person1添加了 name 值，并非修改了原型上的 name 值。
  ```

- 寄生式继承

  - 缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法；原型式缺点均有。本质是原型式继承基础上再封装。

  ```javascript
  // 创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。
  function createObj (o) {
      var clone = Object.create(o);
      clone.sayName = function () {
          console.log('hi');
      }
      return clone;
  }
  ```

- 寄生组合式继承

  - 优点：这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

  ```javascript
  function Parent(name) {
      this.name = name;
  }
  
  Parent.prototype.getName = function() {
      console.log(this.name);
  }
  
  function Child(name) {
      Parent.call(this, name);
  }
  
  function object(o) {
      function F() {};
      F.prototype = o;
      return new F();
  }
  
  function prototype(child, parent) {
      var f = object(parent.prototype);
      f.constructor = child;
      child.prototype = f;
  }
  
  // 使用方法
  prototype(Child, Parent);
  ```

- class

  - 类的所有属性定义在内部，类的所有方法都定义在类的`prototype`属性上。构造函数的prototype属性在ES6的“类”上继续存在。因此，事实上类的所有方法都定义在类的prototype属性上。

    ```javascript
    class B {}
    const b = new B();
    b.constructor === B.prototype.constructor // true
    
    class Point {
      x = 1;
      toString() {}
    }
    const point = new Point();
    point.hasOwnProperty('x')	// true
    point.__proto__.hasOwnProperty('x') // false
    point.hasOwnProperty('toString')	// false
    point.__proto__.hasOwnProperty('toString')	// true
    ```

  - `Object.assign`方法可以一次向类添加多个方法。

    ```javascript
    class Point {
        constructor() {}
    }
    Object.assign(Point.prototype, {
        toString(){},
        toValue(){},
    });
    ```

  - `prototype`对象的`constructor`属性直接指向“类”本身。

    ```javascript
    Point.prototype.constructor === Point // true
    ```

  - ES6内部定义的所有方法都是不可枚举的。

    ```javascript
    // ES6
    class Point {
        constructor() {}
        toString() {}
    }
    Object.keys(Point.prototype) 	// []
    Object.getOwnPropertyNames(Point.prototype)		// [ 'constructor', 'toString' ]
    
    // ES5
    var Point = function() {}
    Point.prototype.toString = function() {}
    Object.keys(Point.prototype)	// [ 'toString' ]
    Object.getOwnPropertyNames(Point.prototype)		// [ 'constructor', 'toString' ]
    ```

  - class作为构造函数的语法糖，同时有`prototype`和`__proto__`属性，因此同时存在两条继承链。作为一个对象，子类的原型(`__proto__`属性)是父类；作为一个构造函数，子类的原型(`prototype`属性)是父类的实例。

    - 子类的`__proto__`属性表示构造函数的集成，总是指向父类。
    - 子类的`prototype`属性的`__proto__`属性表示方法的继承，总是指向父类的`prototype`属性。

  - 父类只要是一个`prototype`属性的函数，就能被子类继承。以下是三种特殊情况：

    ```javascript
    // 子类继承Object类
    class A extends Object {}
    A.__proto__ === Object	// true
    A.prototype.__proto__ === Object.prototype	// true
    
    // 不存在任何继承
    class A {}
    A.__proto__ === Function.prototype	// true
    A.prototype.__proto__ === Object.prototype	// true
    
    // 子类继承null
    class A extends null {}
    A.__proto__ === Function.prototype	// true
    A.prototype.__proto__ === undefined	// true
    ```

  - 子类实例的`__proto__`属性的`__proto__`属性指向父类实例的`__proto__`属性。也就是说，子类的原型的原型就是父类的原型。

    ```javascript
    class A {}
    class B extends A {}
    const a = new A();
    const b = new B();
    
    b.__proto__ === a	// false
    b.__proto__.__proto__ === a.__proto__	// true
    ```

## 闭包

- 产生闭包的条件
  - 函数嵌套
  - 内部函数引用外部函数的数据(变量/函数)
  - 执行内部函数定义就会产生闭包(不用调用内部函数)
- 闭包到底是什么
  - 理解一：闭包是嵌套的内部函数
  - 理解二：包含被引用变量(函数)的对象
  - 闭包存在于嵌套的内部函数中
- 闭包的作用
  - 将函数作为另一个函数的返回值
  - 将函数作为实参传递给另一个函数调用
- 闭包 = 函数 + 函数能够访问的自由变量
- 高阶函数
  - 高阶函数，满足以下规范中的任何一个，那么A就可以称之为高阶函数
    - 若A函数接受的参数是一个函数
    - 若A函数调用的返回值是一个函数
  - 例子
    - Promise、setTimeout、arr.map

## 柯里化

- 定义：把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

  通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式。

- 参数复用

```javascript
function check(reg, txt) {
    return reg.test(txt);
}

check(/\d+/g, 'test');
check(/[a-z]+/g, 'test');

function curringCheck(reg) {
    return function(txt) {
        return reg.test(txt);
    }
}

var hasNumber = curringCheck(/\d+/g);
var hasLetter = curringCheck(/[a-z]+/g);

console.log(hasNumber('test1'));
console.log(hasNumber('testtest'));
console.log(hasLetter('212121'));
```

- 提前确认

```javascript
var on = function(element, event, handler) {
    if (document.addEventListener) {
        if (element && event && handler) {
            element.addEventListener(event, handler, false);
        }
    } else {
        if (element && event && handler) {
            element.attachEvent('on' + event, handler);
        }
    }
}

var on = (function() {
    if (document.addEventListener) {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.addEventListener(event, handler, false);
            }
        }
    } else {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.attachEvent('on' + event, handler);
            }
        }
    }
})();
```

- 延迟运行

```javascript
Function.prototype.bind = function(context) {
    var _this = this;
    var args = Array.prototype.slice.call(arguments, 1);

    return function() {
        return _this.apply(context, args); 
    }
}
```

- 通用模板

```javascript
// 通用模板
// 支持多参数传递
function progressCurrying(fn, args) {
    var _this = this
    var len = fn.length;
    var args = args || [];

    return function() {
        var _args = Array.prototype.slice.call(arguments);
        Array.prototype.push.apply(args, _args);

        // 如果参数个数小于最初的fn.length，则递归调用，继续收集参数
        if (_args.length < len) {
            return progressCurrying.call(_this, fn, _args);
        }

        // 参数收集完毕，则执行fn
        return fn.apply(this, _args);
    }
}

function curry(fn, currArgs) {
    return function() {
        let args = Array.from(arguments);
        if (currArgs !== undefined) {
            args = args.concat(currArgs);
        }

        return args.length < fn.length ? curry(fn, args) : fn.apply(null, args);
    }
}
function sum(a, b, c) {
  return a + b + c;
}
const fn = curry(sum);
console.log(fn(1, 2, 3));	// 6
console.log(fn(1, 2)(3));	// 6
console.log(fn(1)(2)(3));	// 6


function curryIt(fn, args) {
    var len = fn.length;
    var args = args || [];
    
    return function() {
        Array.prototype.push.apply(args, Array.prototype.slice.call(arguments));
        if (args.length < len) {
            return arguments.callee;  // 指向arguments所属的函数
        }
        return fn.apply(null, args);
    }
}

// 求(1)(2)(3)
const curryIt = function() {
    let args = [...arguments];
    
    function func() {
        args.push(...arguments);
        return func;
    }
    func.toString = function() {
        return args.reduce((a, b) => a + b);
    }
    return func;
}
```

## 浅拷贝和深拷贝

- 浅拷贝

  - Object.assign()
  - 直接引用赋值

- 深拷贝

  - 递归实现

  ```javascript
  const deepClone = (obj) => {
      if (typeof obj !== 'object') return obj;
      const objClone = Array.isArray(obj) ? [] : {};
      for (const key in obj) {
          if (obj[key] && typeof obj[key] === 'object') {
              objClone[key] = deepClone(obj[key]);
          } else {
              objClone[key] = obj[key];
          }
      }
      return objClone;
  }
  
  // ES6
  const deepClone = (obj) => {
      if (typeof obj !== 'object') return obj;
      return Array.isArray(obj) ? [...obj] : {...obj};
  }
  ```

  - JSON，无法复制函数、正则、symbol
  - 对象深拷贝，ES6语法

  ```js
  // 对象深拷贝
  const obj1 = { name: 'Tom', age: 18, job: { first: 'tearcher', second: 'father' } };
  const obj2 = { ...obj1 };
  const obj3 = { ...obj1, name: 'Peter' };
  obj2.name = 'Bob';
  console.log(obj1); 
  // { name: 'Tom', age: 18, job: { first: 'tearcher', second: 'father' } }
  console.log(obj2); 
  // { name: 'Bob', age: 18, job: { first: 'tearcher', second: 'father' } }
  console.log(obj3); 
  // { name: 'Peter', age: 18, job: { first: 'tearcher', second: 'father' }, address: 'Nanjing' }
  console.log(obj1 === obj2); 
  // false
  ```


## BOM

- Window，代表的是整个浏览器的窗口，同时window也是网页中的全局对象
- Navigator，代表当前浏览器的信息，通过该对象可以来识别不同的浏览器
- Location，代表当前浏览器的地址栏信息，通过Location可以获取地址信息，或者操作浏览器跳转页面
- History，代表浏览器的历史记录，可以通过该对象来操作浏览器的历史记录，由于隐私原因，该对象不能获取到具体的历史记录，只能操作浏览器向前或向后翻页，而且该操作只在当次访问时有效
- Screen，代表用户的屏幕的信息，通过该对象可以获取到用户的显示器的相关信息

## DOM

|          | nodeName  | nodeType | nodeValue |
| -------- | --------- | -------- | --------- |
| 文档节点 | #document | 9        | null      |
| 元素节点 | 标签名    | 1        | null      |
| 属性节点 | 属性名    | 2        | 属性值    |
| 文本节点 | #text     | 3        | 文档内容  |

- 重排和重绘，重绘不一定需要重排，重排必然会导致重绘

  - 重排(reflow)：当部分渲染树(或者整个渲染树)必须更新并且节点的尺寸发生了变化，浏览器会使渲染树中受到影响的部分失效，并重新构造渲染树。

    - 添加、删除可见的dom
    - 元素的位置改变
    - 元素的尺寸改变（外边距、内边距、边框厚度、宽高等几何属性）
    - 页面渲染初始化
    - 浏览器窗口尺寸改变

    局部重排：把一个DOM的宽高之类的几何信息定死，然后在DOM内部触发重排，就只会重新渲染该DOM内部的元素，而不会影响到外界。

  - 重绘(repaint)：是在一个元素的外观被改变所触发的浏览器行为，浏览器会根据元素的新属性重新绘制，使元素呈现新的外观。

- 页面生成过程

  1. HTML被HTML解析器解析成DOM树
  2. CSS被CSS解析器解析为CSSOM树
  3. 结合DOM树和CSSOM树，生成一棵渲染树(Render Tree)，这一过程称为Attachment
  4. 生成布局(flow)，浏览器在屏幕上“画”出渲染树中的所有节点
  5. 将布局绘制(paint)在屏幕上，显示出整个页面

  第四步和第五步最耗时，两步合称为渲染。

![A933F9CDFA62E4D0E74706242691BA10](https://gitee.com/iera2yy/typora_pic/raw/master/A933F9CDFA62E4D0E74706242691BA10.png)

- 页面渲染
  - 蓝色：网络通信和HTML解析
  - 黄色：JavaScript执行
  - 紫色：样式计算和布局，即重排
  - 绿色：重绘
- 防止图片拖拽：`ondragstart="return false"`
- 防止文本拖拽：`user-select: none;`

## 同步和异步

- Promise

  - 优点

    - promise是一个对象，对象和函数的区别就是对象可以保存状态，函数不可以（闭包除外）
    - 并未剥夺函数return的能力，因此无需层层传递callback，进行回调获取数据
    - 代码风格，容易理解，便于维护
    - 多个异步等待合并便于解决

  - 三个状态

    - pending[待定] 初始状态
    - fulfilled[实现] 操作成功
    - rejected[被否决] 操作失败

  - resolve作用是，将Promise对象的状态从“未完成”变为“成功”（即从 pending 变为 resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；

  - reject作用是，将Promise对象的状态从“未完成”变为“失败”（即从 pending 变为 rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

  - .then()

    - 接收两个函数作为参数，分别代表fulfilled（成功）和rejected（失败）
    - .then()返回一个新的Promise实例，所以它可以链式调用
    - 当前面的Promise状态改变时，.then()根据其最终状态，选择特定的状态响应函数执行
    - 状态响应函数可以返回新的promise，或其他值，不返回值也可以我们可以认为它返回了一个null
    - 如果返回新的promise，那么下一级.then()会在新的promise状态改变之后执行
    - 如果返回其他任何值，则会立即执行下一级.then()

  - 错误处理

    - 第一种：reject('错误信息').then(() => {}, () => {错误处理逻辑})

    ```javascript
    new Promise((resolve, reject) => {
        setTimeout(() => {
            reject('error');
        }, 2000);
    }).then((val) => {
        console.log(val);
    }, (err) => {
        console.log('Error:', err);
    });
    ```

    - 第二种：throw new Error('错误信息').catch( () => {错误处理逻辑})
      推荐使用第二种方式，更加清晰好读，并且可以捕获前面所有的错误（可以捕获N个then回调错误）

    ```javascript
    new Promise(resolve => {
        setTimeout(() => {
            resolve('resolve');
        }, 2000);
    }).then((val) => {
        console.log(val);
        throw new Error('error');
    }).catch(error => {
        console.log(error);
    });
    ```

  - Promise.all()

    - Promise.all([p1, p2, p3])用于将多个promise实例，包装成一个新的Promise实例，返回的实例就是普通的promise
      它接收一个数组作为参数
    - 数组里可以是Promise对象，也可以是别的值，只有Promise会等待状态改变
    - 当所有的子Promise都完成，该Promise完成，返回值是全部值得数组
    - 有任何一个失败，该Promise失败，返回值是第一个失败的子Promise结果

    ```javascript
    //切菜
    function cutUp(){
        console.log('开始切菜。');
        var p = new Promise(function(resolve, reject){        //做一些异步操作
            setTimeout(function(){
                console.log('切菜完毕！');
                resolve('切好的菜');
            }, 1000);
        });
        return p;
    }
    
    //烧水
    function boil(){
        console.log('开始烧水。');
        var p = new Promise(function(resolve, reject){        //做一些异步操作
            setTimeout(function(){
                console.log('烧水完毕！');
                resolve('烧好的水');
            }, 1000);
        });
        return p;
    }
    
    Promise.all([cutUp(), boil()])
        .then((result) => {
            console.log('准备工作完毕');
            console.log(result);
        })
    
    // 开始切菜。
    // 开始烧水。
    // 切菜完毕！
    // 烧水完毕！
    // 准备工作完毕
    // [ '切好的菜', '烧好的水' ]
    ```

  - Promise.race()

    - 类似于Promise.all() ，区别在于它有任意一个完成就算完成

    ```javascript
    let p1 = new Promise(resolve => {
        setTimeout(() => {
            resolve('I\`m p1 ')
        }, 1000)
    });
    let p2 = new Promise(resolve => {
        setTimeout(() => {
            resolve('I\`m p2 ')
        }, 2000)
    });
    Promise.race([p1, p2])
        .then(value => {
            console.log(value)
        })
    // I`m p1
    ```

- 异步问题解决方案

  - **回调函数**，回调是一个函数被作为一个参数传递到另一个函数里，在那个函数执行完后再执行。（ 也即：B函数被作为参数传递到A函数里，在A函数执行完后再执行B ）

  ```javascript
  function A(callback){
      setTimeout(function () {
          callback();
      }, 1000);
  }
  A(B);
  ```

  - **事件触发**，采用事件驱动模式。任务的执行不取决代码的顺序，而取决于某一个事件是否发生。监听函数有：on，bind，listen，addEventListener，observe。
  - **发布/订阅者模式**，在一个"信号中心"，某个任务执行完成，就向信号中心"发布"（publish）一个信号，其他任务可以向信号中心"订阅"（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做"发布/订阅模式"（publish-subscribe pattern），又称"观察者模式"（observer pattern）。

  ```javascript
  // f2向"信号中心"jQuery订阅"done"信号,jQuery.publish("done")的意思是，f1执行完成后，向"信号中心"jQuery发布"done"信号，从而引发f2的执行。
  jQuery.subscribe("done", f2);
  
  function f1(){
  　　setTimeout(function () {
  　　　　jQuery.publish("done");
  　　}, 1000);
  }
  
  // 此外，f2完成执行后，也可以取消订阅（unsubscribe）
  jQuery.unsubscribe("done", f2);
  ```

  - **Promise**

  ```javascript
  // 请实现如下的函数，可以批量请求数据，所有的URL地址在urls参数中，同时可以通过max参数控制请求的并发度。当所有的请求结束后，需要执行callback回调。发请求的函数可以直接使用fetch。
  
  function sendRequest (urls: string[], max: number, callback: () => void) {
    
  }
  
  function sendRequest(arr, max, callback) {
          let fetchArr = [],  // 存储并发max的promise数组
              i = 0;
  
          function toFetch() {
              if (i === arr.length) {   // 所有的都处理完了， 返回一个resolve
                  return Promise.resolve();
              }
  
              let one = fetch(arr[i++]); // 取出第i个url， 放入fetch里面 , 每取一次i++
              one.then(() => {fetchArr.splice(fetchArr.indexOf(one), 1)}); // 当promise执行完毕后，从数组删除
              fetchArr.push(one);  //将当前的promise存入并发数组中     
              // 其实将这个push放到上一行会更好理解，那样就是我们同步的思维顺序，先push进去，再等promise执行完了之后再删除。  但由于then是异步的，所以怎么放都可以。
  
              let p = Promise.resolve();
              if (fetchArr.length >= max) {     // 当并行数量达到最大后，用race比较第一个完成的，然后再调用一下函数自身。
                  p = Promise.race(fetchArr);
              }
              return p.then(() => toFetch());
          }
          
          // arr循环完后， 现在fetchArr里面剩下最后max个promise对象， 使用all等待所有的都完成之后执行callback
          toFetch().then(() => Promise.all(fetchArr)).then(() => {
              callback();
          })
  }
  ```

  - **async/await**
    - 内置执行器。 Generator 函数的执行必须靠执行器，所以才有了 co 函数库，而 async 函数自带执行器。也就是说，async 函数的执行，与普通函数一模一样，只要一行。
    - 更好的语义。 async 和 await，比起星号和 yield，语义更清楚了。async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。
    - 更广的适用性。 yield 命令后面只能是 Thunk 函数或 Promise 对象，而 async 函数的 await 命令后面，可以跟 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）。

  ```javascript
  async function myFunction() {
    await somethingThatReturnsAPromise().catch(function (err){
      console.log(err);
    });
  }
  ```

- 进程是资源分配的最小单位，线程是CPU调度的最小单位

- Ajax，(Asynchronous JavaScript and XML的缩写)是一种异步请求数据的web开发技术，在不需要重新刷新页面的情况下，Ajax 通过异步请求加载后台数据，并在网页上呈现出来。

  - open(method, url, async) 方法需要三个参数:
    -  method：发送请求所使用的方法（GET或POST）；与POST相比，GET更简单也更快，并且在大部分情况下都能用；然而，在以下情况中，请使用POST请求：无法使用缓存文件（更新服务器上的文件或数据库）；向服务器发送大量数据（POST 没有数据量限制）；发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠
    -  url：规定服务器端脚本的 URL(该文件可以是任何类型的文件，比如 .txt 和 .xml，或者服务器脚本文件，比如 .asp 和 .php （在传回响应之前，能够在服务器上执行任务）)；
    -  async：规定应当对请求进行异步（true）或同步（false）处理；true是在等待服务器响应时执行其他脚本，当响应就绪后对响应进行处理；false是等待服务器响应再执行。
  - send() 方法可将请求送往服务器。
  - onreadystatechange：存有处理服务器响应的函数，每当 readyState 改变时，onreadystatechange 函数就会被执行。
  - readyState：存有服务器响应的状态信息。
    - 0-请求未初始化（代理被创建，但尚未调用 open() 方法）
    - 1-服务器连接已建立（open方法已经被调用）
    - 2-请求已接收（send方法已经被调用，并且头部和状态已经可获得）
    - 3-请求处理中（下载中，responseText 属性已经包含部分数据）
    - 4-请求已完成，且响应已就绪（下载操作已完成）
  - responseText：获得字符串形式的响应数据。
  - setRequestHeader()：POST传数据时，用来添加 HTTP 头，然后send(data)，注意data格式；GET发送信息时直接加参数到url上就可以，比如url?a=a1&b=b1。

```javascript
var ajax = {
    get: function(url, fn) {
        var xhr = new XMLHttpRequest();
        xhr.open('GET', url, true);
        xhr.onreadystatechange = function() {
            if (xhr.readyState === 4 && (xhr.status === 200 || xhr.status === 304)) {
                fn.call(this, xhr.responseText);
            }
        };
        xhr.send();
    },
    post: function(url, data, fn) {
        var xhr = new XMLHttpRequest();
        xhr.open('POST', url, true);
        xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
        xhr.onreadystatechange = function() {
            if (xhr.readyState === 4 && (xhr.status === 200 || xhr.status === 304)) {
                fn.call(this, xhr.responseText);
            }
        };
        xhr.send(data);
    }
}
```

- axios
  - axios 是一个基于Promise 用于浏览器和 nodejs 的 HTTP 客户端，本质上也是对原生XHR的封装，只不过它是Promise的实现版本，符合最新的ES规范，它本身具有以下特征：
    - 从浏览器中创建 XMLHttpRequest
    - 支持 Promise API
    - 客户端支持防止CSRF
    - 提供了一些并发请求的接口（重要，方便了很多的操作）
    - 从 node.js 创建 http 请求
    - 拦截请求和响应
    - 转换请求和响应数据
    - 取消请求
    - 自动转换JSON数据
    - PS：防止CSRF:就是让你的每个请求都带一个从cookie中拿到的key, 根据浏览器同源策略，假冒的网站是拿不到你cookie中得key的，这样后台就可以轻松辨别出这个请求是否是用户在假冒网站上的误导输入，从而采取正确的策略。

## 请求终止

- 【原生JS】XMLHttpRequest.abort()

  - 如果该请求已被发出，XMLHttpRequest.abort() 方法将终止该请求。当一个请求被终止，它的 readyState 属性将被置为0（ UNSENT )。

  ```javascript
  let xhr = new XMLHttpRequest(),
      method = "GET",
      url = "https://developer.mozilla.org/"; // 仅为示例，并非真实接口地址。
  xhr.open(method,url,true);
   
  xhr.send();
   
  xhr.abort(); // 终止请求
  ```

- 【Ajax】ajax.abort()

  ```javascript
  let queryTruckAjax;
  if(queryTruckAjax){
      queryTruckAjax.abort(); // 终止请求
  }
  queryTruckAjax = $.ajax({
      type: "POST",
      url:url,
      dataType: "json",
      success: function(data) {
          do thing...
      },error: function () {
      }
  });
  ```

- 【axios】axios.CancelToken

  - Axios 提供了一个 CancelToken的函数，这是一个构造函数，该函数的作用就是用来取消接口请求的。利用axios请求的config参数，向axios添加一个包含cancelToken的config配置对象。

  ```html
  <body>
    <div class="page" id="app">
      <button @click="getMsg" class="get-msg">获取数据</button>
      <button @click="cancelGetMsg" class="cancel">取消获取</button>
      <ul>
        <li v-for="item in items">{{item.name}}</li>
      </ul>
    </div>
    <script>
    var app = new Vue({
      el: '#app',
      data: {
        message: 'Hello Vue!',
        items: [],
        cancel: null // 这里要先初始化
      },
      methods: {
        getMsg () {
          let CancelToken = axios.CancelToken
          let self = this
          // 仅为示例，并非真实接口地址。
          axios.get('http://jsonplaceholder.typicode.com/comments', {
            cancelToken: new CancelToken(c => {
              this.cancel = c
              console.log(c)
              // 这个参数 c 就是CancelToken构造函数里面自带的取消请求的函数，这里把该函数当参数用
            })
          }).then(res => {
            this.items = res.data
          }).catch(err => {
            console.log(err)
          })
        },
   
        cancelGetMsg () {
          this.cancel() // 终止请求
        }
      }
    })
    </script>
  </body>
  ```

- 【微信小程序 】RequestTask.abort()

  ```javascript
  if (requestTask) {
      requestTask.abort(); // 终止请求
  }
  let requestTask = wx.request({
    url: 'test.php', //仅为示例，并非真实的接口地址
    data: {
      x: '',
      y: ''
    },
    header: {
      'content-type': 'application/json' // 默认值
    },
    success (res) {
      console.log(res.data)
    }
  })
  ```

- 【uni-app】 requestTask.abort()

  - 如果希望返回一个 `requestTask` 对象，需要至少传入 success / fail / complete 参数中的一个，如果没有传入 success / fail / complete 参数，则会返回封装后的 Promise 对象。

  ```javascript
  const requestTask = uni.request({
      url: 'https://www.example.com/request', // 仅为示例，并非真实接口地址。
      data: {
          name: 'name',
          age: 18
      },
      success: function(res) {
          console.log(res.data);
      }
  });
   
   // 终止请求
  requestTask.abort();
  ```

## 路由

- Hash模式

  - 使用`window.location.hash`属性及窗口的`onhashchange`事件，可以实现监听浏览器地址hash值变化，执行相应的js切换网页。
    - hash指的是地址中#号以及后面的字符，也称为散列值。hash也称作锚点，本身是用来做页面跳转定位的。如http://localhost/index.html#abc，这里的#abc就是hash；
    - 散列值是不会随请求发送到服务器端的，所以改变hash，不会重新加载页面；
    - 监听 window 的 hashchange 事件，当散列值改变时，可以通过 location.hash 来获取和设置hash值；
    - location.hash值的变化会直接反应到浏览器地址栏。
  - 出发hashchange事件的几种情况
    - 浏览器地址栏散列值的变化（包括浏览器的前进、后退）会触发window.location.hash值的变化，从而触发onhashchange事件；
    - 当浏览器地址栏中URL包含哈希如http://www.baidu.com/#home ，这时按下输入，浏览器发送<http://www.baidu.com/>请求至服务器，请求完毕之后设置散列值为#home，进而触发onhashchange事件；
    - 当只改变浏览器地址栏URL的哈希部分，这时按下回车，浏览器不会发送任何请求至服务器，这时发生的只是设置散列值新修改的哈希值，并触发onhashchange事件；
    - html中`<a>`标签的属性 href 可以设置为页面的元素ID如 #top，当点击该链接时页面跳转至该id元素所在区域，同时浏览器自动设置window.location.hash属性，地址栏中的哈希值也会发生改变，并触发onhashchange事件；

  ```javascript
  //设置 url 的 hash，会在当前url后加上'#abc'
  window.location.hash = 'abc';
  let hash = window.location.hash //'#abc'
  
  window.addEventListener('hashchange',function(){
  	//监听hash变化，点击浏览器的前进后退会触发
  })
  ```

- History模式

  - window.history 属性指向 History 对象，它表示当前窗口的浏览历史。当发生改变时，只会改变页面的路径，不会刷新页面。

  - History 对象保存了当前窗口访问过的所有页面网址。通过 history.length 可以得出当前窗口一共访问过几个网址。

  - 由于安全原因，浏览器不允许脚本读取这些地址，但是允许在地址之间导航。

  - 浏览器工具栏的“前进”和“后退”按钮，其实就是对 History 对象进行操作。

  - 两个属性

    - History.length：当前窗口访问过的网址数量（包括当前网页）
    - History.state：History堆栈最上层的状态值

  - 三个方法

    - History.back()：移动到上一个网址，等同于点击浏览器的后退键。对于第一个访问的网址，该方法无效果。
    - History.forward()：移动到下一个网址，等同于点击浏览器的前进键。对于最后一个访问的网址，该方法无效果。
    - History.go()：接受一个整数作为参数，以当前网址为基准，移动到参数指定的网址。如果参数超过实际存在的网址范围，该方法无效果；如果不指定参数，默认参数为0，相当于刷新当前页面。

    ```javascript
    history.back();
    history.forward();
    history.go(1);//相当于history.forward()
    history.go(-1);//相当于history.back()
    history.go(0); // 刷新当前页面
    // 移动到以前访问过的页面时，页面通常是从浏览器缓存之中加载，而不是重新要求服务器发送新的网页。
    ```

  - History.pushState()

    - 用于在历史中添加一条记录。pushState()方法不会触发页面刷新，只是导致 History 对象发生变化，地址栏会有变化。
    - 语法：`history.pushState(object, title, url)`
    - `object`：是一个对象，通过 pushState 方法可以将该对象内容传递到新页面中。如果不需要这个对象，此处可以填 null。
    - `title`：指标题，几乎没有浏览器支持该参数，传一个空字符串比较安全。
    - `url`：新的网址，必须与当前页面处在同一个域。不指定的话则为当前的路径，如果设置了一个跨域网址，则会报错。

    ```javascript
    var data = { foo: 'bar' };
    history.pushState(data, '', '2.html');
    console.log(history.state) // {foo: "bar"}
    // 如果pushState的URL参数设置了一个新的锚点值（即 hash），并不会触发hashchange事件。反过来，如果URL的锚点值变了，则会在History对象创建一条浏览记录。
    
    // 如果 pushState() 方法设置了一个跨域网址，则会报错。
    // 当前网址为 http://example.com
    history.pushState(null, '', 'https://twitter.com/hello');
    ```

  - History.replaceState()

    - 该方法用来修改 History 对象的当前记录，用法与 pushState() 方法一样。

    ```javascript
    // 假定当前网页是 example.com/example.html
    
    history.pushState({page: 1}, '', '?page=1')
    // URL 显示为 http://example.com/example.html?page=1
    
    history.pushState({page: 2}, '', '?page=2');
    // URL 显示为 http://example.com/example.html?page=2
    
    history.replaceState({page: 3}, '', '?page=3');
    // URL 显示为 http://example.com/example.html?page=3
    
    history.back()
    // URL 显示为 http://example.com/example.html?page=1
    
    history.back()
    // URL 显示为 http://example.com/example.html
    
    history.go(2)
    // URL 显示为 http://example.com/example.html?page=3
    ```

  - popstate 事件

    - 每当 history 对象出现变化时，就会触发 popstate 事件。
    - 仅仅调用pushState()方法或replaceState()方法 ，并不会触发该事件;
    - 只有用户点击浏览器倒退按钮和前进按钮，或者使用 JavaScript 调用History.back()、History.forward()、History.go()方法时才会触发。
    - 另外，该事件只针对同一个文档，如果浏览历史的切换，导致加载不同的文档，该事件也不会触发。
    - 页面第一次加载的时候，浏览器不会触发popstate事件。

    ```javascript
    // 使用的时候，可以为popstate事件指定回调函数，回调函数的参数是一个 event 事件对象，它的 state 属性指向当前的 state 对象。
    window.addEventListener('popstate', function(e) {
    	//e.state 相当于 history.state
    	console.log('state: ' + JSON.stringify(e.state));
    	console.log(history.state);
    });
    ```

    - history 致命的缺点就是当改变页面地址后，强制刷新浏览器时，（如果后端没有做准备的话）会报错，因为刷新是拿当前地址去请求服务器的，如果服务器中没有相应的响应，会出现 404 页面。

## 跨域

- 同源策略

  - 如果两个 URL 的 protocol、port (如果有指定的话)和 host 都相同的话，则这两个 URL 是同源。这个方案也被称为“协议/主机(域名)/端口元组”，或者直接是 “元组”。（“元组” 是指一组项目构成的整体，双重/三重/四重/五重/等的通用形式）。
  - 下表给出了与 URL http://store.company.com/dir/page.html 的源进行对比的示例:

  | URL                                             | 结果 | 原因                             |
  | ----------------------------------------------- | ---- | -------------------------------- |
  | http://store.company.com/dir2/other.html        | 同源 | 只有路径不同                     |
  | http://store.company.com/dir/inner/another.html | 同源 | 只有路径不同                     |
  | https://store.company.com/secure.html           | 失败 | 协议不同                         |
  | http://store.company.com:81/dir/etc.html        | 失败 | 端口不同 ( http:// 默认端口是80) |
  | http://news.company.com/dir/other.html          | 失败 | 主机不同                         |

- 非同源限制

  - 无法读取非同源网页的 Cookie、LocalStorage 和 IndexedDB
  - 无法接触非同源网页的 DOM
  - 无法向非同源地址发送 AJAX 请求

- 跨域解决方案

  - **设置document.domain解决无法读取非同源网页的 Cookie问题**

    - 因为浏览器是通过document.domain属性来检查两个页面是否同源，因此只要通过设置相同的document.domain，两个页面就可以共享Cookie（此方案仅限主域相同，子域不同的跨域应用场景。）

    ```javascript
    // 两个页面都设置
    document.domain = 'test.com';
    ```

  - **跨文档通信 API：window.postMessage()**

    - 调用postMessage方法实现父窗口http://test1.com向子窗口http://test2.com发消息（子窗口同样可以通过该方法发送消息给父窗口）
    - 它可用于解决以下方面的问题：
      - 页面和其打开的新窗口的数据传递
      - 多窗口之间消息传递
      - 页面与嵌套的iframe消息传递
      - 上面三个场景的跨域数据传递

    ```javascript
    // 父窗口打开一个子窗口
    var openWindow = window.open('http://test2.com', 'title');
    // 父窗口向子窗口发消息(第一个参数代表发送的内容，第二个参数代表接收消息窗口的url)
    openWindow.postMessage('Nice to meet you!', 'http://test2.com');
    
    // 监听 message 消息
    window.addEventListener('message', function (e) {
      console.log(e.source); // e.source 发送消息的窗口
      console.log(e.origin); // e.origin 消息发向的网址
      console.log(e.data);   // e.data   发送的消息
    }, false);
    ```

  - **JSONP**

    - JSONP 是服务器与客户端跨源通信的常用方法。最大特点就是简单适用，兼容性好（兼容低版本IE），缺点是只支持get请求，不支持post请求。
    - 核心思想：网页通过添加一个`<script>`元素，向服务器请求 JSON 数据，服务器收到请求后，将数据放在一个指定名字的回调函数的参数位置传回来。

    ```html
    // 原生实现
    <div id="box1"></div>
    <script type="text/javascript">
        function callbackFunction(res) {
            console.log(res);
            document.getElementById('box1').innerHTML = res;
        }
    </script>
    <script src="http://localhost/demo/index.php?callback=callbackFunction"></script>
    
    // index.php
    <?php
        $callback = $_GET['callback'];
        $data = '"hello world"';
        echo $callback . "(". $data .")";
    ?>
    ```

    ```javascript
    // jQuery ajax
    $.ajax({
        url: 'http://www.test.com:8080/login',
        type: 'get',
        dataType: 'jsonp',  // 请求方式为jsonp
        jsonpCallback: "handleCallback",    // 自定义回调函数名
        data: {}
    });
    
    // Vue
    this.$http.jsonp('http://www.domain2.com:8080/login', {
        params: {},
        jsonp: 'handleCallback'
    }).then((res) => {
        console.log(res); 
    })
    ```

  - **CORS**

    - CORS 是跨域资源分享（Cross-Origin Resource Sharing）的缩写。它是 W3C 标准，属于跨源 AJAX 请求的根本解决方法。
    - 普通跨域请求：只需服务器端设置`Access-Control-Allow-Origin: *`
    - 带cookie跨域请求：前后端都需要进行设置

    ```javascript
    //【前端设置】根据xhr.withCredentials字段判断是否带有cookie
    // 原生ajax
    xhr.withCredentials = true;
    
    // jQuery ajax 
    $.ajax({
       url: 'http://www.test.com:8080/login',
       type: 'get',
       data: {},
       xhrFields: {
           withCredentials: true    // 前端设置是否带cookie
       },
       crossDomain: true,   // 会让请求头中包含跨域的额外信息，但不会含cookie
    });
    
    // vue-resource
    Vue.http.options.credentials = true
    
    // axios
    axios.defaults.withCredentials = true
    ```

  - **Proxy代理(脚手架)**

    - 方法一

    > 在package.json中追加如下配置

    ```json
    "proxy": "http://localhost:5000"
    ```

    说明：

    1. 优点：配置简单，前端请求资源时可以不加任何前缀。
    2. 缺点：不能配置多个代理。
    3. 工作方式：上述方式配置代理，当请求了3000不存在的资源时，那么该请求会转发给5000 （优先匹配前端资源）

    - 方法二

    1. 第一步：创建代理配置文件

       ```
       在src下创建配置文件：src/setupProxy.js
       ```

    2. 编写setupProxy.js配置具体代理规则：

       ```js
       const proxy = require('http-proxy-middleware')
       
       module.exports = function(app) {
         app.use(
           proxy('/api1', {  //api1是需要转发的请求(所有带有/api1前缀的请求都会转发给5000)
             target: 'http://localhost:5000', //配置转发目标地址(能返回数据的服务器地址)
             changeOrigin: true, //控制服务器接收到的请求头中host字段的值
             /*
             	changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
             	changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:3000
             	changeOrigin默认值为false，但我们一般将changeOrigin值设为true
             */
             pathRewrite: {'^/api1': ''} //去除请求前缀，保证交给后台服务器的是正常请求地址(必须配置)
           }),
           proxy('/api2', { 
             target: 'http://localhost:5001',
             changeOrigin: true,
             pathRewrite: {'^/api2': ''}
           })
         )
       }
       ```

    说明：

    1. 优点：可以配置多个代理，可以灵活的控制请求是否走代理。
    2. 缺点：配置繁琐，前端请求资源时必须加前缀。

## 防抖和节流

- 防抖，在第一次触发事件时，不立即执行函数，且在给定期限内没有再次触发滚动事件，则执行函数。

```javascript
var debounce = function(fn, wait) {
    let timeout = null;
    return function() {
        if (timeout !== null) {
            clearTimeout(timeout);
        }
        timeout = setTimeout(fn, wait);
    }
}

function handle() {
    console.log(Math.random());
}
document.getElementById("box1").addEventListener('scroll', debounce(handle, 1000), false);
```

- 节流，保证在一定时间内只调用一次事件处理函数，即假设用户一直触发这个事件，且触发时间小于既定值，则会每隔这个时间调用一次。

```javascript
var throttle = function(fn, delay) {
    let valid = true;
    return function() {
        if (!valid) {
            return false;
        }
        valid = false;
        setTimeout(() => {
            fn();
            valid = true;
        }, delay);
    }
}

function handle() {
    console.log(Math.random());
}
document.getElementById("box1").addEventListener('scroll', throttle(handle, 1000), false);
```

## 事件循环

- 在当前执行栈为空的时候，主线程会查看微任务队列是否有事件存在。如果不存在，那么再去宏任务队列中取出一个事件并把对应的回调加入当前执行栈；如果存在，则会依次执行队列中事件对应的回调，直到微任务队列为空，然后去宏任务队列中取出最前面的一个事件，把对应的回调加入当前执行栈...如此反复，进入循环。

- 外部输入数据-->轮询阶段(poll)-->检查阶段(check)-->关闭事件回调阶段(close callback)-->定时器检测阶段(timer)-->I/O事件回调阶段(I/O callbacks)-->闲置阶段(idle, prepare)-->轮询阶段

  - timers: 这个阶段执行定时器队列中的回调如 setTimeout() 和 setInterval()。
  - I/O callbacks: 这个阶段执行几乎所有的回调。但是不包括close事件，定时器和setImmediate()的回调。
  - idle, prepare: 这个阶段仅在内部使用，可以不必理会。
  - poll: 等待新的I/O事件，node在一些特殊情况下会阻塞在这里。
  - check: setImmediate()的回调会在这个阶段执行。
  - close callbacks: 例如socket.on('close', ...)这种close事件的回调。

   ┌───────────────────────┐
  ┌─>│        timers         │
  │  └──────────┬────────────┘
  │  ┌──────────┴────────────┐
  │  │     I/O callbacks     │
  │  └──────────┬────────────┘
  │  ┌──────────┴────────────┐
  │  │     idle, prepare     │
  │  └──────────┬────────────┘      ┌───────────────┐
  │  ┌──────────┴────────────┐      │   incoming:   │
  │  │         poll          │<──connections───     │
  │  └──────────┬────────────┘      │   data, etc.  │
  │  ┌──────────┴────────────┐      └───────────────┘
  │  │        check          │
  │  └──────────┬────────────┘
  │  ┌──────────┴────────────┐
  └──┤    close callbacks    │
     └───────────────────────┘

```javascript
async function async1() {
  console.log('async1 start')// 2  主线程async1()调用
  await async2()
  console.log('async1 end')  // 6 主线程内同步代码执行完毕后接着执行await函数内后面的代码（至此主线程内同步代码都执行完毕）
}

async function async2() {
  console.log('async2')// 3 主线程async1() => async2()  await 会先执行完async2()内的代码
}

console.log('script start')// 1  主线程同步代码
setTimeout(function() { 
  console.log('setTimeout') // 8 setTimeout放入宏队列中下次轮询执行
}, 0)

async1(); 
  
new Promise(function(resolve) {
  console.log('promise1') // 4 promise内的同步代码会在主线程内先执行
  resolve();
}).then(function() {
  console.log('promise2') // 7 promise回调会放入微队列中，主线程代码执行完毕后执行
});

console.log('script end') // 5 主线程同步代码
// script start
// async1 start
// async2
// promise1
// script end
// async1 end
// promise2
// setTimeout
```

## 冒泡和捕获

- 事件的委派
  - 指将事件统一绑定给元素的共同祖先元素，这样当后代元素上的事件触发时，会一直冒泡到祖先元素，从而通过祖先元素的响应函数来处理事件
  - 通过委派可以减少事件绑定的次数，提高程序的性能
- 阻止冒泡和默认事件
  - `event.stopPropagation();` 只阻止了冒泡事件， 默认行为没有阻止（超链接a嵌套按钮，依然会跳转）
  - `event.cancelBubble = true`，IE阻止冒泡事件， 默认行为没有阻止
  - `event.stopImmediatePropagation();` 阻止事件冒泡并且阻止该元素上同事件类型的监听器（例如，多个click）被触发
  - `event.preventDefault();` 只阻止了默认事件，冒泡事件没有阻止（超链接a嵌套按钮，不跳转，但会触发点击事件）
  - `return false;`原生javascript的return false只会阻止默认行为，而是用jQuery的话则既阻止默认行为又防止对象冒泡，兼容IE
- addEventListener
  - passive作用
    - 手势事件有个属性 cancelable，作用是告诉浏览器该事件是否允许监听器通过 preventDefault() 方法阻止，默认为true。如果在touch事件内部调用preventDefault()，事件默认行为被取消，页面也就静止不动了。浏览器并不知道touch事件内部是否调用了preventDefault()，**浏览器只有等内核线程执行到事件监听器对应的JavaScript代码时，才能知道内部是否会调用preventDefault函数来阻止事件的默认行为，所以浏览器本身无法优化这种场景**。

```
// old version
el.addEventListener(type, listener[, useCapture])
// ES6
el.addEventListener(type, listener, {
    capture: false, // === useCapture
    once: false,    // 是否设置单次监听
    passive: false  // 是否让 阻止默认行为(preventDefault()) 失效
})
// 新增参数的三个属性，默认值都是 false。
```

## 微任务和宏任务

- 宏任务：当前调用栈中执行的任务。
  - 按顺序执行，且浏览器在每个宏任务之间渲染页面
  - 浏览器为了能够使得JS内部task与DOM任务能够有序的执行，会在一个task执行结束后，在下一个 task 执行开始前，对页面进行重新渲染 （task->渲染->task->...）

```tex
script(整体代码)
setTimeout
setInterval
I/O
UI交互事件
postMessage
MessageChannel
setImmediate(Node.js环境)
```

- 微任务：当前宏任务执行结束后立即执行的任务。也就是说，在当前task任务后，下一个task之前，在渲染之前。

```tex
Promise.then
Object.observe
MutaionObserver
process.nextTick(Node.js 环境)
```

- 运行机制
  - 执行一个宏任务（栈中没有就从事件队列中获取）
  - 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
  - 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
  - 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
  - 渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

## 生成器

- 生成器(Generators)允许我们在函数执行过程中暂停、并在将来某一时刻恢复执行
- 生成器函数的函数体内容将会是所生成的生成器的执行内容，在这些内容之中，`yield`语句的引入使得生成器函数与普通函数有了区别。`yield`语句的作用与`return`语句有些相似，但`yield`语句的作用并非退出函数体，而是切出当前函数的运行时（此处为一个类协程，Semi-coroutine），并与此同时可以讲一个值（可以是任何类型）带到主线程中。

```javascript
function* fibo() {
  let a = 0
  let b = 1

  yield a
  yield b

  while (true) {
    let next = a + b
    a = b
    b = next
    yield next
  }
}

let generator = fibo()

for (var i = 0; i < 10; i++)
  console.log(generator.next().value) //=> 0 1 1 2 3 5 8 13 21 34 55
```

## 上下文

- 执行上下文的代码分为两个阶段
  - 进入执行上下文
    - 变量对象包括
      1. 函数的所有形参(如果是函数上下文)
         - 由名称和对应值组成的一个变量对象的属性被创建
         - 没有实参，属性值设为undefined
      2. 函数声明
         - 由名称和对应值(函数对象(function-object))组成一个变量对象的属性被创建
         - 如果变量对象以及存在相同名称的属性，则完全替换这个属性
      3. 变量声明
         - 由名称和对应值(undefined)组成一个变量对象的属性被创建
         - 如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性
  - 执行代码
- 总结
  1. 全局上下文的变量对象初始化是全局对象
  2. 函数上下文的变量对象初始化只包括Arguments对象
  3. 在进入执行上下文时会给变量对象添加形参、函数声明、变量声明等初始的属性值，非形参则为undefined
  4. 在执行代码阶段，会再次修改变量对象的属性值

```javascript
function foo(a) {
  var b = 2;
  function c() {}
  var d = function() {};

  b = 3;
}

foo(1);

// 进入执行上下文后
AO = {
    arguments: {
        0: 1,
        length: 1
    },
    a: 1,
    b: undefined,
    c: reference to function c(){},
    d: undefined
}

// 代码执行
AO = {
    arguments: {
        0: 1,
        length: 1
    },
    a: 1,
    b: 3,
    c: reference to function c(){},
    d: reference to FunctionExpression "d"
}
```

- 叠层上下文
  - 层叠上下文，英文称作”stacking context”。是HTML中的一个三维的概念。如果一个元素含有层叠上下文，我们可以理解为这个元素在z轴上就“高人一等”。
  - 谁大谁上：当具有明显的层叠水平标示的时候，如识别的z-indx值，在同一个层叠上下文领域，层叠水平值大的那一个覆盖小的那一个。通俗讲就是官大的压死官小的。
  - 后来居上：当元素的层叠水平一致、层叠顺序相同的时候，在DOM流中处于后面的元素会覆盖前面的元素。
  - 7阶层叠水平(stacking level)
    - 正`z-index`
    - `z-index:auto`或`z-index:0`
    - `inline/inline-block`水平盒子
    - `float`浮动盒子
    - `block`块状水平盒子
    - 负`z-index`
    - `background/border`

## 布局

- 圣杯布局：为了让中间div内容不被遮挡，将中间div设置了左右padding-left和padding-right后，将左右两个div用相对布局position: relative并分别配合right和left属性，以便左右两栏div移动后不遮挡中间div。

```html
<style>
    #container{
        padding-left: 200px;
        padding-right: 150px;
    }

    #container .column{
        float: left;
        height: 100px;
    }

    #center{
        width: 100%;
        background-color: black;
    }

    #left{
        width: 200px;
        margin-left: -100%;
        position: relative;
        right: 200px;
        background-color: red;
    }

    #right{
        width: 150px;
        margin-right: -150px;
        background-color: #bfc;
    }

    #footer{
        clear: both;
    }

    body{
        min-width: 550px;
    }
</style>
<div id="header"></div>
<div id="container">
    <div id="center" class="column"></div>
    <div id="left" class="column"></div>
    <div id="right" class="column"></div>
</div>
<div id="footer"></div>
```

- 双飞翼布局：为了让中间div内容不被遮挡，直接在中间div内部创建子div用于放置内容，在该div里用margin-left和margin-right为左右两栏div留出位置。

```html
<style>
    #container{
        width: 100%;
    }

    .column{
        float: left;
        height: 200px;
    }

    #center{
        margin-left: 200px;
        margin-right: 150px;
        height: 200px;
        background-color: black;
    }

    #left{
        width: 200px;
        margin-left: -100%;
        background-color: red;
    }

    #right{
        width: 150px;
        margin-left: -150px;
        background-color: #bfc;
    }

    #footer{
        clear: both;
    }
</style>

<div id="header"></div>
<div id="container" class="column">
    <div id="center"></div>
</div>
<div id="left" class="column"></div>
<div id="right" class="column"></div>
<div id="footer"></div>
```

- 两者的功能相同，都是为了实现一个两侧宽度固定，中间宽度自适应的三栏布局。
  - 两侧宽度固定，中间宽度自适应
  - 中间部分在DOM结构上优先，以便先行渲染
  - 允许三列中的任意一列成为最高列
  - 只需要使用一个额外的`<div>`标签

## 浏览器架构

- 浏览器进程(Browser process)，浏览器进程负责管理Chrome应用本身，包括地址栏、书签、前进和后退按钮。同时也负责可不见的功能，比如网络请求、文件按访问等，也负责其他进程的调度。
  - UI 线程负责绘制工具栏中的按钮、地址栏等。
  - 网络线程负责从网络中获取数据。
  - 存储线程负责文件等功能。
- 渲染进程(Renderer process)，渲染进程负责站点的渲染，其中也包括JavaScript代码的运行，web worker的管理等。
- 插件进程(Plugin process)，插件进程负责为浏览器提供各种额外的插件功能，例如flash。
- GPU进程(GPU process)，GPU进程负责提供成像的功能。

## V8引擎

- 垃圾回收

  - 默认情况下，64位系统最多使用1.4GB内存，32位系统最多使用0.7GB内存

  - 新生代(new_space)：大多数的对象开始都会被分配在这里，这个区域相对较小但是垃圾回收特别频繁，该区域被分为两半，一半用来分配内存，另一半用于在垃圾回收时将需要保留的对象复制过来。

    - 内存最大值在64位和32位系统分别是32MB和16MB。
    - 采用`Scavenge`算法(复制算法)进行垃圾回收。
    - 对象晋升：对象是否经历过一次Scavenge算法；To空间的内存占比是否已经超过25%。

  - 老生代(old_space)：新生代中的对象在存活一段时间后就会被转移到老生代内存区，相对于新生代该内存区域的垃圾回收频率较低。老生代又分为老生代指针区和老生代数据区，前者包含大多数可能存在指向其他对象的指针的对象，后者只保存原始数据对象，这些对象没有指向其他对象的指针。

    - `Mark-Sweep`算法(标记-清除)和`Mark-Compact`算法(标记-整理)

    - 标记-清除

      1. 垃圾回收器会在内部构建一个根列表，用于从根节点出发去寻找那些可以被访问到的变量。比如在JavaScript中，window全局对象可以看成一个根节点。
      2. 垃圾回收器从所有根节点出发，遍历其可以访问到的子节点，并将其标记为活动的，根节点不能到达的地方即为非活动的，将会被视为垃圾。
      3. 垃圾回收器将会释放所有非活动的内存块，并将其归还给操作系统。

      根节点认定：全局对象；本地函数的局部变量和参数；当前嵌套调用链上的其他函数的变量和参数。

    - 标记-整理

      - 经历一次标记-清除后，内存空间可能会出现不连续的状态，即内存碎片
      - 假设在老生代中有A、B、C、D四个对象
      - 在垃圾回收的标记阶段，将对象A和对象C标记为活动的
      - 在垃圾回收的整理阶段，将活动的对象往堆内存的一端移动
      - 在垃圾回收的清除阶段，将活动对象左侧的内存全部回收

    - 增量标记

      - 由于JS的单线程机制，垃圾回收的过程会阻碍主线程同步任务的执行，待执行完垃圾回收后才会再次恢复执行主任务的逻辑，这种行为被称为**全停顿**(stop-the-world)。在标记阶段同样会阻碍主线程的执行，一般来说，老生代会保存大量存活的对象，如果在标记阶段将整个堆内存遍历一遍，那么势必会造成严重的卡顿。
      - 因此，为了减少垃圾回收带来的停顿时间，V8引擎又引入了`Incremental Marking`(增量标记)的概念，即将原本需要一次性遍历堆内存的操作改为增量标记的方式，先标记堆内存中的一部分对象，然后暂停，将执行权重新交给JS主线程，待主线程任务执行完毕后再从原来暂停标记的地方继续标记，直到标记完整个堆内存。这个理念其实有点像React框架中的Fiber架构，只有在浏览器的空闲时间才会去遍历`Fiber Tree`执行对应的任务，否则延迟执行，尽可能少地影响主线程的任务，避免应用卡顿，提升应用性能。
      - 得益于增量标记的好处，V8引擎后续继续引入了延迟清理(`lazy sweeping`)和增量式整理(`incremental compaction`)，让清理和整理的过程也变成增量式的。同时为了充分利用多核CPU的性能，也将引入**并行标记**和**并行清理**，进一步地减少垃圾回收对主线程的影响，为应用提升更多的性能。

  - 大对象区(large_object_space)：存放体积超越其他区域大小的对象，每个对象都会有自己的内存，垃圾回收不会移动大对象区。

  - 代码区(code_space)：代码对象，会被分配在这里，唯一拥有执行权限的内存区域。

  - map区(map_space)：存放Cell和Map，每个区域都是存放相同大小的元素，结构简单。

  <img src="https://gitee.com/iera2yy/typora_pic/raw/master/A83F10BCA52302FB8A05A552EE9DAA63.png" alt="A83F10BCA52302FB8A05A552EE9DAA63" style="zoom:50%;" />

- 内存泄漏

  - 尽可能少地创建全局变量
  - 手动清除定时器
  - 少用闭包
  - 清除DOM引用
  - 弱引用

## RAIL模型

- Response 事件处理最好在100ms内完成
- Animation 在16ms内产生一帧
- Idle 最大化空闲时间
- Load 传输内容到页面可交互的时间不超过1秒

## 跨页面通信

- 同源页面间
  - BroadCast Channel
  - Service Worker
  - LocalStorage
  - Shared Worker
  - IndexedDB
  - window.open + window.opener
- 非同源页面之间
  - iframe

## 软件架构

- MVC
  - MVC全名是Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写，一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，将业务逻辑聚集到一个部件里面，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。
  - 在 MVC 里面，Model 是数据模型；View 是视图或者说就是我们的软件界面需要去展示的东西；Controller 是用来控制Model的读取、存储，以及如何在 View上 展示数据，更新数据的逻辑控制器。
  - MVC三者之间的联系
    - View 传送指令到 Controller
    - Controller 完成业务逻辑后，要求 Model 改变状态
    - Model 将新的数据发送到 View，用户得到反馈
    - 所有通信都是单向的。View和Model之间的通信是通过Controller来作为桥梁的，也就是说View和Model并不是直接通信
    - 需要服务器端配合，JavaScript可以在前端修改服务器渲染后的数据，所有通信都是单向的，提交一次反馈一次，通信一次相互制约
  - 优点
    - 耦合性低
    - 重用性高
    - 部署快
    - 可维护性高
    - 有利于软件工程化管理
  - 缺点
    - 不适合小型中等规模的应用程序。
    - 增加了系统结果和实现的复杂性。
    - View和Model之间不匹配，用户界面和流程要考虑易用性，用户体验优化同时考虑业务流程的精确和无错。
    - Controler和Model之间界线不清，什么样的逻辑是界面逻辑，什么样的逻辑是业务逻辑，没有明确的定义。
    - View的变化不能完全由Model控制，即Observer模式不足以支持复杂的用户交互。这其实要求VC之间要有依赖。牵一发而动全身，数据，显示不分离，Controller，Model联系过于紧密。
- MVP
  - MVP（Model-View-Presenter）是MVC的改良模式，由IBM的子公司Taligent提出。和MVC的相同之处在于：Controller / Presenter负责业务逻辑，Model管理数据，View负责显示，只不过是将 Controller 改名为 Presenter，同时改变了通信方向。
  - 特点
    - M和P、V和P之间双向通信。
    - View 与 Model 不通信，都通过 Presenter 传递。Presenter完全把Model和View进行了分离，主要的程序逻辑在Presenter里实现。
    - View 非常薄，不部署任何业务逻辑，称为”被动视图”（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里。
    - Presenter与具体的View是没有直接关联的，而是通过定义好的接口进行交互，从而使得在变更View时候可以保持Presenter的不变，这样就可以重用。不仅如此，还可以编写测试用的View，模拟用户的各种操作，从而实现对Presenter的测试–从而不需要使用自动化的测试工具。
  - 优点
    - 模型与视图完全分离，我们可以修改视图而不影响模型；
    - 可以更高效地使用模型，因为所有的交互都发生在一个地方——Presenter内部；
    - 我们可以将一个Presenter用于多个视图，而不需要改变Presenter的逻辑。这个特性非常的有用，因为视图的变化总是比模型的变化频繁；
    - 如果我们把逻辑放在Presenter中，那么我们就可以脱离用户接口来测试这些逻辑（单元测试）。
  - 缺点
    - 视图和Presenter的交互会过于频繁，使得他们的联系过于紧密。也就是说，一旦视图变更了，presenter也要变更。
- MVVM
  - M(odel)层：模型，定义数据结构。
  - C(ontroller)层：实现业务逻辑，数据的增删改查。在MVVM模式中一般把C层算在M层中，（只有在理想的双向绑定模式下，Controller才会完全的消失。这种理想状态一般不存在）
  - ViewModel层：顾名思义是视图View的模型、映射和显示逻辑（如if for等，非业务逻辑），另外绑定器也在此层。ViewModel是基于视图开发的一套模型，如果你的应用是给盲人用的，那么也可以开发一套基于Audio的模型AudioModel。
  - V(iew)层：将ViewModel通过特定的GUI展示出来，并在GUI控件上绑定视图交互事件，V(iew)一般由MVVM框架自动生成在浏览器中。
  - M-V-VM之间的关系
    - 在MVVM架构下，View 和 Model 之间其实并没有直接的联系，而是通过ViewModel进行交互，Model 和 ViewModel 之间的交互是双向的， 因此View 数据的变化会同步到Model中，而Model 数据的变化也会立即反应到View 上。
    - ViewModel 通过双向数据绑定把 View 层和 Model 层连接了起来，ViewModel里面包含DOM Listeners和Data Bindings，DOM Listeners和Data Bindings是实现双向绑定的关键。DOM Listeners监听页面所有View层DOM元素的变化，当发生变化，Model层的数据随之变化；Data Bindings监听Model层的数据，当数据发生变化，View层的DOM元素随之变化。
  - 优点
    - 低耦合。View可以独立于Model变化和修改，一个ViewModel可以绑定到不同的View上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。
    - 可重用性。可以把一些视图的逻辑放在ViewModel里面，让很多View重用这段视图逻辑。
    - 独立开发。开发人员可以专注与业务逻辑和数据的开发(ViewModel)。设计人员可以专注于界面(View)的设计。
    - 可测试性。可以针对ViewModel来对界面(View)进行测试。
  - 缺点
    - 学习成本高
    - Debug困难

## 前端性能优化

- 网页图片加载优化方案

  - 启动页面时加载过多图片，解决方法：图片按需加载

  - 部分图片体积过大，解决方法：减少单位像素所需的字节数(.webp)；减少一张图片总的像素

  - 图片加载完成实现方案

    ```java
    // 判断图片是否加载完成
    // Chrome环境
    const img = new Image();
    img.src = '*****';
    console.log(img.complete);
    
    // 原生方法
    document.getElementById("img").onload = function() {}
    
    // onreadystatechange
    const img1 = document.getElementById("img");
    img1.onreadystatechange = function() {
      if(img1.readyState === "complete" || img1.readyState == "loaded"){}
    }
    ```

- 缓存

  - 本地数据存 cookie；localStorage/sessionStorage；indexedDB
  - 内存缓存
  - Cache API
  - HTTP缓存
    - 强缓存(浏览器端) Expires；Pragma、Cache-Control
    - 协商缓存(服务器端) Last-Modified/If-Modified-Since(最后一次修改时间)；ETag/If-None-Match(校验码：文件编号，文件大小，最后一次修改时间)
      - 在没有调整服务器时间和篡改客户端缓存的情况下，Last-Modified/If-Modified-Since配合起来管理协商缓存是非常可靠的；有时候服务器上资源有变化，但最后修改时间却没有变化。
  - Push Cache

- 发送请求

  - 不必要的重定向 301；302
  - DNS预解析
  - 预先建立连接
  - 使用CDN

- 服务器响应

  - 使用流
  - 业务接口内部耦合
  - 避免内部问题

- 页面解析与处理

  - 资源位置顺序  
  - 合理使用defer/async脚本

- 页面静态资源

- 运行时

- 预加载

![A1BA234AAD4D93064DC5A5F04D31DDDC](https://gitee.com/iera2yy/typora_pic/raw/master/A1BA234AAD4D93064DC5A5F04D31DDDC.png)

## Vue

### SPA

- 单页Web应用(single page web application, SPA)
- 整个应用只有一个完整的页面
- 点击页面中的链接不会刷新页面，只会做页面的局部更新
- 数据都需要通过ajax请求获取，并在前端异步展现

### 双向绑定

- 单向绑定(React)，把Model绑定到View，当用JavaScript代码更新Model时，View就会自动更新。
- 双向绑定(Vue)，Model绑定到View，当用JavaScript代码更新Model时，View就会自动更新。当用户更新了View，Model的数据也自动被更新。

### 模板渲染

- mustache

  - 模板引擎是将数据要变为视图最优雅的解决方案。

  - 数据变为视图的方法

    - 纯DOM法
    - 数组join法
    - ES6反引号法`` `${a}` ``
    - 模板引擎

    <img src="https://gitee.com/iera2yy/typora_pic/raw/master/DBEFBA39FE578962E88E3558D42BF670.png" alt="mustache原理图" style="zoom:40%;" />

  - tokens是一个JS的嵌套数组，是抽象语法树、虚拟节点等等的开山鼻祖

  - mustache底层重点

    1. 将模板字符串编译为tokens形式
    2. 将tokens结合数据，解析为dom字符串

- 模板渲染原理：vue中的模板template无法被浏览器解析并渲染，因为这不属于浏览器的标准，不是正确的HTML语法，所有需要将template转化成一个JavaScript函数，这样浏览器就可以执行这一个函数并渲染出对应的HTML元素，就可以让视图跑起来了，这一个转化的过程，就成为模板编译。

  模板编译又分三个阶段，解析parse，优化optimize，生成generate，最终生成可执行函数render。

  - parse阶段：使用大量的正则表达式对template字符串进行解析，将标签、指令、属性等转化为抽象语法树AST。
  - optimize阶段：遍历AST，找到其中的一些静态节点并进行标记，方便在页面重渲染的时候进行diff比较时，直接跳过这一些静态节点，优化runtime的性能。
  - generate阶段：将最终的AST转化为render函数字符串。

- Vue渲染过程

<img src="https://gitee.com/iera2yy/typora_pic/raw/master/F7EEA68A878E148E665B50A3AA412669.png" alt="F7EEA68A878E148E665B50A3AA412669" style="zoom:50%;" />

### 抽象语法树AST

模板语法->抽象语法树AST->渲染函数(h函数)->虚拟节点->界面

- babel
  1. 将code转换成AST
  2. 将AST进行优化，生成一个更小的AST
  3. 将新生成的AST再转化成code

![EB381C3E07914BA8B5C532AE5E2C5EE5](https://gitee.com/iera2yy/typora_pic/raw/master/EB381C3E07914BA8B5C532AE5E2C5EE5.jpg)

```javascript
// uglify-js的版本需要为2.x, 3.0之后uglifyjs不再暴露Compressor api
// 2.x的uglify不能自动解析es6，所以这里先切换成es5
// npm install uglify-js@2.x
var UglifyJS = require('uglify-js');
// 原始代码
var code = `var a;
var x = { b: 123 };
a = 123,
delete x`;
// 通过 UglifyJS 把代码解析为 AST
var ast = UglifyJS.parse(code);
ast.figure_out_scope();
// 转化为一颗更小的 AST 树
compressor = UglifyJS.Compressor();
ast = ast.transform(compressor);
// 再把 AST 转化为代码
code = ast.print_to_string();
// var a,x={b:123};a=123,delete x;
console.log("code", code);
```

### 虚拟DOM

- 虚拟DOM，用JavaScript对象描述DOM的层次结构。DOM中的一切属性都在虚拟DOM中有对应的属性。
  - 虚拟DOM由h函数产生，h函数用来产生虚拟节点(vnode)
    - `h('a', { props: {href: 'http://www.baidu.com'}}, '百度');`
    - `{ 'sel': 'a', 'data': { props:{ href: 'http://www.baidu.com' } }, 'text': '百度' }`
    - `<a href="http://www.baidu.com">百度</a>`

```json
// 虚拟节点结构
{
  "children": undefined,   // 子元素
  "data": {},              // 属性，样式
  "elm": undefined,        // 对应正在操作的dom节点，undefined表示这个虚拟节点还没上树
  "key": undefined,        // 节点唯一标识
  "sel": "div",            // 选择器
  "text": "我是一个盒子",    // 文字
}
```

- 作用/优势

  - **跨平台优势**，由于 Virtual DOM 是以 JavaScript 对象为基础而不依赖真实平台环境，所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等。
  - **操作DOM慢，JS运行效率高**，我们可以将DOM对比操作放在JS层，提高效率。
  - **提升渲染能力**，Virtual DOM的优势不在于单次的操作，而是在大量、频繁的数据更新下，能够对视图进行合理、高效的更新。例子：若一次操作中有10次更新DOM的动作，虚拟DOM不会立即操作DOM，而是将这10次更新的diff内容保存到本地一个JS对象中，最终将这个JS对象一次性attch到DOM树上，再进行后续操作，避免大量无谓的计算量。

- diff算法

  - key是节点的唯一标识符，告诉diff算法，在更改前后它们是否是同一个DOM节点。
  - 只有是同一个虚拟节点，才进行精细化比较，否则就是暴力拆除并重建。如何定义是统一个虚拟节点？答：**选择器**相同且**key**相同。例如，父节点改变，则整个子节点都重建。
  - 只进行同层级比较，不会进行跨层级比较。例如，增加一层，则增加层内所有子节点全部重建。
  - 在diff中，只对同层级的子节点进行比较，放弃跨级的节点比较，使得时间复杂从`O(n^3)`降低值`O(n)`，也就是说，只有当新旧children都为多个子节点时才需要用核心的Diff算法进行同层级比较。

- diff比较逻辑

  - 两个节点相同，但不在相同层级上，无法复用

    <img src="https://gitee.com/iera2yy/typora_pic/raw/master/v2-b8976a7c2b7e86e3f33f36cb05c0be5d_1440w.jpg" alt="img" style="zoom: 25%;" />

  - 两个节点相同，在同一层级，但父节点不同，无法复用

    <img src="https://gitee.com/iera2yy/typora_pic/raw/master/v2-629a89987b3b5ffd4ee7de4202f706cd_1440w.jpg" alt="img" style="zoom:25%;" />

  - 同层同父节点，可以复用

    <img src="https://gitee.com/iera2yy/typora_pic/raw/master/v2-f724ac345fe5a5fb232185dcabc7b770_1440w.jpg" alt="img" style="zoom:25%;" />

- 命中查找，diff算法优化

  - 命中一种就不在进行命中判断；如果都没命中，则需要循环来寻找，移动到旧前的前面
    1. 新前与旧前
    2. 新后与旧后
    3. 新后与旧前
    4. 新前与旧后
  - 如果旧节点先循环完毕，说明新节点中有要插入的节点
  - 如果是新节点先循环完毕，老节点中还有剩余节点，说明它们是要被删除的节点
  - 当新后与旧前命中时，此时要移动节点。新前指向的节点移动到旧后的后面
  - 当新前与旧后命中时，此时要移动节点。新前指向的节点移动到旧前的前面

- 虚拟DOM中key的作用

  - 当状态中的数据发生变化时，react会根据新数据生成新的虚拟DOM，随后React进行新虚拟DOM与旧虚拟DOM的diff比较，比较规则如下：
    - 旧虚拟DOM中找到了与新虚拟DOM相同的key
      - 若虚拟DOM中内容没变，直接使用之前的真实DOM
      - 若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM
    - 旧虚拟DOM中未找到与新虚拟DOM相同的key
      - 根据数据创建新的真实DOM，随后渲染到页面
  - 用index作为key可能会引发的问题
    - 若对数据进行：逆序添加、逆序删除等破坏顺序操作，会产生没有必要的真实DOM更新 ===> 界面效果没问题，但效率低
    - 如果结构中还包含输入类，如`<input/>`的DOM，会产生错误DOM更新 ===> 界面有问题
    - 如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表展示，使用index作为key是没有问题的

### 响应式系统

- Vue是MVVM框架，当数据模型data变化时，页面视图会得到响应更新。

  原理：对data的getter/setter方法进行拦截(Object.defineProperty或者Proxy)，利用发布订阅的设计模式，在getter方法中进行订阅，在setter方法中发布通知，让所有订阅者完成响应。

  在响应式系统中，Vue会为数据模型data的每一个属性新建一个订阅中心作为发布者，而监听器watch、计算属性computed、视图渲染template/render三个角色同时作为订阅者，对于监听器watch，会直接订阅观察监听的属性，对于计算属性computed和视图渲染template/render，如果内部执行获取了data的某个属性，就会执行该属性的getter方法，然后自动完成对该属性的订阅，当属性被修改时，就会执行该属性的setter方法，从而完成该属性的发布通知，通知所有订阅者进行更新。

- 非侵入式

- Object.defineProperty()方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

  - value：设置属性的值。
  - writable：值是否可以重写。true | false
  - enumerable：目标属性是否可以被枚举。true | false
  - configurable：目标属性是否可以被删除或是否可以再次修改特性。true | false
  - set：目标属性设置值的方法。
  - get：目标属性获取值的方法。

- defineReactive()

  ```javascript
  function defineReactive(data, key, val) {
      Object.defineProperty(data, key, {
          // 可枚举
          enumerable: true,
          // 可以被配置，比如可以被delete
          configurable: true,
          // getter
          get() {
              return val;
          },
          // setter
          set(newValue) {
              if (val === newValue) {
                  return;
              }
              val = newValue;
          }
      });
  }
  ```

- proxy(target, handler)

  - 基于Object.defineProperty的实现所存在的很多限制：无法监听属性的添加和删除、数组索引和长度的变更，不支持Map、Set、WeakMap和WeakSet。
  - 参数`target` 是用Proxy包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）, 参数`handler`也是一个对象，其属性是当执行一个操作时定义代理的行为的函数，也就是自定义的行为。

  ```javascript
  let obj = {
      a: 1
  }
  let proxyObj = new Proxy(obj, {
      get: function (target, prop, value) {   // target代理对象(obj), key键, value值
          return prop in target ? target[prop] : 0;
      },
      set: function (target, prop, value, receiver) { 
        // target代理对象(obj), property键, value设置值, receiver接收对象
          target[prop] = value;
      }
  })
  
  console.log(proxyObj.a);        // 1
  console.log(proxyObj.b);        // 0
  
  proxyObj.a = 666;
  console.log(proxyObj.a)         // 666
  ```

- 数据侦测

  - 改写数组7个函数：push、pop、shift、unshift、splice、sort、reverse

- 依赖

  - Vue1.x，细粒度依赖，用到数据的DOM都是依赖
  - Vue2.x，中等粒度依赖，用到数据的组件都是依赖
  - 在getter中收集依赖，在setter中触发依赖
  - 把依赖收集的代码封装成一个Dep类，它专门用来管理依赖，每个Observer的实例，成员中都有一个Dep的实例。Dep使用发布订阅模式，当数据发生变化时，会循环依赖列表，把所有的Watcher都通知一遍。
  - Watcher是一个中介数据发生变化时通过Watcher中转，通知组件。依赖就是Watcher，只有Watcher触发的getter才会收集依赖，哪个Watcher出发了getter，就把哪个Watcher收集到Dep中。

### computed和watch

- 相同点：计算属性computed和监听器watch都可以观察属性的变化从而做出响应。
- 不同点：
  - computed只有当依赖的数据变化时才会计算，当数据没有变化时，会读取缓存数据
  - 而监听器watch并不具备缓存性，监听器watch提供一个监听函数，当监听的属性发生变化时，会立即执行该函数，更适合用于数据变化时的异步操作

### 生命周期

- `beforeCreate`是new Vue()之后触发的第一个钩子，在当前阶段data、methods、computed以及watch上的数据和方法都不能被访问。
- `created`在实例创建完成后发生，当前阶段已经完成了数据观测，也就是可以使用数据，更改数据，在这里更改数据不会触发updated函数。可以做一些初始数据的获取，在当前阶段无法与Dom进行交互，如果非要想，可以通过vm.$nextTick来访问Dom。
- `beforeMount`发生在挂载之前，在这之前template模板已导入渲染函数编译。而当前阶段虚拟Dom已经创建完成，即将开始渲染。在此时也可以对数据进行更改，不会触发updated。
- `mounted`在挂载完成后发生，在当前阶段，真实的Dom挂载完毕，数据完成双向绑定，可以访问到Dom节点，使用$refs属性对Dom进行操作。
- `beforeUpdate`发生在更新之前，也就是响应式数据发生更新，虚拟dom重新渲染之前被触发，你可以在当前阶段进行更改数据，不会造成重渲染。
- `updated`发生在更新完成之后，当前阶段组件Dom已完成更新。要注意的是避免在此期间更改数据，因为这可能会导致无限循环的更新。
- `beforeDestroy`发生在实例销毁之前，在当前阶段实例完全可以被使用，我们可以在这时进行善后收尾工作，比如清除计时器。
- `destroyed`发生在实例销毁之后，这个时候只剩下了dom空壳。组件已被拆解，数据绑定被卸除，监听被移出，子实例也统统被销毁。

<img src="https://gitee.com/iera2yy/typora_pic/raw/master/13119812-5890a846b6efa045.png" alt="img" style="zoom: 33%;" />

### 组件

- 为什么组件的data必须是一个函数
  - 一个组件可能在很多地方使用，也就是会创建很多个实例，如果data是一个对象的话，对象是引用类型，一个实例修改了data会影响到其他实例，所以data必须使用函数，为每一个实例创建一个属于自己的data，使其同一个组件的不同实例互不影响。
- 组件通信
  - 父子组件通信
    - 父组件 -> 子组件：`prop`
    - 子组件 -> 父组件：`$on/$emit`
    - 获取组件实例：使用`$parent/$children`，`$refs.xxx`，获取到实例后直接获取属性数据或调用组件方法
  - 兄弟组件通信
    - Event Bus：每一个Vue实例都是一个Event Bus，都支持$on/$emit，可以为兄弟组件的实例之间new一个Vue实例，作为Event Bus进行通信。
    - Vuex：将状态和方法提取到Vuex，完成共享
  - 跨级组件通信
    - 使用provide/inject
    - Event Bus：同兄弟组件Event Bus通信
    - Vuex：将状态和方法提取到Vuex，完成共享
    - localStorage/sessionStorage

### 事件绑定

每一个Vue实例都是一个Event Bus，当子组件被创建的时候，父组件将事件传递给子组件，子组件初始化的时候是有`$on`方法将事件注册到内部，在需要的时候使用`$emit`触发函数，而对于原生native事件，使用addEventListener绑定到真实的DOM元素上。

### slot

- slot又名插槽，是Vue的内容分发机制，组件内部的模板引擎使用slot元素作为承载分发内容的出口。插槽slot是子组件的一个模板标签元素，而这一个标签元素是否显示，以及怎么显示是由父组件决定的。
- slot又分三类，默认插槽，具名插槽和作用域插槽。
  - 默认插槽：又名匿名插槽，当slot没有指定name属性值的时候一个默认显示插槽，一个组件内只有有一个匿名插槽。
  - 具名插槽：带有具体名字的插槽，也就是带有name属性的slot，一个组件可以出现多个具名插槽。
  - 作用域插槽：默认插槽、具名插槽的一个变体，可以是匿名插槽，也可以是具名插槽，该插槽的不同点是在子组件渲染作用域插槽时，可以将子组件内部的数据传递给父组件，让父组件根据子组件的传递过来的数据决定如何渲染该插槽。
- 实现原理：当子组件vm实例化时，获取到父组件传入的slot标签的内容，存放在`vm.$slot`中，默认插槽为`vm.$slot.default`，具名插槽为`vm.$slot.xxx`，xxx 为插槽名，当组件执行渲染函数时候，遇到slot标签，使用`$slot`中的内容进行替换，此时可以为插槽传递数据，若存在数据，则可称该插槽为作用域插槽。

### nextTick

- 延迟回调，功能上与setTimeout效果一样
- nextTick优先使用`Promise`和`MutationObserver` 因为他俩属于微任务，会在执行栈空闲的时候立即执行，它的响应速度相比setTimeout会更快，因为无需等渲染。
- 而setImmediate和setTimeout属于宏任务，执行开始之前要等渲染，即task->渲染->task。

### keep-alive

- 场景：用户在某个列表页面选择筛选条件过滤出一份数据列表，由列表页面进入数据详情页面，再返回该列表页面，我们希望：列表页面可以保留用户的筛选（或选中）状态。
- 作用
  - 能够保存页面/组件的状态
  - 避免组件反复创建和渲染，有效提升系统性能
  - 总的来说，keep-alive用于保存组件的渲染状态。

### template

- 预编译：对于 Vue 组件来说，模板编译只会在组件实例化的时候编译一次，生成渲染函数之后在也不会进行编译。因此，编译对组件的 runtime 是一种性能损耗。

  而模板编译的目的仅仅是将template转化为`render function`，这个过程，正好可以在项目构建的过程中完成，这样可以让实际组件在 runtime 时直接跳过模板渲染，进而提升性能，这个在项目构建的编译template的过程，就是预编译。

- 与jsx的区别：对于 runtime 来说，只需要保证组件存在 render 函数即可，而我们有了预编译之后，我们只需要保证构建过程中生成 render 函数就可以。

  在 webpack 中，我们使用`vue-loader`编译.vue文件，内部依赖的`vue-template-compiler`模块，在 webpack 构建过程中，将template预编译成 render 函数。

  与 react 类似，在添加了jsx的语法糖解析器`babel-plugin-transform-vue-jsx`之后，就可以直接手写render函数。

  所以，template和jsx的都是render的一种表现形式，不同的是：

  JSX相对于template而言，具有更高的灵活性，在复杂的组件中，更具有优势，而 template 虽然显得有些呆滞。但是 template 在代码结构上更符合视图与逻辑分离的习惯，更简单、更直观、更好维护。

### route

- vue-router三种导航钩子
  - 全局导航钩子
  - 组件内钩子
  - 单独路由独享组件
- route和router区别
  - route 是“路由信息对象”，包括path，params，hash，query，fullPath，matched，name等路由信息参数。
  - router是“路由实例”对象包括了路由的跳转方法，钩子函数等。

### Mixins

- Mixins特点
  - 方法和参数在各组件中不共享
  - 值为对象的选项，如methods,components等，选项会被合并，键冲突的组件会覆盖混入对象的
  - 值为函数的选项，如created,mounted等，就会被合并调用，混合对象里的钩子函数在组件里的钩子函数之前调用
- Mixins(混入)与组件的区别
  - 组件：在父组件中引入组件，相当于在父组件中给出一片独立的空间供子组件使用，然后根据props来传值，但本质上两者是相对独立的。
  - Mixins：则是在引入组件之后与组件中的对象和方法进行合并，相当于扩展了父组件的对象与方法，可以理解为形成了一个新的组件。
- Mixins(混入)与Vuex的区别
  - vuex：用来做状态管理的，里面定义的变量在每个组件中均可以使用和修改，在任一组件中修改此变量的值之后，其他组件中此变量的值也会随之修改。
  - Mixins：可以定义共用的变量，在每个组件中使用，引入组件中之后，各个变量是相互独立的，值的修改在组件中不会相互影响。

### Vuex

- 状态管理模式
- 默认五种基本对象
  - state：存储状态（变量）
  - getters：对数据获取之前的再次编译，可以理解为state的计算属性。我们在组件中使用 $sotre.getters.fun()
  - mutations：修改状态，并且是同步的。在组件中使用$store.commit('',params)。这个和我们组件中的自定义事件类似。
  - actions：异步操作。在组件中使用是$store.dispath('')
  - modules：store的子模块，为了开发大型项目，方便状态管理而使用的。这里我们就不解释了，用起来和上面的一样。

### Vue3.0

- Vue2.0与Vue3.0的区别
  - 重构响应式系统，使用Proxy替换Object.defineProperty，使用Proxy优势：
    - 可直接监听数组类型的数据变化
    - 监听的目标为对象本身，不需要像Object.defineProperty一样遍历每个属性，有一定的性能提升
    - 可拦截apply、ownKeys、has等13种方法，而Object.defineProperty不行
    - 直接实现对象属性的新增/删除
    - 基于 Object.defineProperty 的实现所存在的很多限制：无法监听属性的添加和删除、数组索引和长度的变更
  - 新增Composition API，更好的逻辑复用和代码组织
  - 重构 Virtual DOM
    - 模板编译时的优化，将一些静态节点编译成常量
    - slot优化，将slot编译为lazy函数，将slot的渲染的决定权交给子组件
    - 模板中内联事件的提取并重用（原本每次渲染都重新生成内联函数）
  - 代码结构调整，更便于Tree shaking，使得体积更小
  - 使用Typescript替换Flow
- Composition API
  - 代码组织问题，Compostion API可以让开发者根据业务逻辑组织自己的代码，让代码具备更好的可读性和可扩展性，也就是说当下一个开发者接触这一段不是他自己写的代码时，他可以更好的利用代码的组织反推出实际的业务逻辑，或者根据业务逻辑更好的理解代码。	
  - 实现代码的逻辑提取与复用，当然mixin也可以实现逻辑提取与复用，但是像前面所说的，多个mixin作用在同一个组件时，很难看出property是来源于哪个mixin，来源不清楚，另外，多个mixin的property存在变量命名冲突的风险。而Composition API刚好解决了这两个问题。
    - 单纯组件引用：父组件 + 子组件 >>> 父组件 + 子组件
    - mixins：父组件 + 子组件 >>> new父组件
    - mixins作用：多个组件可以共享数据和方法，在使用mixin的组件中引入后，mixin中的方法和属性也就并入到该组件中，可以直接使用。钩子函数会两个都被调用，mixin中的钩子首先执行。
- Vite和webpack
  - webpack会先打包，然后启动开发服务器，请求服务器时直接给予打包结果。
  - vite是直接启动开发服务器，请求哪个模块再对该模块进行实时编译。

## React

### 特点

- 采用组件化模式、声明式编码，提高开发效率及组件复用率
- 在React Native中可以使用React语法进行移动端开发
- 使用虚拟DOM+优秀的Diffing算法，尽量减少与真实DOM的交互

### 构造函数

- 通过给`this.state`赋值对象来初始化内部`state`
- 为事件处理函数绑定实例

### 事件处理

- 通过onXxx属性指定事件处理函数(注意大小写)
  - React使用的是自定义(合成)事件，而不是使用原生的DOM事件——为了更好地**兼容性**
  - React中的事件是通过事件委托方式处理的(委托给组件最外层的元素)——为了**高效**
- 通过event.target得到发生事件的DOM元素对象——不要过度使用ref
- 原生事件: 在 `componentDidMount生命周期`里边进行`addEventListener`绑定的事件
- 合成事件: 通过 JSX 方式绑定的事件，比如 `onClick={() => this.handle()}`
  - React 上注册的事件最终会绑定在`document`这个 DOM 上，而不是 React 组件对应的 DOM(减少内存开销就是因为所有的事件都绑定在 document 上，其他节点没有绑定事件)
  - React 自身实现了一套事件冒泡机制，所以这也就是为什么我们 `event.stopPropagation()` 无效的原因。
  - React 通过队列的形式，从触发的组件向父组件回溯，然后调用他们 JSX 中定义的 callback
  - React 有一套自己的合成事件 `SyntheticEvent`，不是原生的，这个可以自己去看官网
  - React 通过对象池的形式管理合成事件对象的创建和销毁，减少了垃圾的生成和新对象内存的分配，提高了性能

### 组件的三大属性

- 受控组件

  - 类似于Vue的双向绑定

- 非受控组件

  - 阻止默认事件发生，`event.preventDefault()`

- 组件中render方法中的this为组件实例对象

- 组件自定义方法中this为undefined

  - 强制绑定this：通过函数对象的bind()
  - 箭头函数

- state

  - 组件对象最重要的属性，值是对象(可以包含多个key-value的组合)
  - 组件被称为“状态机”，通过更新组件的state来更新对应的页面显示(重新渲染组件)

- props

  - 读取内部某个属性值`this.props.name`
  - 对props中的属性值进行类型限制和必要性限制

  ```react
  // React v15.5之前
  Person.propTypes = {
  	name: React.PropTypes.string.isRequired,
  	age: React.PropTypes.number
  }
  
  // React v15.5之后
  // 使用prop-types库限制
  Person.propTypes = {
  	name: PropTypes.string.isRequired,
  	age: PropTypes.number
  }
  ```

  - 扩展属性，将对象的所有属性通过props传递`<Person {...person}/>`
  - 默认属性值

  ```react
  Person.defaultProps = {
  	age: 18,
  	sex: '男'
  }
  ```

  - 组件类的构造函数

  ```react
  constructor(props) {
  	super(props);
  	// super();
  	console.log(this.props); //传给super能打印，否则不能
  }
  ```

- refs

  - 字符串形式

  ```react
  showData1 = () => {
      const { input1 } = this.refs;
      alert(input1.value);
  }
  
  render() {
      return (
          <div>
              <input type="text" ref="input1" onBlur={this.showData1} placeholder="请输入内容"/>
          </div>
      )
  }
  ```

  - 回调函数形式
    - 如果`ref`回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数`null`，然后第二次会传入参数DOM元素。这是因为在每次渲染时会创建一个新的函数实例，所以React清空旧的`ref`并且设置新的。通过将`ref`的回调函数定义成class的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

  ```react
  // 内联形式
  showData1 = () => {
      const { input1 } = this;
      alert(input1.value);
  }
  
  render() {
      return (
          <div>
              <input ref={c => this.input1 = c} type="text" onBlur={this.showData1} placeholder="请输入内容"/>
          </div>
      )
  }
  
  // 类绑定函数
  showData1  = () => {
      const {input1} = this;
      alert(input1.value);
  }
  
  saveInput = (c) => {
      this.input1 = c;
      console.log(c);
  }
  
  render() {
      return (
          <div>
              <input ref={this.saveInput} type="text" placeholder="请输入内容"/>
              <button onClick={this.showData1}>点击显示左侧内容</button>
          </div>
      )
  }
  ```

  - createRef

  ```react
  myRef = React.createRef(); // 一个creatRef对应一个ref
  
  showData1  = () => {
      alert(this.myRef.current.value);
  }
  
  render() {
      return (
          <div>
              <input ref={this.myRef} type="text" onBlur={this.showData1} placeholder="请输入内容"/>
          </div>
      )
  }
  ```

### 组件的生命周期

<img src="https://gitee.com/iera2yy/typora_pic/raw/master/react生命周期(旧).png" alt="react生命周期(旧)" style="zoom:70%;" />

- 初始化阶段：由`ReactDOM.render()`触发---初次渲染
  - constructor()
  - componentWillMount()
  - render()
  - componentDidMount() ====> 常用，开启监听，发送ajax请求，例如：开启定时器、发送网络请求、订阅消息
- 更新阶段：由组件内部`this.setState()`或父组件`render`触发
  - shouldComponentUpdate()
  - componentWillUpdate()
  - render()  ===> 必用，初始化渲染或更新渲染调用
  - componentDidUpdate()
- 卸载组件：由`ReactDOM.unmountComponentAtNode()`触发
  - componentWillUnmount() ===> 常用，一般做收尾，例如：关闭定时器、取消订阅消息

<img src="https://gitee.com/iera2yy/typora_pic/raw/master/react生命周期(新).png" alt="react生命周期(新)" style="zoom: 50%;" />

- 初始化阶段：由`ReactDOM.render()`触发---初次渲染
  - constructor()
  - getDerivedStateFromProps()
  - render()
  - componentDidMount() ====> 常用，开启监听，发送ajax请求，例如：开启定时器、发送网络请求、订阅消息
- 更新阶段：由组件内部`this.setState()`或父组件重新`render`触发
  - getDerivedStateFromProps()
  - shouldComponentUpdate()
  - render()  ===> 必用，初始化渲染或更新渲染调用
  - getSnapshotBeforeUpdate()
  - componentDidUpdate()
- 卸载组件：由`ReactDOM.unmountComponentAtNode()`触发
  - componentWillUnmount() ===> 常用，一般做收尾，例如：关闭定时器、取消订阅消息

### 脚手架

- index.html说明

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <!-- %PUBLIC_URL%代表public文件夹的路径 -->
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <!-- 开启一个理想的视口，用于做移动端网页的适配 -->
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <!-- 用于配置浏览器页签 + 地址栏的颜色(仅支持安卓手机浏览器) -->
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <!-- 用于指定网页添加到手机主屏幕后的图标 -->
    <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
    <!-- 应用加壳时的配置文件 -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <title>React App</title>
  </head>
  <body>
    <!-- 若浏览器不支持js则显示标签中的内容 -->
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>

```

### 路由

- 基本使用
  - 明确界面的导航区和展示区
  - 导航区的a标签改为Link标签`<Link to="/xxxx">Demo</Link>`
  - 展示区写Route标签进行路径匹配`<Route path='/xxxx' component={Demo}/>`
  - `<App/>`最外侧包裹一个`<BrowserRouter>`或`<HashRouter>`
- 路由组件和一般组件
  - 写法不同
    - 一般组件：`<Demo/>`
    - 路由组件：`<Route path="/demo" component={Demo}>`
  - 存放位置不同
    - 一般组件：components
    - 路由组件：pages
  - 接收到的props不同
    - 一般组件：写组件标签时传递了什么，就能收到什么
    - 路由组件：接收到三个固定的属性
      - history
        - go(n)
        - goBack()
        - goForward()
        - push(path, state)
        - replace(path, state)
      - location
        - pathname ""
        - search ""
        - state {}
      - match
        - params {}
        - path ""
        - url ""
- 多级路径刷新页面样式丢失
  - public/index.html中引入样式时不写`./`，而写`/`（常用）
  - public/index.html中引入样式时不写`./`，而写`%PUBLIC_URL%`（常用）
  - 使用HashRouter
- 严格匹配和模糊匹配
  - 默认使用的是模糊匹配，最左匹配
  - 开启严格匹配`<Route exact={true}>`
  - 严格匹配不要随意开启，有时候开启会导致无法进行匹配二级路由
- 嵌套路由
  - 注册子路由时要写上父路由的path值
  - 路由的匹配是按照注册路由的顺序进行的
- 路由传递参数
  - params参数
    - 路由链接(携带参数)：`<Link to={``/home/${msgObj.id}/${msgObj.title}``}>{msgObj.title}</Link> `
    - 注册路由(声明接收)：`<Route path="/home/:id/:title" component={Detail}/>`
    - 接收参数：`this.props.match.params`
  - search参数
    - 路由链接(携带参数)：`<Link to={``/home/?id=${msgObj.id}&title=${msgObj.title}``}>{msgObj.title}</Link>`
    - 注册路由(声明接收)：`<Route path="/home" component={Detail}/> `
    - 接收参数：`this.props.location.search`
    - 备注：获取到的search是urlencoded编码字符串，需借助querystring解析
  - state参数
    - 路由链接(携带参数)：`<Link to={{pathname: '/home', state: {id: msgObj.id, title: msgObj.title}}}>{msgObj.title}</Link>`
    - 注册路由(声明接收)：`<Route path="/home" component={Detail}/>`
    - 接收参数：`this.props.location.state`
- 编程式路由导航
  - 借助`this.props.history`对象上的api对操作路由跳转、前进、后退
    - this.props.history.push()
    - this.props.history.replace()
    - this.props.history.goBack()
    - this.props.history.goForward()
    - this.props.history.go()
- BrowserRouter与HashRouter的区别
  - 底层原理
    - BrowserRouter使用的是H5的history API，不兼容IE9及以下版本
    - HashRouter使用的是URL的哈希值
  - url表现形式
    - BrowserRouter的路径中没有#，例如：localhost:3000/demo/test
    - HashRouter的路径包含#，例如：localhost:3000/#/demo/test
  - 刷新后对路由state参数的影响
    - BrowserRouter没有影响，因为state保存在history对象中
    - HashRouter刷新后会导致路由state参数的丢失
  - 备注：HashRouter可以用于解决一些路径错误相关的问题

### JSX

- 语法规则
  - 定义虚拟DOM时，不要写引号
  - 标签中混入JS表达式时要用{}
  - 样式的类名指定不要用class，要用className
  - 内联样式要用`style={{key: value}}`的形式去写
  - 只有一个根标签
  - 标签必须闭合
  - 标签首字母
    - 若小写字母开头，则将该标签转为html中同名元素，若html中无该标签对应的同名元素，则报错
    - 若大写字母开头，react就去渲染对应的组件，若组件没有定义，则报错

### Fiber

> Virtual DOM 层，描述页面长什么样。
>
> Reconciler 层，负责调用组件生命周期方法，进行 Diff 运算等。Fiber所处层
>
> Renderer 层，根据不同的平台，渲染出相应的页面，比较常见的是 ReactDOM 和 ReactNative。
>
> const fiber = {
>  stateNode,    // 节点实例
>  child,             // 子节点
>  sibling,          // 兄弟节点
>  return,          // 父节点
> }

- 优先级高的任务（如键盘输入）可以打断优先级低的任务（如Diff）的执行，从而更快的生效。任务的优先级有六种：
  - synchronous，与之前的Stack Reconciler操作一样，同步执行
  - task，在next tick之前执行
  - animation，下一帧之前执行
  - high，在不久的将来立即执行
  - low，稍微延迟执行也没关系
  - offscreen，下一次render时或scroll时才执行
- Fiber Reconciler 在执行过程
  - 阶段一(Reconciliation Phase)，生成 Fiber 树，得出需要更新的节点信息。这一步是一个渐进的过程，可以被打断。
  - 阶段二(Commit Phase)，将需要更新的节点一次过批量更新，这个过程不能被打断。
- 页面卡顿
  - 在页面元素很多，且需要频繁刷新的场景下，React15会出现掉帧的现象。
  - 其根本原因，是大量的同步计算任务阻塞了浏览器的 UI 渲染。默认情况下，JS 运算、页面布局和页面绘制都是运行在浏览器的主线程当中，他们之间是互斥的关系。如果 JS 运算持续占用主线程，页面就没法得到及时的更新。
  - 破解JavaScript中同步操作时间过长的方法其实很简单——分片，维护每一个分片的数据结构，就是Fiber。
- 运行流程
  - Fiber 树在首次渲染的时候会一次过生成。
  - 在后续需要 Diff 的时候，会根据已有树和最新 Virtual DOM 的信息，生成一棵新的树。这颗新树每生成一个新的节点，都会将控制权交回给主线程，去检查有没有优先级更高的任务需要执行。
  - 如果没有，则继续构建树的过程；如果过程中有优先级更高的任务需要进行，则 Fiber Reconciler会丢弃正在生成的树，在空闲的时候再重新执行一遍。

### diff

- 相同点
  - vue和react的diff算法，都是忽略跨级比较，只做同级比较。vue diff时调动patch函数，参数是vnode和oldVnode，分别代表新旧节点。
- 不同点
  - vue对比节点。当节点元素相同，但是classname不同，认为是不同类型的元素，删除重建，而react认为是同类型节点，只是修改节点属性。
  - vue的列表对比，采用的是两端到中间比对的方式，而react采用的是从左到右依次对比的方式。当一个集合只是把最后一个节点移到了第一个，react会把前面的节点依次移动，而vue只会把最后一个节点移到第一个。总体上，vue的方式比较高效。
- tree diff
  - 基于策略一，React 对树的算法进行了简洁明了的优化，即对树进行分层比较，两棵树只会对同一层次的节点进行比较。当出现节点跨层级移动时，并不会出现想象中的移动操作，而是以 A 为根节点的树被整个重新创建。
  - 在开发组件时，保持稳定的 DOM 结构会有助于性能的提升。例如，可以通过 CSS 隐藏或显示节点，而不是真的移除或添加 DOM 节点。

![img](https://gitee.com/iera2yy/typora_pic/raw/master/0c08dbb6b1e0745780de4d208ad51d34_1440w.png)

- component diff
  - 如果是同一类型的组件，按照原策略继续比较 virtual DOM tree。
  - 如果不是，则将该组件判断为 dirty component，从而替换整个组件下的所有子节点。
  - 对于同一类型的组件，有可能其 Virtual DOM 没有任何变化，如果能够确切的知道这点那可以节省大量的 diff 运算时间，因此 React 允许用户通过 shouldComponentUpdate() 来判断该组件是否需要进行 diff。
- element diff
  - 当节点处于同一层级时，React diff 提供了三种节点操作，分别为：INSERT_MARKUP（插入）、MOVE_EXISTING（移动）和 REMOVE_NODE（删除）。

### Hooks

- React Hook/Hooks是什么?

```
(1). Hook是React 16.8.0版本增加的新特性/新语法
(2). 可以让你在函数组件中使用 state 以及其他的 React 特性
```

- 三个常用的Hook

```
(1). State Hook: React.useState()
(2). Effect Hook: React.useEffect()
(3). Ref Hook: React.useRef()
```

- State Hook

```
(1). State Hook让函数组件也可以有state状态, 并进行状态数据的读写操作
(2). 语法: const [xxx, setXxx] = React.useState(initValue)  
(3). useState()说明:
        参数: 第一次初始化指定的值在内部作缓存
        返回值: 包含2个元素的数组, 第1个为内部当前状态值, 第2个为更新状态值的函数
(4). setXxx()2种写法:
        setXxx(newValue): 参数为非函数值, 直接指定新的状态值, 内部用其覆盖原来的状态值
        setXxx(value => newValue): 参数为函数, 接收原本的状态值, 返回新的状态值, 内部用其覆盖原来的状态值
```

- Effect Hook

```
(1). Effect Hook 可以让你在函数组件中执行副作用操作(用于模拟类组件中的生命周期钩子)
(2). React中的副作用操作:
        发ajax请求数据获取
        设置订阅 / 启动定时器
        手动更改真实DOM
(3). 语法和说明: 
        useEffect(() => { 
          // 在此可以执行任何带副作用操作
          return () => { // 在组件卸载前执行
            // 在此做一些收尾工作, 比如清除定时器/取消订阅等
          }
        }, [stateValue]) // 如果指定的是[], 回调函数只会在第一次render()后执行；如果不指定则每次渲染都调用
    
(4). 可以把 useEffect Hook 看做如下三个函数的组合
      componentDidMount()
      componentDidUpdate()
      componentWillUnmount() 
```

- Ref Hook

```
(1). Ref Hook可以在函数组件中存储/查找组件内的标签或任意其它数据
(2). 语法: const refContainer = useRef()
(3). 作用:保存标签对象,功能与React.createRef()一样
```

- `useContext`用于在函数组件内部获取Context存储的状态。
- `useReducer`用于管理复杂的数据结构（useState一般用于管理扁平结构的状态），基本实现了redux的核心功能。
- 性能优化相关Hooks API
  - `useCallback`本质是对函数进行依赖分析，依赖变更时才重新执行。
  - `useMemo`React.memo对每一个 props 项进行浅对比，如果引用没有变化，就不会触发重渲染，相比React.memo，useMemo在组件内部调用，可以访问组件的props和state，所以它拥有更细粒度的依赖控制。
- `useLayoutEffect`用法和useEffect一致，与useEffect的差别是执行时机，useLayoutEffect是在浏览器绘制节点之前执行。
- 非useRef相关的Hooks API，本质上都形成了闭包，闭包有自己独立的状态，这就是Capture Values的本质。

### Redux

- Redux是JavaScript状态容器，使每个State变化可预测，动作与状态统一管理。

  - 某个组件的状态，需要让其他组件随时拿到（共享）
  - 一个组件需要改变另一个组件的状态（通信）

- 三大原则

  - **单一数据源**，整个应用的state被储存在一棵object tree中，并且这个object tree只存在于唯一一个store中。
  - **State是只读的**，唯一改变state的方法就是触发action，action是一个用于描述已发生事件的普通对象。
  - **使用纯函数来执行修改**，为了描述action如何改变state tree，需要编写reducers。

  > **纯函数**
  >
  > 不得改写参数
  >
  > 不能调用系统 I/O 的API
  >
  > 不能调用Date.now()或者Math.random()等不纯的方法，因为每次会得到不一样的结果

- 用户交互工作流程

  - 首先，用户（通过View）发出Action，发出方式就用到了dispatch方法。
  - 然后，Store自动调用Reducer，并且传入两个参数：当前State和收到的Action，Reducer会返回新的State。
  - State一旦有变化，Store就会调用监听函数，来更新View。

- 三大核心概念

  - action
    - 动作的对象
    - 包含2个属性
      - type：标识属性，值为字符串，唯一，必要属性
      - data：数据属性，值类型任意，可选属性
    - 例子 `{ type: 'ADD_STUDENT', data: { name: 'Tom', age:18 } }`
  - reducer
    - 用于初始化状态、加工状态
    - 加工时，根据旧的state和action，产生新的state的纯函数
  - store
    - 将state、action、reducer联系在一起的对象
    - 如何得到此对象
      - import {createStore} from 'redux'
      - Import reducer from './reducers'
      - const store = createStore(reducer)
    - 此对象的功能
      - getState()得到state
      - dispatch(action)分发action，触发reducer调用，产生新的state
      - subscribe(listener)注册监听，当产生了新的state时，自动调用

<img src="https://gitee.com/iera2yy/typora_pic/raw/master/redux原理图.png" alt="redux原理图" style="zoom: 33%;" />

<img src="https://gitee.com/iera2yy/typora_pic/raw/master/react-redux模型图.png" alt="react-redux模型图" style="zoom: 50%;" />

### setState

- setState(stateChange, [callback])------对象式的setState，对象式的setState是函数式的setState的简写方式(语法糖)
  - stateChange为状态改变对象(该对象可以体现出状态的更改)
  - callback是可选的回调函数, 它在状态更新完毕、界面也更新后(render调用后)才被调用
- setState(updater, [callback])------函数式的setState
  - updater为返回stateChange对象的函数
  - updater可以接收到state和props
  - callback是可选的回调函数, 它在状态更新、界面也更新后(render调用后)才被调用
- 使用原则：
  - 如果新状态不依赖于原状态 ===> 使用对象方式
  - 如果新状态依赖于原状态 ===> 使用函数方式
  - 如果需要在setState()执行后获取最新的状态数据，要在第二个callback函数中读取

```jsx
// react异步机制（出于性能）
state = { count: 0 }

this.setState({count: count + value * 1}, () =>{
    console.log('$$$$$', this.state.count)				// $$$$$ 1
})
console.log('@@@@@', this.state.count)						// @@@@@ 0
```

- 同步方式

```jsx
state = {
    number:1
};

// 回调函数
componentDidMount(){
    this.setState({number:3}, () => {
        console.log(this.state.number) // 3
    })
}

// setTimeout
componentDidMount(){
    setTimeout(() => {
      this.setState({number:3})
      console.log(this.state.number) // 3
    }, 0)
}

// 原生事件中修改状态
componentDidMount() {
    document.body.addEventListener('click', this.changeVal, false);
}
changeVal = () => {
    this.setState({
      number: 3
    })
    console.log(this.state.number)   // 3
}
```

- 连续两次调用合并问题

```jsx
state = { index: 0 }
// 对象式setState会被合并
componentDidMount() {
  this.setState({ index: this.state.index + 1 }, () => {
  	console.log(this.state.index);												// 1
  })
  this.setState({ index: this.state.index + 1 }, () => {
  	console.log(this.state.index);												// 1
  })
}
// 函数式setState不会被合并
componentDidMount() {
  this.setState(preState => ({ index: preState.index + 1 }), () => {
    console.log(this.state.index);												// 2
  })
  this.setState(preState => ({ index: preState.index + 1 }), () => {
    console.log(this.state.index);												// 2
  })
}
```

### useState

```javascript
import React from "react";
class Point extends React.Component<any, any> {
  constructor(props) {
    super(props);
    this.state = {
      number: 0
    };
  }
  handleClick = () => {
    for (let i = 0; i < 5; i++) {
      setTimeout(() => {
        this.setState({ number: this.state.number + 1 });
        console.log(this.state.number);	// 1 2 3 4 5
      }, 1000);
    }
  };
  render() {
    return (
      <div>
        <button onClick={this.handleClick}>num++</button>
      </div>
    );
  }
}

export default Point;

import React from "react";
const Point: React.FC = () => {
  const [num, setNumber] = React.useState(0);

  const handleClick = () => {
    for (let i = 0; i < 5; i++) {
      setTimeout(() => {
        setNumber(num + 1);	// setNumber(n => n + 1)   n = 0 1 2 3 4
        console.log(num);	// 0 0 0 0 0
      }, 1000);
    }
  };

  return (
    <div>
      <button onClick={handleClick}>num</button>
    </div>
  );
};

export default Point;
```

### lazyLoad

```jsx
//1.通过React的lazy函数配合import()函数动态加载路由组件 ===> 路由组件代码会被分开打包
const Login = lazy(()=>import('@/pages/Login'))

//2.通过<Suspense>指定在加载得到路由打包文件前显示一个自定义loading界面
<Suspense fallback={<h1>loading.....</h1>}>
    <Switch>
        <Route path="/xxx" component={Xxxx}/>
        <Redirect to="/login"/>
    </Switch>
</Suspense>
```

### Fragment

	<Fragment><Fragment> // 可以传参
	<></>								 // 不能传参

> 渲染时不会多套一层div

### Context

> 一种组件间通信方式, 常用于【祖组件】与【后代组件】间通信

```js
1) 创建Context容器对象：
const XxxContext = React.createContext()

2) 渲染子组时，外面包裹xxxContext.Provider, 通过value属性给后代组件传递数据：
<xxxContext.Provider value={数据}>
  子组件
</xxxContext.Provider>

3) 后代组件读取数据：

//第一种方式:仅适用于类组件 
static contextType = xxxContext  // 声明接收context
this.context // 读取context中的value数据

//第二种方式: 函数组件与类组件都可以
<xxxContext.Consumer>
  {
  value => ( // value就是context中的value数据
  要显示的内容
  )
}
</xxxContext.Consumer>
```

	在应用开发中一般不用context, 一般都用它的封装react插件

### PureComponent

- Component的2个问题 

> 1. 只要执行setState(),即使不改变状态数据, 组件也会重新render() ==> 效率低
>
> 2. 只当前组件重新render(), 就会自动重新render子组件，纵使子组件没有用到父组件的任何数据 ==> 效率低

- 效率高的做法

>  只有当组件的state或props数据发生改变时才重新render()

- 原因

>  Component中的shouldComponentUpdate()总是返回true

	办法1: 
		重写shouldComponentUpdate()方法
		比较新旧state或props数据, 如果有变化才返回true, 如果没有返回false
	办法2:  
		使用PureComponent
		PureComponent重写了shouldComponentUpdate(), 只有state或props数据有变化才返回true
		注意: 
			只是进行state和props数据的浅比较, 如果只是数据对象内部数据变了, 返回false  
			不要直接修改state数据, 而是要产生新数据
	项目中一般使用PureComponent来优化

### render props

- 如何向组件内部动态传入带内容的结构(标签)?

  Vue中: 
  	使用slot技术, 也就是通过组件标签体传入结构  <A><B/></A>
  React中:
  	使用children props: 通过组件标签体传入结构
  	使用render props: 通过组件标签属性传入结构,而且可以携带数据，一般用render函数属性

- children props

  <A>
    <B>xxxx</B>
  </A>
  {this.props.children}
  问题: 如果B组件需要A组件内的数据, ==> 做不到 

- render props

  <A render={(data) => <C data={data}></C>}></A>
  A组件: {this.props.render(内部state数据)}
  C组件: 读取A组件传入的数据显示 {this.props.data} 

### 错误处理

> 错误边界(Error boundary)：用来捕获后代组件错误，渲染出备用页面
>
> 只能捕获后代组件生命周期产生的错误，不能捕获自己组件产生的错误和其他组件在合成事件、定时器中产生的错误

```js
// 生命周期函数，一旦后台组件报错，就会触发
static getDerivedStateFromError(error) {
    console.log(error);
    // 在render之前触发
    // 返回新的state
    return {
        hasError: true,
    };
}

componentDidCatch(error, info) {
    // 统计页面的错误。发送请求发送到后台去
    console.log(error, info);
}
```

## NodeJS

### cluster

- `cluster`模块是node.js中用于实现和管理多进程的模块。常规的node.js应用程序是单线程单进程的，这也意味着它很难充分利用服务器多核CPU的性能，而`cluster`模块就是为了解决这个 问题的，它使得node.js程序可以以多个实例并存的方式运行在不同的进程中，以求更大地榨取服务器的性能。
- node.js的主从模型中，`master`主进程相当于一个包工头，主管监听端口，而`slave`进程被用于实际的任务执行，当任务请求到达后，它会根据某种方式将连接循环分发给worker进程来处理。理论上，如果根据当前各个worker进程的负载状况或者相关信息来挑选工作进程，效率应该比直接循环发放要更高，但node.js文档中声明这种方式由于受到操作系统调度机制的影响，会使得分发变得不稳定，所以会将"**循环法**"作为默认的分发策略。

```javascript
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  console.log(`主进程 ${process.pid} 正在运行`);

  // 衍生工作进程。
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  cluster.on('exit', (worker, code, signal) => {
    console.log(`工作进程 ${worker.process.pid} 已退出`);
  });
} else {
  // 工作进程可以共享任何 TCP 连接。
  // 在本例子中，共享的是 HTTP 服务器。
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end('你好世界\n');
  }).listen(8000);

  console.log(`工作进程 ${process.pid} 已启动`);
}
```

### 模块机制

- CommonJS规范

  - 模块引用，模块上下文提供`require()`方法来引入外部模块。

  ```javascript
  //引入一个模块到当前上下文中
  const math = require('math');
  math.add(1, 2);
  ```

  - 模块定义，模块上下文提供了`exports`对象用于导入导出当前模块的方法或者变量，并且它是唯一的导出出口。模块中存在一个`module`对象，它代表模块自身，`exports`是module的属性。一个文件就是一个模块，将方法作为属性挂载在exports上就可以定义导出的方式。

  ```javascript
  exports.add = function () {
      let sum = 0, i = 0, args = arguments, l = args.length;
      while(i < l) {
          sum += args[i++];
      }
      return sum;
  }
  ```

  - 模块表示，模块标识就是传递给`require()`方法的参数，它必须是符合小驼峰命名的字符串，或者以`.`、`..`开头的相对路径或者绝对路径，可以没有文件后缀名`.js`。

- Node的模块实现

  - 路径分析
  - 文件定位
  - 编译执行
  - 加入内存

- 进程间通信

  - 通过stdin/stdout传递json：最直接的方式，适用于能够拿到“子”进程handle的场景，适用于关联进程之间通信，无法跨机器
  - Node原生IPC支持：最native的方式，比上一种“正规”一些，具有同样的局限性
  - 通过sockets：最通用的方式，有良好的跨环境能力，但存在网络的性能损耗
  - 借助message queue：最强大的方式，既然要通信，场景还复杂，不妨扩展出一层消息中间件，漂亮地解决各种通信问题

# CSS

## 优先级

- `!important`
- 行内样式，通过`style=""`，直接写在标签里
- ID选择器，`#`加名字，ID在html里唯一
- 类选择器，`.`加名字，通过class命名
- 标签，`div{}, p{}, span{}`之类
- 通配符，`*`，`*{}`
- 继承，子标签内继承父标签的样式，`<div style=""><div>子标签</div></div>`
- 浏览器默认样式

## 选择器效率

- ID选择器`#header`
- 类选择器`.box1`
- 标签选择器`div`
- 兄弟选择器`h2 + p`
- 子选择器`li > ul`
- 后代选择器`ul a`
- 通用选择器`*`
- 属性选择器`type = "text"`
- 伪类/伪元素选择器`a:hover`

## 样式隔离

- CSS存在问题

  - 全局污染：CSS 选择器的作用域是全局的，所以很容易引起选择器冲突；而为了避免全局冲突，又会导致类命名的复杂度上升
  - 复用性低：CSS 缺少抽象的机制，选择器很容易出现重复，不利于维护和复用

- CSS模块化

  - Vue，stype标签中加scoped

  - CSS Modules

    - CSS Modules 指的是我们像 import js 一样去引入我们的 css 代码，代码中的每一个类名都是引入对象的一个属性, 编译时会将css类名加上唯一hash。
    - css module 需要 webpack 配置 css-loader 或者 scss-loader , module 为 true
    - 作用域默认为 local 即只在当前模块生效
    - 被:global 包裹起来的类名，不会被模块化

  

```jsx
  // in react
  // index.module.css
  .title{
  	backgound-color: red;
  }
  // import hello from './index.module.css'
  render() {
  	return <h2 className={hello.title}>Hello React</h2>
  }
```

  - CSS in JS

    ```javascript
    const style = {
      width: 200,
      color: '#fff',
      backgroundColor: 'red'
    };
    ```

## BFC

- 具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。
- 触发条件
  - body 根元素
  - 浮动元素：float 除 none 以外的值
  - 绝对定位元素：position (absolute、fixed)
  - display 为 inline-block、table-cells、flex
  - overflow 除了 visible 以外的值 (hidden、auto、scroll)
- 应用
  - 同一个BFC下外边距会发生折叠
  - 清除浮动
  - 阻止元素被浮动元素覆盖

## 清除浮动

```CSS
// 清除浮动
.clearfix::before,
.clearfix::after{
		content: '';
		display: table;
		clear: both;
}
```

## flex

- flex-grow，当子元素的空间小于父元素的空间时，如何处理剩余空间。默认值为0表示不占有剩余空间。当子元素都设置为1时表示平均分配剩余空间。
- flex-shrink，当子元素的空间大于父元素的空间时，如何缩小子元素。默认值为1表示等比缩小。当一个子元素为0其余子元素为1时，表示为0的子元素不缩小，其余元素缩小。
- flex-basis，用于设置项目占据的主轴空间，设置为auto表示项目占据的主轴大小等于项目的实际内容大小，设置为固定值表示项目占据的主轴大小等于固定值。flex-basis优先级高于width，当flex-basis设置为auto时，弹性盒宽度为width的值。

## sticky

- 不会触发BFC
- `z-index`无效
- 当父元素的`height：100%`时，页面滑动到一定高度之后sticky属性会失效
- 父元素不能有`overflow: hidden`或者`overflow: auto`属性
- 父元素高度不能低于sticky高度，必须指定`top`、`bottom`、`left`、`right`4个值之一

## 属性的继承性

- 可继承的属性
- 不可继承的属性

## 盒模型

- content-box：padding和border不被包含在定义的width和height之内。
  `盒子的实际宽度 = 设置的width + padding + border`
- border-box：padding和border被包含在定义的width和height之内。
  `盒子的实际宽度 = 设置的width（padding和border不会影响实际宽度）`

# 计算机网络基础

## 体系结构

- 五层协议
  - **应用层**，为特定应用程序提供数据传输服务，例如 HTTP、DNS 等协议。数据单位为报文。（电子邮箱协议SMTP；DNS、HTTP）
  - **传输层**，为进程提供通用数据传输服务。运输层包括两种协议：传输控制协议 TCP，提供面向连接、可靠的数据传输服务，数据单位为报文段；用户数据报协议 UDP，提供无连接、尽最大努力的数据传输服务，数据单位为用户数据报。TCP 主要提供完整性服务，UDP 主要提供及时性服务。
  - **网络层**，为主机提供数据传输服务。网络层把传输层传递下来的报文段或者用户数据报封装成分组。（地址解析协议ARP；网络控制报文协议ICMP，ping；虚拟专用网VPN；网络地址转换NAT；路由选择协议RIP、OSPF、BGP）
  - **数据链路层**，主机之间可以有很多链路，链路层协议就是为同一链路的主机提供数据传输服务。数据链路层把网络层传下来的分组封装成帧。
  - **物理层**，考虑的是怎样在传输媒体上传输数据比特流，而不是指具体的传输媒体。物理层的作用是尽可能屏蔽传输媒体和通信手段的差异，使数据链路层感觉不到这些差异。（单工通信、半双工通信、全双工通信）
- OSI
  - **表示层**，数据压缩、加密以及数据描述，这使得应用程序不必关心在各台主机中数据内部格式不同的问题。
  - **会话层**，建立及管理会话。
- TCP/IP
  - 只有四层，相当于五层协议中数据链路层和物理层合并为网络接口层。

## TCP

- 拥塞控制算法
  - 慢开始
  - 拥塞避免
  - 快重传
  - 快恢复
- 三次握手
  - 第一次握手：客户端发送SYN=1(初始序列号seq=x)的包到服务器，TCP客户进程进入SYN-SENT(同步已发送)状态。
  - 第二次握手：服务器收到连接请求报文，在确认报文中将SYN和ACK均置为1，确认号ack=x+1，同时选择一个初始序号seq=y，服务器进程进入SYN-RCVD(同步收到)状态。
  - 第三次握手：客户进程收到服务器的确认后，发送确认报文，ACK置1，确认号ack=y+1，序号seq=x+1，客户机进入ESTABLISHED(已建立连接)状态。
  - SYN报文段不能携带数据，ACK报文段可以携带数据。
- 四次挥手
  - 第一次挥手：客户端发送FIN=1(初始序列号seq=u)的包到服务器，TCP客户端进入FIN-WAIT-1(终止等待1)状态。
  - 第二次挥手：服务器收到连接释放报文，发出确认报文，ACK=1，ack=u+1，seq=v，服务器进入CLOSE-WAIT(关闭等待)状态，客户端收到服务器的确认请求后，客户端进入FIN-WAIT-2(终止等待2)状态，等待服务器发送连接释放报文。
  - 第三次挥手：服务器将最后的数据发送完毕后，向客户端发送连接释放报文，FIN=1，ACK=1，ack=u+1，seq=w，服务器进入LAST-ACK(最后确认)状态
  - 第四次挥手：客户端收到服务器的连接释放报文后，对此进行确认，发送ACK=1，ack=w+1，seq=u+1，进入TIME-WAIT(时间等待)状态，经过2MSL(报文最大生存时间)后进入CLOSE状态，服务器收到确认后则直接进入CLOSE状态
  - TIME-WAIT存在理由
    - 可靠地实现TCP全双工连接的终止：A 发 FIN， B 响应 ACK，B 再发 FIN，A 响应 ACK 实现连接的关闭。而如果 A 响应的 ACK 包丢失，B 会以为 A 没有收到自己的关闭请求，然后会重试向 A 再发 FIN 包；
    - 保证网络中迷失的数据包正常过期：如果双方挥手之后，一个网络四元组（src/dst ip/port）被回收，而此时网络中还有一个迟到的数据包没有被 B 接收，A 应用程序又立刻使用了同样的四元组再创建了一个新的连接后，这个迟到的数据包才到达 B，那么这个数据包就会让 B 以为是 A 刚发过来的。
- 当发送IP包时，需要计算IP报头的校验和：
  - 把校验和字段置为0；
  - 对IP头部中的每16bit进行二进制求和；
  - 如果和的高16bit不为0 ，则将和的高16bit和低16bit反复相加，直到和的高16bit为0，从而获得一个16bit的值；
  - 将该16bit的值取反，存入校验和字段。
- **keepAlive机制**：当超过一段时间之后，TCP自动发送一个数据为空的报文给对方，如果对方回应了这个报文，说明对方还在线，链接可以继续保持，如果对方没有报文返回，并且重试了多次之后则认为链接丢失，没有必要保持链接。

## DNS

- 解析顺序
  - **浏览器缓存**，当用户通过浏览器访问某域名时，浏览器首先会在自己的缓存中查找是否有该域名对应的IP地址（若曾经访问过该域名且没有清空缓存便存在）；
  - **系统缓存**，当浏览器缓存中无域名对应IP则会自动检查用户计算机系统Hosts文件DNS缓存是否有该域名对应IP；
  - **路由器缓存**，当浏览器及系统缓存中均无域名对应IP则进入路由器缓存中检查，以上三步均为客户端的DNS缓存；
  - **ISP（互联网服务提供商）DNS缓存**，当在用户客服端查找不到域名对应IP地址，则将进入ISP DNS缓存中进行查询。比如你用的是电信的网络，则会进入电信的DNS缓存服务器中进行查找；
  - **根域名服务器**，当以上均未完成，则进入根服务器进行查询。全球仅有13台根域名服务器，1个主根域名服务器，其余12为辅根域名服务器。根域名收到请求后会查看区域文件记录，若无则将其管辖范围内顶级域名（如.com）服务器IP告诉本地DNS服务器；
  - **顶级域名服务器**，顶级域名服务器收到请求后查看区域文件记录，若无则将其管辖范围内主域名服务器的IP地址告诉本地DNS服务器；
  - **主域名服务器**，主域名服务器接受到请求后查询自己的缓存，如果没有则进入下一级域名服务器进行查找，并重复该步骤直至找到正确纪录；

- 两种查询方式
  1. 递归查询：本机向本地域名服务器发出一次查询请求，就静待最终的结果。如果本地域名服务器无法解析，自己会以DNS客户机的身份向其它域名服务器查询，直到得到最终的IP地址告诉本机。
  2. 迭代查询：本地域名服务器向根域名服务器查询，根域名服务器告诉它下一步到哪里去查询，然后它再去查，每次它都是以客户机的身份去各个服务器查询。

##  HTTP

- 请求类型

  - 简单请求

    > 对于简单请求，浏览器直接发出 CORS 请求。具体来说，就是在头信息之中，增加一个Origin字段。

    - 请求方式：GET、POST、HEAD
    - HTTP头部信息不超过一下几种字段：无自定义头部字段、Accept、Accept-Language、Content-Language、Last-Event-ID、Content-Type（只有三个值application/x-www-form-urlencoded、multipart/form-data、text/pain）

  - 非简单请求

    > 非简单请求是那种对服务器提出特殊要求的请求，比如请求方法是PUT或DELETE，或者Content-Type字段的类型是application/json。

    - 请求方式：PUT、DELETE
    - 自定义头部字段
    - 发送json格式数据
    - 正式通信之前，浏览器会先发送OPTION请求，进行预检，这一次的请求称为“预检请求”
    - 服务器成功响应预检请求后，才会发送真正的请求，并且携带真实数据

- **keep-alive机制**：若开启后，在一次http请求中，服务器进行响应后，不再直接断开TCP连接，而是将TCP连接维持一段时间。在这段时间内，如果同一客户端再次向服务端发起http请求，便可以复用此TCP连接，向服务端发起请求，并重置timeout时间计数器，在接下来一段时间内还可以继续复用。

- **keep-alive和keepAlive的区别**：http keep-alive是为了让tcp活得更久一点，以便在同一个连接上传送多个http，提高socket的效率。而tcp keepAlive是TCP的一种检测TCP连接状况的保鲜机制。

- 一次完整的http请求过程

  - 域名解析
  - 发起TCP的3次握手
  - 建立TCP连接后发起http请求
  - 服务器响应http请求，浏览器得到html代码
  - 浏览器解析html代码，并请求html代码中的资源

- 状态码

  - 1XX 信息状态码
    - 100（继续）请求者应当继续提出请求。 服务器返回此代码表示已收到请求的第一部分，正在等待其余部分。
    - 101（切换协议）请求者已要求服务器切换协议，服务器已确认并准备切换。
  - 2XX 成功状态码
    - 200（成功）服务器已成功处理了请求。 通常，这表示服务器提供了请求的网页。 
    - 201（已创建）请求成功并且服务器创建了新的资源。 
    - 202（已接受） 服务器已接受请求，但尚未处理。 
  - 3XX 重定向状态码
    - 301 永久重定向，Location响应首部的值仍为当前URL，因此为隐藏重定向
    - 302 临时重定向，显式重定向，Location响应首部的值为新的URL
    - 304 Not Modified 未修改，比如本地缓存的资源文件和服务器上比较，发现并没有修改，`If-None-Match/If-Modified-Since`
      - Last-Modified和HTTP-IF-MODIFIED-SINCE只判断资源的最后修改时间，而ETags和If-None-Match可以是资源任何的任何属性，比如资源的MD5等。
  - 4XX 客户端错误状态码
    - 400（错误请求）服务器不理解请求的语法。 
    - 403（禁止）服务器拒绝请求。
    - 404（未找到）服务器找不到请求的网页，请求的URL资源并不存在。
    - 408（请求超时）服务器等候请求时发生超时。 
  - 5XX 服务器端错误状态码
    - 500 Internal Server Error 服务器内部错误
    - 502 Bad Gateway 前面代理服务器联系不到后端服务器时出现
    - 503 Service Unavailable 服务不可用
    - 504 Gateway Timeout 代理能联系到后端服务器，但后端服务器在规定时间内没有给代理服务器响应

- Token流程

  1. 客户端使用用户名跟密码请求登录
  2. 服务端收到请求，去验证用户名与密码
  3. 验证成功后，服务端会签发一个 token 并把这个 token 发送给客户端
  4. 客户端收到 token 以后，会把它存储起来，比如放在 cookie 里或者 localStorage 里
  5. 客户端每次向服务端请求资源的时候需要带着服务端签发的 token
  6. 服务端收到请求，然后去验证客户端请求里面带着的 token ，如果验证成功，就向客户端返回请求的数据

- Cookie和Session的区别

  - **安全性**： Session 比 Cookie 安全，Session 是存储在服务器端的，Cookie 是存储在客户端的。
  - **存取值的类型不同**：Cookie 只支持存字符串数据，想要设置其他类型的数据，需要将其转换成字符串，Session 可以存任意数据类型。
  - **有效期不同**： Cookie 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭（默认情况下）或者 Session 超时都会失效。
  - **存储大小不同**： 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie，但是当访问量过多，会占用过多的服务器资源。

- Token和Session的区别

  - Session 是一种记录服务器和客户端会话状态的机制，使服务端有状态化，可以记录会话信息。而 Token 是令牌，访问资源接口（API）时所需要的资源凭证。Token 使服务端无状态化，不会存储会话信息。
  - Session 和 Token 并不矛盾，作为身份认证 Token 安全性比 Session 好，因为每一个请求都有签名还能防止监听以及重放攻击，而 Session 就必须依赖链路层来保障通讯安全了。如果你需要实现有状态的会话，仍然可以增加 Session 来在服务器端保存一些状态。
  - 所谓 Session 认证只是简单的把 User 信息存储到 Session 里，因为 SessionID 的不可预测性，暂且认为是安全的。而 Token ，如果指的是 OAuth Token 或类似的机制的话，提供的是 认证 和 授权 ，认证是针对用户，授权是针对 App 。其目的是让某 App 有权利访问某用户的信息。这里的 Token 是唯一的。不可以转移到其它 App上，也不可以转到其它用户上。Session 只提供一种简单的认证，即只要有此 SessionID ，即认为有此 User 的全部权利。是需要严格保密的，这个数据应该只保存在站方，不应该共享给其它网站或者第三方 App。所以简单来说：如果你的用户数据可能需要和第三方共享，或者允许第三方调用 API 接口，用 Token 。如果永远只是自己的网站，自己的 App，用什么就无所谓了。

- Token和Cookie

  - token相对cookie的优势
    - 支持跨域访问 ，将token置于请求头中，而cookie是不支持跨域访问的；
    - 无状态化， 服务端无需存储token ，只需要验证token信息是否正确即可，而session需要在服务端存储，一般是通过cookie中的sessionID在服务端查找对应的session；
    - 无需绑定到一个特殊的身份验证方案（传统的用户名密码登陆），只需要生成的token是符合我们预期设定的即可；
    - 更适用于移动端 （Android，iOS，小程序等等），像这种原生平台不支持cookie，比如说微信小程序，每一次请求都是一次会话，当然我们可以每次去手动为他添加cookie；
    - 避免CSRF跨站伪造攻击 ，还是因为不依赖cookie；
    - 非常适用于RESTful API ,这样可以轻易与各种后端（java，.net，python…）相结合，去耦合。

- Token和JWT(JSON Web Token)

  - 相同点
    - 都是访问资源的令牌
    - 都可以记录用户的信息
    - 都是使服务端无状态化
    - 都是只有验证成功后，客户端才能访问服务端上受保护的资源
  - 区别
    - Token：服务端验证客户端发送过来的 Token 时，还需要查询数据库获取用户信息，然后验证 Token 是否有效。
    - JWT：将 Token 和 Payload 加密后存储于客户端，服务端只需要使用密钥解密进行校验（校验也是 JWT 自己实现的）即可，不需要查询或者减少查询数据库，因为 JWT 自包含了用户信息和加密的数据。

- localStorage和sessionStorage

  - sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问，并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅是会话级别的存储，只允许同一窗口访问。
  - localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。同源可以读取并修改localStorage数据。

- CSRF和XSS

  - CSRF 跨域请求伪造cross-site request forgery
    - 要完成一次CSRF攻击
      1. 登录受信任网站A，并在本地生成Cookie
      2. 在不登出A的情况下，访问危险网站B
    - 防御策略
      - 验证HTTP Referer字段
      - 在请求地址中添加token并验证
      - 在HTTP头中自定义属性并验证
  - XSS 跨站脚本攻击 cross site scripting
    - 危害
      - 窃取网站浏览中的cookie值
      - 劫持流量实现恶意跳转
    - 反射型XSS，常见的是：恶意连接
    - 存储型XSS，常见的是：将恶意代码上传或存储到服务器
    - DOM-based型XSS
    - Cookie安全策略：
      - http-only，使用document.cookie就无法读取；
      - secure-only，仅https协议中发送；
      - host-only，标注了domain字段的值为`www.test1.com`，下次请求时请求头中的Origin字段就必须得是`www.test1.com`，响应才会返回由HostOnly标注的数据。
    - 防御方式：
      - 存储型和反射型：改成纯前端渲染，把代码和数据分隔开；对 HTML 做充分转义。
      - DOM型：避免在字符串中拼接不可信数据。
      - 转义掉`<`、`>`、`'`、`"`、`\`，或者对数据进行`JSON.stringify`

- HTTPS

  - HTTP + 加密 + 认证 + 完整性保护 = HTTPS，即HTTP + SSL/TLS
  - 传统的HTTP协议通信：传统的HTTP报文是直接将报文信息传输到TCP然后TCP再通过TCP套接字发送给目的主机上。
  - HTTPS协议通信：HTTPS是HTTP报文直接将报文信息传输给SSL套接字进行加密，SSL加密后将加密后的报文发送给TCP套接字，然后TCP套接字再将加密后的报文发送给目的主机，目的主机将通过TCP套接字获取加密后的报文给SSL套接字，SSL解密后交给对应进程。

  <img src="https://gitee.com/iera2yy/typora_pic/raw/master/03644FBDEDCF7F3AE5A1CF5B50E3C17D.jpg" alt="03644FBDEDCF7F3AE5A1CF5B50E3C17D" style="zoom:70%;" />

- HTTP1.1

  - 缓存处理，引入更多的缓存控制策略
  - 宽带优化及网络连接的使用
  - 错误通知的管理，新增错误状态响应码
  - Host头处理，适应虚拟主机技术
  - 长连接，默认开启keep-alive

- HTTP2.0

  - 二进制分帧协议
  - 多路复用
  - 头部压缩
  - 服务器推送
  - 流控

- HTTP3.0

  - QUIC协议用于解决TCP协议中的问题
    - 多次握手：TCP 协议需要三次握手建立连接，而如果需要 TLS 证书的交换，那么则需要更多次的握手才能建立可靠连接，这在如今长肥网络的趋势下是一个巨大的痛点
    - 队头阻塞：TCP 协议下，如果出现丢包，则一条连接将一直被阻塞等待该包的重传，即使后来的数据包可以被缓存，但也无法被递交给应用层去处理。
    - 无法判断一个 ACK 是重传包的 ACK 还是原本包的 ACK：比如 一个包 seq=1, 超时重传的包同样是 seq=1，这样在收到一个 ack=1 之后，我们无法判断这个 ack 是对之前的包的 ack 还是对重传包的 ack，这会导致我们对 RTT 的估计出现误差，无法提供更准确的拥塞控制
    - 无法进行连接迁移：一条连接由一个四元组标识，在当今移动互联网的时代，如果一台手机从一个 wifi 环境切换到另一个 wifi 环境，ip 发生变化，那么连接必须重新建立，inflight 的包全部丢失。

- websocket

  - HTTP协议有一个的缺陷为：通信只能由客户端发起。在一些场景下，这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。我们只能使用轮询：每隔一段时候，就发出一个询问，了解服务器有没有新的信息，轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）。最典型的场景就是聊天室。
  - Webscoket是Web浏览器和服务器之间的一种全双工通信协议，其中WebSocket协议由IETF定为标准，WebSocket API由W3C定为标准。一旦Web客户端与服务器建立起连接，之后的全部数据通信都通过这个连接进行。通信过程中，可互相发送JSON、XML、HTML或图片等任意格式的数据。
  - 与HTTP协议相比
    - 相同点
      - 都是基于TCP的应用层协议；
      - 都使用Request/Response模型进行连接的建立；
      - 在连接的建立过程中对错误的处理方式相同，在这个阶段WS可能返回和HTTP相同的返回码；
      - 都可以在网络中传输数据。
    - 不同点
      - WS使用HTTP来建立连接，但是定义了一系列新的header域，这些域在HTTP中并不会使用；
      - WS的连接不能通过中间人来转发，它必须是一个直接连接；
      - WS连接建立之后，通信双方都可以在任何时刻向另一方发送数据；
      - WS连接建立之后，数据的传输使用帧来传递，不再需要Request消息；
      - WS的数据帧有序。

# 操作系统基础

- 基本特征
  - 并发
  - 共享
  - 虚拟
  - 异步
- 基本功能
  - 进程管理
  - 内存管理
  - 文件管理
  - 设备管理

## 中断分类

- **外中断**，由 CPU 执行指令以外的事件引起，如 I/O 完成中断，表示设备输入/输出处理已经完成，处理器能够发送下一个输入/输出请求。此外还有时钟中断、控制台中断等。
- **异常**，由 CPU 执行指令的内部事件引起，如非法操作码、地址越界、算术溢出等。
- **陷入**，在用户程序中使用系统调用。

## 进程通信

- **管道**，只支持半双工通信（单向交替传输）；只能在父子进程或者兄弟进程中使用。
- **命名管道（FIFO）**，去除了管道只能在父子进程中使用的限制。
- **消息队列**，消息队列可以独立于读写进程存在，从而避免了 FIFO 中同步管道的打开和关闭时可能产生的困难；避免了 FIFO 的同步阻塞问题，不需要进程自己提供同步方法；读进程可以根据消息类型有选择地接收消息，而不像 FIFO 那样只能默认地接收。
- **信号量**，它是一个计数器，用于为多个进程提供对共享数据对象的访问。
- **共享内存**，允许多个进程共享一个给定的存储区。因为数据不需要在进程之间复制，所以这是最快的一种 IPC。需要使用信号量用来同步对共享存储的访问。
- **嵌套字**，与其它通信机制不同的是，它可用于不同机器间的进程通信。

## 死锁

- 必要条件：互斥、占有和等待、不可抢占、环路等待。
- 处理方法：鸵鸟策略、死锁检测与死锁恢复（抢占、回滚、杀死进程）、死锁预防（运行之前）、死锁避免（运行时）。

## 内存管理

- **虚拟内存**，为了让物理内存扩充成更大的逻辑内存，从而让程序获得更多的可用内存。（分页式，将地址空间划分成固定大小的页，每一页再与内存进行映射）。
- **分页系统地址映射**，内存管理单元（MMU）管理着地址空间和物理内存的转换，其中的页表（Page table）存储着页（程序地址空间）和页框（物理内存空间）的映射表。
- **页面置换算法**，最佳（OPT，存在于理论）、最近最久未使用（LRU）、先进先出（FIFO）、第二次机会算法、时钟（Clock）。
- **分段式**，把每个表分成段，一个段构成一个独立的地址空间。每个段的长度可以不同，并且可以动态增长。
- **段页式**，程序的地址空间划分成多个拥有独立地址空间的段，每个段上的地址空间划分成大小相同的页。这样既拥有分段系统的共享和保护，又拥有分页系统的虚拟内存功能。
- 分页与分段的比较
  - 对程序员的透明性：分页透明，但是分段需要程序员显式划分每个段。
  - 地址空间的维度：分页是一维地址空间，分段是二维的。
  - 大小是否可以改变：页的大小不可变，段的大小可以动态改变。
  - 出现的原因：分页主要用于实现虚拟内存，从而获得更大的地址空间；分段主要是为了使程序和数据可以被划分为逻辑上独立的地址空间并且有助于共享和保护。

## 设备管理

- 磁盘结构
  - 盘面（Platter）：一个磁盘有多个盘面；
  - 磁道（Track）：盘面上的圆形带状区域，一个盘面可以有多个磁道；
  - 扇区（Track Sector）：磁道上的一个弧段，一个磁道可以有多个扇区，它是最小的物理储存单位，目前主要有 512 bytes 与 4 K 两种大小；
  - 磁头（Head）：与盘面非常接近，能够将盘面上的磁场转换为电信号（读），或者将电信号转换为盘面的磁场（写）；
  - 制动手臂（Actuator arm）：用于在磁道之间移动磁头；
  - 主轴（Spindle）：使整个盘面转动。
- 磁盘调度算法
  - 旋转时间（主轴转动盘面，磁头移动到适当的扇区上）
  - 寻道时间（制动手臂移动，磁头移动到适当的磁道上；寻道时间最长，磁盘调度的主要目标是使磁盘的平均寻道时间最短）
  - 实际的数据传输时间
  - **先来先服务**（FCFS），按照磁盘请求的顺序进行调度。
  - **最短寻道时间优先**（SSTF），优先调度与当前磁头所在磁道距离最近的磁道。
  - **电梯算法**（SCAN），电梯总是保持一个方向运行，直到该方向没有请求为止，然后改变运行方向。

# TypeScript

## 定义

- 以JavaScript为基础构建的语言
- 一个JavaScript的超集
- 可以在任何支持JavaScript的平台中执行
- TypeScript扩展了JavaScript，并添加了类型

## 新增内容

- 类型
- 支持ES的新特性
- 强大的开发工具
- 添加ES不具备的新特性
- 丰富的配置选项

# 设计模式

## 概述

- 包装对象并为其提供不同的接口。（适配器）
- 子类决定如何实现算法中的步骤。（模板方法）
- 子类决定要创建的具体类。（工厂方法）
- 确保仅创建一个对象。（单例）
- 封装可互换的行为，并使用委托来决定使用哪个行为。（策略）
- 客户统一对待对象集合和单个对象。（组合）
- 封装基于状态的行为，并使用委托在行为之间进行切换。（状态）
- 提供了一种遍历对象集合而不暴露其实现的方法。（迭代器）
- 简化了一组类的接口。（外观）
- 包装对象以提供新的行为。（装饰者）
- 允许在状态更改时通知对象。（观察者）
- 将请求封装为对象。（命令）

## 单例模式

- 作用
  - 保证某个类的对象的唯一性
  - 模块间通信
  - 防止变量污染

```javascript
// 懒汉模式
var Singleton = (function() {
    var singleton;
    function init() { return { m: 'x' } };

    return {
        getInstance: function() {
            if (!singleton) {
                singleton = init();
            }
            return singleton;
        }
    }
})();

console.log(Singleton.getInstance().m);

// 饿汉模式
var Singleton = (function() {
    var singleton = init();
    function init() { return { m: 'x' } };

    return {
        getInstance: function() {
            return singleton;
        }
    }
})();

console.log(Singleton.getInstance().m);
```

