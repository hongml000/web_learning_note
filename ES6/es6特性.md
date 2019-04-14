# let和const命令

## let
* ES6 新增了let命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。
* let声明的变量必须在声明之后才能使用，如果在这之前使用会报错
* let声明的变量会让这个变量只在当前代码块有作用，即使全局有这个变量，在一个代码块里只要用了let声明，那么这个变量在这个代码块里就必须在let声明后才使用，与全局的不再有关系
* let不允许相同代码块中，重复声明同一个变量
* 不允许在函数内部，重新声明参数

```javascript
for(let i = 0; i < 3; i ++){
    console.log(i);     //0 1 2，这个代码块中没let i，所以使用的是父作用域的
}    
for(let i = 0; i < 3; i++){
    let i = 'abc';
    console.log(i);     //会输出3次abc，说明循环中的i，和下面代码块中的i其实是在不同的作用域中
}
console.log(i);     //报错ReferenceError: i is not defined，i只在上面的代码块中有用

//let不允许相同代码块中，重复声明同一个变量
if (true) {
    let i = 'abc';
    console.log(i);
    let i = 123;        //报错
    var i = 123;        //报错
    console.log(i);
}

function func(arg) {
  let arg;
}
func() // 报错
```

* ES6中可以在块级定义函数(ES5只能在全局或函数中定义，且函数声明会提到它们的头部)，块级中的函数声明会被提到块级头部。
* ES6 的块级作用域允许声明函数的规则，只在使用大括号的情况下成立
```javascript
// 不报错
'use strict';
if (true) {
  function f() {}
}
```
* 考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。
```javascript
let func = function(){
    return 22;
}
```

## const
* 声明一个只读变量，一旦声明，变量值不可改变（改变会报错）
* const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值
* const的作用域与let命令相同：只在声明所在的块级作用域内有效
* const命令声明的常量也是不提升，只能在声明的位置后面使用
* const声明的常量，也与let一样不可重复声明
* const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动，所以声明简单类型的数据（数值、字符串、布尔值）一般没有问题，但声明数组和对象就要小心了
```javascript
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
* 如果真的想将对象冻结，应该使用Object.freeze方法
```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

## let和const命令不再与window变量挂钩
* var命令和function命令声明的全局变量，依旧是顶层对象(window)的属性
* let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性
```javascript
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```


# 简写特性
* 属性简写，当键值对名一样时，允许简写键
* 方法简写
```javascript
const o = {
  method() {
    return "Hello!";
  }
};
// 等同于
const o = {
  method: function() {
    return "Hello!";
  }
};


let birth = '2000/01/01';
const Person = {
  name: '张三',
  //等同于birth: birth
  birth,
  // 等同于hello: function ()...
  hello() { console.log('我的名字是', this.name); }

};
```

# 方法的name属性
函数的name属性，返回函数名。对象方法也是函数，因此也有name属性。
```javascript
let func = function(){
    return 123;
}
console.log(func.name);     //func

const person = {
  sayName() {
    console.log('hello!');
  },
};

person.sayName.name   // "sayName"
```


# =>
=>是ES6中的arrow function，相当于是匿名函数
```javascript
// 只包含一种表达式时，return可以省略
(x) => x + 6;
//等同于
function (x){
  return x + 6;
}
// 返回单个对象时，要注意
x => {foo:x}    // 出错
x => {{ foo: x }} //OK


// 包含多个表达式时，return不可省略
x => {
    if (x>0){
        return x*x
    }else{
      return x
    }
}
// 相当于
function(x){
    if (x>0){
        return x*x
    }else{
      return x
    }
}
```

# ... 扩展运算符（spread）
将一个数组转为用逗号分隔的参数序列
```javascript
console.log(...[1, 2, 3])
// 1 2 3
console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

// 常用于函数参数
arr = [1,2,3,4,5]
arr.push(...[3,4,5])
console.log(arr)    // [1, 2, 3, 4, 5, 3, 4, 5]

// 扩展运算符将字符串转为真正的数组
[...'hello']  // [ "h", "e", "l", "l", "o" ]
```