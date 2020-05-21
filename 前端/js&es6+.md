## javascript

> 什么是原型

- 对象的 `__proto__` 属性指向原型

> 什么是原型链

- 就是多个对象通过 `__proto__` 的方式连接了起来

> object.prototype的属性和方法

- Object.defineProperty(parame)方法直接在一个对象上定义一个新属性，或者修改一个已经存在的属性， 并返回这个对象。
- Object.hasOwnProperty(parame)方法用来判断某个对象是否含有指定的自身属性。
- Object.toString()将对象转换为字符串,不同类型的对象可能会重写自己的toString方法
    - `[1,2,3].toString() // 1,2,3`  Array包含的元素使用逗号连接起来组成字符串并返回
    - Date的toString方法会返回Date的时间字符串等
    - 普通object类型对象会返回[object Object]

> js原始类型

- boolean
- null
- undefined
- number
- string
- symbol
    - 功能类似于一种标识唯一性的ID
        ```javascript
            let s1 = Symbol()
            let s2 = Symbol('another symbol')
            let s3 = Symbol('another symbol')

            s1 === s2 // false
            s2 === s3 // false
        ```
    - 使用Symbol来作为对象属性名(key)
        ```javascript
            const PROP_NAME = Symbol()
            const PROP_AGE = Symbol()

            let obj = {
            [PROP_NAME]: "一斤代码"
            }
            obj[PROP_AGE] = 18

            obj[PROP_NAME] // '一斤代码'
            obj[PROP_AGE] // 18
        ```
    - typeof检查时返回的是symbol

> 对象（Object）类型

- 对象类型存储的是地址（指针）。当你创建了一个对象类型的时候，计算机会在内存中帮我们开辟一个空间来存放值，但是我们需要找到这个空间，这个空间会拥有一个地址（指针）。

> typeof 是否能正确判断类型？

- typeof 对于原始类型来说，除了 null 都可以显示正确的类型
    ```javascript
        typeof 1 // 'number'
        typeof '1' // 'string'
        typeof undefined // 'undefined'
        typeof true // 'boolean'
        typeof Symbol() // 'symbol'
    ```
- typeof 对于对象来说，除了函数都会显示 object
    ```javascript
        typeof [] // 'object'
        typeof {} // 'object'
        typeof console.log // 'function'
    ```

> instanceof 能正确判断对象的原理是什么？

- 内部机制是通过原型链来判断的

> this

- 对于直接调用函数，不管函数放在哪，this一定是window
- 对于对象.函数来说，谁调用了函数，谁就是this。
- 对于new来说，this永远绑定在实例上。
- 箭头函数中的 this 只取决包裹箭头函数的第一个普通函数的 this
- 给函数bind或apply或call几次，this永远由第一次bind或apply或call决定
    - this 取决于第一个参数
    - 如果第一个参数为空，那么就是 window

> == vs ===

- == 
    - 如果对比的双方不一样的话，就会进行类型转换
    - boolean会转换成1或0
    - 对象会转换成原始类型 如转换成'[object Object]'
- 如果比较的是值类型，那么“==”比较其值；“===”比较其类型与值
- 如果是引用类型，那么比较的都是地址

> 闭包

- 什么是闭包
    - 函数 A 内部有一个函数 B，函数 B 可以访问到函数 A 中的变量，那么函数 B 就是闭包。
    - 闭包存在的意义就是让我们可以间接访问函数内部的变量。

> 深浅拷贝

- 浅拷贝 (浅拷贝只解决了第一层的问题)
    - object.assign
        ```javascript
            let a = {
                age: 1
            }
            let b = object.assign({},a)
            b.age = 2
            console.log(a.age) // 1
        ```
    - 扩展运算符`...`
        ```javascript
            let a = {
                age: 1
            }
            let b = { ...a }
            b.age = 2
            console.log(a.age) // 1
        ```
- 深拷贝
    - JSON.parse(JSON.stringify(object)) 
    - 递归

## es6+

> var、let及const区别

- 函数提升优先于变量提升，函数提升会把整个函数挪到作用域顶部，变量提升只会把声明挪到作用域顶部
- var存在提升，我们能在声明之前使用。let、const因为暂时性死区的原因，不能在声明前使用
- var在全局作用域下声明变量会导致变量挂载在window上，其他两者不会
- let声明变量，const声明常量，但是后者声明的变量不能再次赋值

