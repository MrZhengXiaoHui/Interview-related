## html

> Doctype是什么？

- 用于告知浏览器以何种模式来渲染文档

> html语义化是什么？

- 当页面样式加载失败的时候能够让页面呈现出清晰的结构
- 有利于seo优化，利于被搜索引擎收录
- 便于项目的开发及维护，使html代码更具有可读性，便于其他设备解析

> 行内元素、块级元素？

- 行内元素：a、b、i、span、input等
- 块级元素：div、p、h1~h6、form等

> src和href的区别是什么？

- src：指向外部资源的位置，指向的内容将会嵌入文档中当前标签所在的位置。当浏览器解析到该元素时，会暂停其他资源的下载和处理
- href：指向网络资源的位置，建立和当前元素（锚点）或当前文档（链接）之间的链接。当浏览器解析到该元素时，会并行下载资源并且不会停止对当前文档的处理

> iframe有那些缺点？

- 会阻塞主页面的onload事件
- 即使内容为空，加载也需要时间
- 没有语义

> html5常用新标签

- `<nav>` 表示导航
- `<header>` 表示页眉
- `<footer>` 表示页脚
- `<section>` 表示区块
- `<aside>` 表示侧边栏

## css

> 常用页面布局

- 三栏布局（三栏布局两侧宽度固定不变，中间部分自动填充整个区域）
    - (1) 定位
        ```html
            <!-- 外层一个div盒子包裹三个子 -->
            <div class="box">
                <div class="left">1</div>
                <div class="center">2</div>
                <div class="right">3</div>
            </div>
        ```
        ```css
            /* 
                外层盒子相对定位，
                内层左右盒子固定宽度且绝对定位，
                内层中间盒子宽度100%，内边距设置成左右盒子的宽度值，并设置box-sizing: border-box;改变盒模型
            */
            .box{
                width: 100%;
                height: 100%;
                position: relative;
            }
            
            .left {
                width: 200px;
                background-color: antiquewhite;
                position: absolute;
                top: 0;
                left: 0;
            }

            .right {
                width: 200px;
                background-color: antiquewhite;
                position: absolute;
                top: 0;
                right: 0;
            }

            .center {
                width: 100%;
                background-color: burlywood;
                padding: 0 200px;
                box-sizing: border-box;
            }
        ```
    - (2) flex
         ```html
            <!-- 外层一个div盒子包裹三个子盒子 -->
            <div class="box">
                <div class="left">1</div>
                <div class="center">2</div>
                <div class="right">3</div>
            </div>
        ```
        ```css
            /*
                外层盒子设置display: flex;
                内层盒子左右固定宽度，中间盒子设置flex:1
            */
            .box{
                width: 100%;
                height: 100%;
                display: flex;
            }
            .left {
                width: 200px;
                background-color: antiquewhite;
            }

            .right {
                width: 200px;
                background-color: antiquewhite;
            }

            .center {
                width: 100%;
                flex: 1;
                background-color: burlywood;
            }
        ```
- 两栏布局(左侧固定右侧自适应布局)
    - (1) 定位
        ```html
            <!-- 外层一个div盒子包裹两个子盒子 -->
            <div class="box">
                <div class="left">1</div>
                <div class="center">2</div>
            </div>
        ```
        ```css
            /* 
                外层盒子相对定位，
                内层左盒子固定宽度且绝对定位，
                内层中间盒子宽度100%，内边距设置成左盒子的宽度值，并设置box-sizing: border-box;改变盒模型
            */
            .box {
                width: 100%;
                height: 100%;
                position: relative;
            }

            .left {
                width: 200px;
                position: absolute;
                left: 0;
                top: 0;
                background-color: antiquewhite;
            }

            .center {
                width: 100%;
                padding-left: 200px;
                box-sizing: border-box;
                background-color: burlywood;
            }
        ```
    - (2) flex
        ```html
            <!-- 外层一个div盒子包裹两个子盒子 -->
            <div class="box">
                <div class="left">1</div>
                <div class="center">2</div>
            </div>
        ```
        ```css
            /*
               外层盒子设置display: flex;
               内层盒子左固定宽度，中间盒子设置flex:1 
            */
            .box {
                width: 100%;
                height: 100%;
                display: flex;
            }

            .left {
                width: 200px;
                background-color: antiquewhite;
            }

            .center {
                width: 100%;
                flex: 1;
                background-color: burlywood;
            }
        ```
    - (3) 浮动
        ```html
            <!-- 外层一个div盒子包裹两个子盒子 -->
            <div class="box">
                <div class="left">1</div>
                <div class="center">2</div>
            </div>
        ```
        ```css
            /*
               内层盒子左固定宽度并设置左浮动，中间盒子设置左侧外边距为左盒子的宽
            */
            .box {
                width: 100%;
                height: 100%;
            }

            .left {
                width: 200px;
                float:left;
                background-color: antiquewhite;
            }

            .center {
                margin-left:200px;
                background-color: burlywood;
            }
        ```
