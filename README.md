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

- 父子组件
  - `props`
  - `$emit/$on`
  - `$children/$parent` \$children获取组件内所有的子组件实例;\$parent获取父组件实例对象
  - `$attrs/$listeners` \$attrs可以直接获取未在props定义的值。子组件需要调用父组件的方法可以通过\$listeners
  - `ref` 通过在子组件标签定义ref属性，在父组件中可以使用$refs访问子组件实例
- 兄弟组件
  - `$parent`  能够访问父组件的属性和方法
  - `eventBus` 将eventBus绑到vue的原型对象上，通过emit(触发)、on(监听)、off(移除)
  - `vuex`
- 跨层级关系
  - `provide/inject` 依赖注入
  - `$root` 能够访问根父组件的属性和方法
  - `eventBus`
  - `vuex`

vue3移除了on、off、once实例方法
vue3中移除了eventbus，但可以借助第三方工具来完成，Vue 官方推荐使用 mitt 或 tiny-emitter

## 题目：v-if和v-for哪个优先级高？

- 在vue2中，v-for优先于v-if被解析；在vue3中，v-if的优先级高于v-if
- 通常有两种情况下导致我们这样做：
  - 为了过滤列表中的项目(比如v-for="user in users" v-if="user.isActive")。
    - 在vue2中可以正常执行，但是在vue3中会报错
    - 解决方式可以定义一个计算属性，让其返回过滤后的列表即可
  - 为了避免渲染本应该被隐藏的列表(比如v-for="user in users" v-if="shouldShowUsers")
    - 在vue2中会先渲染一遍再隐藏，会消耗性能。在vue3中会先隐藏不循环
    - 解决方式可以把v-if移动到容器元素上

## 题目：简述 Vue 的生命周期以及每个阶段做的事

- 每个vue组件实例被创建后都会经过一系列初始化步骤，比如，它需要数据观测，模版编译，挂载实例到dom上，以及数据变化时更新dom。这个过程中会运行叫做生命周期钩子的函数，以便用户在特定阶段有机会添加他们自己的代码。
- vue生命周期总共可以分为8个阶段：创建前后，载入前后，更新前后，销毁前后，以及一些特殊场景的生命周期。vue3中新增了三个用于调试和服务端渲染的场景。

生命周期v2 | 生命周期v3 | 描述 | 实践
--- | :---: | :---: | :---:
beforeCreate | setup | 组件实例被创建之初 | 通常用于插件开发中执行一些初始化任务
create | setup | 组件实例已经完全创建 | 可以访问各种数据，获取接口数据等
beforeMount | onBeforeMount | 组件挂载之前 |
mounted | onMounted | 组件挂载到实例上去之后 | dom已创建，可用于获取访问数据和dom元素；访问子组件等
beforeUpdate | onBeforeUpdate | 组件数据发生变化，更新之前 | 此时view层还未更新，可用于获取更新前各种状态
updated | onUpdated | 组件数据更新之后 | 完成view层的更新，更新后，所有状态已是最新
beforeDestroy | onBeforeUnMounted | 组件实例销毁之前 | 实例被销毁前调用，可用于一些定时器或订阅的取消
destroyed | onUnmounted | 组件实例销毁之后 | 销毁一个实例。可清理它与其它实例的连接，解绑它的全部指令及事件监听器
activated | onActivated | keep-alive 缓存的组件激活时调用 |
deactivated | onDeactivated | keep-alive 缓存的组件停用时调用 |
errorCaptured | onErrorCaptured | 在捕获一个来自后代组件的错误时被调用 |
\- | onRenderTracked | 调试钩子，响应式依赖被收集时调用 |
\- | onRenderTriggered | 调试钩子，响应式依赖被触发时调用 |
serverPrefetch | onServerPrefetch | ssr only，组件实例在服务器上被渲染前调用

- setup和created谁先执行？setup中为什么没有beforeCreate和created？
  - setup最先执行，此时组件实例在setup内部已经创建，所以created的处理对于setup来讲明显在后面，对于开发者来说已经没有意义，所以setup中没必要再使用beforeCreate和created

## 题目：能说说双向绑定以及它的实现原理吗？

- vue中双向数据绑定是一个指令v-model，可以绑定一个动态值到视图，同时视同中变化能改变该值。v-model是语法糖，默认情况下相当于:value和@input。
- 使用v-model可以减少大量繁琐的事件处理代码，提高开发效率，代码可读性也更好
- 通常在表单项上使用v-model
- 原生的表单项可以直接使用v-model，自定义组件上如果要使用它需要在组件内部绑定value并处理输入事件
- 输出包含v-model模板的组件渲染函数，发现它会被转换为value属性的绑定以及一个事件监听，事件回调函数中会做相应变量更新操作

### v-model和sync修饰符有什么区别

- 相同点：都是语法糖，都可以实现父子组件中的数据的双向通信。
- 区别：
  - v-model 默认对应的是input或者textarea等组件的input事件，如果在子组件替换这个input事件，其本质和.sync修饰符一模一样
  - 一个组件可以多个属性用.sync修饰符，可以同时"双向绑定多个“prop”，而并不像v-model那样，一个组件只能有一个。

### 自定义组件使用v-model如果想要改变事件名或者属性名应该怎么做

- 自定义model对象，对象.prop为属性名，对象.event为事件名

## 题目：vue中如何扩展一个组件

- 常见的组件扩展方法有：mixins、slots、extends等
- 混入mixins是分发vue组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。
  - 场景：当我们存在多个组件中的数据或者功能很相近时，就可以使用mixins将公共部分提取出来，通过mixins封装的函数，组件调用他们是不会改变函数作用域外部的
  - 冲突：
    - 当组件选项与混入选项冲突时以组件优先
    - 当组件和mixin同时定义生命周期选项,两个都会触发,而且mixin会先触发.
    - 如果组件和mixin同时定义相同方法,会使用组件方法,会覆盖mixin.
    - 如果组件和mixin同时定义相同计算属性,会使用组件方法,会覆盖mixin.
  - 隐式依赖:
    - mixin和使用它的组件之间没有层次关系。组件里的变量名称修改后，mixin里没改
  - vue3中引入的composition API (类似于hook) 解决mixins的缺点
    - 解决命名冲突
    - 解决隐式依赖-必须将变量显式的传给函数

## 题目：vue 返回上一页不刷新页面且滚动条位置不变

1. 不刷新页面
   - 设置路由：

      ```javascript
      {
        path:'/home/my',
        name:'my',
        component: () => import('@/home/my'),
        meta: { 
          keepAlive: true // 需要缓存
        }
      }
      ```

   - 设置app.vue

     ```html
     <keep-alive>
        <router-view v-if="$route.meta.keepAlive"></router-view>
     </keep-alive>
     <router-view v-else="!$route.meta.keepAlive"></router-view>
     ```

2. 滚动条不变
   - 页面离开前存一下滚动条位置(dom.srcollTop)
   - 页面返回时设置滚动条位置(使用nextTick，再获取dom，设置dom.srcollTop位置)

## 题目：前端实现图片懒加载

## 题目：前端处理长列表