> Proxy

- Proxy 是 ES6 中新增的功能，它可以用来自定义对象中的操作
- let p = new Proxy(target, handler)
- target代表需要添加代理的对象，handler用来自定义对象中的操作

> map、filter、reduce

- map作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后放入到新的数组中
    - map 的回调函数接受三个参数，分别是当前索引元素，索引，原数组
    - ['1','2','3'].map(parseInt)
        - 第一轮遍历 parseInt('1', 0) -> 1
        - 第二轮遍历 parseInt('2', 1) -> NaN
        - 第三轮遍历 parseInt('3', 2) -> NaN
- filter的作用也是生成一个新数组，在遍历数组的时候将返回值为true的元素放入新数组，可以利用这个函数删除一些不需要的元素
    - filter 的回调函数接受三个参数，分别是当前索引元素，索引，原数组
- reduce 可以将数组中的元素通过回调函数最终转换为一个值
    - 接受两个参数，分别是回调函数和初始值

> es6模块化如何使用，开发环境如何打包

- 模块化的基本语法
    ```javascript
        // util1.js
        export default {
            a: 100
        }
        
        // util2.js
        export function fn1(){
            alert('fn1)
        }
        export function fn2(){
            alert('fn2)
        }

        // index.js
        import util1 from './util1.js'
        import { fn1, fn2 } from './util2.js'
        console.log(util1)
        fn1()
        fn2()
    ```
- 开发环境配置
    - babel
    - webpack
    - rollup
    
> 关于js众多模块化标准

- AMD成为标准，require.js（也有CMD）
- 前端打包工具，nodejs模块化可以被使用
- ES6出现，想统一现在所有模块化标准

> class和普通构造函数有何区别

- js普通构造函数
    ```javascript
        function MathHandle(x, y) {
            this.x = x
            this.y = y
        }
        
        MathHandle.prototype.add = function () {
            return this.x + this.y
        }

        var m = new MathHandle(1, 2)
        console.log(m.add())
    ```
- class语法
    ```javascript
        class MathHandle {
            constructor(x, y) {
                this.x = x
                this.y = y
            }
            add() {
                return this.x + this.y
            }
        }

        const m = new MathHandle(1, 2)
        console.log(m.add())

        typeof MathHandle // 'function'
        MathHandle.prototype.constructor === MathHandle // true
        m.__proto__ === MathHandle.prototype // true
    ```
- 继承-js
    ```javascript
        // 动物
        function Animal() {
            this.eat = funtion () {
                console.log('Anima eat')
            }
        }

        // 狗
        function Dog() {
            this.bark = funtion () {
                console.log('Dog bark')
            }
        }

        // 绑定原型，实现继承
        Dog.prototype = new Animal()
        
        var hashiqi = new Dog()
        hashiqi.bark()
        hashiqi.eat()
    ```
- 继承-class
    ```javascript
        class Animal {
            constructor(name) {
                this.name = name
            }
            eat() {
                console.log(this.name + ' eat')
            }
        }
        
        class Dog extends Animal {
            constructor(name) {
                super(name) // 注意  继承的constructor
                this.name = name
            }
            say() {
                console.log(this.name + ' say')
            }
        }

        const dog = new Dog('哈士奇')
        dog.say()
        dog.eat()
    ```
- 总结
    - class在语法上更加贴合面向对象的写法
    - class实现继承更加易读、易理解
    - 更易于写java等后端语言的使用
    - 本质还是语法糖，使用prototype

> promise的基本使用

- Callback Hell
    ```javascript
        function loadImg(src, callback, fail) {
            var img = document.createElement('img')
            img.onload = function () {
                callback(img)
            }
            img.onerror = function () {
                fail()
            }
            img.src = src
        }
        var src = 'https://www.imooc.com/static/img/index/logo.png'
        loadImg(src, function(img){
            console.log(img.width)
        }, function() {
            console.log('failed')
        })
    ```
- Promise语法
    ```javascript
        function loadImg(src) {
            const promise = new Promise(function(resolve, reject) {
                var img = document.createElement('img')
                img.onload = function () {
                    resolve(img)
                }
                img.onerror = function () {
                    reject()
                }
                img.src = src
            })
            return promise
        }

        var src = 'https://www.imooc.com/static/img/index/logo.png'
        var result = loadImg(src)

        result.then(function(img) {
            console.log(img.width)
        }, function() {
            console.log('failed')
        })
    ```