- 上中下结构布局
    - 定位
        ```html
            <!-- 三个div盒子 -->
            <div class="header">1</div>
            <div class="container">2</div>
            <div class="footer">3</div>
        ```
        ```css
            /*
                初始化html和body样式并设置宽高为100%
                顶部盒子固定定位设置top:0
                底部盒子固定定位设置bottom：0
                中间盒子设置内边距等于顶部和底部盒子的高度值，并设置box-sizing: border-box;改变盒模型。设置最小高度为100%

            */
            html,body{
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }
            .header{
                width: 100%;
                height: 20px;
                background-color: #b9ffd8;
                position: fixed;
                top: 0;
                left: 0;
            }
            .container{
                width: 100%;
                min-height: 100%;
                padding: 20px 0;
                box-sizing: border-box;
                background-color: #d7ff7c;
            }
            .footer{
                width: 100%;
                height: 20px;
                position: fixed;
                bottom: 0;
                left: 0;
                background-color: #1d1d1d;
            }
        ```
- css实现垂直居中
    ```css
        .content{
            weith:200px;
            height:200px;
            /* flex
            display:flex;
            justify-content:center;
            align-items:center;
            */

            /* 定位
            position:absolute;
            top:50%;
            left:50%;
            margin-left:-100px;
            margin-top:-100px;
            */
            
            /* css3
            transform:translate(-50%,-50%);
            */
        }
        <div class="content"></div>
    ```

> BFC (块级格式化上下文)

- 从理论上讲父元素包裹的元素不会影响旁边的元素
- 官方定义
    - 它决定了元素如何对其内容进行定位 以及与其他元素的关系和相互作用 当涉及到可视化布局的时候 Block Formatting Context提供了一个环境 HTML元素在这个环境中按照一定规则进行布局
- BFC的目的是 形成一个完全独立的空间 让空间里的子元素不会影响到外面的布局
- 触发BFC (常用规则)
    1. float不为none
    2. position不为relative和static
    3. overflow为auto scroll和hidden
    4. display的值为table-cell和inline-block
- 利用BFC解决问题
    1. 解决浮动元素令父元素高度坍塌的问题
        - 假设页面里有一个父元素和几个子元素，这几个子元素都设为浮动时，父元素高度坍塌，这是因为浮动的子元素脱离了文档流，父元素检测不到它的存在无法被它撑开，这时给父元素添加属性overflow:hidden。也可以添加属性display:table-cell，或者是position:fixed等等,这些属性都是触发了BFC。触发了BFC的容器就是页面上的一个完全隔离开的容器，容器里的子元素绝对不会影响到外面的元素，为了保证这样的规则，不得不让浮动的子元素也参与进来，变相地实现了清除内部浮动的目的
        - 常用的办法
            1. 让父元素也浮动起来，这样父元素和子元素一起脱离文档流，这样父元素就能自适应子元素的高度
                - 缺点：会影响父元素之后的元素排列
            2. 给父元素添加一个固定高度
            3. 在一个浮动的子元素后面增加一个空元素，设置clear:both来清除浮动
                - 缺点：增加无意义标签，不利于维护
            4. 为浮动的最后一个子元素设置伪元素::after{clear:both}
    2. 解决自适应布局的问题
        - 两栏布局 (左侧固定，右侧自适应)
            - 左边设置float:left;width:200px。右边设置margin-left:200px
    3. 解决外边距垂直方向重合问题
        - 兄弟元素之间的外边距，在垂直方向上会取最大值而不是取和，可以通过触发BFC来防止它们之间相互影响
        - 比如为其中一个元素增加一个父元素，并触发父元素BFC，如overflow:hidden这样就不会重叠了

> flex布局 (简单的说几个常用的)

