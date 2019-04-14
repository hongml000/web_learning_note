# class
## 在ES5中，构造函数是这样的
```javascript
function Person(x,y){
    this.x = x;
    this.y = y;
    this.hello = function(){
        return this.x + ' ' + this.y
    }
}
Person.prototype.toString = function(){
    return '(' + this.x + ',' + this.y + ')';
}
person = new Person(1,2);
console.log(person.hello());     //1,2
console.log(person.toString());   
```

## class类
1. 在ES6中，提供了class关键字，引用了类的概念。使用class Person定义一个类，取代function Person的构造方法，使得JS更加面向对象。class本质上还是一个方法
2. 将构造方法，放至以constructor方法中。constructor必须存在的，如果没显性写，会默认添加
3. 方法与方法之间，不能用逗号","隔开，否则会报错
4. 在模块和类的内部，默认使用严格模式，不需要定义'use strict'
```javascript
class Person{
    constructor(x,y){
        this.x = x;
        this.y = y;
        // 实例对象的方法属性
        this.hello = function(){
            return this.x + ' ' + this.y
        }
    }
    // 原型链上的方法
    toString(){
        return '(' + this.x + ',' + this.y + ')';
    }
}
person = new Person(1,2);
console.log(person.hello());     //1,2
console.log(person.toString());    
```

### 类的静态方法
如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就是静态方法
```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}
Foo.classMethod() // 'hello'
var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```