- 总结
    - new Promise 实例，而且要return
    - new Promise 时要传入函数，函数有resolve，reject两个参数
    - 成功时执行resolve() 失败时执行reject()
    - then监听结果

> 总结一下es6其他常用功能

- let/const
    ```javascript
        let i = 20;
        i = 200;
        const j = 20;
        // j = 200  报错  常量不能再赋值
    ```
- 多行字符串/模板变量
    ```javascript
        let name = 'zhangsan', age = 20;
        const html = `
                <div>
                    <p>${name}</p>
                    <p>${age}</p>
                </div>
            `;
    ```
- 解构赋值
    ```javascript
        const obj = {a: 100, b: 200}
        const {a, b} = obj
        const arr = ['xxx', 'yyy', 'zzz']
        const [x, y, z] = arr
    ```
- 块级作用域
    ```javascript
        const obj = {a: 100, b: 200}
        for (let item in obj){
            console.log(item)
        }
        // console.log(item) 访问不到
    ```
- 函数默认参数
    ```javascript
        function fn(a, b=0){

        }
    ```
- 箭头函数
    ```javascript
        const arr = [1, 2, 3]
        arr.map(itme => item + 1)
        arr.map((item, index) => {
            console.log(item)
            return item + 1
        })
    ```
    ```javascript
        function fn() {
            console.log('real', this) // real {a: 100}

            var arr = [1, 2, 3]
            arr.map(function (item) {
                console.log(this) // window
            })

            arr.map(item => {
                console.log(this) // {a: 100}
            })
        }
        fn.call({a: 100})
    ```

## 异步

> 什么是单线程，和异步的关系

- 单线程 - 只有一个线程，只能做一件事
    ```javascript
        // 循环运行期间，JS 执行和 DOM 渲染暂时卡顿
        var i, sum = 0;
        for(i = 0; i< 1000000000; i++){
            sum += i;
        }
        console.log(sum)

        // alert 不处理，JS 执行和 DOM 渲染暂时卡顿
        console.log(1)
        alert('hello')
        console.log(2)
    ```
- 原因 - 避免DOM渲染的冲突
    - 浏览器需要渲染DOM
    - js可以修改DOM结构
    - js执行的时候，浏览器DOM渲染会暂停
    - 两段js也不能同时执行（都修改DOM就冲突了）
    - webworker支持多线程，但是不能访问DOM
- 解决方案 - 异步
    ```javascript
        console.log(100)
        setTimeout(function () {
            console.log(200)  // 反正 1000ms 之后执行
        }, 1000)              // 先不管它，先让其他js代码运行  
        console.log(300)
        console.log(400)
    ```
    ```javascript
        console.log(100) 
        $.ajax({
            url: 'xxx',
            success: function (result) { // ajax 加载完才执行
                console.log(result)      // 先不管它，先让其他js代码运行 
            }
        })
        console.log(300)
        console.log(400)
    ```

> 什么是event-loop

- 文字解释
    - 事件轮询，js实现异步的具体解决方案
    - 同步代码，直接执行
    - 异步函数先放在异步队列中
    - 待同步函数执行完毕，轮询执行异步队列的函数
- 实例分析
    ```javascript
        setTimeout(function () {
            console.log(100)
        })
        console.log(200)
        /*
        // 主进程                    // 异步队列
        console.log(200)            function () {
                                        console.log(100)
                                    }
        */
    ```
    ```javascript
        setTimeout(function () {
            console.log(1)
        }, 100)
        setTimeout(function () {
            console.log(2)
        })
        console.log(3)

        /*
        // 主进程                      // 异步队列

        console.log(3)                // 立即被放入
                                        function(){
                                            console.log(2)
                                        }
                                        
                                        // 100ms 之后被放入
                                        function(){
                                            console.log(1)
                                        }
        */
    ```
    ```javascript
        $.ajax({
            url: 'xxx',
            success: function () {
                console.log('a')
            }
        })
        setTimeout(function () {
            console.log('b')
        }, 100)
        setTimeout(function () {
            console.log('c')
        })
        console.log('d')

        /*
        // 主进程                      // 异步队列

        console.log('d')              // 立即被放入
                                        function(){
                                            console.log('c')
                                        }
                                        
                                        // 100ms 之后被放入
                                        function(){
                                            console.log('b')
                                        }
                                        // ajax 加载成功后执行
                                        console.log('a')

                                        不知道ajax加载和100ms快，所以不确定ajax先执行还是100ms快
        */
    ```

