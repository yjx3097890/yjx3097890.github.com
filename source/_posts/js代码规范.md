---
title: js代码规范
cover: /img/default-banner-light.jpg
comments: true
date: 2018-05-22 11:24:46
tags: [javascript]
categories: [计算机技术]
---
从arbnb的GitHub翻译来的。
<!-- more --> 

## 类型
1. 基本类型: 按值访问，操作的是值.

        string
        number
        boolean
        null
        undefined
        
        const foo = 1;
        let bar = foo;
        bar = 9;
        console.log(foo, bar); // => 1, 9
        
2. 引用类型: 按引用访问，操作的是值的指针.

        object
        array
        function
    
        const foo = [1, 2];
        const bar = foo;
        bar[0] = 9;
        console.log(foo[0], bar[0]); // => 9, 9

## 引用
1. 使用 const 来声明所有引用; 避免使用 var. 

          // bad
          var a = 1;
          var b = 2;
    
          // good
          const a = 1;
          const b = 2;
          
2. 如果一定要为变量重新赋值，则使用 let 代替 var。

          // bad
          var count = 1;
          if (true) {
            count += 1;
          }
    
          // good, use the let.
          let count = 1;
          if (true) {
            count += 1;
          }
          
3. 记住 let 和 const 都是块级作用域.

        // const 和 let 只在他们所定义的块级内有效.
        {
          let a = 1;
          const b = 1;
        }
        console.log(a); // ReferenceError
        console.log(b); // ReferenceError

## 对象
1. 使用字面量方式创建对象。
 
          // bad
          const item = new Object();
    
          // good
          const item = {};
	
2. 当需要使用动态属性名来创建对象时，请使用`[计算后的属性名]`。这样允许你在一个地方定义对象的所有属性。
    
          function getKey(k) {
            return `a key named ${k}`;
          }
    
          // bad
          const obj = {
            id: 5,
            name: 'San Francisco',
          };
          obj[getKey('enabled')] = true;
    
          // good
          const obj = {
            id: 5,
            name: 'San Francisco',
            [getKey('enabled')]: true,
          };

3. 使用方法简写.

          // bad
          const atom = {
            value: 1,
        
            addValue: function (value) {
              return atom.value + value;
            },
          };
        
          // good
          const atom = {
            value: 1,
        
            addValue(value) {
              return atom.value + value;
            },
          };

4. 使用属性简写.

          const lukeSkywalker = 'Luke Skywalker';
        
          // bad
          const obj = {
            lukeSkywalker: lukeSkywalker,
          };
        
          // good
          const obj = {
            lukeSkywalker,
          };
      
5. 在对象声明的开头定义需要简写的属性。
    
          const anakinSkywalker = 'Anakin Skywalker';
          const lukeSkywalker = 'Luke Skywalker';
        
          // bad
          const obj = {
            episodeOne: 1,
            twoJediWalkIntoACantina: 2,
            lukeSkywalker,
            episodeThree: 3,
            mayTheFourth: 4,
            anakinSkywalker,
          };
        
          // good
          const obj = {
            lukeSkywalker,
            anakinSkywalker,
            episodeOne: 1,
            twoJediWalkIntoACantina: 2,
            episodeThree: 3,
            mayTheFourth: 4,
          };

6. 只有对象的属性是无效的标识符时，才用引号。

        // bad
        const bad = {
          'foo': 3,
          'bar': 4,
          'data-blah': 5,
        };
        
        // good
        const good = {
          foo: 3,
          bar: 4,
          'data-blah': 5,
        };
        
7. 不要直接调用Object.prototype的方法，如：hasOwnProperty, propertyIsEnumerable, 和 isPrototypeOf。
    因为这些方法可能对象属性覆盖：`{ hasOwnProperty: false }`，或是空对象：`Object.create(null)`。
    
        // bad
        console.log(object.hasOwnProperty(key));
        
        // good
        console.log(Object.prototype.hasOwnProperty.call(object, key));
        
        // best
        const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
        /* or */
        import has from 'has'; // https://www.npmjs.com/package/has
        // ...
        console.log(has.call(object, key));
    
8. 浅拷贝对象时更建议使用对象的扩展运算符而不是`Object.assign`，使用解构赋值拷贝一个对象并忽略一些属性。

        // very bad
        const original = { a: 1, b: 2 };
        const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
        delete copy.a; // so does this
        
        // bad
        const original = { a: 1, b: 2 };
        const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }
        
        // good
        const original = { a: 1, b: 2 };
        const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }
        
        const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
        
## 数组

1. 使用字面量方式创建数组。

          // bad
          const items = new Array();
        
          // good
          const items = [];
  
2. 使用 Array.push，而不是直接为数组添加元素。

        const someStack = [];
        
        // bad
        someStack[someStack.length] = 'abracadabra';
        
        // good
        someStack.push('abracadabra');

3. 使用数组扩展运算符 `...` 来复制数组。

        // bad
        const len = items.length;
        const itemsCopy = [];
        let i;
        
        for (i = 0; i < len; i++) {
          itemsCopy[i] = items[i];
        }
        
        // good
        const itemsCopy = [...items];
        
4. 使用扩展运算符 `...` 将一个类数组对象转换成数组, 代替Array.from。

        const foo = document.querySelectorAll('.foo');
        
        // good
        const nodes = Array.from(foo);
        
        // best
        const nodes = [...foo];

5. 使用Array.from而不是`...`对类数组对象使用map操作，因为`...`会多产生一个媒介数组。

        // bad
        const baz = [...foo].map(bar);
        
        // good
        const baz = Array.from(foo, bar);
        
6.  如果一个数组有多行，在数组的开始方括号后和结束方括号前留白。

        // bad
        const arr = [
          [0, 1], [2, 3], [4, 5],
        ];
        
        const objectInArray = [{
          id: 1,
        }, {
          id: 2,
        }];
        
        const numberInArray = [
          1, 2,
        ];
        
        // good
        const arr = [[0, 1], [2, 3], [4, 5]];
        
        const objectInArray = [
          {
            id: 1,
          },
          {
            id: 2,
          },
        ];
        
        const numberInArray = [
          1,
          2,
        ];


## 解构

1. 当访问和使用对象的多个属性时，使用对象解构赋值。 

          // bad
          function getFullName(user) {
            const firstName = user.firstName;
            const lastName = user.lastName;
        
            return `${firstName} ${lastName}`;
          }
        
          // good
          function getFullName(obj) {
            const { firstName, lastName } = obj;
            return `${firstName} ${lastName}`;
          }
        
          // best
          function getFullName({ firstName, lastName }) {
            return `${firstName} ${lastName}`;
          }
          
