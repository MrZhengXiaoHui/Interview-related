## 虚拟DOM (virtual dom)

> vdom是什么？为何会存在vdom

- 什么是vdom
    - virtual dom，虚拟DOM
    - 用js模拟DOM结构
    - DOM变化的对比，放在js层来做（图灵完备语言）
    - 提高重绘性能
- 为何会存在vdom
    - DOM操作是"昂贵"的，js运行效率高
    - 尽量减少DOM操作，而不是清空重新渲染
    - 将DOM对比操作放在js层，提高效率

> vdom的如何应用，核心api是什么

- 介绍snabbdom
    - 一个虚拟DOM库，专注于简单性，模块化，强大的功能和性能。
    ```html
        <div id="container"><div>
        <button id="btn-change">change</button>

        <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom.js"></script>
        <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-class.js"></script>
        <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-props.js"></script>
        <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-style.js"></script>
        <script src="https://cdn.bootcss.com/snabbdom/0.7.3/snabbdom-eventlisteners.js"></script>
        <script src="https://cdn.bootcss.com/snabbdom/0.7.3/h.js"></script>
        
        <script type="text/javascript">
            var snabbdom = window.snabbdom

            // 定义 pathch
            var pathch = snabbdom.init([
                snabbdom_class,
                snabbdom_props,
                snabbdom_style,
                snabbdom_eventlisteners
            ])

            // 定义 h
            var h = snabbdom.h

            var container = document.getElementById('container')

            // 生成 vnode
            var vnode = h('ul#list', {}, [
                h('li.item', {}, 'Item 1'),
                h('li.item', {}, 'Item 2')
            ])
            pathch(container, vnode)

            document.getElementById('btn-change').addEventListener('click',function () {
                // 生成 newVnode
                var newVnode = h('ul#list', {}, [
                    h('li.item', {}, 'Item 1'),
                    h('li.item', {}, 'Item B'),
                    h('li.item', {}, 'Item 3'),
                ])
                pathch(vnode, newVnode)
            })

        </script>
    ```
- 核心API
    - h函数
        - `h('<标签名>', {...属性...}, [...子元素...])`
        - `h('<标签名>', {...属性...}, '...')`
    - patch函数
        - `pathch(container, vnode)`
        - `pathch(vnode, newVnode)`

> 介绍一下diff算法

- 什么是diff算法
    - Linux 的 diff命令
    - git diff命令
- vdom为何用diff算法
    - DOM操作是"昂贵"的，因此尽量减少DOM操作
    - 找出本次DOM必须更新的节点来更新，其他的不更新
    - 这个"找出"的过程，就需要diff算法
- diff算法的实现流程
    - `pathch(container, vnode)`
        ```
            {
                tag: 'ul',
                attrs: {
                    id: 'list'
                },
                children: [
                    {
                        tag: 'li',
                        attrs: {
                            className: 'item'
                        },
                        children: ['Item 1']
                    }
                ]
            }
                    
            <ul id="list"> 
                <li class="item">Item 1</li>
            </ul>
        ```
        ```javascript
            // 实现
            function createElement(vnode){
                var tag = vnode.tag 
                var attrs = vnode.attrs || {}
                var children = vnode.children || []

                if(!tag){
                    return null
                }

                // 创建真实的 DOM 元素
                var elem = document.createElement(tag)
                // 属性
                var attrName
                for(attrName in attrs){
                    if(attrs.hasOwnProperty(attrName)){
                        // 给 elem 添加属性
                        elem.setAttribute(attrName, attrs[attrName]) 
                    }
                }
                // 子元素
                children.forEach(function (childVnode) {
                    // 给 elem 添加子元素
                    elem.appendChild(createElement(childVnode)) // 递归 
                })
                
                // 返回真实的 DOM 元素
                return elem
            }
        ```
    - `pathch(vnode, newVnode)`
        ```
            {
                tag: 'ul',
                attrs: { id: 'list' },
                children: [
                    {
                        tag: 'li',
                        attrs: { className: 'item' },
                        children: ['Item 1']
                    },
                    {
                        tag: 'li',
                        attrs: { className: 'item' },
                        children: ['Item 2']
                    }
                ]
            } 

            {
                tag: 'ul',
                attrs: { id: 'list' },
                children: [
                    {
                        tag: 'li',
                        attrs: { className: 'item' },
                        children: ['Item 1']
                    },
                    {
                        tag: 'li',
                        attrs: { className: 'item' },
                        children: ['Item 222']
                    },
                    {
                        tag: 'li',
                        attrs: { className: 'item' },
                        children: ['Item 3']
                    }
                ]
            } 
        ```
        ```javascript
            // 实现
            function updateChildren(vnode, newVnode) {
                var children = vnode.children || []
                var newChildren = newVnode.children || []

                children.forEach(function (childVnode, index) {
                    var newChildVnode = newChildren[index]
                    if(childVnode.tag === newChildVnode.tag){
                        // 深层次对比，递归
                        updateChildren(childVnode,newChildVnode)
                    } else {
                        // 替换
                        replaceNode(childVnode,newChildVnode)
                    }
                })  
            }

            function replaceNode(vnode, newVnode){
                var elem = vnode.elem // 真实的 DOM 节点
                var newElem = createElement(newVnode)

                // 替换
            }
        ```
    - 不仅仅是以上内容
        - 节点新增和删除
        - 节点重新排序
        - 节点属性、样式、事件绑定
        - 如何极致压榨性能
        - ......

