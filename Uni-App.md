### 创建项目

官网地址：[uni-app官网 (dcloud.net.cn)](https://uniapp.dcloud.net.cn/quickstart-cli.html)

#### 1.HbuilderX创建项目

可视化操作

#### 2.Cli创建项目(推荐)

```bash
# 安装vue3 + ts版本
npx degit dcloudio/uni-preset-vue#vite-ts my-vue3-project
# vue脚手架创建
vue create -p dcloudio/uni-preset-vue my-project
```

**优势**

通过命令行创建 uni-app 项目，**不必依赖 HBuilderX**，TypeScript 类型支持友好。

**命令行创建** **uni-app** **项目：**

vue3 + ts 版

::: code-group

```sh [github]
# 通过 npx 从 github 下载
npx degit dcloudio/uni-preset-vue#vite-ts 项目名称
```

```sh [👉国内 gitee]
# 通过 git 从 gitee 克隆下载 (👉备用地址)
git clone -b vite-ts https://gitee.com/dcloud/uni-preset-vue.git
```

::: danger 常见问题

- 运行 `npx` 命令下载失败，请尝试换成**手机热点重试**
- 换手机热点依旧失败，请尝试从[国内备用地址下载](https://gitee.com/dcloud/uni-preset-vue/tree/vite-ts/)
- 在 `manifest.json` 文件添加 [小程序 AppID](https://mp.weixin.qq.com/) 用于真机预览
- 运行 `npx` 命令需依赖 NodeJS 环境，[NodeJS 下载地址](https://nodejs.org/zh-cn)
- 运行 `git` 命令需依赖 Git 环境，[Git 下载地址](https://git-scm.com/download/)

#### 安装插件

VSCode: uni-create-view、uni-helper、uniapp小程序扩展

**配置扩展**

设置中配置 uni-create-view 插件的默认配置

#### 支持ts提示

安装types

```bash
pnpm i -D @types/wechat-miniprogram @uni-helper/uni-app-types
```

配置tsconfig.json

```json
// tsconfig.json
{
  "extends": "@vue/tsconfig/tsconfig.json",
  "compilerOptions": {
    "sourceMap": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    },
    "lib": ["esnext", "dom"],
    // 类型声明文件
    "types": [
      "@dcloudio/types", // uni-app API 类型
      "miniprogram-api-typings", // 原生微信小程序类型
      "@uni-helper/uni-app-types" // uni-app 组件类型
    ]
  },
  // vue 编译器类型，校验标签类型
  "vueCompilerOptions": {
    // 原配置 `experimentalRuntimeMode` 现调整为 `nativeTags`
    "nativeTags": ["block", "component", "template", "slot"], // [!code ++]
    "experimentalRuntimeMode": "runtime-uni-app" // [!code --]
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"]
}
```

**工作区设置参考**

```json
// .vscode/settings.json
{
  // 在保存时格式化文件
  "editor.formatOnSave": true,
  // 文件格式化配置
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  // 配置语言的文件关联
  "files.associations": {
    "pages.json": "jsonc", // pages.json 可以写注释
    "manifest.json": "jsonc" // manifest.json 可以写注释
  }
}
```

::: danger 版本升级

- 原依赖 `@types/wechat-miniprogram` 现调整为 [miniprogram-api-typings](https://github.com/wechat-miniprogram/api-typings)。
- 原配置 `experimentalRuntimeMode` 现调整为 `nativeTags`。

:::

这一步处理很关键，否则 TS 项目无法校验组件属性类型。

**json支持注释**

```json
"files.associations": {
    "manifest.json": "jsonc",
    "pages.json": "jsonc"
  },
```

### 需要安装的依赖

package.json

```json
{
  "name": "uni-app-xiaotuxian",
  "version": "0.0.0",
  "author": {
    "name": "itheima",
    "url": "https://web.itheima.com/"
  },
  "scripts": {
    "dev:app": "uni -p app",
    "dev:app-android": "uni -p app-android",
    "dev:app-ios": "uni -p app-ios",
    "dev:custom": "uni -p",
    "dev:h5": "uni",
    "dev:h5:ssr": "uni --ssr",
    "dev:mp-alipay": "uni -p mp-alipay",
    "dev:mp-baidu": "uni -p mp-baidu",
    "dev:mp-kuaishou": "uni -p mp-kuaishou",
    "dev:mp-lark": "uni -p mp-lark",
    "dev:mp-qq": "uni -p mp-qq",
    "dev:mp-toutiao": "uni -p mp-toutiao",
    "dev:mp-weixin": "uni -p mp-weixin",
    "dev:quickapp-webview": "uni -p quickapp-webview",
    "dev:quickapp-webview-huawei": "uni -p quickapp-webview-huawei",
    "dev:quickapp-webview-union": "uni -p quickapp-webview-union",
    "build:app": "uni build -p app",
    "build:app-android": "uni build -p app-android",
    "build:app-ios": "uni build -p app-ios",
    "build:custom": "uni build -p",
    "build:h5": "uni build",
    "build:h5:ssr": "uni build --ssr",
    "build:mp-alipay": "uni build -p mp-alipay",
    "build:mp-baidu": "uni build -p mp-baidu",
    "build:mp-kuaishou": "uni build -p mp-kuaishou",
    "build:mp-lark": "uni build -p mp-lark",
    "build:mp-qq": "uni build -p mp-qq",
    "build:mp-toutiao": "uni build -p mp-toutiao",
    "build:mp-weixin": "uni build -p mp-weixin",
    "build:quickapp-webview": "uni build -p quickapp-webview",
    "build:quickapp-webview-huawei": "uni build -p quickapp-webview-huawei",
    "build:quickapp-webview-union": "uni build -p quickapp-webview-union",
    "tsc": "vue-tsc --noEmit --skipLibCheck",
    "lint": "eslint . --ext .vue,.js,.jsx,.cjs,.mjs,.ts,.tsx,.cts,.mts --fix --ignore-path .gitignore",
    "prepare": "husky install",
    "lint-staged": "lint-staged"
  },
  "lint-staged": {
    "*.{js,ts,vue}": [
      "eslint --fix"
    ]
  },
  "dependencies": {
    "@dcloudio/uni-app": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-app-plus": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-components": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-h5": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-alipay": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-baidu": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-jd": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-kuaishou": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-lark": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-qq": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-toutiao": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-weixin": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-mp-xhs": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-quickapp-webview": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-ui": "^1.4.28",
    "pinia": "2.0.27",
    "pinia-plugin-persistedstate": "^3.2.0",
    "vue": "^3.2.47",
    "vue-i18n": "^9.2.2"
  },
  "devDependencies": {
    "@dcloudio/types": "^3.3.3",
    "@dcloudio/uni-automator": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-cli-shared": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-stacktracey": "3.0.0-alpha-3081220230802001",
    "@dcloudio/uni-vue-devtools": "3.0.0-alpha-3080220230511001",
    "@dcloudio/vite-plugin-uni": "3.0.0-alpha-3081220230802001",
    "@rushstack/eslint-patch": "^1.1.4",
    "@types/node": "^18.11.9",
    "@uni-helper/uni-app-types": "^0.5.8",
    "@uni-helper/uni-ui-types": "^0.5.11",
    "@vue/eslint-config-prettier": "^7.0.0",
    "@vue/eslint-config-typescript": "^11.0.0",
    "@vue/runtime-core": "^3.2.45",
    "@vue/tsconfig": "^0.4.0",
    "eslint": "^8.22.0",
    "eslint-plugin-vue": "^9.3.0",
    "husky": "^8.0.0",
    "lint-staged": "^13.0.3",
    "miniprogram-api-typings": "^3.12.0",
    "prettier": "^2.7.1",
    "sass": "^1.56.1",
    "typescript": "^5.1.6",
    "uni-ui-components-types": "^1.0.0",
    "vite": "^4.4.9",
    "vue-tsc": "^1.8.8"
  }
}

```

#### 开发依赖配置更新

```json
"devDependencies": {
    "@dcloudio/types": "^3.4.7",
    "@dcloudio/uni-automator": "3.0.0-3090920231225001",
    "@dcloudio/uni-cli-shared": "3.0.0-3090920231225001",
    "@dcloudio/uni-stacktracey": "3.0.0-3090920231225001",
    "@dcloudio/vite-plugin-uni": "3.0.0-3090920231225001",
    "@rushstack/eslint-patch": "^1.6.1",
    "@types/node": "^18.19.6",
    "@types/uni-app": "^1.4.8", // 支持在项目中使用uni等关键字不报错
    "@uni-helper/uni-app-types": "^0.5.12",
    "@vue/eslint-config-prettier": "^7.1.0",
    "@vue/eslint-config-typescript": "^11.0.3",
    "@vue/runtime-core": "^3.4.13",
    "@vue/tsconfig": "^0.4.0",
    "eslint": "^8.56.0",
    "eslint-plugin-vue": "^9.20.1",
    "miniprogram-api-typings": "^3.12.2",
    "prettier": "^2.8.8",
    "typescript": "^5.3.3",
    "vite": "4.0.3",
    "vue-tsc": "^1.8.27"
  }
```

### 目录结构

我们先来认识 uni-app 项目的目录结构。

```sh {1,4,9,10}
├─pages            业务页面文件存放的目录
│  └─index
│     └─index.vue  index页面
├─static           存放应用引用的本地静态资源的目录(注意：静态资源只能存放于此)
├─unpackage        非工程代码，一般存放运行或发行的编译结果
├─index.html       H5端页面
├─main.js          Vue初始化入口文件
├─App.vue          配置App全局样式、监听应用生命周期
├─pages.json       **配置页面路由、导航栏、tabBar等页面类信息**
├─manifest.json    **配置appid**、应用名称、logo、版本等打包信息
└─uni.scss         uni-app内置的常用样式变量
```

:::

### 编译和运行 uni-app 项目

1. 安装依赖 `pnpm install`
2. 编译成微信小程序 `pnpm dev:mp-weixin`
3. 导入微信开发者工具

::: tip 温馨提示
编译成 H5 端可运行 `pnpm dev:h5` 通过浏览器预览项目。
:::

::: tip 温馨提示

`VS Code` 可通过快捷键 `Ctrl + i` 唤起代码提示。

:::

### 安装 [uni-ui 组件库](https://uniapp.dcloud.net.cn/component/uniui/quickstart.html#npm安装)

```sh
pnpm i @dcloudio/uni-ui
```

**配置自动导入组件**

```json
// pages.json
{
  // 组件自动导入
  "easycom": {
    "autoscan": true,
    "custom": {
      // uni-ui 规则如下配置  // [!code ++]
      "^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue" // [!code ++]
    }
  },
  "pages": [
    // …省略
  ]
}
```

**安装类型声明文件**

```sh
pnpm i -D @uni-helper/uni-ui-types
```

**配置类型声明文件**

```json
// tsconfig.json
{
  "compilerOptions": {
    // ...
    "types": [
      "@dcloudio/types", // uni-app API 类型
      "miniprogram-api-typings", // 原生微信小程序类型
      "@uni-helper/uni-app-types", // uni-app 组件类型
      "@uni-helper/uni-ui-types" // uni-ui 组件类型  // [!code ++]
    ]
  },
  // vue 编译器类型，校验标签类型
  "vueCompilerOptions": {
    "nativeTags": ["block", "component", "template", "slot"]
  }
}
```

### 封装请求方法

/src/utils.ts

```ts
import { useMemberStore } from '@/stores'

// 请求基地址
const baseURL: string = 'https://pcapi-xiaotuxian-front-devtest.itheima.net'

const httpInterceptor: UniApp.InterceptorOptions = {
  // 拦截前触发
  invoke(options: UniApp.RequestOptions) {
    // 1.非http拼接地址
    if (!options.url.startsWith('http')) {
      options.url = baseURL + options.url
    }
    // 2.请求超时,默认60秒
    options.timeout = 10000
    // 3.添加小程序端请求标识
    options.header = {
      ...options.header,
      'source-client': 'miniapp',
    }
    // 4.添加token
    const memberStore = useMemberStore()
    const token = memberStore.profile?.token
    if (token) {
      options.header.Authorization = token
    }
  },
    // 直接重写返回值有缺点，无法使用泛型
  /* async returnValue(result: Promise<UniApp.RequestSuccessCallbackResult>) {
    return (await result).data
  }, */
}

// 添加拦截器
uni.addInterceptor('request', httpInterceptor)
uni.addInterceptor('uploadFile', httpInterceptor)

/**
 * 基本数据结构
 */
interface Data<T> {
  code: string
  msg: string
  result: T
}

/**
 * 全局请求方法
 * @param options UniApp.RequestOptions
 * @returns Promise
 */
export const http = <T>(options: UniApp.RequestOptions) => {
  // 返回一个promise对象
  return new Promise<Data<T>>((resolve, reject) => {
    uni.request({
      ...options,
      // 请求成功
      success: (result: UniApp.RequestSuccessCallbackResult) => {
        // 状态码2xx，表示成功
        if (result.statusCode >= 200 && result.statusCode < 300) {
          resolve(result.data as Data<T>)
        } else if (result.statusCode === 401) {
          // 401错误 -> 清除用户信息 跳转登录页面
          const memberStore = useMemberStore()
          memberStore.clearProfile()
          uni.navigateTo({ url: '/pages/login/login' })
          reject(result)
        } else {
          // 其他一些错误 -> 根据后端提示错误信息
          uni.showToast({
            icon: 'none',
            title: (result.data as Data<T>).msg || '请求错误',
          })
          reject(result)
        }
      },
      // 网络错误，请求失败
      fail: (err) => {
        uni.showToast({ icon: 'none', title: '网络错误，请尝试更换网络' })
        reject(err)
      },
    })
  })
}
```

#### 请求方法封装2

```ts
import { useUserStore } from '@/store/user'

let BASE_URL = ''

// #ifdef H5
BASE_URL = import.meta.env.VITE_H5_API
// #endif

// #ifdef APP-PLUS
BASE_URL = import.meta.env.VITE_APP_API
// #endif

// UniApp自带的拦截器
uni.addInterceptor('request', {
  // 前置钩子，可以对方法参数进行修改
  invoke(options: UniApp.RequestOptions) {
    // 前缀非http的追加域名
    if (!options.url.startsWith('http')) {
      options.url = BASE_URL + options.url
    }
    // 设置请求的超时时间为10秒
    options.timeout = 10000
    if (!options.header) {
      options.header = {}
    }
    options.header['Content-Type'] = 'application/x-www-form-urlencoded'
    // 添加用户的身份标识
    const userStore = useUserStore()
    const cookie = userStore.user.cookie
    if (cookie) {
      // 添加Cookie到请求头中
      options.header.Cookie = cookie
    }
  },
})

/**
 * 基本数据结构
 */
interface Response<T> {
  status: number
  data: T
  msg: string
}

export const request = <T>(options: UniApp.RequestOptions) => {
  return new Promise<Response<T> | UniApp.RequestSuccessCallbackResult>((resolve, reject) => {
    uni.request({
      ...options,
      async success(result) {
        if (result.statusCode >= 200 && result.statusCode < 300) {
          if (typeof result.data === 'string') {
            resolve(result)
          } else {
            resolve(result.data as Response<T>)
          }
        } else if (result.statusCode === 401) {
          // 身份验证失败
          await uni.showToast({ icon: 'none', title: '身份验证失败' })
        } else {
          await uni.showToast({ icon: 'none', title: '请求失败' })
          reject(new Error((result.data as Response<T>).msg || '请求失败'))
        }
      },
      // 网络请求错误
      async fail(result) {
        await uni.showToast({ icon: 'none', title: '网络请求错误: ' + result.errMsg })
        reject(new Error(result.errMsg))
      },
      complete(result) {},
    })
  })
}

function get<T, V extends object>(url: string, data: V) {
  return request<T>({
    url,
    method: 'GET',
    data,
  })
}

function post<T, V extends object>(url: string, data: V) {
  return request<T>({
    url,
    method: 'POST',
    data,
  })
}

export default {
  get,
  post,
}
```