2. 使用数组的解构赋值。

        const arr = [1, 2, 3, 4];
        
        // bad
        const first = arr[0];
        const second = arr[1];
        
        // good
        const [first, second] = arr;
        
3. 使用对象解构来实现多值返回，而不使用数组解构。这样你可以随时增加新的属性或改变属性的次序，而无需修改位置。
        
        // bad
        function processInput(input) {
            // then a miracle occurs
            return [left, right, top, bottom];
        }
        
        // 调用者需要考虑返回的数据的顺序
        const [left, _, top] = processInput(input);
        
        // good
        function processInput(input) {
        // then a miracle occurs
        return { left, right, top, bottom };
        }
        
        // 调用者按需选择数据
        const { left, right } = processInput(input);


## 字符串

1. 字符串变量使用单引号 ''。

        // bad
        const name = "Capt. Janeway";
        
        // good
        const name = 'Capt. Janeway';
        
2. 长于100个字符的字符串，不应采用字符串连接符分多行编写。

        // bad
        const errorMessage = 'This is a super long error that was thrown because \
        of Batman. When you stop to think about how Batman had anything to do \
        with this, you would get nowhere \
        fast.';
        
        // bad
        const errorMessage = 'This is a super long error that was thrown because ' +
          'of Batman. When you stop to think about how Batman had anything to do ' +
          'with this, you would get nowhere fast.';
        
        // good
        const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';


3. 当使用编程的方式来构建字符串时，应采用模板字符串，而不是拼接字符串。

          // bad
          function sayHi(name) {
            return 'How are you, ' + name + '?';
          }
        
          // bad
          function sayHi(name) {
            return ['How are you, ', name, '?'].join();
          }
        
          // good
          function sayHi(name) {
            return `How are you, ${name}?`;
          }
          
4. 永远不要对字符串使用 eval() 方法，它会带来太多的漏洞。

5. 在字符串里不要使用不必要的转义符。

        // bad
        const foo = '\'this\' \i\s \"quoted\"';
        
        // good
        const foo = '\'this\' is "quoted"';
        const foo = `my name is '${name}'`;

## 函数
1. 使用命名的函数表达式，而不是函数声明。

          // bad
          function foo() {
            // ...
          }
          
          // bad
          const foo = function () {
            // ...
          };
          
          // good
          // lexical name distinguished from the variable-referenced invocation(s)
          const short = function longUniqueMoreDescriptiveLexicalFoo() {
            // ...
          };
  
2. 立即执行的函数表达式。

        // 立即执行的函数表达式 (IIFE)
        (function () {
          console.log('Welcome to the Internet. Please follow me.');
        }());
        
        (function () {
                  console.log('Welcome to the Internet. Please follow me.');
                })();
        
3. 永远不要在非函数块内声明函数（比如if, while 等），将该函数赋值给一个变量来代替。虽然浏览器都支持这种语法，但解释方式却不一致，这当然不是你想看到的。

4. 注意，ECMA-262将 block 定义为语句列表。而函数声明并不是一条语句。

        // bad
        if (currentUser) {
          function test() {
            console.log('Nope.');
          }
        }
        
        // good
        let test;
        if (currentUser) {
          test = () => {
            console.log('Yup.');
          };
        }
5. 永远不要将参数命名为 arguments。这将覆盖原函数作用域的 arguments 对象。

        // bad
        function nope(name, options, arguments) {
          // ...stuff...
        }
        
        // good
        function yup(name, options, args) {
          // ...stuff...
        }

6. 永远不要使用 arguments，可以选择使用rest语法 ... 代替。

        // bad
        function concatenateAll() {
        const args = Array.prototype.slice.call(arguments);
        return args.join('');
        }
        
        // good
        function concatenateAll(...args) {
        return args.join('');
        }

7. 使用默认参数语法，而不是改变函数参数。

        // really bad
        function handleThings(opts) {
          // 不! 我们不应该改变函数参数。
          // 更糟糕的是: 若opts被判断为false，它将会被设置成一个空对象
          // 但这样可能会导致一些隐蔽的bugs.
          opts = opts || {};
          // ...
        }
        
        // still bad
        function handleThings(opts) {
          if (opts === void 0) {
            opts = {};
          }
          // ...
        }
        
        // good
        function handleThings(opts = {}) {
          // ...
        }
        
8. 避免默认参数的副作用。

        var b = 1;
        // bad
        function count(a = b++) {
          console.log(a);
        }
        count();  // 1
        count();  // 2
        count(3); // 3
        count();  // 3

9. 将默认参数放在最后。

        // bad
        function handleThings(opts = {}, name) {
          // ...
        }
        
        // good
        function handleThings(name, opts = {}) {
          // ...
        }
        
10. 永远不要使用函数构造器来创建一个新函数。

        // bad
        var add = new Function('a', 'b', 'return a + b');
        
        // still bad
        var subtract = Function('a', 'b', 'return a - b');
        
11. 在函数签名中添加空格.

        // bad
        const f = function(){};
        const g = function (){};
        const h = function() {};
        
        // good
        const x = function () {};
        const y = function a() {};
        
12. 永远不要改变参数。

        // bad
        function f1(obj) {
          obj.key = 1;
        }
        
        // good
        function f2(obj) {
          const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
        }

13. 不要为参数再分配值。

        // bad
        function f1(a) {
          a = 1;
          // ...
        } 
        function f2(a) {
          if (!a) { a = 1; }
          // ...
        }
        
        // good
        function f3(a) {
          const b = a || 1;
          // ...
        }
        function f4(a = 1) {
          // ...
        }
        
14. 使用扩展运算符来调用可变参数函数(Variadic functions)。

        // bad
        const x = [1, 2, 3, 4, 5];
        console.log.apply(console, x);
        
        // good
        const x = [1, 2, 3, 4, 5];
        console.log(...x);
        
        // bad
        new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));
        
        // good
        new Date(...[2016, 8, 5]);
        
15. 当函数的签名或调用有多行时，要像多行列表一样：每个条目一行，并用逗号结尾。

        // bad
        function foo(bar,
                     baz,
                     quux) {
          // ...
        }
        
        // good
        function foo(
          bar,
          baz,
          quux,
        ) {
          // ...
        }
        
        // bad
        console.log(foo,
          bar,
          baz);
        
        // good
        console.log(
          foo,
          bar,
          baz,
        );
        

## 箭头函数
1. 如果必须要使用行内匿名函数时，请使用箭头函数符号。

        // bad
        [1, 2, 3].map(function (x) {
            const y = x + 1;
            return x * y;
        });
        
        // good
        [1, 2, 3].map((x) => {
            const y = x + 1;
            return x * y;
        });
        