## vue

> 说一下使用jquery和vue框架的区别

- 数据和视图的分离，解耦(开放封闭原则)
- 以数据驱动视图，只关心数据变化，DOM操作被封装

> 生命周期钩子函数

- 在 beforeCreate 钩子函数调用的时候，是获取不到 props 或者 data 中的数据的，因为这些数据的初始化都在 initState 中。
- 然后会执行 created 钩子函数，在这一步的时候已经可以访问到之前不能访问到的数据，但是这时候组件还没被挂载，所以是看不到的。
- 接下来会先执行 beforeMount 钩子函数，开始创建 VDOM，最后执行 mounted 钩子，并将 VDOM 渲染为真实 DOM 并且渲染数据。组件中如果有子组件的话，会递归挂载子组件，只有当所有子组件全部挂载完毕，才会执行根组件的挂载钩子。
- 接下来是数据更新时会调用的钩子函数 beforeUpdate 和 updated，这两个钩子函数没什么好说的，就是分别在数据更新前和更新后会调用。
- 另外还有 keep-alive 独有的生命周期，分别为 activated 和 deactivated 。用 keep-alive 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 deactivated 钩子函数，命中缓存渲染后会执行 actived 钩子函数。
- 最后就是销毁组件的钩子函数 beforeDestroy 和 destroyed。前者适合移除事件、定时器等等，否则可能会引起内存泄露的问题。然后进行一系列的销毁操作，如果有子组件的话，也会递归销毁子组件，所有子组件都销毁完毕后才会执行根组件的 destroyed 钩子函数。

> 组件通信

- 父子组件通信
    - 父组件通过 props 传递数据给子组件，子组件通过 emit 发送事件传递数据给父组件
    - 如果你使用 Vue 2.3 及以上版本的话还可以使用 $listeners 和 .sync 这两个属性。
    - $listeners 属性会将父组件中的 (不含 .native 修饰器的) v-on 事件监听器传递给子组件，子组件可以通过访问 $listeners 来自定义监听器。
        ```html
            <!--父组件中-->
            <input :value.sync="value" />
            <!--以上写法等同于-->
            <input :value="value" @update:value="v => value = v"></comp>
            <!--子组件中-->
            <script>
                this.$emit('update:value', 1)
            </script>
        ```
- 兄弟组件通信
    - 对于这种情况可以通过查找父组件中的子组件实现，也就是 this.$parent.$children，在 $children 中可以通过组件 name 查询到需要的组件实例，然后进行通信。
    - eventBus。在一个js里，new Vue()实例。在组件里引用,绑定自定义事件event.$on()。兄弟组件中引用js，触发事件event.$emit。组件销毁是要移除自定义事件event.$off
