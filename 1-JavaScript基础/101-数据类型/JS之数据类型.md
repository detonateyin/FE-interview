# JS之数据类型

## 一、基本数据类型
JS的数据类型分为基础类型和引用类型，基础类型包括
- Number
- String
- Boolean
- Object
- Null
- undefinded
- Symbol(ES6)
- BigInt(ES2020)

引用类型是指Object，以及其衍生对象子类型Araay、Function

### 关于Symbol
> ES6 引入了一种新的原始数据类型Symbol，表示独一无二的值。Symbol 值通过Symbol函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。
> —— 阮一峰ESMAScript6入门

即
1. Symbol 的引入是为了防止对象属性名冲突
2. Symbol 是一种新的原始数据类型
3. Symbol 值是独一无二的

### 关于BigInt
> BigInt 是一种内置对象，它提供了一种方法来表示大于 2^53-1的整数。这原本是 Javascript 中可以用 Number 表示的最大数字。BigInt 可以表示任意大的整数。
> —— MDN

即
1. BigInt的引入是为了解决原来JS超过最大安全数时会丢失精度的问题

### 关于包装类

JS只有三种包装类：
**Number**、**Boolean**、**String**

当基础类型调用方法和属性的时候，其包装类会调用构造方法生成一个对象，然后调用这个对象的方法，之后会销毁这个对象，基础类型还是基础类型

    var a = 11
    => undefined
    a.toString()
    => "11"
    typeof a
    => "number"
    Object.prototype.toString.call(a)
    => "[object Number]"


<br/>

## 二、数据类型判断
判断JS数据类型的方法共有四种：
- typeof
- toString
- instanceof
- constructor
  
先说结论，toString不出意外可以判断所有类型，typeof适用于判断非引用类型，对于引用类型，可以使用instanceof和constructor。

<br/>

### 1.  typeof
对一个数据类型使用typeof时，会返回该数据类型的字符表达式，表格如下


  Type | Result
  --- | --
  Undefined | "undefined"
  Null | "object"
  Boolean | "boolean"
  Number | "number"
  String | "string"
  Function | "function"
  Object | "object"
  Symbol | "symbol"

需注意到，typeof除了function能识别以外，所有引用类型都会返回"object"，同时还会犯判断Null为Object的错误，这里简单解释下typeof实现原理

JS在底层存储变量时，会在变量的机器码的低位1-3位储存其类型信息，如：
    
  - 000 对象
  - 010 浮点数
  - 100 字符串
  - 110 布尔
  - 1 整数

  特殊：
  - 全是0 Null
  - -2^30 undefined

所以用typeof判断null时，由于其全是0，就会返回对象

<br/>  

### 2. Object.prototype.toString.call
Object原型对象中有一个toString()的方法，每个对象都继承有 toString()这一方法，如果此方法在自定义对象中未被覆盖，toString() 返回 "[object type]"，其中 type 是对象的类型。

    Object.prototype.toString.call(1)// "[object Number]"

    Object.prototype.toString.call('hi') // "[object String]"

    Object.prototype.toString.call({a:'hi'}) // "[object Object]"

    Object.prototype.toString.call([1,'a']) // "[object Array]"

    Object.prototype.toString.call(true) // "[object Boolean]"

    Object.prototype.toString.call(() => {}) // "[object Function]"

    Object.prototype.toString.call(null) // "[object Null]"

    Object.prototype.toString.call(undefined) // "[object Undefined]"

    Object.prototype.toString.call(Symbol(1)) // "[object Symbol]"

    Object.prototype.toString.call(new Date()) // "[object Date]"

    Object.prototype.toString.call(/abc/) // "[object RegExp]"

    Object.prototype.toString.call(arguments) // "[object Arguments]"

当然，如果对象上的toString()方法被覆盖了，就无法获得这些返回值，实际上Array这样的对象子类型就覆盖了这个方法，所以不能直接调用，而要通过call来调用

1. ### instanceof
instanceof是检测构造函数constructor.prototype是否存在于实例对象的原型链上的方法，通过使用A instanceof B，可以判断A的原型链上是否有B，这里涉及到原型链的知识，有另外的文章会详解。

    // 定义构造函数
    function C(){}
    function D(){}

    var o = new C();

    o instanceof C; // true，因为 Object.getPrototypeOf(o) === C.prototype

    o instanceof D; // false，因为 D.prototype 不在 o 的原型链上

缺点是，可能会被非标准的__proto__伪属性修改

2. ### constructor
通过判断对象的构造函数，也可以达到判断引用类型的目的，这点区别于instanceof，对象的constructor并不会顺着原型链往上查找

    [].constructor === Array
    //true
    [].constructor === Object
    //false

缺点是，实例的constructor属性是可以被改写的
