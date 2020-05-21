## webpack

> 性能优化

- 减少Webpack时间
    - 优化Loader
        - 优化Loader文件的搜索范围
            ```javascript
                module: {
                    rules: [
                        {
                            // js 文件才使用 babel
                            test: /\.js$/,
                            loader: 'babel-loader',
                            // 只在 src 文件夹下查找
                            include: [resolve('src')],
                            // 不会去查找的路径
                            exclude: /node_modules/
                        }
                    ]
                }
            ```
        - 将 Babel 编译过的文件缓存起来
            ```javascript
                loader: 'babel-loader?cacheDirectory=true'
            ```
    - HappyPack
        - HappyPack 可以将 Loader 的同步执行转换为并行的
            ```javascript
                plugins: [
                    new HappyPack({
                        id: 'happybabel',
                        loaders: ['babel-loader?cacheDirectory'],
                        // 开启 4 个线程
                        threads: 4
                    })
                ]
            ```
    - DllPlugin
        - DllPlugin 可以将特定的类库提前打包然后引入。
            ```javascript
                plugins: [
                    new webpack.DllPlugin({
                        // name 必须和 output.library 一致
                        name: '[name]-[hash]',
                        // 该属性需要与 DllReferencePlugin 中一致
                        context: __dirname,
                        path: path.join(__dirname, 'dist', '[name]-manifest.json')
                    })
                ]
            ```
    - 代码压缩
        - 在 Webpack3 中，我们一般使用 UglifyJS 来压缩代码
        - 在 Webpack4 中，只需要将 mode 设置为 production 就可以默认开启以上功能。
    - 减少 Webpack 打包后的文件体积
        - 按需加载
        - Tree Shaking 可以实现删除项目中未被引用的代码

> webpack.config.js

```javascript
    module.exports = {
        // 入口文件
        entry: './src/index.js',
        // 出口文件
        output: {
            path: __dirname,
            filename: './build/bundle.js'
        },
        // 加载器
        module: {
            rules: [
                {
                    test: /\.js?$/,
                    exclude: /(node_modules)/,
                    loader: 'babel-loader'
                }
            ]
        }
    }
```