# 前端

## 题目：js都有哪些类型

### 原始类型 (7种)

> 原始类型存储的是值，是没有函数可以调用的

`'1'.toString()` 是可以使用的。在这种情况下，`'1'`不是原始类型了，而被强制转换成了`String`类型也就是对象类型，所以可以调用`toString`函数。

`null`: `typeof null`会输出`object`，在js的最初版本中使用的是32位系统，为了性能考虑使用低位存储变量的类型信息，`000`开头代表是对象，然而`null`表示为全零，所以将它错误的判断为`object`。

```javascript
// boolean 布尔类型
// null 
// undefined
// number
// string
// symbol
// bigint
```

### 对象类型

> 对象类型和原始类型不同的是，原始类型存储的是值，一般存储在栈上，对象类型存储的是地址(指针)，数据存储在堆上

当创建一个对象类型的时候，计算机会在堆内存中帮我们开辟一个空间来存放值，但是我们需要找到这个空间，这个空间会拥有一个地址(指针)。

```javascript
function test(person) {
  person.age=26
  person={
    name:'yyy',
    age:30
  }
return person
}
const p1 = {
  name: 'zzz',
  age:25
}
const p2 = test(p1) 
/*
函数传参是传递对象指针的副本

*/
console.log(p1) // ---> {name:'zzz',age:26}
console.log(p2) // ---> {name:'yyy',age:30}
```

## 题目：js类型判断

### typeof

```javascript
// typeof对于原始类型来说，除了null都可以显示正确的类型
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof 1n // 'bigint'

// typeof对于对象类型来说，除了函数都会显示object
typeof [] // 'object'
typeof {} // 'object'
typeof function // 'function'
```

### instanceof

> `instanceof`通过原型链的方式来判断是否为构建函数的实例，常用于判断具体的对象类型

```javascript
const Person = function() {}
const p1 = new Person()
p1 instanceof Person // true

var str = 'hello world'
str instanceof String // false

var str1 = new String('hello world')
str1 instanceof String // true
```

### Object.prototype.toString.call

> `Object.prototype.toString.call` 综合来看是最佳选择，能判断的类型最完整，基本上是开源库选择最多的方式

```javascript
Object.prototype.toString.call(null) // '[object Null]'
Object.prototype.toString.call(1) // '[object Number]'
Object.prototype.toString.call('') // '[object String]'
Object.prototype.toString.call(1n) // '[object BigInt]'
Object.prototype.toString.call([]) // '[object Array]'
Object.prototype.toString.call({}) // '[object Object]'
Object.prototype.toString.call(function() {}) // '[object Function]'
```

### isXXX API

```javascript
Array.isArray([]) //true
isNaN('') // false
```

## 题目：类型转换

### 转Boolean

> 在条件判断时，除了`undefined`、`null`、`false`、`NaN`、`''`、`0`、`-0`，其他所有值都转为`true`，包括所有对象

### 对象转原始类型

> 对象在转类型的时候，会调用内置的`[[ToPrimitive]]`函数，对于该函数来说，算法逻辑一般来说如下：

- 如果已经是原始类型了，那就不需要转换了
- 如果需要转字符串类型就调用`x.toString()`，转换为基础类型的话就返回转换的值。不是字符串类型的话就先调用`valueOf`，结果不是基础类型的话再调用`toString`
- 调用valueOf()，如果转换为基础类型，就返回转换的值
- 如果都没有返回原始类型，就会报错

```javascript
// 可以重写Symbol.toPrimitive,该方法在转原始类型时调用优先级最高
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
console.log(1 + a) // 3
```

### 四则运算

加法运算符的特点

- 运算中其中一方为字符串，那么就会把另一方也转换为字符串
- 如果一方不是字符串或者数字，那么会将它转换为数字或字符串

```javascript
1 + '1' // '11'
// 触发特点一，所以将数字1转换为字符串，得到结果'11'

true + true // 2
// 触发特点二，所以将true转换为数字1

4 + [1,2,3] // '41,2,3'
// 触发特点二，所以将数组通过toString转为字符串1,2,3，得到结果41,2,3

'a' + + 'b' // 'aNaN'
// 因为 + 'b' 等于 NaN，所以结果为'aNaN'
```

除了加法运算符的特点

- 只要其中一方是数字，那么另一方就会被转为数字

### 比较运算符

- 如果是对象，就通过toPrimitive转换对象
- 如果是字符串，就通过unicode字符索引来比较

```javascript
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  }
}
a > 1 // true
// 因为a是对象，所以会通过valueOf转换为原始类型再比较值
```

## 题目：this

- 对于直接调用函数来说，不管函数放在了什么地方，this一定是window
- 对于对象内的函数来说，谁调用了函数，谁就是this
- 对于new的方式来说，this永远绑定在实例上，不会被任何方式改变this
- 箭头函数中的this只取决包裹箭头函数的第一个普通函数的this。另外箭头函数使用bind这类函数是无效的
- bind，call，apply这些改变上下文的api，对函数来说，this取决于第一个参数，如果第一个参数为空，那么就是window
- 不管我们给函数bind几次，函数的this永远由第一个bind决定

## 题目：==vs===

`==`：如果对比双方的类型不一样的话，就会进行类型转换

1. 首先会判断两者类型是否相同。相同的话就是比较大小了
2. 类型不同的话，那么就会进行类型转换
3. 会先判断是否在对比null和undefined，是的就会返回true
4. 判断两者类型是否为string和number，是的就会将字符串转换为number
5. 判断其中一方是否为boolean，是的话就会把boolean转为number再进行判断流程
6. 判断其中一方是否为object且另一方为string、number，是的话就会把object转为原始类型再进行判断，也就是执行x.toString和valueOf
   - `'1' == {name:'yyy'}` ----> `'1' == '[object Object]'`
7. 都不是的话，最后返回false

`===`：判断两者类型和值相同就行

## 题目：vue中组件之间的通信方式？