- 跨多层级组件通信
    - 对于这种情况可以使用 Vue 2.2 新增的 API provide / inject
        ```javascript
            // 父组件 A
            export default {
                provide: {
                    data: 1
                }
            }
            // 子组件 B
            export default {
                inject: ['data'],
                mounted() {
                    // 无论跨几层都能获得父组件的 data 属性
                    console.log(this.data) // => 1
                }
            }
        ```
- 任意组件
    - 这种方式可以通过 Vuex 或者 Event Bus 解决

> extend 能做什么

- 作用是扩展组件生成一个构造器，通常会与 $mount 一起使用。
    ```javascript
        // 创建组件构造器
        let Component = Vue.extend({
            template: '<div>test</div>'
        })
        // 挂载到 #app 上
        new Component().$mount('#app')
        // 除了上面的方式，还可以用来扩展已有的组件
        let SuperComponent = Vue.extend(Component)
        new SuperComponent({
            created() {
                console.log(1)
            }
        })
        new SuperComponent().$mount('#app') 
    ```

> mixin 和 mixins 区别

- mixin 用于全局混入，会影响到每个组件实例，通常插件都是这样做初始化的。
    ```javascript
        Vue.mixin({
            beforeCreate() {
                // ...逻辑
                // 这种方式会影响到每个组件的 beforeCreate 钩子函数
            }
        })
    ```
- mixins 应该是我们最常使用的扩展组件的方式了。如果多个组件中有相同的业务逻辑，就可以将这些逻辑剥离出来，通过 mixins 混入代码，比如上拉下拉加载数据这种逻辑等等

> computed 和 watch 区别

- computed 是计算属性，依赖其他属性计算值，并且 computed 的值有缓存，只有当计算值变化才会返回内容。
- watch 监听到值的变化就会执行回调，在回调中可以进行一些逻辑操作。
- 所以一般来说需要依赖别的属性来动态获得值的时候可以使用 computed，对于监听到值的变化需要做一些复杂业务逻辑的情况可以使用 watch。

> keep-alive 组件有什么作用

- 如果你需要在组件切换的时候，保存一些组件的状态防止多次渲染，就可以使用 keep-alive 组件包裹需要保存的组件。
- 对于 keep-alive 组件来说，它拥有两个独有的生命周期钩子函数，分别为 activated 和 deactivated 。
- 用 keep-alive 包裹的组件在切换时不会进行销毁，而是缓存到内存中并执行 deactivated 钩子函数，命中缓存渲染后会执行 actived 钩子函数。

>  Vue3.0 要使用 Proxy 替换原本的 API 原因

- Proxy 无需一层层递归为每个属性添加代理，一次即可完成以上操作，性能上更好
- 原本的实现有一些数据更新不能监听到，但是 Proxy 可以完美监听到任何方式的数据改变.
- 唯一缺陷可能就是浏览器的兼容性不好了

> NextTick

- nextTick 可以让我们在下次 DOM 更新循环结束之后执行延迟回调，用于获得更新后的 DOM。

> vue的三要素

- 响应式：vue如何监听到data的每个属性变化？
- 模板引擎：vue的模板如何被解析，指令如何处理？
- 渲染：vue的模板如何被渲染成html？以及渲染过程

> vue中如何实现响应式

- 什么是响应式
    - 修改data属性之后，vue立即监听到
    - data属性被代理到vm上
- Object.defineProperty
    ```javascript
        var obj = {}
        var _name = 'zhangsan'
        Object.defineProperty(obj, 'name', {
            get: function () {
                console.log('get', _name) // 监听
                return _name
            },
            set: function (newVal) {
                console.log('set', newVal) // 监听
                _name = newVal
            }
        })
    ```

> vue如何解析模板

- 模板是什么
    - 本质：字符串
    - 有逻辑，如v-if，v-for等
    - 与html格式很像，但有很大区别
    - 最终还要转换为html来显示
    - 模板最终必须转换成js代码，因为：
    - 有逻辑(v-if v-for)，必须用js才能实现(图灵完备)
    - 转换为html渲染页面，必须用js才能实现
    - 因此，模板最终要转换成一个js函数(render函数)
