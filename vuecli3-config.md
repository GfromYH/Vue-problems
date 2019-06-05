## vuecli3中关于vue,config.js的配置问题

- 请求后端是碰到的跨域问题

  + 在Vuecli3中我们需要配置Vue.config.js文件

  ~~~javascript
  //vue.config.js
  const path = require('path')
  const debug = process.env.NODE_ENV !== 'production'
  
  module.exports = {
      baseUrl: '/', // 根域上下文目录
      outputDir: 'dist', // 构建输出目录
      assetsDir: 'assets', // 静态资源目录 (js, css, img, fonts)
      lintOnSave: false, // 是否开启eslint保存检测，有效值：ture | false | 'error'
      runtimeCompiler: true, // 运行时版本是否需要编译
      transpileDependencies: [], // 默认babel-loader忽略mode_modules，这里可增加例外的依赖包名
      productionSourceMap: true, // 是否在构建生产包时生成 sourceMap 文件，false将提高构建速度
      configureWebpack: config => { // webpack配置，值位对象时会合并配置，为方法时会改写配置
          if (debug) { // 开发环境配置
              config.devtool = 'cheap-module-eval-source-map'
          } else { // 生产环境配置
          }
          // Object.assign(config, { // 开发生产共同配置
          //     resolve: {
          //         alias: {
          //             '@': path.resolve(__dirname, './src'),
          //             '@c': path.resolve(__dirname, './src/components'),
          //             'vue$': 'vue/dist/vue.esm.js'
          //         }
          //     }
          // })
      },
      chainWebpack: config => { // webpack链接API，用于生成和修改webapck配置，https://github.com/vuejs/vue-cli/blob/dev/docs/webpack.md
          if (debug) {
              // 本地开发配置
          } else {
              // 生产开发配置
          }
      },
      parallel: require('os').cpus().length > 1, // 构建时开启多进程处理babel编译
      pluginOptions: { // 第三方插件配置
      },
      pwa: { // 单页插件相关配置 https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-pwa
      },
      devServer: {
          open: false,
          host: 'localhost',
          port: 8080,
          https: false,
          hotOnly: false,
          proxy: { // 配置跨域
              '/api': {
                  target: 'http://localhost:3000/api/',
                  ws: true,
                  changOrigin: true,
                  pathRewrite: {
                      '^/api': ''
                  }
              }
          },
          before: app => { }
      }
  }
  
  ~~~



  > ​	这里我碰到的问题是在配置跨域中 关于**pathRewrite**

  > ​	我后端的接口地址开头均需要**/api/xxx** ,但因为之前并没有在target后加
  >
  > 上/api,因为pathRewrite会让url地址**只要匹配到/api**均会重写为**空**
  >
  > 但浏览器还是会拼接起完整地址，但是此时你并不会请求到后端，这样导致报错。

  由于这个原因，困扰了我，后来请求了帮助才修改掉这个问题。


