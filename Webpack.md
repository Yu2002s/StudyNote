```bash
# 安装webpack
npm install -D webpack webpack-cli
# 安装typescript和加载器
npm install -D typescript ts-loader
# 安装webpack热更新工具
npm install webpack-dev-server
# webpack打包命令
webpack --mode production|development
# 热更新并打开默认浏览器
webpack serve --mode development --open
# 安装babel
cnpm i -D @babel/core @babel/preset-env
```

### Webpack配置文件

```js
// 引入包
const path = require('path')

const htmlWebpackPlugin = require('html-webpack-plugin')

// webpack 中所有配置信息
module.exports = {
  // 入口文件
  entry: './src/index.ts',
  mode: 'production',
  // 打包文件所在的目录
  output: {
    // 指定的打包目录
    path: path.resolve(__dirname, 'dist'),
    // 打包后的文件
    filename: 'bundle.js',
    // 重新打包时对生成文件进行清空
    clean: true,
    environment: {
      // 兼容ie
      arrowFunction: false
    }
  },
  // 指定webpack 打包时候使用的模块
  module: {
    // 指定加载的规则
    rules: [
      {
        // test指定的是规则生效的文件
        test: /\.ts$/,
        // 要使用的loader
        use: [
          {
            loader: 'babel-loader',
            // 设置 babel
            options: {
              // 设置预定义环境
              presets: [
                [
                  // 指定环境插件
                  "@babel/preset-env",
                  {
                    // 要兼容的目标浏览器
                    targets: {
                      "chrome": '88',
                      'ie': '11'
                    },
                    "corejs": '3',
                    // 使用 coreJS 的方式 "usage" 表示按需加载
                    "useBuiltIns": "usage"
                  }
                ]
              ]
            }
          },
          'ts-loader'
        ],
        // 排除的文件
        exclude: /node-moudules/
      }
    ]
  },
  // 配置webpack插件
  plugins: [
    new htmlWebpackPlugin({
      // title: '这是一个自定义的title'
      template: './src/index.html'
    }),
  ],
  resolve: {
    extensions: ['.ts', '.js']
  }
}
```

