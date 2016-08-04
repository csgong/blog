# Classes

JavaScript classes are introduced in ECMAScript 6 and are syntactical sugar over JavaScript's existing prototype-based inheritance.
The class syntax is not introducing a new object-oriented inheritance model to JavaScript.
JavaScript classes provide a much simpler and clearer syntax to create objects and deal with inheritance.

## Defining classes 

Classes are in fact "special functions", and just as you can define function expressions and function declarations, 
the class syntax has two components: class expressions and class declarations.

* Class declarations

```javascript
class Polygon {
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }
}
```
* Hoisting

An important difference between function declarations and class declarations is that function declarations are hoisted and class declarations are not. 
You first need to declare your class and then access it, otherwise code like the following will throw a ReferenceError:
```javascript
var p = new Polygon(); // ReferenceError
class Polygon {}
```
* Class expressions

A class expression is another way to define a class. Class expressions can be named or unnamed. 
The name given to a named class expression is local to the class's body.
```javascript
// unnamed
var Polygon = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
// named
var Polygon = class Polygon {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
//上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是MyClass而不是Me，Me只在Class的内部代码可用，指代当前类
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined
//IICE(immediately-invoked class expression )
let person = new class {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
}('张三');
person.sayName(); // =>"张三"
```
## Class body and method definitions

* Strict mode

The bodies of class declarations and class expressions are executed in [strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode).

* Constructor

The constructor method is a special method for creating and initializing an object created with a class. 
There can only be one special method with the name "constructor" in a class. 
A SyntaxError will be thrown if the class contains more than one occurrence of a constructor method.
A constructor can use the super keyword to call the constructor of a parent class.
```javascript
class Point {
  constructor(x, y, color) {
    super();
    this.color = color;
  }
}
```

* Static methods

The static keyword defines a static method for a class. 
Static methods are called without instantiating their class and are also not callable when the class is instantiated. 
Static methods are often used to create utility functions for an application.
```javascript
class Point {
    static distance(x,y) {
        return x+y;
    }
}
console.log(Point.distance(1,2)) //=> 3;
```

* Sub classing with extends

```javascript
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }
  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

## Mix-ins

Abstract subclasses or mix-ins are templates for classes. An ECMAScript class can only have a single superclass, 
so multiple inheritance from tooling classes, for example, is not possible. 
The functionality must be provided by the superclass.
```javascript
var RandomizerMixin = Base => class extends Base {
  randomize() { }
};
//A class that uses these mix-ins can then be written like this:
class Foo { }
class Bar extends CalculatorMixin(RandomizerMixin(Foo)) { }
```

# More info

* [MDN Classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)
* [es6katas#Class](http://es6katas.org/)
* [es6.ruanyifeng#class](http://es6.ruanyifeng.com/#docs/class)
* [lukehoban#classes](https://github.com/lukehoban/es6features#classes)