> jquery的Deferred

- jquery 1.5之后ajax的改变
- 使用jquery Deferred
    ```javascript
        function waitHandle() {
            var dtd = $.Deferred()
            var wait = function (dtd) {
                var task = function () {
                    console.log('执行完成')
                    // 成功
                    dtd.resolve()
                    // 失败
                    //dtd.reject()
                }
                setTimeout(task, 2000)
                return dtd.promise()  // 注意，这里返回的是promise而不是直接返回 deferred对象
            }
            return wait(dtd)
        }

        var w = waitHandle()  // w接收的就是一个promise对象
        $.when(w)
            .then(function () {
                console.log('ok 1')
            },function () {
                console.log('err 1')
            })
            .then(function () {
                console.log('ok 2')
            },function () {
                console.log('err 2')
            })
        // w.reject() // 执行这句话会直接报错
    ```
- 开放封闭的原则
    - 对扩展开放，对开发封闭

> promise 的基本使用和原理

- 基础语法
    ```javascript
        function loadImg(src) {
            const promise = new Promise(function(resolve, reject) {
                var img = document.createElement('img')
                img.onload = function () {
                    resolve(img)
                }
                img.onerror = function () {
                    reject('图片加载失败')
                }
                img.src = src
            })
            return promise
        }

        var src = 'https://www.imooc.com/static/img/index/logo.png'
        var result = loadImg(src)

        result.then(function(img) {
            console.log(img.width)
            return img
        }, function() {
            console.log('failed')
        }).then(function(img) {
            console.log(img.height)
            return img
        }, function() {
            console.log('failed')
        })
    ```
- 异常捕获（error和reject都要考虑）
    ```javascript
        // 规定：then 只接受一个参数，最后统一用 catch 捕获异常
        result.then(function(img) {
            console.log(img.width)
            return img
        }).then(function(img) {
            console.log(img.height)
        }).catch(function (ex) {
            // 最后统一 catch
            console.log(ex)
        })
    ```
- 多个串联 - 链式执行
    ```javascript
        var src1 = 'https://www.imooc.com/static/img/index/logo.png'
        var result1 = loadImg(src1)
        var src2 = 'https://img.mukewang.com/5d79998b0001a15618720632.jpg'
        var result2 = loadImg(src2)

        // 链式操作
        result1.then(function (img) {
            console.log('第一个图片加载完成')
            return result2 // 重要！！！
        }).then(function (img) {
            console.log('第二个图片加载完成')
        }).catch(function (ex) {
            // 最后统一 catch
            console.log(ex)
        })
    ```
- promise.all 和 promise.race
    ```javascript
        // promise.all 接收一个 promise 对象的数组
        // 待全部完成之后，统一执行 success
        Promise.all([result1, result2])
            .then(datas => {
                // 接收到的 datas 是一个数组，依次包含了多个 promise返回的内容
                console.log(datas[0])
                console.log(datas[1])
            })
        
        // promise.race 接收一个包含多个 promise 对象的数组
        // 只要有一个完成，就执行 success
        Promise.race([result1, result2])
            .then(data => {
                // data 即最先执行完成的 promise 的返回值
                console.log(data)
            })
    ```
- promise标准
    - 状态变化
        - 三种状态：pending  fulfilled  rejected
        - 初始状态是 pending
        - pending变为fulfilled，或者pending变为rejected
        - 状态变化不可逆
    - then
        - promise实例必须实现then这个方法
        - then()必须可以接受两个函数作为参数
        - then()返回的必须是一个promise实例    

> async/await