2. 如果函数体仅包含一个表达式，可去掉大括号，采用隐式返回。除此之外，请使用大括号和 return语句。

         // bad
        [1, 2, 3].map(number => {
            const nextNumber = number + 1;
            `A string containing the ${nextNumber}.`;
        });
                
        // good
        [1, 2, 3].map(number => `A string containing the ${number}.`);
        
        // good
        [1, 2, 3].map(number => {
            const nextNumber = number + 1;
            return `A string containing the ${nextNumber}.`;
        });
        
        // good
        [1, 2, 3].map((number, index) => ({
          [index]: number,
        }));
       
        
3. 当一个返回的表达式跨越多行时，为了更好的可读性将他们用括号括起来。

        // bad
        [1, 2, 3].map(number => 'As time went by, the string containing the ' +
        `${number} became much longer. So we needed to break it over multiple ` +
        'lines.'
        );
        
        // good
        [1, 2, 3].map(number => (
        `As time went by, the string containing the ${number} became much ` +
        'longer. So we needed to break it over multiple lines.'
        ));
        
4. 如果函数只有一个参数，可省略括号和大括号。或者一直使用括号。
        
        // bad
        [1, 2, 3].map((x) => x * x);
        
        // good
        [1, 2, 3].map(x => x * x);
        
        // good
        [1, 2, 3].map(number => (
          `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
        ));
        
        // bad
        [1, 2, 3].map(x => {
          const y = x + 1;
          return x * y;
        });
        
        // good
        [1, 2, 3].map((x) => {
          const y = x + 1;
          return x * y;
        });
        
5. 防止箭头函数符号（=>）和比较操作符（=>, <=）相混淆。

        // bad
        const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;
        
        // bad
        const itemHeight = (item) => item.height > 256 ? item.largeSize : item.smallSize;
        
        // good
        const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);
        
        // good
        const itemHeight = (item) => {
          const { height, largeSize, smallSize } = item;
          return height > 256 ? largeSize : smallSize;
        };

## 类和构造器

1. 始终使用 class，避免直接操作 prototype。

        // bad
        function Queue(contents = []) {
        this._queue = [...contents];
        }
        Queue.prototype.pop = function () {
        const value = this._queue[0];
        this._queue.splice(0, 1);
        return value;
        }
        
        // good
        class Queue {
            constructor(contents = []) {
              this._queue = [...contents];
            }
            pop() {
              const value = this._queue[0];
              this._queue.splice(0, 1);
              return value;
            }
        }
        
2. 使用 extends 语法实现继承。

        // bad
        const inherits = require('inherits');
        function PeekableQueue(contents) {
            Queue.apply(this, contents);
        }
        inherits(PeekableQueue, Queue);
        PeekableQueue.prototype.peek = function () {
            return this._queue[0];
        }
        
        // good
        class PeekableQueue extends Queue {
            peek() {
              return this._queue[0];
            }
        }
        
3. 各方法可返回 this 来实现方法的链式调用。

        // bad
        Jedi.prototype.jump = function () {
          this.jumping = true;
          return true;
        };
        
        Jedi.prototype.setHeight = function (height) {
          this.height = height;
        };
        
        const luke = new Jedi();
        luke.jump(); // => true
        luke.setHeight(20); // => undefined
        
        // good
        class Jedi {
          jump() {
            this.jumping = true;
            return this;
          }
        
          setHeight(height) {
            this.height = height;
            return this;
          }
        }
        
        const luke = new Jedi();
        
        luke.jump().setHeight(20);
        
4. 可以自己写一个toString()方法，只需要保证他能正确运行并没有副作用即可。

        class Jedi {
          constructor(options = {}) {
            this.name = options.name || 'no name';
          }
        
          getName() {
            return this.name;
          }
        
          toString() {
            return `Jedi - ${this.getName()}`;
          }
        }

5. 如果不指定，class会有默认的构造函数。空的构造函数或只调用父类的构造函数是不需要的。

        // bad
        class Jedi {
          constructor() {}
        
          getName() {
            return this.name;
          }
        }
        
        // bad
        class Rey extends Jedi {
          constructor(...args) {
            super(...args);
          }
        }
        
        // good
        class Rey extends Jedi {
          constructor(...args) {
            super(...args);
            this.name = 'Rey';
          }
        }

6. 防止类方法名重复。

        // bad
        class Foo {
          bar() { return 1; }
          bar() { return 2; }
        }
        
        // good
        class Foo {
          bar() { return 1; }
        }
        
        // good
        class Foo {
          bar() { return 2; }
        }

## 模块

1. 始终使用模块（import / export），而不是非标准的模块系统。

        // bad
        const AirbnbStyleGuide = require('./AirbnbStyleGuide');
        module.exports = AirbnbStyleGuide.es6;
        
        // ok
        import AirbnbStyleGuide from './AirbnbStyleGuide';
        export default AirbnbStyleGuide.es6;
        
        // best
        import { es6 } from './AirbnbStyleGuide';
        export default es6;

2. 不要使用通配符引用。

        // bad
        import * as AirbnbStyleGuide from './AirbnbStyleGuide';
        
        // good
        import AirbnbStyleGuide from './AirbnbStyleGuide';
  
3. 不要直接在引用的位置直接输出。

        // bad
        // filename es6.js
        export { es6 as default } from './airbnbStyleGuide';
        
        // good
        // filename es6.js
        import { es6 } from './AirbnbStyleGuide';
        export default es6;

4. 一个路径只在一个地方引入。
    
        // bad
        import foo from 'foo';
        // … some other imports … //
        import { named1, named2 } from 'foo';
        
        // good
        import foo, { named1, named2 } from 'foo';
        
        // good
        import foo, {
          named1,
          named2,
        } from 'foo';
        
5. 不要export可变的变量。
    
         // bad
         let foo = 3;
         export { foo };
         
         // good
         const foo = 3;
         export { foo };
         
6. 如果模块中只有单个export，使用default export比较好。         
     
         // bad
         export function foo() {}
         
         // good
         export default function foo() {}
         
7. 将import语句，放在所有非import语句之前。

        // bad
        import foo from 'foo';
        foo.init();
        
        import bar from 'bar';
        
        // good
        import foo from 'foo';
        import bar from 'bar';
        
        foo.init();

8. 多行import语句应该像多行数组或对象一样缩进。

        // bad
        import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';
        
        // good
        import {
          longNameA,
          longNameB,
          longNameC,
          longNameD,
          longNameE,
        } from 'path';

9. 不要在import语句中使用webpack loader语法。

        // bad
        import fooSass from 'css!sass!foo.scss';
        import barCss from 'style!css!bar.css';
        
        // good
        import fooSass from 'foo.scss';
        import barCss from 'bar.css';

     
## 迭代器与生成器

1. 不要使用迭代器。尽量使用Javascript的高阶函数，比如 map() / every() / filter() / find() / findIndex() / reduce() / some()，来替代 for-of、for-in之类的循环。

        const numbers = [1, 2, 3, 4, 5];
        
        // bad
        let sum = 0;
        for (let num of numbers) {
          sum += num;
        }
        sum === 15;
        
        // good
        let sum = 0;
        numbers.forEach((num) => {
          sum += num;
        });
        sum === 15;
        
        // best (use the functional force)
        const sum = numbers.reduce((total, num) => total + num, 0);
        sum === 15;
        
        // bad
        const increasedByOne = [];
        for (let i = 0; i < numbers.length; i++) {
          increasedByOne.push(numbers[i] + 1);
        }
        
        // good
        const increasedByOne = [];
        numbers.forEach((num) => {
          increasedByOne.push(num + 1);
        });
        
        // best (keeping it functional)
        const increasedByOne = numbers.map(num => num + 1);

2. 现在不要使用生成器。如果非要使用，确保函数标签有合适的空格。

        // bad
        function * foo() {
          // ...
        }
        
        // bad
        const bar = function * () {
          // ...
        };
        
        // bad
        const baz = function *() {
          // ...
        };
        
        // bad
        const quux = function*() {
          // ...
        };
        
        // bad
        function*foo() {
          // ...
        }
        
        // bad
        function *foo() {
          // ...
        }
        
        // very bad
        function
        *
        foo() {
          // ...
        }
        
        // very bad
        const wat = function
        *
        () {
          // ...
        };
        
        // good
        function* foo() {
          // ...
        }
        
        // good
        const foo = function* () {
          // ...
        };

## 属性

1. 使用点标记访问属性。

        const luke = {
            jedi: true,
            age: 28,
        };
        
        // bad
        const isJedi = luke['jedi'];
        
        // good
        const isJedi = luke.jedi;
        
2. 当属性为变量时，采用中括号 []。

        const luke = {
          jedi: true,
          age: 28,
        };
        
        function getProp(prop) {
          return luke[prop];
        }
        
        const isJedi = getProp('jedi');

3. 计算指数时，使用指数操作符 ** 。
    
        // bad
        const binary = Math.pow(2, 10);
        
        // good
        const binary = 2 ** 10;

## 变量
1. 始终使用 const 或 let 来声明变量，否则会导致全局变量。

        // bad
        superPower = new SuperPower();
        
        // good
        const superPower = new SuperPower();

2. 每个变量都使用一个 const或let 声明。
 
        // bad
        const items = getItems(),
          goSportsTeam = true,
          dragonball = 'z';
        
        // bad
        // (与上面相比, 试着指出错误)
        const items = getItems(),
          goSportsTeam = true;
          dragonball = 'z';
        
        // good
        const items = getItems();
        const goSportsTeam = true;
        const dragonball = 'z';
        
3. 将所有的 const 和 let 分组。

        // bad
        let i, len, dragonball,
          items = getItems(),
          goSportsTeam = true;
        
        // bad
        let i;
        const items = getItems();
        let dragonball;
        const goSportsTeam = true;
        let len;
        
        // good
        const goSportsTeam = true;
        const items = getItems();
        let dragonball;
        let i;
        let length;
        
4. 在需要的地方为变量赋值，但应把他们放在合理的地方。

        // bad - unnecessary function call
        function checkName(hasName) {
          const name = getName();
        
          if (hasName === 'test') {
            return false;
          }
        
          if (name === 'test') {
            this.setName('');
            return false;
          }
        
          return name;
        }
        
        // good
        function checkName(hasName) {
          if (hasName === 'test') {
            return false;
          }
        
          const name = getName();
        
          if (name === 'test') {
            this.setName('');
            return false;
          }
        
          return name;
        }
        
5. 不要使用链式赋值，会隐式的建立全局变量。

        // bad
        (function example() {
          // JavaScript interprets this as
          // let a = ( b = ( c = 1 ) );
          // The let keyword only applies to variable a; variables b and c become
          // global variables.
          let a = b = c = 1;
        }());
        
        console.log(a); // throws ReferenceError
        console.log(b); // 1
        console.log(c); // 1
        
        // good
        (function example() {
          let a = 1;
          let b = a;
          let c = a;
        }());
        
        console.log(a); // throws ReferenceError
        console.log(b); // throws ReferenceError
        console.log(c); // throws ReferenceError
        
        // the same applies for `const`
        
6. 不要使用一元递增，递减操作符（++ ，--）。

        // bad
        
        const array = [1, 2, 3];
        let num = 1;
        num++;
        --num;
        
        let sum = 0;
        let truthyCount = 0;
        for (let i = 0; i < array.length; i++) {
          let value = array[i];
          sum += value;
          if (value) {
            truthyCount++;
          }
        }
        
        // good
        
        const array = [1, 2, 3];
        let num = 1;
        num += 1;
        num -= 1;
        
        const sum = array.reduce((a, b) => a + b, 0);
        const truthyCount = array.filter(Boolean).length;
        
7. 赋值语句中不要在等号（ = ）前后断行，如果太长，可以用括号包裹。

        // bad
        const foo =
          superLongLongLongLongLongLongLongLongFunctionName();
        
        // bad
        const foo
          = 'superLongLongLongLongLongLongLongLongString';
        
        // good
        const foo = (
          superLongLongLongLongLongLongLongLongFunctionName()
        );
        
        // good
        const foo = 'superLongLongLongLongLongLongLongLongString';
        
## 提升

1. var 声明的变量将会被提升到最近作用域的顶部，但赋值却不会。通过const 和 let 声明的变量被赋予了新的概念叫做时空盲区(TDZ，会抛出ReferenceError)。

        // 我们知道这个方法不会正常工作 (假设没有一个全局的notDefined存在)
        function example() {
          console.log(notDefined); // => throws a ReferenceError
        }
        
        // 由于变量提升，在引用变量之后创建一个变量声明会正常工作。
        // 注意：变量的赋值 `true` 没有被提升。
        function example() {
          console.log(declaredButNotAssigned); // => undefined
          var declaredButNotAssigned = true;
        }
        
        // 编译器会将变量声明提升到其作用域的顶部，
        // 这就意味着我们的例子可以这样重写：
        function example() {
          let declaredButNotAssigned;
          console.log(declaredButNotAssigned); // => undefined
          declaredButNotAssigned = true;
        }
        
        // 使用 const 和 let 的情况
        function example() {
          console.log(declaredButNotAssigned); // => throws a ReferenceError
          console.log(typeof declaredButNotAssigned); // => throws a ReferenceError
          const declaredButNotAssigned = true;
        }
        
2. 匿名函数表达式的变量名会提升，但函数的赋值不会。

        function example() {
          console.log(anonymous); // => undefined
        
          anonymous(); // => TypeError anonymous is not a function
        
          var anonymous = function () {
            console.log('anonymous function expression');
          };
        }
        
3. 被命名的函数表达式的变量名会被提升，而函数名和函数体则不会。

        function example() {
          console.log(named); // => undefined
        
          named(); // => TypeError named is not a function
        
          superPower(); // => ReferenceError superPower is not defined
        
          var named = function superPower() {
            console.log('Flying');
          };
        }
        
        // 当函数名和变量名相同时也一样
        function example() {
          console.log(named); // => undefined
        
          named(); // => TypeError named is not a function
        
          var named = function named() {
            console.log('named');
          }
        }
        
4. 函数声明会提升函数名和函数体。

        function example() {
          superPower(); // => Flying
        
          function superPower() {
            console.log('Flying');
          }
        }

## 比较操作

1. 使用 === 和 !== 来代替 == 和 !=.

2. 像 if 一样的条件语句会强制使用 ToBoolean 抽象方法来计算他的表达式，这种强制转换始终遵循以下这些简单的规则：
    
    * 对象：转化成 true  
    * Undefined：转化成 false  
    * Null：转化成 false  
    * 布尔值：转化成 该布尔值 
    * 数字：若是+0, -0, 或 NaN 则转化成 false , 否则 true
    * 字符串：如果是空字符串 ''，则转化成 false, 否则 true
    
            if ([0] && []) {
              // true
              // 数组是对象，对象会被转化成true
            }
        
3. 使用布尔值的简写，但数字和字符串要显式比较。

        // bad
        if (isValid === true) {
          // ...
        }
        
        // good
        if (isValid) {
          // ...
        }
        
        // bad
        if (name) {
          // ...
        }
        
        // good
        if (name !== '') {
          // ...
        }
        
        // bad
        if (collection.length) {
          // ...
        }
        
        // good
        if (collection.length > 0) {
          // ...
        }

4. 在case和default语句中使用大括号包括声明语句 (如： let, const, function, and class)。

        // bad
        switch (foo) {
          case 1:
            let x = 1;
            break;
          case 2:
            const y = 2;
            break;
          case 3:
            function f() {
              // ...
            }
            break;
          default:
            class C {}
        }
        
        // good
        switch (foo) {
          case 1: {
            let x = 1;
            break;
          }
          case 2: {
            const y = 2;
            break;
          }
          case 3: {
            function f() {
              // ...
            }
            break;
          }
          case 4:
            bar();
            break;
          default: {
            class C {}
          }
        }

5. 三元表达式（？:）不应该嵌套，并且通常在一行中。
        
        // bad
        const foo = maybe1 > maybe2
          ? "bar"
          : value1 > value2 ? "baz" : null;
        
        // split into 2 separated ternary expressions
        const maybeNull = value1 > value2 ? 'baz' : null;
        
        // better
        const foo = maybe1 > maybe2
          ? 'bar'
          : maybeNull;
        
        // best
        const foo = maybe1 > maybe2 ? 'bar' : maybeNull;

6. 避免不必要的三元表达式（？:）。

        // bad
        const foo = a ? a : b;
        const bar = c ? true : false;
        const baz = c ? false : true;
        
        // good
        const foo = a || b;
        const foo = a && b;
        const bar = !!c;
        const baz = !c;
    
7. 当混合使用操作符时，把它们放在圆括号中。

        // bad
        const foo = a && b < 0 || c > 0 || d + 1 === 0;
        
        // bad
        const bar = a ** b - 5 % d;
        
        // bad
        // one may be confused into thinking (a || b) && c
        if (a || b && c) {
          return d;
        }
        
        // good
        const foo = (a && b < 0) || c > 0 || (d + 1 === 0);
        
        // good
        const bar = (a ** b) - (5 % d);
        
        // good
        if (a || (b && c)) {
          return d;
        }
        
        // good
        const bar = a + b / c * d;

## 块

1. 将所有的多行块用大括号括起来。

        // bad
        if (test)
          return false;
        
        // good
        if (test) return false;
        
        // good
        if (test) {
          return false;
        }
        
        // bad
        function foo() { return false; }
        
        // good
        function bar() {
          return false;
        }

2. 如果在 if 和 else 的语句中使用多行快，则将 else 与 if 块的反括号放在同一行。

        // bad
        if (test) {
          thing1();
          thing2();
        }
        else {
          thing3();
        }
        
        // good
        if (test) {
          thing1();
          thing2();
        } else {
          thing3();
        } 

3. 如果if块总会执行return语句，之后的else是不必要的。else if块和if块中都有return语句时，应该拆分为多个if块。

        // bad
        function foo() {
          if (x) {
            return x;
          } else {
            return y;
          }
        }
        
        // bad
        function cats() {
          if (x) {
            return x;
          } else if (y) {
            return y;
          }
        }
        
        // bad
        function dogs() {
          if (x) {
            return x;
          } else {
            if (y) {
              return y;
            }
          }
        }
        
        // good
        function foo() {
          if (x) {
            return x;
          }
        
          return y;
        }
        
        // good
        function cats() {
          if (x) {
            return x;
          }
        
          if (y) {
            return y;
          }
        }
        
        // good
        function dogs(x) {
          if (x) {
            if (z) {
              return y;
            }
          } else {
            return z;
          }
        }
        
## 条件控制语句

1. 如果你的控制语句太长或超过了最大长度，则每个条件因该被放到一个新行，将逻辑操作符放在行首。
        
        // bad
        if ((foo === 123 || bar === 'abc') && doesItLookGoodWhenItBecomesThatLong() && isThisReallyHappening()) {
          thing1();
        }
        
        // bad
        if (foo === 123 &&
          bar === 'abc') {
          thing1();
        }
        
        // bad
        if (foo === 123
          && bar === 'abc') {
          thing1();
        }
        
        // bad
        if (
          foo === 123 &&
          bar === 'abc'
        ) {
          thing1();
        }
        
        // good
        if (
          foo === 123
          && bar === 'abc'
        ) {
          thing1();
        }
        
        // good
        if (
          (foo === 123 || bar === 'abc')
          && doesItLookGoodWhenItBecomesThatLong()
          && isThisReallyHappening()
        ) {
          thing1();
        }
        
        // good
        if (foo === 123 && bar === 'abc') {
          thing1();
        }
        
2. 不要使用逻辑操作符代替控制语句。

        // bad
        !isRunning && startRunning();
        
        // good
        if (!isRunning) {
          startRunning();
        }

## 注释

1. 多行注释请使用 /** ... */ 。函数的注释应包含函数描述，所有参数的指定类型和值，以及返回值。

        // bad
        // make() 基于传入的标签名，返回一个新的元素
        //
        // @param {String} tag
        // @return {Element} element
        function make(tag) {
        
          // ...stuff...
        
          return element;
        }
        
        // good
        /**
         * make() 基于传递的标签名，返回一个新的元素 
         * @param {String} tag
         * @return {Element} element
         */
        function make(tag) {
        
          // ...stuff...
        
          return element;
        }
        
2. 单行注释请使用 // 。将单行注释放到要注释的内容的上一行，且独占一行。在注释行的前面加一个空行，除非该注释是块的第一行。

        // bad
        const active = true;  // is current tab
        
        // good
        // is current tab
        const active = true;
        
        // bad
        function getType() {
          console.log('fetching type...');
          // set the default type to 'no type'
          const type = this._type || 'no type';
        
          return type;
        }
        
        // good
        function getType() {
          console.log('fetching type...');
        
          // set the default type to 'no type'
          const type = this._type || 'no type';
        
          return type;
        }
        
        // also good
        function getType() {
          // set the default type to 'no type'
          const type = this._type || 'no type';
        
          return type;
        }
        
3. 在注释内容前加一个空格，方便阅读。
        
        // bad
        //is current tab
        const active = true;
        
        // good
        // is current tab
        const active = true;
        
        // bad
        /**
         *make() returns a new element
         *based on the passed-in tag name
         */
        function make(tag) {
        
          // ...
        
          return element;
        }
        
        // good
        /**
         * make() returns a new element
         * based on the passed-in tag name
         */
        function make(tag) {
        
          // ...
        
          return element;
        }

4. 在注释前加上 FIXME 或 TODO，能帮助其他开发者马上理解你是在指出一个需要解决的问题，还是在提出一个问题的解决方案，需要实现。它们与正常的注释不同，因为它们是可操作的。该操作包括 FIXME -- 需要搞清楚这个问题 和 TODO -- 需要实现。

5. 使用 // FIXME: 标注问题。
        
        class Calculator extends Abacus {
          constructor() {
            super();
        
            // FIXME: 不应在这里使用全局变量
            total = 0;
          }
        }

6. 使用 // TODO: 注释问题的解决方案。

        class Calculator extends Abacus {
          constructor() {
            super();
        
            // TODO: total应该可以通过一个选项的参数配置
            this.total = 0;
          }
        }

## 空白

1. 将tab设置为2空格。

          // bad
          function () {
          ∙∙∙∙const name;
          }
        
          // bad
          function () {
          ∙const name;
          }
        
          // good
          function () {
          ∙∙const name;
          }
          
2. 在左大括号的前面加一个空格。

        // bad
        function test(){
           console.log('test');
        }
        
        // good
        function test() {
           console.log('test');
        }
        
        // bad
        dog.set('attr',{
            age: '1 year',
            breed: 'Bernese Mountain Dog',
        });
        
        // good
        dog.set('attr', {
            age: '1 year',
            breed: 'Bernese Mountain Dog',
        });
        
3. 在控制语句中（if、 while等）的左小括号的前面加一个空格。但在函数调用和声明中的参数列表前不应加空格。

          // bad
          if(isJedi) {
            fight ();
          }
        
          // good
          if (isJedi) {
            fight();
          }
        
          // bad
          function fight () {
            console.log ('Swooosh!');
          }
        
          // good
          function fight() {
            console.log('Swooosh!');
          }
          
4. 操作符两侧都要加空格.

        // bad
        const x=y+5;
        
        // good
        const x = y + 5;
        
5. 在文件的最后加一个空行。

        // bad
        import { es6 } from './AirbnbStyleGuide';
          // ...
        export default es6;
        // bad
        import { es6 } from './AirbnbStyleGuide';
          // ...
        export default es6;↵
        ↵
        // good
        import { es6 } from './AirbnbStyleGuide';
          // ...
        export default es6;↵
        
6. 当使用较长的方法链式调用时(大于2个)，应采用缩进。在前方加点，这会强调该行是一个方法调用，而不是一个新的语句。

        // bad
        $('#items').find('.selected').highlight().end().find('.open').updateCount();
        
        // bad
        $('#items').
          find('.selected').
            highlight().
            end().
          find('.open').
            updateCount();
        
        // good
        $('#items')
          .find('.selected')
            .highlight()
            .end()
          .find('.open')
            .updateCount();
        
        // bad
        const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').class('led', true)
            .attr('width', (radius + margin) * 2).append('svg:g')
            .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
            .call(tron.led);
        
        // good
        const leds = stage.selectAll('.led')
            .data(data)
          .enter().append('svg:svg')
            .classed('led', true)
            .attr('width', (radius + margin) * 2)
          .append('svg:g')
            .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
            .call(tron.led);
            
        // good
        const leds = stage.selectAll('.led').data(data);
    
7. 在块的后面和下一个语句之前加一个空行。

        // bad
        if (foo) {
          return bar;
        }
        return baz;
        
        // good
        if (foo) {
          return bar;
        }
        
        return baz;
        
        // bad
        const obj = {
          foo() {
          },
          bar() {
          },
        };
        return obj;
        
        // good
        const obj = {
          foo() {
          },
        
          bar() {
          },
        };
        
        return obj;
        
        // bad
        const arr = [
          function foo() {
          },
          function bar() {
          },
        ];
        return arr;
        
        // good
        const arr = [
          function foo() {
          },
        
          function bar() {
          },
        ];
        
        return arr;

8. 不要用空行包裹你的块内容。

          // bad
          function bar() {
        
            console.log(foo);
        
          }
        
          // also bad
          if (baz) {
        
            console.log(qux);
          } else {
            console.log(foo);
        
          }
        
          // good
          function bar() {
            console.log(foo);
          }
        
          // good
          if (baz) {
            console.log(qux);
          } else {
            console.log(foo);
          }
  
9. 不要在小括号内添加空格。

          // bad
          function bar( foo ) {
            return foo;
          }
        
          // good
          function bar(foo) {
            return foo;
          }
        
          // bad
          if ( foo ) {
            console.log(foo);
          }
        
          // good
          if (foo) {
            console.log(foo);
          }
          
10. 不要在中括号内添加空格。

        // bad
        const foo = [ 1, 2, 3 ];
        console.log(foo[ 0 ]);
        
        // good
        const foo = [1, 2, 3];
        console.log(foo[0]);
        
11. 在单行大括号内添加空格。

        // bad
        const foo = {clark: 'kent'};
        
        // good
        const foo = { clark: 'kent' };

12. 避免一行代码大于100个字符（包括空白）。

        // bad
        const foo = jsonData && jsonData.foo && jsonData.foo.bar && jsonData.foo.bar.baz && jsonData.foo.bar.baz.quux && jsonData.foo.bar.baz.quux.xyzzy;
        
        // bad
        $.ajax({ method: 'POST', url: 'https://airbnb.com/', data: { name: 'John' } }).done(() => console.log('Congratulations!')).fail(() => console.log('You have failed this city.'));
        
        // good
        const foo = jsonData
          && jsonData.foo
          && jsonData.foo.bar
          && jsonData.foo.bar.baz
          && jsonData.foo.bar.baz.quux
          && jsonData.foo.bar.baz.quux.xyzzy;
        
        // good
        $.ajax({
          method: 'POST',
          url: 'https://airbnb.com/',
          data: { name: 'John' },
        })
          .done(() => console.log('Congratulations!'))
          .fail(() => console.log('You have failed this city.'));

13. 不要在逗号前加空格，在逗号后加空格。
        
        // bad
        var foo = 1,bar = 2;
        var arr = [1 , 2];
        
        // good
        var foo = 1, bar = 2;
        var arr = [1, 2];

14. 在函数和他的调用之间不加空格。

        // bad
        func ();
        
        func
        ();
        
        // good
        func();


15. 在对象字面量的 : 前不加空格，在之后加空格。

        // bad
        var obj = { "foo" : 42 };
        var obj2 = { "foo":42 };
        
        // good
        var obj = { "foo": 42 };

16. 不要在行尾加空格。

17. 不要在文件末尾有多个空行，只能有一个新行。

        // bad
        var x = 1;
        
        
        
        var y = 2;
        
        // good
        var x = 1;
        
        var y = 2;
 

## 逗号

1. 不要以逗号开头.

          // bad
          const story = [
              once
            , upon
            , aTime
          ];
        
          // good
          const story = [
            once,
            upon,
            aTime,
          ];
        
          // bad
          const hero = {
              firstName: 'Ada'
            , lastName: 'Lovelace'
            , birthYear: 1815
            , superPower: 'computers'
          };
        
          // good
          const hero = {
            firstName: 'Ada',
            lastName: 'Lovelace',
            birthYear: 1815,
            superPower: 'computers',
          };
          
2. 对象、函数参数或数组最后一行也以逗号结尾.

          // bad - 没有尾随逗号的git差异比较
          const hero = {
               firstName: 'Florence',
          -    lastName: 'Nightingale'
          +    lastName: 'Nightingale',
          +    inventorOf: ['coxcomb graph', 'modern nursing']
          };
        
          // good - 有尾随逗号的差异比较
          const hero = {
               firstName: 'Florence',
               lastName: 'Nightingale',
          +    inventorOf: ['coxcomb chart', 'modern nursing'],
          };
        
          // bad
          const hero = {
            firstName: 'Dana',
            lastName: 'Scully'
          };
        
          const heroes = [
            'Batman',
            'Superman'
          ];
        
          // good
          const hero = {
            firstName: 'Dana',
            lastName: 'Scully',
          };
        
          const heroes = [
            'Batman',
            'Superman',
          ];
          
          // bad
          function createHero(
            firstName,
            lastName,
            inventorOf
          ) {
            // does nothing
          }
          
          // good
          function createHero(
            firstName,
            lastName,
            inventorOf,
          ) {
            // does nothing
          }
          
          // good (note that a comma must not appear after a "rest" element)
          function createHero(
            firstName,
            lastName,
            inventorOf,
            ...heroArgs
          ) {
            // does nothing
          }

## 分号

1. 语句结尾要有分号.

         // bad - raises exception  报luke或leia未定义。 
         const luke = {}
         const leia = {}
         [luke, leia].forEach(jedi => jedi.father = 'vader')
         
         // bad - raises exception
         const reaction = "No! That's impossible!"
         (async function meanwhileOnTheFalcon() {
           // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
           // ...
         }())
         
         // bad - returns `undefined` instead of the value on the next line - always happens when `return` is on a line by itself because of ASI!
         function foo() {
           return
             'search your feelings, you know it to be foo'
         }
         
         // good
         const luke = {};
         const leia = {};
         [luke, leia].forEach((jedi) => {
           jedi.father = 'vader';
         });
         
         // good
         const reaction = "No! That's impossible!";
         (async function meanwhileOnTheFalcon() {
           // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
           // ...
         }());
         
         // good
         function foo() {
           return 'search your feelings, you know it to be foo';
         }
 

## 类型转换

1. 字符串:

        // => this.reviewScore = 9;
        
        // bad
        const totalScore = new String(this.reviewScore); // typeof totalScore is "object" not "string"
        
        // bad
        const totalScore = this.reviewScore + ''; // invokes this.reviewScore.valueOf()
        
        // bad
        const totalScore = this.reviewScore.toString(); // isn’t guaranteed to return a string
        
        // good
        const totalScore = String(this.reviewScore);

2. 数字: 使用 Number 进行类型转换。在使用 parseInt 转换字符串时要始终带着基数。

        const inputValue = '4';
        
        // bad
        const val = new Number(inputValue);
        
        // bad
        const val = +inputValue;
        
        // bad
        const val = inputValue >> 0;
        
        // bad
        const val = parseInt(inputValue);
        
        // good
        const val = Number(inputValue);
        
        // good
        const val = parseInt(inputValue, 10);
        
3. 如果由于某种原因，你要做一些不wild事情， parseInt 方法会是你的瓶颈。由于性能原因，此时就需要使用比特位移（Bitshift），这时就要注释一下为什么这么做以及怎么做的。

        // good
        /**
         * parseInt是导致代码慢的原因.
         * 使用比特位移将字符串转换成数字对执行速度提升很大。
         */
        const val = inputValue >> 0;

4. 注意在使用比特位移时要格外小心。数字采用64位值展示，但比特位移操作只能返回32位整数。比特位移在操作大于32位的整数时，会产生意外行为。最大的32位整数是2,147,483,647:

        2147483647 >> 0 //=> 2147483647
        2147483648 >> 0 //=> -2147483648
        2147483649 >> 0 //=> -2147483647
        
5. 布尔值：

        const age = 0;
        
        // bad
        const hasAge = new Boolean(age);
        
        // good
        const hasAge = Boolean(age);
        
        // good
        const hasAge = !!age;

## 命名约定

1. 不要用单字母作为名称。要让命名有意义。

        // bad
        function q() {
          // ...stuff...
        }
        
        // good
        function query() {
          // ..stuff..
        }
        
2. 在命名对象、函数和实例时，采用驼峰方式。

        // bad
        const OBJEcttsssss = {};
        const this_is_my_object = {};
        function c() {}
        
        // good
        const thisIsMyObject = {};
        function thisIsMyFunction() {}
  
3. 在命名构造器或类时，采用帕斯卡命名法（所有单词首字母大写且直接相连）。

        // bad
        function user(options) {
          this.name = options.name;
        }
        
        const bad = new user({
          name: 'nope',
        });
        
        // good
        class User {
          constructor(options) {
            this.name = options.name;
          }
        }
        
        const good = new User({
          name: 'yup',
        });
        
4. 不要在单词首尾使用下划线 _ 。

        // bad
        this.__firstName__ = 'Panda';
        this.firstName_ = 'Panda';
        this._firstName = 'Panda';
        
        // good
        this.firstName = 'Panda';
        
        // good, in environments where WeakMaps are available
        // see https://kangax.github.io/compat-table/es6/#test-WeakMap
        const firstNames = new WeakMap();
        firstNames.set(this, 'Panda');
        
5. 请勿保存 this 的引用。使用箭头函数或Function.prototype.bind。

        // bad
        function foo() {
          const self = this;
          return function () {
            console.log(self);
          };
        }
        
        // bad
        function foo() {
          const that = this;
          return function () {
            console.log(that);
          };
        }
        
        // good
        function foo() {
          return () => {
            console.log(this);
          };
        }
        
6. 文件名应该与默认导出的名字一致。
        
        // file 1 contents
        class CheckBox {
          // ...
        }
        export default CheckBox;
        
        // file 2 contents
        export default function fortyTwo() { return 42; }
        
        // file 3 contents
        export default function insideDirectory() {}
        
        // in some other file
        // bad
        import CheckBox from './checkBox'; // PascalCase import/export, camelCase filename
        import FortyTwo from './FortyTwo'; // PascalCase import/filename, camelCase export
        import InsideDirectory from './InsideDirectory'; // PascalCase import/filename, camelCase export
        
        // bad
        import CheckBox from './check_box'; // PascalCase import/export, snake_case filename
        import forty_two from './forty_two'; // snake_case import/filename, camelCase export
        import inside_directory from './inside_directory'; // snake_case import, camelCase export
        import index from './inside_directory/index'; // requiring the index file explicitly
        import insideDirectory from './insideDirectory/index'; // requiring the index file explicitly
        
        // good
        import CheckBox from './CheckBox'; // PascalCase export/import/filename
        import fortyTwo from './fortyTwo'; // camelCase export/import/filename
        import insideDirectory from './insideDirectory'; // camelCase export/import/directory name/implicit "index"
        // ^ supports both insideDirectory.js and insideDirectory/index.js

7. 文件导出的默认函数应采用驼峰方式命名，同时文件名应该与函数名一致。

        function makeStyleGuide() {
        }
        
        export default makeStyleGuide;

8. 当文件导出的是一个单例、或类、或构造函数、或仅是一个对象时，采用帕斯卡命名法。

        const AirbnbStyleGuide = {
          es6: {
          }
        };
        
        export default AirbnbStyleGuide;
        
9. 首字母缩略词应该全部大写或小写。

       // bad
       import SmsContainer from './containers/SmsContainer';
       
       // bad
       const HttpRequests = [
         // ...
       ];
       
       // good
       import SMSContainer from './containers/SMSContainer';
       
       // good
       const HTTPRequests = [
         // ...
       ];
       
       // also good
       const httpRequests = [
         // ...
       ];
       
       // best
       import TextMessageContainer from './containers/TextMessageContainer';
       
       // best
       const requests = [
         // ...
       ]; 

10. 当一个常量（const）被导出时可以用大写字母命名。
        
        // bad
        const PRIVATE_VARIABLE = 'should not be unnecessarily uppercased within a file';
         
        // bad
        export let REASSIGNABLE_VARIABLE = 'do not use let with uppercase variables';
        
        // allowed but does not supply semantic value
        export const apiKey = 'SOMEKEY';
        
        // better in most cases
        export const API_KEY = 'SOMEKEY';

## 访问器

1. 不需要用属性的访问器函数。不用js的getters/setters，是因为他们会产生不可预料的副作用，并且不好测试、维护。


2. 如果非要编写访问器函数，采用 getVal() 和 setVal('hello') 的写法。

        // bad
        class Dragon {
          get age() {
            // ...
          }
        
          set age(value) {
            // ...
          }
        }
        
        // good
        class Dragon {
          getAge() {
            // ...
          }
        
          setAge(value) {
            // ...
          }
        }

3. 若访问的属性是 boolean 类型，则采用 isVal() 或 hasVal() 的写法。

        // bad
        if (!dragon.age()) {
          return false;
        }
        
        // good
        if (!dragon.hasAge()) {
          return false;
        }
        
## js原生对象

1. 使用Number.isNaN代替全局方法isNaN。isNaN方法会先将参数转换为数字再判断。

        // bad
        isNaN('1.2'); // false
        isNaN('1.2.3'); // true
        
        // good
        Number.isNaN('1.2.3'); // false
        Number.isNaN(Number('1.2.3')); // true
        
2. 使用Number.isFinite代替全局方法isFinite。isFinite方法会先将参数转换为数字再判断。

        // bad
        isFinite('2e3'); // true
        
        // good
        Number.isFinite('2e3'); // false
        Number.isFinite(parseInt('2e3', 10)); // true

##  事件

1. 当触发携带参数的事件时，请传递一个对象，而不是单个值。这使得在随后想通过事件传递更多数据时，无需找到并更新所有事件的处理函数。

        // bad
        $(this).trigger('listingUpdated', listing.id);
        
        ...
        
        $(this).on('listingUpdated', function (e, listingId) {
          // do something with listingId
        });

        // good
        $(this).trigger('listingUpdated', { listingId: listing.id });
        
        ...
        
        $(this).on('listingUpdated', function (e, data) {
          // do something with data.listingId
        });

## jQuery

1. 在jQuery对象的变量前加一个 $。

        // bad
        const sidebar = $('.sidebar');
        
        // good
        const $sidebar = $('.sidebar');
        
        // good
        const $sidebarBtn = $('.sidebar-btn');

2. 缓存jQuery的查找值.

        // bad
        function setSidebar() {
          $('.sidebar').hide();
        
          // ...stuff...
        
          $('.sidebar').css({
            'background-color': 'pink'
          });
        }
        
        // good
        function setSidebar() {
          const $sidebar = $('.sidebar');
          $sidebar.hide();
        
          // ...stuff...
        
          $sidebar.css({
            'background-color': 'pink'
          });
        }
        
3. 对于DOM查询，采用级联形式 $('.sidebar ul') 或 父子级形式 $('.sidebar > ul')。

4. 对于以查找到的jQuery对象采用 find 方法查找。[性能测试](https://jsperf.com/jquery-find-vs-context-sel/16)

        // bad
        $('ul', '.sidebar').hide();
        
        // bad
        $('.sidebar').find('ul').hide();
        
        // good
        $('.sidebar ul').hide();
        
        // good
        $('.sidebar > ul').hide();
        
        // good
        $sidebar.find('ul').hide();