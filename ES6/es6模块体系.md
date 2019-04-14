# 模块体系
ES6模块体系自动采用严格模式，不管你有没有在模块头部加上"use strict";严格模式有以下限制：
* 变量必须声明后再使用
* 函数的参数不能有同名属性，否则报错
* 不能使用with语句
* 不能对只读属性赋值，否则报错
* 不能使用前缀 0 表示八进制数，否则报错
* 不能删除不可删除的属性，否则报错
* 不能删除变量delete prop，会报错，只能删除属性delete global[prop]
* eval不会在它的外层作用域引入变量
* eval和arguments不能被重新赋值
* arguments不会自动反映函数参数的变化
* 不能使用arguments.callee
* 不能使用arguments.caller
* 禁止this指向全局对象
* 不能使用fn.caller和fn.arguments获取函数调用的堆栈
* 增加了保留字（比如protected、static和interface）

## export 定义对外接口
* export用于规定模块的对外接口
* 一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量
* 可以使用as关键字对输出的变量进行重命名
```javascript
// 变量的输出写法

// 写法一
export var m = 1;
// 写法二
var m = 1;
export {m};
// 写法三
var n = 1;
export {n as num,m};

//报错1
var m = 1;
export m;
//报错2
export 1;


// 方法和类的输出写法

// 正确1
export function f() {};
// 正确2
function f() {}
export {f as func};

// 报错
function f() {}
export f;
```
* export语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值
* export命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错，下一节的import命令也是如此

```javascript
function foo() {
  export default 'bar' // SyntaxError
}
foo()
```


## import 导入模块 
* import用于输入其他模块提供的功能
* import命令，用于加载profile.js文件，并从中输入变量。import命令接受一对大括号，里面指定要从其他模块导入的变量名。大括号里面的变量名，必须与被导入模块（profile.js）对外接口的名称相同
```javascript
// main.js
import {firstName, lastName, year} from './profile.js';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```
* import命令输入的变量都是只读的，因为它的本质是输入接口。也就是说，不允许在加载模块的脚本里面，改写接口
```javascript
import {a} from './xxx.js'

a = {}; // Syntax Error : 'a' is read-only;
```
* 但是，如果a是一个对象，改写a的属性是允许的。a的属性可以成功改写，并且其他模块也可以读到改写后的值。但这种写法很难查错，建议凡是输入的变量，都当作完全只读，轻易不要改变它的属性。
```javascript
import {a} from './xxx.js'

a.foo = 'hello'; // 合法操作，但不要轻易使用，其它文件也会导入更改后的值，难查错
```

* from后面可以是相对路径，也可以是绝对路径，.js后缀也可以省略
* from后面也可以只写模块，不写路径，但这就要在配置文件中说明该模块的位置
* import命令具有提升效果，会提升到整个模块的头部，首先执行
* 由于import是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构
```javascript
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```
* 如果多次重复执行同一句import语句，那么只会执行一次，而不会执行多次
* 可以使用*来加载整体，即用星号指定一个对象，所有输出值都加载在这个对象上面。但这个对象是静态分析的，不允许修改
```javascript
// circle.js

export function area(radius) {
  return Math.PI * radius * radius;
}

export function circumference(radius) {
  return 2 * Math.PI * radius;
}

// other.js
import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
// 下面两行都是不允许的
circle.foo = 'hello';
circle.area = function () {};
```

## export default
* 使用import命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。但是，用户肯定希望快速上手，未必愿意阅读文档，去了解模块有哪些属性和方法
* export default命令，为模块指定默认输出一个匿名函数（也可以写实名，但其它模块导入时依然当作是匿名处理），其他模块加载该模块时，import命令可以为该匿名函数指定任意名字。但这时的import不可使用{}
```javascript
// export-default.js
export default function () {
  console.log('foo');
}

// import-default.js
import customName from './export-default';
customName(); // 'foo'

import {custom} from './export-default' //报错
```
* export default也可以用来输出类，export default命令其实只是输出一个叫做default的变量
```javascript
// 正解 
var a = 1;
export default a;
export default 42;

// 错误
export default var a = 1;   //已经输出一个default变量了，不能再有声明了
```

## export 与 import 的复合写法
* 如果在一个模块之中，先输入后输出同一个模块，import语句可以与export语句写在一起。但需要注意的是，写成一行以后，foo和bar实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用foo和bar
* export *，表示再输出circle模块的所有属性和方法。注意，export *命令会忽略circle模块的default方法
```javascript
// 接口改名
export { foo as myFoo } from 'my_module';

// 整体输出
export * from 'my_module';      
```

## 模块的继承
```javascript
// circleplus.js
export * from 'circle';         // 将circle模块的所有方法和属性转接口出去
export var e = 2.71828182846;   // 输出接口e
export default function(x) {    // 在自己模块中添加默认方法
  return Math.exp(x);
}

// import circleplus.js
import * as math from 'circleplus';
import exp from 'circleplus';
console.log(exp(math.e));
```