- 用法
    - 使用 await ，函数必须用async标识
    - await后面跟的是一个promise实例
    - 需要babel-polyfill

    ```javascript
        import 'babel-polyfill'

        function loadImg(src) {
            const promise = new Promise(function(resolve, reject) {
                var img = document.createElement('img')
                img.onload = function () {
                    resolve(img)
                }
                img.onerror = function () {
                    reject('图片加载失败')
                }
                img.src = src
            })
            return promise
        }
        const src1 = 'https://www.imooc.com/static/img/index/logo.png'
        const src2 = 'https://img.mukewang.com/5d79998b0001a15618720632.jpg'

        const load = async function () {
            const result1 = await loadImg(src1)
            console.log(result1)
            const result2 = await loadImg(src2)
            console.log(result2)
        }
        load()
    ```

> Generator

- 原理比较复杂
- 不是异步的直接替代方式
- 有更好更简洁的解决方案async/await
- koa 从Generator改为async/await
```javascript
    // 除了return语句，还可以用yield返回多次
    // 也可以把异步变为同步
    function* getInitList() {
        // 写法
        const res = yield axios.get('')
        // 处理逻辑 
        ...
    }
```

> 常用定时器函数

- setTimeout、setInterval、requestAnimationFrame 

## 手写代码

- 实现一个深拷贝（简易版）
    ```javascript
        function deepClone(obj) {
            // 判断是否是对象类型
            function isObject(o) {
                // 1.typeof o === 'object' || typeof o === 'function' 为true
                // 2.但是typeof判断null也是对象 所以不等于 typeof o !== null
                return (typeof o === 'object' || typeof o === 'function') && typeof o !== null
            }

            // 不是对象类型
            if (!isObject(obj)) {
                throw new Error('非对象')
            }

            // 是对象类型
            let isArray = Array.isArray(obj)
            let newObj = isArray ? [...obj] : {...obj} // 判断是数组还是对象
            
            // Reflect.ownKeys(newObj) 用于返回对象所有属性
            Reflect.ownKeys(newObj).forEach(key => {
                // 1.判断值是否是对象类型
                // 2.不是的话直接赋值
                // 3.是的话调用deepClone函数在执行一遍,直到不是对象类型
                newObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key]
            })

            // 最后返回对象
            return newObj
        }
    ```
- 通过Proxy来实现一个数据响应式
    ```javascript
        let onWatch = (obj, setBind, getLogger) => {
            let handler = {
                get(target, property, receiver) {
                    getLogger(target, property)
                    // 查找并返回target对象的property属性
                    return Reflect.get(target, property, receiver)
                },
                set(target, property, value, receiver) {
                    setBind(value, property)
                    // 设置target对象的property属性为value
                    return Reflect.set(target, property, value)
                }
            }
            return new Proxy(obj, handler)
        }
        let obj = { a: 1 }
        let p = onWatch(
            obj, 
            (v, property) => {
                console.log(`监听到属性${property}改变为${v}`)
            },
            (target, property) => {
                console.log(`'${property}' = ${target[property]}`)
            }
        )
        p.a = 2 // 监听到属性a改变
        p.a // 'a' = 2
    ```
- 手写call函数
    ```javascript
        /*
            首先 context 为可选参数，如果不传的话默认上下文为 window
            接下来给 context 创建一个 fn 属性，并将值设置为需要调用的函数
            因为 call 可以传入多个参数作为调用函数的参数，所以需要将参数剥离出来
            然后调用函数并将对象上的函数删除
        */
        Function.prototype.myCall = function(context) {
            if(typeof this !== 'function') {
                throw new TypeError('Error')
            }
            context = context || window
            context.fn = this
            const args = [...arguments].slice(1)
            const result = context.fn(...args)
            delete context.fn
            return result
        }
    ```
- 手写apply函数
    ```javascript
        Function.prototype.myApple = function(context) {
            if(typeof this !== 'function') {
                throw new TypeError('Error')
            }
            context = context || window
            context.fn = this
            let result
            
            if(arguments[1]) {
                result = context.fn(...arguments[1])
            } else {
                result = context.fn()
            }

            delete context.fn
            return result
        }
    ```