- render函数
    - with的用法
        ```javascript
            var obj = {
                name: 'zhangsan',
                age: 20,
                getAddress: function () {
                    console.log('beijing')
                }
            }

            function fn() {
                with(obj) {
                    console.log(name)
                    console.log(age)
                    getAddress()
                }
            }
            fn()
        ```
    - 模板中所有信息都包含在了render函数中
    - this即vm
    - price 即 this.price 即 vm.price 即 data 中的price
    - _c 即 this._c 即 vm._c
    ```html
        <div id="app">
            <p>{{ price }}</p>
        </div>
        <script>
            var vm = new Vue({
                el: 'app',
                data: {
                    price: 100
                }
            })

            // 以下是手写的 render 函数
            function render() {
                with(this) { // this 就是vm
                    return _c(
                        'div',
                        {
                            attrs: { 'id': 'app' }
                        },
                        [
                            _c('p', [ _v(_s(price)) ])
                        ]
                    )
                }
            }
            function render1() {
                return vm._c(
                    'div',
                    {
                        attrs: { 'id': 'app' }
                    },
                    [
                        vm._c('p', [ vm._v(vm._s(vm.price)) ])
                    ]
                )
            }
            // vm.price   100 就是值
            // vm._s   JSON.stringify  转换成字符串
            // vm._v   文本节点
            // vm._c  函数
        </script>
    ```
    ```html
        <div id="app">
            <div>
                <input v-model="title">
                <button v-on:click="add"></button>
            </div>
            <div>
                <ul>
                    <li v-for="item in list">{{item}}</li>
                </ul>
            </div>
        </div>
        <script type="text/javascript">
            // data 独立
            var data = {
                title: '',
                list: []
            }
            // 初始化 Vue 实例
            var vm = new Vue({
                el: '#app',
                data: data,
                methods: {
                    add: function () {
                        this.list.push(this.title)
                        this.title = ''
                    }
                }
            })
            // render 函数
            with(this) {
                return _c('div', {
                    attrs: {
                        "id": "app"
                    }
                }, [_c('div', [_c('input', {
                    directives: [{
                        name: "model",
                        rawName: "v-model",
                        value: (title),
                        expression: "title"
                    }],
                    domProps: {
                        "value": (title)
                    },
                    on: {
                        "input": function ($event) {
                            if ($event.target.composing) return;
                            title = $event.target.value
                        }
                    }
                }), 
                _v(" "), 
                _c('button', {
                    on: {
                        "click": add
                    }
                })]), 
                _v(" "), 
                _c(
                    'div', 
                    [
                        _c('ul', _l((list), function (item) {
                            return _c('li', [_v(_s(item))])
                        }), 0)
                    ]
                )])
            }
        </script>
    ```
    - v-model怎么实现的
        ```javascript
            _c('input', {
                directives: [{
                    name: "model",
                    rawName: "v-model",
                    value: (title),
                    expression: "title"
                }],
                domProps: {
                    "value": (title)
                },
                on: {
                    "input": function ($event) {
                        if ($event.target.composing) return;
                        title = $event.target.value
                    }
                }
            })
        ```
    - v-for怎么实现的
        ```javascript
            _c('ul',
                _l((list), function (item) {
                    return _c('li', [_v(_s(item))])
                })
            , 0)
        ```
- render函数与vdom
    - snabbdom的 h函数 与vue的 _c函数 基本是相似的
    - render函数执行之后，返回的是vnode
        ```javascript
            vm.update(vnode) {
                const prevVnode = vm._vnode
                vm._vnode = vnode
                if(!prevVnode) {
                    vm.$el = vm.__patch__(vm.$el, vnode)
                } else {
                    vm.$el = vm.__patch__(prevVnode, vnode)
                }
            }
            function updateComponent() {
                // vm._render 即 上面的 render 函数 ，返回 vnode
                vm._update(vm._render())
            }
        ```
        - updateComponent 中实现了 vdom 的patch
        - 页面首次渲染执行 updateComponent
        - data 中每次修改属性，执行 updateComponent

> vue的整个实现流程

- 第一步：解析模板成render函数
    - with的用法
    - 模板中的所有信息都被render函数包含
    - 模板中用到的data中的属性，都变成了js变量
    - 模板中的v-model v-for v-on都变成了js逻辑
    - render 函数返回vnode
