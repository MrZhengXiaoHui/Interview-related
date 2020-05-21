## hybrid

> hybrid是什么，为何用hybrid？

- hybrid文字解释
    - hybrid即"混合"，即前端和客户端的混合开发
    - 需前端开发人员和客户端开发人员配合完成
    - 某些环节也可能涉及到server端
- 存在价值，为何会用hybrid
    - 可以快速迭代更新
    - 体验流畅
    - 减少开发和沟通成本，双端公用一套代码
- webview
    - 是app中的一个组件(app可以有webview，也可以没有)
    - 用于加载h5页面，即一个小型的浏览器内核
- file:// 协议
    - file协议：本地文件，快
    - http(s)协议：网络加载，慢
- hybrid实现流程
    - 不是所有场景都适合使用hybrid
    - 使用NA(原生)：体验要求极致，变化不频繁(如头条的首页)
    - 使用hybrid：体验要求高，变化频繁(如头条的新闻详情页)
    - 使用h5：体验无要求，不常用(如举报、反馈等页面)
    - 具体实现
        - 前端做好静态页面(html js css)，将文件交给客户端
        - 客户端拿到前端静态页面，以文件形式存储在app中
        - 客户端在一个webview中
        - 使用file协议加载静态页面
        
> 介绍一下hybrid更新和上线的流程

- hybrid更新
    - 要替换每个客户端的静态文件
    - 只能客户端来做
    - 客户端去server下载最新的静态文件
    - 我们维护server的静态文件
- 完整流程
    - 分版本，有版本号，如201803211015
    - 将静态文件压缩成zip包，上传到服务端
    - 客户端每次启动，都去服务端检查版本号
    - 如果服务端版本号大于客户端版本号，就去下载最新的zip包
    - 下载完之后解压包，然后将现有文件覆盖

> hybrid和h5的主要区别

- 优点
    - 体验更好，更NA体验基本一致
    - 可快速迭代，无需app审核
- 缺点
    - 开发成本高。联调、测试、查bug都比较麻烦
    - 运维成本高。
- 适用的场景
    - hybrid：产品的稳定功能，体验要求高，迭代频繁
    - h5：单次的运营活动(如xx红包)或不常用功能

> 前端js和客户端如何通讯?

- 新闻详情页适用hybrid，前端如何获取新闻内容?
    - 不能用ajax获取。第一跨域，第二速度慢
    - 客户端获取新闻内容，然后js通讯拿到内容，再渲染
- js和客户端通讯的基本形式
    - js访问客户端能力，传递参数和回调函数
    - 客户端通过回调函数返回内容
- schema协议简介和使用
    - schema协议 - 前端和客户端通讯的约定
    - 使用
        ```javascript
            /* 以下是演示，无法正常运行，微信有严格的权限验证，外部页面不能随意使用 schem */
            var iframe = document.createElement('iframe')
            iframe.style.display = 'none'
            iframe.src = 'weixin://dl/scan' // 扫一扫 //ifram 访问 schema  
            var body = document.body || document.getElementsByTagName('body')[0]
            body.appendChild(iframe)
            setTimeout(function() {
                body.removeChild(iframe) // 销毁 iframe
                iframe = null
            })

            /* 如果要加上参数和 callback，那么就要这么写 */
            window['_weixin_scan_callback'] = function (result) {
                alert(result)
            }
            var iframe = document.createElement('iframe')
            iframe.style.display = 'none'
            iframe.src = 'weixin://dl/scan?k1=va&k2=v2&k3=v3&callback=_weixin_scan_callback'
            var body = document.body || document.getElementsByTagName('body')[0]
            body.appendChild(iframe)
            setTimeout(function() {
                body.removeChild(iframe) // 销毁 iframe
                iframe = null
            })
        ```
- schema使用的封装
    ```javascript
        (function(window, undefined) {

            // 调用 schema 的封装
            function _invoke(action, data, callback) {
                // 拼装 schema 协议
                var schema = 'myapp://utils/' + action

                // 拼接参数
                schema += '?a=a'
                var key 
                for(key in data) {
                    if(data.hasOwnProperty(key)) {
                        schema += '&' + key + data[key]
                    }
                }

                // 处理callback
                var callbackName = ''
                if(typeof callback === 'string') {
                    callbackName = callback
                } else {
                    callbackName = action + Date.now()
                    window[callbackName] = callback
                }
                schema += 'callback=callbackName'

                // 触发
                var iframe = document.createElement('iframe')
                iframe.style.display = 'none'
                iframe.src = schema
                var body = document.body || document.getElementsByTagName('body')[0]
                body.appendChild(iframe)
                setTimeout(function() {
                    body.removeChild(iframe) // 销毁 iframe
                    iframe = null
                })
            }
            
            // 暴露到全局变量
            window.invoke = {
                share: function (data, callback) {
                    _invoke('share', data, callback)
                },
                scan: function (data, callback) {
                    _invoke('scan', data, callback)
                },
                login: function (data, callback) {
                    _invoke('login', data, callback)
                },
            }
        })(window)
    ```
- 内置上线
    - 将以上封装的代码打包，叫做invoke.js，内置到客户端
    - 客户端每次启动webview，都默认执行invoke.js
    - 本地加载，免去网络加载的时间，更快。
    - 本地加载，没有网络请求，黑客看不到schema协议，更安全