- 手写bind函数
    ```javascript
        /*
            bind 返回了一个函数，对于函数来说有两种方式调用，一种是直接调用，一种是通过 new 的方式，我们先来说直接调用的方式
            对于直接调用来说，这里选择了 apply 的方式实现，但是对于参数需要注意以下情况：
                因为 bind 可以实现类似这样的代码 f.bind(obj, 1)(2)，所以我们需要将两边的参数拼接起来，于是就有了这样的实现 args.concat(...arguments)
            最后来说通过 new 的方式，在之前的章节中我们学习过如何判断 this，对于 new 的情况来说，不会被任何方式改变 this，所以对于这种情况我们需要忽略传入的 this
        */
        Function.prototype.myBind = function(context) {
            if(typeof this !== 'function') {
                throw new TypeError('Error')
            }
            const _this = this
            const args = [...arguments].slice(1)
            // 返回一个函数
            return function F() {
                // 因为返回了一个函数，我们可以 new F(),所以需求判断
                if(this instanceof F) {
                    return new _this(...args, ...arguments)
                }
                return _this.apply(context, args.concat(...arguments))
            }
        }
    ```
- new的原理
    ```javascript
        /*
          在调用 new 的过程中会发生以上四件事情:  
            1、新生成了一个对象
            2、链接到原型
            3、绑定 this
            4、返回新对象
        */
        /*
            创建一个空对象
            获取构造函数
            设置空对象的原型
            绑定this并执行构造函数
            确保返回值为对象
        */
        function create() {
            let obj = {}
            let Con = [].shift.call(arguments)
            obj.__proto__ = Con.prototype
            let result = Con.apply(obj, arguments)
            return result instanceof Object ? result : obj
        }
    ```
- instanceof的原理
    ```javascript
        /*
            instanceof 可以正确的判断对象的类型，因为内部机制是通过判断对象的原型链中是不是能找到类型的 prototype
        */
        /*
            首先获取类型的原型
            然后获得对象的原型
            然后一直循环判断对象的原型是否等于类型的原型，直到对象原型为 null，因为原型链最终为 null 
        */
        function myInstanceof(left, right) {
            let prototype = right.prototype
            left = left.__proto__
            while(true) {
                if(left === null || left === undefined) {
                    return false
                }
                if(prototype === left) {
                    return true
                }
                left = left.__proto__
            }
        }
    ```
- 节流
    ```javascript
        // func是用户传入需要防抖的函数
        // wait是等待时间
        const throttle = (func, wait = 50) => {
        // 上一次执行该函数的时间
        let lastTime = 0
        return function(...args) {
            // 当前时间
            let now = +new Date()
            // 将当前时间和上一次执行函数时间对比
            // 如果差值大于设置的等待时间就执行函数
            if (now - lastTime > wait) {
            lastTime = now
            func.apply(this, args)
            }
        }
        }

        setInterval(
            throttle(() => {
                console.log(1)
            }, 500),
            1
        )
    ```
- 防抖
    ```javascript
        // func是用户传入需要防抖的函数
        // wait是等待时间
        const debounce = (func, wait = 50) => {
        // 缓存一个定时器id
        let timer = 0
        // 这里返回的函数是每次用户实际调用的防抖函数
        // 如果已经设定过定时器了就清空上一次的定时器
        // 开始一个新的定时器，延迟执行用户传入的方法
        return function(...args) {
                if (timer) clearTimeout(timer)
                timer = setTimeout(() => {
                    func.apply(this, args)
                }, wait)
            }
        }
    ```

## web存储

> cookie

- 一般由服务器生成，可以设置过期时间
- 数据存储大小 4k
- 每次请求都会携带在 header 中，对于请求性能影响

> localStorage

- 除非被清理，否则一直存在
- 数据存储大小 5M

> sessionStorage

- 页面关闭就清理
- 数据存储大小 5M

## 设计模式 (常用的几个)

> 工厂模式

```javascript
    /*
        假设有一份很复杂的代码需要用户去调用，但是用户并不关心这些负责的代码，
        只需要你提供给我一个接口去调用，用户只负责传递需要的参数，至于这些参数怎么使用，
        内部逻辑是不关心的，只需要你最后返回我一个实例。这个构造过程就是工厂。
    */
    class Man {
        constructor(name) {
            this.name = name
        }
        alertName() {
            alert(this.name)
        }
    }

    class Factory {
        static create(name) {
            return new Man(name)
        }
    }

    Factory.create('xxx').arertName()
```

> 单例模式