- 又称"弹性布局"
    - 容器设置display:flex;
    - 属性
        - justify-content
            1. flex-start （默认值） 左对齐
            2. flex-end 右对齐
            3. center 居中
            4. space-between 两端对齐，如果是三个及以上的盒子之间的间隔都相等
            5. space-around 两侧的会有间隔，内部间隔比两侧的间隔大一倍
        - align-items
            - center 上下居中 （配合着justify-content:center属性做垂直居中）
        - flex-wrap属性 控制是否换行
    - 其他属性参考 [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html "Flex 布局教程：语法篇")

> rem、em、px

- rem
    - 根据html的font-size属性的值 进行计算 1rem = html的font-size
- em
    - 根据当前对象内font-size属性的值 进行计算 1em = 当前对象内font-size
- px
    - 像素。相对于显示器屏幕分辨率


> less、sass/scss

- 都是动态样式语言 css的超集
- 变量符不一样，Less是@，而Scss是$
- 编译环境不一样。Sass的安装需要Ruby环境，是在服务端处理的，Less 可以运行在 Node 或浏览器端
- Sass支持条件语句，可以使用if{}else{},for{}循环等等。而Less不支持。

> 为什么用sass或less？

- 有动态语言的特性，可以定义变量、运算等
- 结构清晰，便于扩展
- 可以实现继承
- 完全兼容css代码

> 介绍一下标准的CSS的盒子模型？与低版本IE的盒子模型有什么不同的？

- 标准盒子模型：宽度=内容的宽度（content）+ border + padding + margin
- 低版本IE盒子模型：宽度=内容宽度（content+border+padding）+ margin

> CSS优先级算法如何计算？

- 元素选择符： 1
- class选择符： 10
- id选择符：100
- 元素标签：1000
- !important声明的样式优先级最高，如果冲突再进行计算。
- 如果优先级相同，则选择最后出现的样式。
- 继承得到的样式的优先级最低。

> display有哪些值？

- display: inline 内联
- display: none 隐藏
- display: block 块元素
- display: table 表格显示
- display: list-item 项目列表
- display: inline-block 行内元素

> position的值

- static（默认）：按照正常文档流进行排列；
- relative（相对定位）：不脱离文档流，参考自身静态位置通过 top, bottom, left, right 定位；
- absolute(绝对定位)：参考距其最近一个不为static的父级元素通过top, bottom, left, right 定位；
- fixed(固定定位)：所固定的参照对像是可视窗口。

> css实现三角形

```css
    div{
        width: 0;
        height: 0;
        border-top: 40px solid transparent;
        border-left: 40px solid transparent;
        border-right: 40px solid transparent;
        border-bottom: 40px solid #ff0000;
    }
```

> 文字溢出实现...的效果

```css
    // 单行溢出
    div{
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
        width: 90%;
    }
    // 多行溢出
    div{
        overflow: hidden;  
        text-overflow: ellipsis;  
        display: -webkit-box;  
        -webkit-line-clamp: 3;  
        -webkit-box-orient: vertical;
    }
```

> display:none与visibility：hidden的区别？

- display：none 不显示对应的元素，在文档布局中不再分配空间（回流+重绘）
- visibility：hidden 隐藏对应元素，在文档布局中仍保留原来的空间（重绘）

> Chrome浏览器默认字体大小是多少？怎么让浏览器支持小于12px的文字

- 默认大小是16px
- 让浏览器支持小于12px的字体，可以设置缩放 transform:scale(0.8)

> css3 新增的属性

- 圆角 border-radius
- 阴影 box-shadow
- 文字阴影 text-shadow
- 线性渐变 gradient
- 动画 transform
    - rotate 旋转
    - scale 缩放
    - translate 定位
    - skew 倾斜
- 背景等等

> 伪类和伪元素

- 伪类单冒号
   - p:first-of-type    选择属于其父元素的首个 p 元素的每个 p 元素。
   - p:last-of-type    选择属于其父元素的最后 p 元素的每个 p 元素。
   - p:only-of-type    选择属于其父元素唯一的 p 元素的每个 p 元素。
   - p:only-child    选择属于其父元素的唯一子元素的每个 p 元素。
   - p:nth-child(2)    选择属于其父元素的第二个子元素的每个 p 元素。
   - :enabled  :disabled 控制表单控件的禁用状态。
   - :checked        单选框或复选框被选中。
- 伪元素双冒号