- 第二步：响应式开始监听
    - Object.defineProperty
    - 将data的属性代理到vm上
- 第三步：首次渲染，显示页面，且绑定依赖
    - 初次渲染，执行 updateComponent，执行vm._render()
    - 执行render函数，会访问到vm.list和vm.title
    - 会被响应式的get方法监听到
        - 为何要监听get，直接监听set不行吗？
            - data中有很多属性，有些被用到，有些可能不被用到
            - 被用到的会走到get，不被用到的不会走到get
            - 未走到get中的属性，set的时候我们也无需关心
            - 避免不必要的重复渲染
    - 执行updateComponent，会走到vdom的patch方法
    - patch将vnode渲染成DOM，初次渲染完成
- 第四步：data属性变化，触发rerender
    - 修改属性，被响应式的set监听到
    - set中执行updateComponent
    - updateComponent重新执行vm._render()
    - 生成的vnode和prevVnode，通过patch进行对比
    - 渲染到html

## react 

> react 的生命周期

- 生命周期函数指在某一个时刻组件会自动调用执行的函数
- 组件初始化 initialization
    - 设置 props和state
- 组件的挂载 Mounting
    - componentWillMount
        - 在组件即将被挂载到页面的时刻自动执行
    - render
    - componentDidMount
        - 组件被挂载到页面之后，自动被执行
- 组件的更新 Updation
    - state更新时
        - shouldComponentUpdate
            - 组件被更新之前，它会自动被执行
            - retuen true会往下执行
            - return false不会往下执行
        - componentWillUpdate
            - 组件被更新之前，它会自动执行，但是它在shouldComponentUpdate之后
            - 如果shouldComponentUpdate返回true，它才执行
            - 如果返回false，这个函数就不会被执行了
        - render
        - componentDidUpdate
            - 组件更新完成之后，它会被执行
    - props更新时
        - componentWillReceiveProps
            - 一个组件要从父组件接收参数
            - 如果这个组件第一次存在于父组件中，不会执行
            - 如果这个组件之前已经存在于父组件中，才会执行
        - shouldComponentUpdate
            - 组件被更新之前，它会自动被执行
            - retuen true会往下执行
            - return false不会往下执行
        - componentWillUpdate
            - 组件被更新之前，它会自动执行，但是它在shouldComponentUpdate之后
            - 如果shouldComponentUpdate返回true，它才执行
            - 如果返回false，这个函数就不会被执行了
        - render
        - componentDidUpdate
            - 组件更新完成之后，它会被执行
- 组件的卸载 Unmounting
    - componentWillUnmount
        - 当这个组件即将从页面中剔除的时候，会被执行

> setState

- setState是异步的
- 可以通过第二个参数通过回调函数处理后面的逻辑

> 通信

- 父子组件通信
    - 父组件通过 props 传递数据给子组件，子组件通过调用父组件传来的函数传递数据给父组件
- 兄弟组件通信
    - 对于这种情况可以通过共同的父组件来管理状态和事件函数。
    - 比如说其中一个兄弟组件调用父组件传递过来的事件函数修改父组件中的状态，然后父组件将状态传递给另一个兄弟组件。
- 跨多层级组件通信
    - 如果你使用 16.3 以上版本的话，对于这种情况可以使用 Context API。
        ```JSX
            // 创建 Context，可以在开始就传入值
            const StateContext = React.createContext()
            class Parent extends React.Component {
                render () {
                    return (
                        // value 就是传入 Context 中的值
                        <StateContext.Provider value='yck'>
                            <Child />
                        </StateContext.Provider>
                    )
                }
            }
            class Child extends React.Component {
                render () {
                    return (
                        <ThemeContext.Consumer>
                            // 取出值
                            {context => (
                                name is { context }
                            )}
                        </ThemeContext.Consumer>
                    );
                }
            }
        ```
- 任意组件
    - 这种方式可以通过 Redux 或者 Event Bus 解决

> HOC 是什么？

- 高阶组件（HOC）
- 作用就是为了更好的复用代码

> Hooks

> react 几个性能优化