```javascript
    /*
        单例模式的核心就是保证全局只有一个对象可以访问
    */
    class Singleton{
        constructor() {}
    }
    Singleton.getInstance = (function() {
        let instance
        return function() {
            if (!instance) {
            instance = new Singleton()
            }
            return instance
        }
    })()
```

> 适配器模式

```javascript
    /*
        适配器用来解决两个接口不兼容的情况，不需要改变已有的接口，
        通过包装一层的方式实现两个接口的正常协作。
    */
    class Plug {
        getName() {
            return '1类插头'
        }
    }

    class Target {
        constructor() {
            this.plug = new Plug()
        }
        getName() {
            return this.plug.getName + ' 适配器转 2类插头'
        }
    }
    let target = new Target()
    target.getName() // 1类插头 适配器转 2类插头
```

> 装饰模式

```javascript
    /*
        装饰模式不需要改变已有的接口，作用是给对象添加功能。
    */
    function readonly(target, key, descriptor) {
        descriptor.writable = false
        return descriptor
    }

    class Test {
        @readonly
        name = 'xxx'
    }

    let t = new Test()
    t.xxx = '111'  // 不可修改

    // 类似于 Object.defineProperty(name, 'xxx', descriptor)
    // writable=false 属性不可修改
```

> 代理模式

```javascript
    /*
        代理是为了控制对对象的访问，不让外部直接访问到对象。
    */
    <ul id="ul">
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
    </ul>
    <script>
        let ul = document.querySelector('#ul')
        ul.addEventListener('click', (event) => {
            console.log(event.target);
        })
    </script>
```

> 发布-订阅模式

```javascript
    /*
        发布-订阅模式也叫做观察者模式。
        通过一对一或者一对多的依赖关系，当对象发生改变时，订阅方都会收到通知。
    */
    // 比如我们点击一个按钮触发了点击事件就是使用了该模式
    <ul id="ul"></ul>
    <script>
        let ul = document.querySelector('#ul')
        ul.addEventListener('click', (event) => {
            console.log(event.target);
        })
    </script>
```

> 外观模式

```javascript
    /*
        外观模式提供了一个接口，隐藏了内部的逻辑，更加方便外部调用。
    */
    // 实现一个兼容多种浏览器的添加事件方法
    // 将这些判断逻辑统一封装在一个接口中，外部需要添加事件只需要调用 addEvent 即可
    function addEvent(elm, evType, fn, useCapture) {
        if (elm.addEventListener) {
            elm.addEventListener(evType, fn, useCapture)
            return true
        } else if (elm.attachEvent) {
            var r = elm.attachEvent("on" + evType, fn)
            return r
        } else {
            elm["on" + evType] = fn
        }
    }
```

## 浏览器相关的一些问题

> 事件机制

- 事件触发三阶段
    - window往事件触发处传播，遇到注册的捕获事件会触发
    - 传播到事件触发处时触发注册的事件
    - 从事件触发处往window传播，遇到注册的冒泡事件会触发
- 注册事件
    - addEventListener 注册事件
        - capture：布尔值，和 useCapture 作用一样
        - once：布尔值，值为 true 表示该回调只会调用一次，调用后会移除监听
        - passive：布尔值，表示永远不会调用 preventDefault
    - stopPropagation阻止事件冒泡，也能阻止捕获事件
- 事件代理
    - 优点
        - 节省内存
        - 不需要给子节点注销事件

> 输入 URL 到页面渲染的整个流程

- 浏览器根据请求的URL交给DNS域名解析，找到真实IP，向服务器发起请求；
- 服务器交给后台处理完成后返回数据，浏览器接收文件（HTML、JS、CSS、图象等）；
- 浏览器对加载到的资源（HTML、JS、CSS等）进行语法解析，建立相应的内部数据结构（如HTML的DOM）；
- 载入解析到的资源文件，渲染页面，完成。

> 跨域

- JSONP
    - JSONP 的原理很简单，就是利用 `<script>` 标签没有跨域限制的漏洞。
    - 通过 `<script>` 标签指向一个需要访问的地址并提供一个回调函数来接收数据当需要通讯时。
    - 但是只限于 get 请求
- CORS
    - CORS 需要浏览器和后端同时支持。IE 8 和 9 需要通过 XDomainRequest 来实现。
- document.domain
    - 该方式只能用于二级域名相同的情况下，比如 a.test.com 和 b.test.com 适用于该方式