- 避免重复渲染,判断是否让render函数执行
    ```javascript
        // 性能优化
        shouldComponentUpdate(nextProps, nextState) {
            // 对比数据是否相同，从而是否让render函数执行
            if (nextProps.content != this.props.content) {
                return true;
            } else {
                return false;
            }
        }

        // react 为了防止每个页面都写shouldComponentUpdate
        // 底层实现了api Component改成 PureComponent
        // 如果用 PureComponent 的话 要用 immutable管理数据。不然会遇到坑
        import React, { Component } from 'react';
        import React, { PureComponent } from 'react';
    ```
- 做循环时key的取值，尽量不要用index下标
    - 一般用item做为key值
    - 设置key值是为了虚拟dom做对比的时候，快速定位
- ajax请求放在componentDidMount里
- 当普通组件只有render函数时，用无状态组件定义
    - 无状态组件性能比普通组件好，因为普通组件要执行生命周期函数

> 单向数据流

- react 父组件向子组件传值是只读属性
- 一般修改父组件的数据是父组件向子组件传递父组件的一个方法让子组件调用，从而修改数据

> 说一下对组件化的理解

- 组件的封装
    - 视图
    - 数据
    - 变化逻辑(数据驱动视图变化)
- 组件的复用
    - props传递
    - 复用

> JSX本质是什么

- JSX语法
    - html形式
    - 引入js变量和表达式
    - if...else...
    - 循环
    - style和className
    - 事件
- JSX解析成js
    - React.createElement 参数说明
        ```javascript
            // React.createElement 参数说明
            React.createElement('div', {id: 'div1'}, child1, child2, child3)
            React.createElement('div', {id: 'div1'}, [...])
        ```
    - 解析
        ```jsx
            /* JSX 代码 */
            var profile = <div>
                            <img src="avatar.png" className="profile"/>
                            <h3> {[user.firstName, user.lastName].join(' ')}</h3>
                          </div>;

            // 解析结果
            var profile = React.createElement("div", null,
                React.createElement("img", { src:"avatar.png" className: "profile"}),
                React.createElement("h3", null, [user.firstName, user.lastName].join(' '))
            )
        ```
    - JSX其实是语法糖
    - 开发环境会将JSX编译成js代码
    - JSX的写法大大降低了学习成本和编码工作量
    - 同时，JSX也会增加debug成本
- 独立的标准
    - JSX是React引入的，但不是React独有的
    - React已经将它作为一个独立标准开放，其他项目也可用
    - React.createElement是可以自定义修改的
    - 说明：本身功能已经具备；和其他标准兼容和扩展性没问题

> JSX和vdom的关系

- 分析：为何需要vdom
    - vdom是React初次推广开来的，结合JSX
    - JSX就是模板，最终要渲染成html
    - 初次渲染+修改state后的re-render
    - 正好符合vdom的应用场景
- React.createElement和h
    ```jsx
            /* JSX 代码 */
            var profile = <div>
                            <img src="avatar.png" className="profile"/>
                            <h3> {[user.firstName, user.lastName].join(' ')}</h3>
                          </div>;

            // 解析结果
            var profile = React.createElement("div", null,
                React.createElement("img", { src:"avatar.png" className: "profile"}),
                React.createElement("h3", null, [user.firstName, user.lastName].join(' '))
            )


            // h函数
            var vnode = h("div", null,
                h("img", { src:"avatar.png" className: "profile"}),
               h("h3", null, [user.firstName, user.lastName].join(' '))
            )
        ```
- 何时patch？
    - 初次渲染 - `ReactDOM.render(<App/>, container)`
    - 会触发patch(container, vnode)
    - re-render - `setState`
    - 会触发patch(vnode, newVnode)
- 自定义组件的解析
    - 自定义组件要用render返回
    - new 自定义组件

> 说一下setState的过程

- setState的异步
    - setState为何需要异步
        - 可能会一次执行多次setState
        - 你无法规定、限制用户如何使用setState
        - 没必要每次setState都重新渲染，考虑性能
        - 即便是每次重新渲染，用户也看不到中间的效果
        - 只看到结果即可
- vue修改属性也是异步
    - set中执行updateComponent  是异步的
- setState的过程
    - 每个组件实例，都有renderComponent方法
    - 执行renderComponent会重新执行实例的render
    - render函数返回newVnode，然后拿到preVnode
    - 执行patch(proVnode, newVnode)

> 阐述自己对react和vue的认识

- 两者的本质区别
    - vue - 本质是MVVM框架，由MVC发展而来
    - React - 本质是前端组件化框架，由后端组件化发展而来
    - 但这并不妨碍他们两者都能实现相同的功能
- 模板的区别
    - vue - 使用模板(最初由angular提出)
    - React - 使用JSX
    - 模板语法上，我更加倾向于JSX
        ```html
            // vue
            <div>
                <p v-if="ok">YES</p>
                <p v-else>NO</p>
            </div>

            // react
            <div>
                { ok ? <p>YES</p> : <p>NO</p> }
            </div>
        ```
    - 模板分离上，我更加倾向于vue
        - react 模板和js混在一起，未分离
- 组件化的区别
    - React本身就是组件化，没有组件化就不是React
    - vue也支持组件化，不过是在MVVM上的扩展
- 两者共同点
    - 都支持组件化
    - 都是数据驱动视图
- 如何选择
    - 国内使用，首推vue。文档更易读、易学、社区够大
    - 如果团队水平较高，推荐使用react。组件化和JSX

## vue和react对比

> 路由

- 监听url的变化
    - Hash 模式
        ```javascript
            /*
                www.test.com/#/ 就是 Hash URL，当 # 后面的哈希值发生变化时，可以通过 hashchange 事件来监听到 URL 的变化，从而进行跳转页面，并且无论哈希值如何变化，服务端接收到的 URL 请求永远是 www.test.com。
            */
            window.addEventListener('hashchange', () => {
                // ... 具体逻辑
            })
        ```
    - History 模式
        ```javascript
            /*
                History 模式是 HTML5 新推出的功能，主要使用 history.pushState 和 history.replaceState 改变 URL。
                通过 History 模式改变 URL 同样不会引起页面的刷新，只会更新浏览器的历史记录。
            */
            // 新增历史记录
            history.pushState(stateObject, title, URL)
            // 替换当前历史记录
            history.replaceState(stateObject, title, URL)
            // 当用户做出浏览器动作时，比如点击后退按钮时会触发 popState 事件
            window.addEventListener('popstate', e => {
                // e.state 就是 pushState(stateObject) 中的 stateObject
                console.log(e.state)
            })
        ```
    - 两种模式对比
        - Hash 模式只可以更改 # 后面的内容，History 模式可以通过 API 设置任意的同源 URL
        - History 模式可以通过 API 添加任意类型的数据到历史记录中，Hash 模式只能更改哈希值，也就是字符串
        - Hash 模式无需后端配置，并且兼容性好。History 模式在用户手动输入地址或者刷新页面的时候会发起 URL 请求，后端需要配置 index.html 页面用于匹配不到静态资源的时候

> 说一下对MVVM的理解

- MVC
    - MVC:主要分为三个部分
        - 视图（view）：用于用户界面
        - 控制器（controller）：用于业务逻辑
        - 模型（model）：用于数据保存
    - View传送指令到Controller，
    - Controller完成业务逻辑后，要求Model改变状态，
    - Model将新的数据发送到View，用户得到反馈 
- MVVM
    - MVVM是Model-View-ViewModel的缩写。MVVM是一种设计思想。
        - Model层代表数据模型，也可以在Model中定义数据修改和操作的业务逻辑；
        - View代表UI组件，它负责将数据模型转化成UI展现出来；
        - ViewModel是一个同步View和Model的对象。
    - 在MVVM架构下，View和Model之间并没有直接的联系，而是通过ViewModel进行交互，Model和ViewModel之间的交互是双向的，因此View数据的变化会同步到Model中，而Model数据的变化也会立即反应到View上。
    - ViewModel通过双向数据绑定把View层和Model层连接了起来，而View和Model之间的同步工作完全是自动的，无需人为干涉，因此开发者只需关注业务逻辑，不需要手动操作DOM，不需要关注数据状态的同步问题，复杂的数据状态维护完全由MVVM来统一管理。