- postMessage
    - 这种方式通常用于获取嵌入页面中的第三方页面数据。一个页面发送消息，另一个页面判断来源并接收消息

> 浏览器渲染原理

- 浏览器接收到 HTML 文件并转换为 DOM 树
- 将 CSS 文件转换为 CSSOM 树
- 当我们生成 DOM 树和 CSSOM 树以后，就需要将这两棵树组合为渲染树。

> 重绘（Repaint）和回流（Reflow）

- 重绘是当节点需要更改外观而不会影响布局的，比如改变 color 就叫称为重绘
- 回流是布局或者几何属性需要改变就称为回流。
- 回流必定会发生重绘，重绘不一定会引发回流。

> 减少重绘和回流

- 使用 transform 替代 top
- 使用 visibility 替换 display: none ，因为前者只会引起重绘，后者会引发回流（改变了布局）
- 不要把节点的属性值放在一个循环里当成循环里的变量
- 不要使用 table 布局，可能很小的一个小改动会造成整个 table 的重新布局
- CSS 选择符从右往左匹配查找，避免节点层级过多

## 性能优化

> 图片优化

- 计算图片大小
    - 减少像素点
    - 减少每个像素点能够显示的颜色
- 图片加载优化
    - 不用图片。修饰类图片完全可以用css去代替
    - 移动端图片没必要放原图。一般图片都用CDN加载，可以计算出适配屏幕的宽度，然后去请求相应裁剪好的图片
    - 小图使用base64格式
    - 将多个图标文件整合到一张图片中（雪碧图）
    - 选择正确的图片格式：
        - 对于能够显示WebP格式的浏览器尽量使用WebP格式。
            - 因为 WebP 格式具有更好的图像数据压缩算法，能带来更小的图片体积，而且拥有肉眼识别无差异的图像质量，缺点就是兼容性并不好
        - 小图使用PNG，其实对于大部分图标这类图片，完全可以使用SVG代替
        - 照片使用JPEG 

> DNS 预解析

- DNS 解析也是需要时间的，可以通过预解析的方式来预先获得域名所对应的 IP。
    ```html
        <link rel="dns-prefetch" href="//yuchengkai.cn">
    ```

> 节流

- 考虑一个场景，滚动事件中会发起网络请求，但是我们并不希望用户在滚动过程中一直发起请求，而是隔一段时间发起一次，对于这种情况我们就可以使用节流。

> 防抖

- 考虑一个场景，有一个按钮点击会触发网络请求，但是我们并不希望每次点击都发起网络请求，而是当用户点击按钮一段时间后没有再次点击的情况才去发起网络请求，对于这种情况我们就可以使用防抖。

> 预加载

- 预加载其实是声明式的 fetch ，强制浏览器请求资源，并且不会阻塞 onload 事件，可以使用以下代码开启预加载
    ```html
        <link rel="preload" href="http://example.com">
    ```
- 预加载可以一定程度上降低首屏的加载时间，因为可以将一些不影响首屏但重要的文件延后加载，唯一缺点就是兼容性不好。

> 预渲染

- 可以通过预渲染将下载的文件预先在后台渲染，可以使用以下代码开启预渲染
    ```html
        <link rel="prerender" href="http://example.com"> 
    ```
- 预渲染虽然可以提高页面的加载速度，但是要确保该页面大概率会被用户在之后打开，否则就是白白浪费资源去渲染。

> 懒执行

- 懒执行就是将某些逻辑延迟到使用时再计算。
- 该技术可以用于首屏优化，对于某些耗时逻辑并不需要在首屏就使用的，就可以使用懒执行。
- 懒执行需要唤醒，一般可以通过定时器或者事件的调用来唤醒

> 懒加载

- 懒加载就是将不关键的资源延后加载。

> CDN

- CDN 的原理是尽可能的在各个地方分布机房缓存数据，这样即使我们的根服务器远在国外，在国内的用户也可以通过国内的机房迅速加载资源。
- 因此，我们可以将静态资源尽量使用 CDN 加载，由于浏览器对于单个域名有并发请求上限，可以考虑使用多个 CDN 域名。
- 并且对于 CDN 加载静态资源需要注意 CDN 域名要与主站不同，否则每次请求都会带上主站的 Cookie，平白消耗流量。
