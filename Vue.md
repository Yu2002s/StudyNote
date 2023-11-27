### 构建项目	

vite

```bash
npm init vite@latest
```

vue

```bash
npm init vue@latest
npm create vue@latest # 方式2
```

npx

```bash
npx create-vue demo
```

#### 项目初始化

一个项目要有统一的规范，需要使用eslint+stylelint+prettier来对我们的代码质量做检测和修复，需要使用husky来做commit拦截，需要使用commitlint来统一提交规范，需要使用preinstall来统一包管理工具。

#### 环境准备

- node v16.14.2 
- pnpm 8.0.0

#### 初始化项目

本项目使用vite进行构建，vite官方中文文档参考：[cn.vitejs.dev/guide/](https://cn.vitejs.dev/guide/)

**pnpm:performant npm ，意味“高性能的 npm”。[pnpm](https://so.csdn.net/so/search?q=pnpm&spm=1001.2101.3001.7020)由npm/yarn衍生而来，解决了npm/yarn内部潜在的bug，极大的优化了性能，扩展了使用场景。被誉为“最先进的包管理工具”**

pnpm安装指令

```bash
npm i -g pnpm
```

项目初始化命令:

```bash
pnpm create vite
```

进入到项目根目录pnpm install安装全部依赖.安装完依赖运行程序:**pnpm run dev**

运行完毕项目跑在http://127.0.0.1:5173/

#### 项目配置

##### eslint配置

**eslint中文官网:http://eslint.cn/**

ESLint最初是由[Nicholas C. Zakas](http://nczonline.net/) 于2013年6月创建的开源项目。它的目标是提供一个插件化的**javascript代码检测工具**

首先安装eslint

```
pnpm i eslint -D
```

生成配置文件:.eslint.cjs

```bash
npx eslint --init 
# 或
npm init @eslint/config
```

**.eslint.cjs配置文件**

```js
module.exports = {
   //运行环境
    "env": { 
        "browser": true,//浏览器端
        "es2021": true,//es2021
    },
    //规则继承
    "extends": [ 
       //全部规则默认是关闭的,这个配置项开启推荐规则,推荐规则参照文档
       //比如:函数不能重名、对象不能出现重复key
        "eslint:recommended",
        //vue3语法规则
        "plugin:vue/vue3-essential",
        //ts语法规则
        "plugin:@typescript-eslint/recommended"
    ],
    //要为特定类型的文件指定处理器
    "overrides": [
    ],
    //指定解析器:解析器
    //Esprima 默认解析器
    //Babel-ESLint babel解析器
    //@typescript-eslint/parser ts解析器
    "parser": "@typescript-eslint/parser",
    //指定解析器选项
    "parserOptions": {
        "ecmaVersion": "latest",//校验ECMA最新版本
        "sourceType": "module"//设置为"script"（默认），或者"module"代码在ECMAScript模块中
    },
    //ESLint支持使用第三方插件。在使用插件之前，您必须使用npm安装它
    //该eslint-plugin-前缀可以从插件名称被省略
    "plugins": [
        "vue",
        "@typescript-eslint"
    ],
    //eslint规则
    "rules": {
    }
}
```

vue3环境代码校验插件

```json
# 让所有与prettier规则存在冲突的Eslint rules失效，并使用prettier进行代码检查
"eslint-config-prettier": "^8.6.0",
"eslint-plugin-import": "^2.27.5",
"eslint-plugin-node": "^11.1.0",
# 运行更漂亮的Eslint，使prettier规则优先级更高，Eslint优先级低
"eslint-plugin-prettier": "^4.2.1",
# vue.js的Eslint插件（查找vue语法错误，发现错误指令，查找违规风格指南
"eslint-plugin-vue": "^9.9.0",
# 该解析器允许使用Eslint校验所有babel code
"@babel/eslint-parser": "^7.19.1",
```

安装指令

```bash
pnpm install -D eslint-plugin-import eslint-plugin-vue eslint-plugin-node eslint-plugin-prettier eslint-config-prettier eslint-plugin-node @babel/eslint-parser
```

.eslintrc.cjs配置文件

```js
// @see https://eslint.bootcss.com/docs/rules/

module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true,
    jest: true,
  },
  /* 指定如何解析语法 */
  parser: 'vue-eslint-parser',
  /** 优先级低于 parse 的语法解析配置 */
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module',
    parser: '@typescript-eslint/parser',
    jsxPragma: 'React',
    ecmaFeatures: {
      jsx: true,	
    },
  },
  /* 继承已有的规则 */
  extends: [
    'eslint:recommended',
    'plugin:vue/vue3-essential',
    'plugin:@typescript-eslint/recommended',
    'plugin:prettier/recommended',
  ],
  plugins: ['vue', '@typescript-eslint'],
  /*
   * "off" 或 0    ==>  关闭规则
   * "warn" 或 1   ==>  打开的规则作为警告（不影响代码执行）
   * "error" 或 2  ==>  规则作为一个错误（代码不能执行，界面报错）
   */
  rules: {
    // eslint（https://eslint.bootcss.com/docs/rules/）
    'no-var': 'error', // 要求使用 let 或 const 而不是 var
    'no-multiple-empty-lines': ['warn', { max: 1 }], // 不允许多个空行
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-unexpected-multiline': 'error', // 禁止空余的多行
    'no-useless-escape': 'off', // 禁止不必要的转义字符

    // typeScript (https://typescript-eslint.io/rules)
    '@typescript-eslint/no-unused-vars': 'error', // 禁止定义未使用的变量
    '@typescript-eslint/prefer-ts-expect-error': 'error', // 禁止使用 @ts-ignore
    '@typescript-eslint/no-explicit-any': 'off', // 禁止使用 any 类型
    '@typescript-eslint/no-non-null-assertion': 'off',
    '@typescript-eslint/no-namespace': 'off', // 禁止使用自定义 TypeScript 模块和命名空间。
    '@typescript-eslint/semi': 'off',

    // eslint-plugin-vue (https://eslint.vuejs.org/rules/)
    'vue/multi-word-component-names': 'off', // 要求组件名称始终为 “-” 链接的单词
    'vue/script-setup-uses-vars': 'error', // 防止<script setup>使用的变量<template>被标记为未使用
    'vue/no-mutating-props': 'off', // 不允许组件 prop的改变
    'vue/attribute-hyphenation': 'off', // 对模板中的自定义组件强制执行属性命名样式
  },
}
```

eslintignore忽略文件

```
dist
node_modules
```

运行脚本

```bash
"scripts": {
    "lint": "eslint src",
    "fix": "eslint src --fix",
}
```

##### 配置prettier

有了eslint，为什么还要有prettier？eslint针对的是javascript，他是一个检测工具，包含js语法以及少部分格式问题，在eslint看来，语法对了就能保证代码正常运行，格式问题属于其次；

而prettier属于格式化工具，它看不惯格式不统一，所以它就把eslint没干好的事接着干，另外，prettier支持

包含js在内的多种语言。

总结起来，**eslint和prettier这俩兄弟一个保证js代码质量，一个保证代码美观。**

安装依赖包

```bash
pnpm install -D eslint-plugin-prettier prettier eslint-config-prettier
```

.prettierrc.json添加规则

```json
{
  "singleQuote": true,
  "semi": false,
  "bracketSpacing": true,
  "htmlWhitespaceSensitivity": "ignore",
  "endOfLine": "auto",
  "trailingComma": "all",
  "tabWidth": 2
}
```

.prettierignore忽略文件

```
/dist/*
/html/*
.local
/node_modules/**
**/*.svg
**/*.sh
/public/*
```

**通过pnpm run lint去检测语法，如果出现不规范格式,通过pnpm run fix 修改**

##### 配置stylelint

[stylelint](https://stylelint.io/)为css的lint工具。可格式化css代码，检查css语法错误与不合理的写法，指定css书写顺序等。

我们的项目中使用scss作为预处理器，安装以下依赖：

```bash
pnpm add sass sass-loader stylelint postcss postcss-scss postcss-html stylelint-config-prettier stylelint-config-recess-order stylelint-config-recommended-scss stylelint-config-standard stylelint-config-standard-vue stylelint-scss stylelint-order stylelint-config-standard-scss -D
```

`.stylelintrc.cjs`**配置文件**

**官网:https://stylelint.bootcss.com/**

```js
// @see https://stylelint.bootcss.com/

module.exports = {
  extends: [
    'stylelint-config-standard', // 配置stylelint拓展插件
    'stylelint-config-html/vue', // 配置 vue 中 template 样式格式化
    'stylelint-config-standard-scss', // 配置stylelint scss插件
    'stylelint-config-recommended-vue/scss', // 配置 vue 中 scss 样式格式化
    'stylelint-config-recess-order', // 配置stylelint css属性书写顺序插件,
    'stylelint-config-prettier', // 配置stylelint和prettier兼容
  ],
  overrides: [
    {
      files: ['**/*.(scss|css|vue|html)'],
      customSyntax: 'postcss-scss',
    },
    {
      files: ['**/*.(html|vue)'],
      customSyntax: 'postcss-html',
    },
  ],
  ignoreFiles: [
    '**/*.js',
    '**/*.jsx',
    '**/*.tsx',
    '**/*.ts',
    '**/*.json',
    '**/*.md',
    '**/*.yaml',
  ],
  /**
   * null  => 关闭该规则
   * always => 必须
   */
  rules: {
    'value-keyword-case': null, // 在 css 中使用 v-bind，不报错
    'no-descending-specificity': null, // 禁止在具有较高优先级的选择器后出现被其覆盖的较低优先级的选择器
    'function-url-quotes': 'always', // 要求或禁止 URL 的引号 "always(必须加上引号)"|"never(没有引号)"
    'no-empty-source': null, // 关闭禁止空源码
    'selector-class-pattern': null, // 关闭强制选择器类名的格式
    'property-no-unknown': null, // 禁止未知的属性(true 为不允许)
    'block-opening-brace-space-before': 'always', //大括号之前必须有一个空格或不能有空白符
    'value-no-vendor-prefix': null, // 关闭 属性值前缀 --webkit-box
    'property-no-vendor-prefix': null, // 关闭 属性前缀 -webkit-mask
    'selector-pseudo-class-no-unknown': [
      // 不允许未知的选择器
      true,
      {
        ignorePseudoClasses: ['global', 'v-deep', 'deep'], // 忽略属性，修改element默认样式的时候能使用到
      },
    ],
  },
}
```

.stylelintignore忽略文件

```
/node_modules/*
/dist/*
/html/*
/public/*
```

运行脚本

```json
"scripts": {
	"lint:style": "stylelint src/**/*.{css,scss,vue} --cache --fix"
}
```

最后配置统一的prettier来格式化我们的js和css，html代码

```json
 "scripts": {
    "dev": "vite --open",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint src",
    "fix": "eslint src --fix",
    "format": "prettier --write \"./**/*.{html,vue,ts,js,json,md}\"",
    "lint:eslint": "eslint src/**/*.{ts,vue} --cache --fix",
    "lint:style": "stylelint src/**/*.{css,scss,vue} --cache --fix"
  },
```

**当我们运行`pnpm run format`的时候，会把代码直接格式化**

##### 配置husky

在上面我们已经集成好了我们代码校验工具，但是需要每次手动的去执行命令才会格式化我们的代码。如果有人没有格式化就提交了远程仓库中，那这个规范就没什么用。所以我们需要强制让开发人员按照代码规范来提交。

要做到这件事情，就需要利用husky在代码提交之前触发git hook(git在客户端的钩子)，然后执行`pnpm run format`来自动的格式化我们的代码。

安装`husky`

```bash
pnpm install -D husky
```

执行

```bash
npx husky-init
```

会在根目录下生成个一个.husky目录，在这个目录下面会有一个pre-commit文件，这个文件里面的命令在我们执行commit的时候就会执行

在`.husky/pre-commit`文件添加如下命令：

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"
pnpm run format
```

当我们对代码进行commit操作的时候，就会执行命令，对代码进行格式化，然后再提交。

##### 配置commitlint

对于我们的commit信息，也是有统一规范的，不能随便写,要让每个人都按照统一的标准来执行，我们可以利用**commitlint**来实现。

安装包

```
pnpm add @commitlint/config-conventional @commitlint/cli -D
```

添加配置文件，新建`commitlint.config.cjs`(注意是cjs)，然后添加下面的代码：

```js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  // 校验规则
  rules: {
    'type-enum': [
      2,
      'always',
      [
        'feat',
        'fix',
        'docs',
        'style',
        'refactor',
        'perf',
        'test',
        'chore',
        'revert',
        'build',
      ],
    ],
    'type-case': [0],
    'type-empty': [0],
    'scope-empty': [0],
    'scope-case': [0],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
    'header-max-length': [0, 'always', 72],
  },
}
```

在`package.json`中配置scripts命令

```json
# 在scrips中添加下面的代码
{
"scripts": {
    "commitlint": "commitlint --config commitlint.config.cjs -e -V"
  },
}
```

配置结束，现在当我们填写`commit`信息的时候，前面就需要带着下面的`subject`

```
'feat',//新特性、新功能
'fix',//修改bug
'docs',//文档修改
'style',//代码格式修改, 注意不是 css 修改
'refactor',//代码重构
'perf',//优化相关，比如提升性能、体验
'test',//测试用例修改
'chore',//其他修改, 比如改变构建流程、或者增加依赖库、工具等
'revert',//回滚到上一个版本
'build',//编译相关的修改，例如发布版本、对项目构建或者依赖的改动
```

配置husky

```bash
npx husky add .husky/commit-msg 
```

在生成的commit-msg文件中添加下面的命令

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"
pnpm commitlint
```

当我们 commit 提交信息时，就不能再随意写了，必须是 git commit -m 'fix: xxx' 符合类型的才可以，**需要注意的是类型的后面需要用英文的 :，并且冒号后面是需要空一格的，这个是不能省略的**

##### 强制使用pnpm

团队开发项目的时候，需要统一包管理器工具,因为不同包管理器工具下载同一个依赖,可能版本不一样,

导致项目出现bug问题,因此包管理器工具需要统一管理！！！

在根目录创建`scripts/preinstall.js`文件，添加下面的内容

```js
if (!/pnpm/.test(process.env.npm_execpath || '')) {
  console.warn(
    `\u001b[33mThis repository must using pnpm as the package manager ` +
    ` for scripts to work properly.\u001b[39m\n`,
  )
  process.exit(1)
}
```

配置命令

```json
"scripts": {
	"preinstall": "node ./scripts/preinstall.js"
}
```

**当我们使用npm或者yarn来安装包的时候，就会报错了。原理就是在install的时候会触发preinstall（npm提供的生命周期钩子）这个文件里面的代码。**

##### SVG图标配置

在开发项目的时候经常会用到svg矢量图,而且我们使用SVG以后，页面上加载的不再是图片资源,

这对页面性能来说是个很大的提升，而且我们SVG文件比img要小的很多，放在项目中几乎不占用资源。

**安装SVG依赖插件**

```bash
pnpm install vite-plugin-svg-icons -D
```

**在`vite.config.ts`中配置插件**

```ts
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons'
import path from 'path'
export default () => {
  return {
    plugins: [
      createSvgIconsPlugin({
        // Specify the icon folder to be cached
        iconDirs: [path.resolve(process.cwd(), 'src/assets/icons')],
        // Specify symbolId format
        symbolId: 'icon-[dir]-[name]',
      }),
    ],
  }
}
```

**入口文件导入**

```js
import 'virtual:svg-icons-register'
```

**svg封装为全局组件**

因为项目很多模块需要使用图标,因此把它封装为全局组件！！！

**在src/components目录下创建一个SvgIcon组件:代表如下**

```vue
<template>
  <div>
    <svg :style="{ width: width, height: height }">
      <use :xlink:href="prefix + name" :fill="color"></use>
    </svg>
  </div>
</template>

<script setup lang="ts">
defineProps({
  //xlink:href属性值的前缀
  prefix: {
    type: String,
    default: '#icon-'
  },
  //svg矢量图的名字
  name: String,
  //svg图标的颜色
  color: {
    type: String,
    default: ""
  },
  //svg宽度
  width: {
    type: String,
    default: '16px'
  },
  //svg高度
  height: {
    type: String,
    default: '16px'
  }

})
</script>
<style scoped></style>
```

在src文件夹目录下创建一个index.ts文件：用于注册components文件夹内部全部全局组件！！！

```ts
import SvgIcon from './SvgIcon/index.vue';
import type { App, Component } from 'vue';
const components: { [name: string]: Component } = { SvgIcon };
export default {
    install(app: App) {
        Object.keys(components).forEach((key: string) => {
            app.component(key, components[key]);
        })
    }
}
```

在入口文件引入src/index.ts文件,通过app.use方法安装自定义插件

```ts
import gloablComponent from './components/index';
app.use(gloablComponent);
```

**批量全局注册组件(代码优化)**

index.ts vue插件

```ts
import { App, Component } from 'vue'
import SvgIcon from '@/components/SvgIcon/index.vue'

// 定义类型
type ComponentType = {
    [key: string]: Component
}

// 所有组件储存在对象中
const allGlobalComponents: ComponentType = { SvgIcon }

export default {
  // vue插件的install方法
  install(app: App) {
    // 将对象的key转换成数组并进行便利
    Object.keys(allGlobalComponents).forEach((key: string) => {
      // 使用app的全局注册方法，key为组件名，进行全局注册组件
      app.component(key, allGlobalComponents[key])
    })
  },
}
```

### 响应式

```ts
const a = ref({name: '123'})
const b = shallowRef({name: '321'})
// ref 深层次响应式
// shallowRef浅层次ref
// 修改shallowRef的值，需要直接修改value的值，修改vue中值的属性将无法更新视图层
// ref与shallowRef一起写将影响shallowRef的视图更新
// triggerRef 强制更新视图层

// customRef 自定义Ref
function MyRef<T> (value: T) {
    return customRef((track, trigger) => {
        return {
            get() {
                track()
                return value
            },
            set (newValue) {
                value = newValue
                trigger()
            }
        }
    })
}
```

toRef: 只能修改响应式对象的值，非响应式视图毫无变化

```ts
const like = toRef(man, 'like')
like.value = 'xxx'
```

toRefs: 响应式对象中属性支持响应式

```ts
const toRefs = <T extends object>(object: T) => {
    const map: any = {}
    for (let key in object) {
        map[key] = toRef(object, key)
    }
    return map
}

const {name, age, like} = toRefs(man)
```

toRaw: 把响应式对象包装成普通对象

reactive实现

```ts
const isObject = (target) => target != null && typeof target === 'object'

export const reactive = <T extends object> (target: T) => {
    return new Proxy(target, {
        get (target, key, receiver) {
            track()
            const res = Reflect.get(target, key, receiver) as object
            if (isObject(res)) {
                return reactive(res)
            }
            return res
        },
        set (target, key, value, receiver) {
            trigger(target, key)
            return Reflect.set(target, key, value, receiver)
        }
    })
}
```

effect

```ts
let activeEffect

export const effect = (fn: Function) => {
    const _effect = function() {
        activeEffect = _effect
        let res = fn()
        return res
    }
    
    _effect()
    return _effect
}

const targetMap = new WeekMap()

export const track = (target, key) => {
    let depsMap = targetMap[target]
    if (!depsMap) {
        depsMap = new Map()
        targetMap.set(target, depsMap)
    }
    let deps = depsMap.get(key)
    if (!deps) {
        deps = new Set()
        depsMap.set(key, deps)
    }
    
    deps.add(activeEffect)
}

export const trigger = (target, key) => {
    const depsMap = targetMap.get(target)
    const deps = depsMap.get(key)
    
    deps.forEach(effect => {
        effect()
    })
}
```

### 计算属性

```vue
<template>
    <div>
        <input type='text' v-model='firstName'/>
        <input type='text' v-model='lastName'/>
    </div>
	<div>
        fullName: {{ name }}
    </div>
</template>

<script>
    const firstName = ref<string>('张')
	const lastName = ref<string>('三')

    // 选项式写法
    const name = computed({
        get() {
            return firstName.value + lastName.value
        },
        set (newVal) {
            [firstName.value, lastName.value] = newVal.split('-')
        }
    })

    // 函数式写法（只支持getter）
    const name = computed(() => {
        return firstName.value + lastName.value
    })
</script>
```

#### 实战用法

```vue
<script setup lang="ts">
import {computed, ref} from "vue";

interface Data {
  id: number,
  name: string,
  price: number,
  num: number
}

const goods = ref<Data>({
  id: 0,
  name: '',
  price: 0,
  num: 0
})

const goodsName = ref<string>('')

const data = ref<Data[]>([
  {
    id: 0,
    name: '东宇的帽子',
    price: 666,
    num: 1
  },
  {
    id: 1,
    name: '东宇的鞋子',
    price: 199,
    num: 2
  },
  {
    id: 2,
    name: '东宇的衣服',
    price: 88,
    num: 4
  }
])

const deleteGoods = (index: number) => {
  data.value.splice(index, 1)
}

const editGoods = (index: number) => {
  const currentGoods = data.value[index]
  currentGoods.num = 1
}

const addNum = (goods: Data) => {
  goods.num++
}

const reduceNum = (goods: Data) => {
  if (goods.num <= 0) return
  goods.num--
}

const total = computed<number>((): number => {
  return data.value.reduce((prev: number, next: Data) => {
    return prev + next.num
  }, 0)
})

const totalPrice = computed<number>((): number => {
  return data.value.reduce((prev: number, next: Data) => {
    return prev + next.price * next.num
  }, 0)
})

const addGoods = () => {
  goods.value.id = data.value.length == 0 ? 1 : data.value[data.value.length - 1].id + 1
  const goodsItem = goods.value
  data.value.unshift({
    id: goodsItem.id,
    name: goodsItem.name,
    price: goodsItem.price,
    num: goodsItem.num
  })
  goodsItem.id = 0
  goodsItem.name = ''
  goodsItem.price = 0
  goodsItem.num = 0
}

const searchGoods = () => {
  data.value = data.value.filter((item) => {
    return item.name.includes(goodsName.value)
  })
  goodsName.value = ''
}

const resetSearch = () => {
  goodsName.value = ''
}

</script>

<template>
  <div>
    <h3>搜索商品</h3>
    <input v-model="goodsName" type="text" placeholder="搜索关键字">
    <button @click="searchGoods">搜索</button>
    <button @click="resetSearch">重置</button>
  </div>
  <hr>
  <div>
    <h3>添加商品</h3>
    <form @submit.prevent="addGoods">
      <label>
        名称：
        <input v-model="goods.name" type="text" placeholder="商品名称">
      </label>
      <label>
        价格：
        <input v-model="goods.price" type="number" placeholder="商品价格">
      </label>
      <label>
        数量：
        <input v-model="goods.num" type="number" placeholder="商品数量">
      </label>
      <div style="margin-top: 20px;">
        <button type="submit">添加商品</button>
      </div>
    </form>
  </div>
  <table border cellpadding="0" cellspacing="0" style="margin-top: 20px;">
    <thead>
    <tr>
      <th>物品ID</th>
      <th>物品名称</th>
      <th>物品单价</th>
      <th>物品数量</th>
      <th>物品总价</th>
      <th>操作</th>
    </tr>
    </thead>
    <tbody>
    <tr v-for="(item, index) in data" :key="item.id">
      <td>{{ item.id }}</td>
      <td> {{ item.name }}</td>
      <td>{{ item.price }}</td>
      <td>
        <button @click="reduceNum(item)">-</button>
        {{ item.num }}
        <button @click="addNum(item)">+</button>
      </td>
      <td>{{ item.price * item.num }}</td>
      <td>
        <button @click="deleteGoods(index)">删除</button>
        <button @click="editGoods(index)">修改</button>
      </td>
    </tr>
    </tbody>
    <tfoot>
    <tr>
      <td colspan="3">总价: {{ totalPrice }}</td>
      <td colspan="3">总数量: {{ total }}</td>
    </tr>
    </tfoot>
  </table>
</template>

<style scoped>
td, th {
  padding: 10px;
}
</style>
```

计算属性源码

```ts
import {effect} from './effect'

export const computed = (getter: Function) => {
    let _value = effect(getter)
    let cateValue
    let _dirty = true
    
    class ComputedRefImpl {
       get value () {
           if (_dirty) {
               catchValue = _value()
               _dirty = false
           }
           return catchValue
       }
    }
    return new ComputedRefImpl()
}
```

### 侦听器

```vue
<script setup lang="ts">

import {ref, watchEffect} from "vue";

const message = ref<string>('飞机')
const message2 = ref<string>('飞机杯子')

const unWatch = watchEffect((onCleanup) => {
  console.log('message: ', message.value)
  console.log('message2: ', message2.value)
  onCleanup(() => {
    console.log('before')
  })
}, {
  flush: "pre",
  onTrigger (e) {
    debugger
  }
})

unWatch()

</script>
```

其他实现

```ts
// 侦听user对象
watch(user, (newUser, oldUser) => {
  // console.log(newUser, oldUser)
})

// 监听对象可以是一个getter函数，当函数内的属性发生改变时，则会触发回调
// 请勿直接侦听响应式的对象的属性值，请使用 getter函数返回需要侦听的属性
watch(() => user.age * 2, (sum) => {
  // console.log(sum)
})

// 侦听多个属性
watch([user, () => user.age], ([newValue1, newValue2]) => {
  // console.log(newValue1, newValue2)
})

// 定义一个响应式对象
const obj = reactive({ count: 0 })
// 不能直接侦听响应式对象的属性值
/*watch(obj.count, (newValue) => {

})*/
// 使用构造函数侦听响应式对象的属性
watch((): number => obj.count, (newVal: number) => {
  // console.log(newVal)
})

// 深层侦听器
watch(obj, (newValue, oldValue) => {
  // 在对象的属性值发生更改时调用
  // console.log(newValue, oldValue)
})
obj.count++

const state = reactive({
  someObject: {
    age: 19
  }
})

// 侦听响应式对象的属性时，只有对象被替换时，才会被侦听到
watch(
  () => state.someObject,
  (newValue, oldValue) => {
    // console.log('响应式侦听', newValue, oldValue)
  },
  {
    // 开启深层侦听
    deep: true,
    // 创建侦听器时立即调用
    immediate: true
  }
)

state.someObject.age++ // 如未开启深层侦听，则无法被侦听到
// 直接修改对象
/*state.someObject = {
  age: 12
}*/

console.log('-----------watchEffect-------------')

const id = ref(1)
const data = ref(null)

// 侦听id发生变化则进行发送请求
watch(id, async () => {
  const response = await fetch(`https://jsonplaceholder.typicode.com/todos/${id.value}`)
  data.value = await response.json()
}, {
  // immediate: true
})

// 这个侦听方法不需要指定immediate，他会自动跟踪id，类似计算属性
/*
watchEffect(async () => {
  const response = await fetch(`https://jsonplaceholder.typicode.com/todos/${id.value}`)
  data.value = await response.json()
}, {
  // 如果需要在访问到更新之后的dom
  flush: 'post'
})
*/

// 后置刷新， flush 的简写
watchPostEffect(() => {
})

setTimeout(() => {
  // 异步回调中监听需要手动取消侦听
  const unWatch = watchEffect(() => {
  })
  // 停止侦听
  unWatch()
}, 100)

```

### Props传参

组件

```VUE
 <MyProps foo="Props测试传值" title="这是标题" :bar="10" />
```

#### 父传子

```ts
const props = defineProps({
  title: {
    type: String,
    default: 'default'
  }
})

console.log(props)

// 使用数组的形式
const props = defineProps(['foo'])

// 使用对象的形式
defineProps({
  foo: String,
  title: String,
  bar: Number,
  author: Person
})

// 使用对象形式的复杂配置
const props = defineProps({
  foo: {
    // 指定类型
    type: [String, Number],
    // 必传项
    required: true,
    validator(value: unknown): boolean {
      // 这里可以对参数进行校验
      return typeof value === 'string'
    }
  },
  title: {
    type: String,
    // 指定默认值
    default: '这是标题'
  },
  bar: {
    type: Number
  },
  obj: {
    type: Object,
    // 对于对象类型，需要使用default方法进行配置默认值
    default(): object {
      return {
        message: '123'
      }
    }
  },
  fun: {
    type: Function,
    default() {
      // 函数的默认值
      // console.log('123')
    }
  },
  // person: Person
})

// 使用ts的泛型类型
defineProps<{
  foo: string
  title: string
  bar?: number // 表示可不传
}>()


import type { PropType } from 'vue'

interface Props {
  foo: string | number
  title: string
  bar?: number
  obj: Object
}

// 使用接口方式进行定义泛型类型
defineProps<Props>()
// const obj2 = {message: '123'}
// 泛型类型进行定义默认值
withDefaults(defineProps<Props>(), {
  foo: '默认值',
  obj: () => ({ message: '123' })
})

interface Book {
  name: '三国演义',
  year: number
}

// 定义复杂类型
defineProps<{
  book: Book
}>()

// 复杂类型的运行时声明
const props = defineProps>({
  book: Object as PropType<Book>
})

// console.log('MyProps: ', props)
```

#### 子传父

子组件定义

```ts
// 子向父传递事件
// 数组形式定义
const emit = defineEmits(['large-text', 'small-text'])
// 选项式定义
const emit = defineEmits({
  largeText(payload) {
    // 对触发事件参数进行验证
    console.log('largeText: ', payload)
    return true
  },
  smallText(payload) {
    return true
  }
})

// 使用泛型类型
const emit = defineEmits<{
  (e: 'largeText', value: number): void
  (e: 'smallText', value: number): void
}>()
```

使用

```ts
function bigText() {
  emit('largeText', 0.5)
}

function smallText() {
  emit('smallText', 0.3)
}
```

子组件

```vue
<button @click="$emit('largeText', 1)">放大文本</button>
<button @click="bigText">放大文本2</button>
<button @click="smallText">缩小文本</button>
```

父组件

```vue
 <div :style="{fontSize: fontSize + 'em'}">
      <MyComponent 
                   @largeText="args => fontSize += args" 
                   @smallText="args => fontSize -= args" />
   </div>
```

暴露组件属性

```ts
defineExpose({
  name: '东宇',
  open: () => {
    console.log('test....')
  }
})
```

#### 兄弟

A组件

``` vue
<script setup lang="ts">
import Bus from '@/Bus'

let flag = false
const emmitB = () => {
  flag = !flag
  Bus.emit('on-click', flag)
}
</script>

<template>
<div>
  <h1>兄弟组件传参</h1>
  <h3>A组件</h3>
  <button @click="emmitB">派发一个事件</button>
</div>
</template>
```

B组件

``` vue
<script setup lang="ts">
import Bus from '@/Bus'
import { ref } from 'vue'

const Flag = ref(false)

Bus.on('on-click', (flag: boolean) => {
  Flag.value = flag
})
</script>

<template>
<div class="B">
  <h3>B组件</h3>
  {{Flag}}
</div>
</template>
```

实现消息订阅与发布

``` ts
type BusClass = {
  emit: (name: string) => void
  on: (name: string, callback: Function) => void
}

type ParamsKey = string | null | symbol

type List = {
  [key: ParamsKey]: Array<Function>
}

class Bus implements BusClass {

  list: List

  constructor() {
    this.list = {}
  }

  emit(name: string, ...args: Array<any>) {
    const eventName: Array<Function> = this.list[name]
    eventName.forEach(fn => {
      fn.apply(this, args)
    })
  }
  on(name: string, callback: Function) {
    const fn: Array<Function> = this.list[name] || []
    fn.push(callback)
    this.list[name] = fn
  }
}

export default new Bus()
```

#### mitt

1.全局挂载

```ts
const Mit = mitt()
const app = createApp(App)

// ts相关
declare module 'vue' {
  export interface ComponentCustomProperties {
    $Bus: typeof Mit
  }
}

// 挂载到全局中
app.config.globalProperties.$Bus = Mit
```

2.按需引入

```ts
import mitt from 'mitt'

const emitter = mitt()
export default emitter
```

A组件

```ts
import { getCurrentInstance } from 'vue'

// 获取组件实例
const instance =  getCurrentInstance()

// 发送消息
instance?.proxy?.$Bus.emit('on-click', 'mitt')

const Bus = (str: any) => {
  console.log(str)
}

instance?.proxy?.$Bus.on('on-click', Bus)

// 取消监听
instance?.proxy?.$Bus.off('on-click', Bus)

// 清除全部监听
instance?.proxy.$Bus.all.clear()
```

B组件

```ts
// 接收消息
instance?.proxy?.$Bus.on('on-click', (str: string) => {
  console.log(str)
})
// 接收全部消息
instance?.proxy?.$Bus.on('*', (type: string, str: string) => {
  console.log(type, str)
})
```

### ref与$parent

ref,提及到ref可能会想到它可以获取元素的DOM或者获取子组件实例的VC。既然可以在父组件内部通过ref获取子组件实例VC，那么子组件内部的方法与响应式数据父组件可以使用的。

比如:在父组件挂载完毕获取组件实例

父组件内部代码:

```vue
<template>
  <div>
    <h1>ref与$parent</h1>
    <Son ref="son"></Son>
  </div>
</template>
<script setup lang="ts">
import Son from "./Son.vue";
import { onMounted, ref } from "vue";
const son = ref();
onMounted(() => {
  console.log(son.value);
});
</script>
```

但是需要注意，如果想让父组件获取子组件的数据或者方法需要通过defineExpose对外暴露,因为vue3中组件内部的数据对外“关闭的”，外部不能访问

```vue
<script setup lang="ts">
import { ref } from "vue";
//数据
let money = ref(1000);
//方法
const handler = ()=>{
}
defineExpose({
  money,
   handler
})
</script>
```

$parent可以获取某一个组件的父组件实例VC,因此可以使用父组件内部的数据与方法。必须子组件内部拥有一个按钮点击时候获取父组件实例，当然父组件的数据与方法需要通过defineExpose方法对外暴露

```vue
<button @click="handler($parent)">点击我获取父组件实例</button>
```

### useAttrs

在Vue3中可以利用useAttrs方法获取组件的属性与事件(包含:原生DOM事件或者自定义事件),次函数功能类似于Vue2框架中$attrs属性与$listeners方法。

比如:在父组件内部使用一个子组件my-button

```vue
<my-button type="success" size="small" title='标题' @click="handler"></my-button>
```

子组件内部可以通过useAttrs方法获取组件属性与事件.因此你也发现了，它类似于props,可以接受父组件传递过来的属性与属性值。需要注意如果defineProps接受了某一个属性，useAttrs方法返回的对象身上就没有相应属性与属性值。

```vue
<script setup lang="ts">
import {useAttrs} from 'vue';
let $attrs = useAttrs();
</script>
```

### 递归组件

定义数据类型

```ts
interface Tree {
  name: string,
  checked: boolean,
  children?: Tree[]
}
```

定义数据源

```ts
const treeList = reactive<Tree[]>([
  {
    name: '1',
    checked: false,
    children: [
      {
        name: '1-1',
        checked: true
      }
    ]
  },
  {
    name: '2',
    checked: true,
  },
  {
    name: '3',
    checked: false,
    children: [
      {
        name: '3-1',
        checked: true,
        children: [
          {
            name: '3-1-1',
            checked: true
          }
        ]
      }
    ]
  }
])
```

开始遍历显示

```vue
<script setup lang="ts">
interface Tree {
  name: string,
  checked: boolean,
  children?: Tree[]
}

defineProps<{
  data: Tree[]
}>()

const click = (tree: Tree, event: Event) => {
  console.log(tree, event)
}
</script>

<template>
<div @click.stop="click(item, $event)" v-for="(item, index) in data" :key="index" style="margin-left: 10px;">
  <input v-model="item.checked" type="checkbox" name="cb" id="">
  <span>{{item.name}}</span>
  <Tree v-if="item?.children?.length" :data="item?.children ?? []"/>
</div>
</template>
```

> ?表示属性不存在时就返回undefined
>
> ??表示如果之前为null或undefined时，则返回后面的值

### 动态组件

具体实现

```ts
const components = reactive([
  {
    title: '只因你太美',
    com: markRaw(Inject)
  },
  {
    title: '来呀来呀充八万',
    com: markRaw(Store)
  },
  {
    title: '充钱你才能变得更强',
    com: markRaw(MyTransitionGroup)
  }
])

const current = ref(components[0].com)
const active = ref(0)

const switchTab = (com: any, index: number) => {
  active.value = index
  current.value = com
}
```

**markRaw 标记会原始对象，将不会具备响应式功能**

组件内容

```vue
<template>
  <div>
    <div style="display: flex;">
      <div @click="switchTab(item.com, index)" v-for="(item, index) in components" :key="index" class="tab" :class="[active === index ? 'active' : '']">
        {{ item.title }}
      </div>
    </div>
    
    <component :is="current" />
  </div>
</template>
```

### 插槽

定义插槽

```vue
<template>
  <div>
    <h2>插槽</h2>
    <button>
      <!-- 定义一个默认的插槽 -->
      <!--   如果父组件没有提供任何的插槽内容，则将使用此处插槽内部定义的默认内容   -->
      <slot>Submit</slot>
    </button>
    <!-- 具名插槽   -->
    <div class="container">
      <header>
        <!--   通过name属性定义插槽的名字     -->
        <slot name="header" :count="1"></slot>
      </header>
      <main>
        <slot></slot>
      </main>
      <footer>
        <slot name="footer"></slot>
      </footer>
    </div>
    <ul>
      <li v-for="item in items" :key="item.name">
        <!--    通过v-bind将插槽的数据传递给app组件    -->
        <slot name="item" v-bind="item"></slot>
      </li>
    </ul>
  </div>
</template>
```

组件

```vue
<MySlot>
      <!--   插槽名可以动态进行修改   -->
      <template v-slot:[slotName]="slotProps">
        <!--    header插槽的内容    -->
        <!--    接收由子组件插槽传递过来的参数    -->
        <span>Header {{ slotProps.count }}</span>
      </template>
      <!--  <template #default>
              <div>内容</div>
            </template>-->
      <!--  默认插槽无需使用template进行包裹   -->
      <div>内容</div>
      <template #footer> Footer</template>
      <template #item="{ name, age }">
        <p>{{ name }}</p>
        <p>{{ age }}</p>
      </template>
</MySlot>
```

### 异步组件

定义异步组件

```vue
<script setup lang="ts">
import { ref } from 'vue'

const promise = new Promise<string>((resolve) => {
  setTimeout(() => {
    resolve('这是返回的数据')
  }, 3000)
})
const data = ref('')
const res: string = await promise
console.log(res)
data.value = res
</script>

<template>
<div>
  {{ data}}
</div>
</template>
```

实现异步加载效果

```ts
const SyncCom = defineAsyncComponent(() => import('@/components/SyncCom.vue'))
```

```vue
<Suspense>
    <template #default>
		<SyncCom/>
    </template>
    <template #fallback>
		<div>正在加载中...</div>
    </template>
</Suspense>
```

#### 搭配路由使用

```vue
<RouterView v-slot="{ Component }">
  <template v-if="Component">
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <!-- 主要内容 -->
          <component :is="Component"></component>

          <!-- 加载中状态 -->
          <template #fallback>
            正在加载...
          </template>
        </Suspense>
      </KeepAlive>
    </Transition>
  </template>
</RouterView>
```

### Transition

```vue
<button @click="isShow = !isShow">Toggle</button>
<Transition>
    <p v-if="isShow">这是一段文本内容</p>
</Transition>
<Transition name="fade">
    <p v-if="isShow">这是fade动画</p>
</Transition>
<Transition name="slide-fade">
    <p v-if="isShow">这是slide-fade动画</p>
</Transition>
<!--
    duration设置动画总时长
    appear 表示出现时就执行动画
    使用插槽可以实现可复用的动画效果
    使用插槽的组件的style标签不要加上scoped属性
    mode 指定模式，out-in表示先执行离开时动画在执行进入时动画
-->
<Transition name="bounce" :duration="{enter: 800, leave: 700}"
            @leave="onLeave" appear mode="out-in">
    <p v-if="isShow" style="text-align: center">Animation动画</p>
</Transition>
```

css定义

根据**name**属性定义css选择器

> xxx-enter-from 节点动画进入开始时
>
> xxx-enter-active 节点动画进行时
>
> xxx-enter-to 节点动画结束时
>
> xxx-leave-from 节点移除动画开始时
>
> xxx-leave-active 节点移除动画进行时
>
> xxx-leave-to 节点被移除时

```css
<style scoped>
.v-enter-from,
.v-leave-to {
  opacity: 0;
}

.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease-in-out;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.8s ease-out;
}

/*离开和进入时的样式*/
.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(40px);
  opacity: 0;
}

/* 插入时的动画*/
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

/* 移除时的动画*/
.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

/* 插入时的动画*/
.bounce-enter-active {
  animation: bounce-in 0.5s;
}

/* 移除时的动画*/
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}

@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}
</style>
```

### TransitionGroup

使用方法

```vue
<script setup lang="ts">
import { ref } from 'vue'

const data = ref<string[]>(['这是Item', '我是一个标签哈哈哈', '充八万了把'])

function addItem() {
  const index: number = getIndex()
  data.value.splice(index, 0, 'addItem' + index)
}

function deleteItem() {
  if (data.value.length === 0) return
  const index: number = getIndex()
  data.value.splice(index, 1)
}

function getIndex(): number {
  return Math.floor(Math.random() * data.value.length)
}

function reset() {
  data.value.sort(() => Math.random() - 0.5)
}

</script>

<template>
<div>
  <h2>TransitionGroup</h2>
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in data" :key="item">
      {{ item }}
    </li>
  </TransitionGroup>
  <button @click="addItem">添加一项</button>
  <button @click="deleteItem">删除一项</button>
  <button @click="reset">重新排序</button>
</div>
</template>

<style scoped>

.list-enter-from,
.list-leave-to {
  transform: translateX(40px);
  opacity: 0;
}

.list-move, /* 对移动中元素应用过渡 */
.list-enter-active,
.list-leave-active {
  transition: all 0.8s ease;
}

/* 确保将离开的元素从布局中删除
   以便能正确计算出移动的动画 */
.list-leave-active {
  position: absolute;
}
</style>

```

#### 打乱组合动画

```vue
<TransitionGroup move-class="mmm" tag="div" class="wraps">
    <div class="items" v-for="item in list" :key="item.id">
        {{ item.number}}
    </div>
</TransitionGroup>
<button @click="random">toggle</button>
```

```ts
import _ from 'lodash'

const list = ref(Array.apply(null, { length: 81 } as number[]).map((_, index) => {
  return {
    id: index,
    number: index % 9
  }
}))

const random = () => {
  list.value = _.shuffle(list.value)
}
```

```css
.wraps {
  display: flex;
  flex-wrap: wrap;
  width: calc(25px * 10 + 9px);
}

.wraps .items {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 25px;
  height: 25px;
  border: 1px solid #ccc;
}

.mmm {
  transition: all 1s;
}
```

状态动画

`npm install gsap`

```vue
<input v-model="num.current" step="20" type="number" name="" id="">
{{ num.tweenedNumber}}
```

``` ts
import { gsap } from 'gsap'

const num = reactive({
  current: 0,
  tweenedNumber: 0
})

watch(() => num.current, (newValue: number, oldValue: number) => {
  gsap.to(
    num,
    {
      duration: 1,
      tweenedNumber: newValue.toFixed(0)
    }
  )
})
```

### 依赖注入

vue3提供两个方法provide与inject,可以实现隔辈组件传递参数

祖宗组件提供数据:

provide方法用于提供数据，此方法执需要传递两个参数,分别提供数据的key与提供数据value

根组件

```ts
// 应用层
provide<string>(/* 注入名 */'message',/* 注入值 */ 'hello world')

const location = ref<string>('充八万')

function cong() {
  location.value = '充好了'
}

provide('location', {
  location,
  cong
})

// 值将不允许被修改
provide('read-only', readonly(location))
```

孙子组件

```ts
const message = inject<string>('message',/* 默认值 */ 'hello')
const { location, cong } = inject<Ref<string> | Function>('location')
```

### tsx支持

安装依赖

```bash
npm install @vitejs/plugin-vue-jsx -D
```

简单用法

```tsx
import {defineComponent} from 'vue'

export default defineComponent({
  data() {
    return {
      age: 19
    }
  },
  render() {
    return (
      <div>{ this.age }</div>
    )
  }
})
```

具体用法

```tsx
import { defineComponent, ref } from 'vue'

interface Props {
  name?: string
}

const A = (_: any, {slots}) => (
  <>
    <div>{slots.default ? slots.default() : '默认值'}</div>
    <div>{slots.foo?.()}</div>
  </>
)

export default defineComponent({
  props: {
    name: String
  },
  emits: ['on-click'],
  setup(props: Props, { emit }) {
    const flag = ref(false)
    // tsx需要手动解包
    // 不支持v-if
    // return () => (<div v-show={flag.value}>我是tsx</div>)
    const data = [
      {
        name: 'dongyu1'
      },
      {
        name: 'dongyu2'
      }
    ]
    /*return () => (
      <>
        <div>{flag.value ? <div>true</div>: <div>false</div>}</div>
      </>
    )*/

    const fn = (item: any) => {
      console.log(item)
      emit('on-click', item.name)
    }

    const slot = {
      default: () => (<div>default slots</div>),
      foo: () => (<div>foo slots</div>)
    }

    const v = ref<string>('')
    return () => (
      <>
        <input type="text" v-model={v.value} />
        <div>{v.value}</div>
        <hr/>
        <div>{props.name}</div>
        <hr/>
        <A v-slots={slot}></A>
        <hr/>
        {
          data.map(item => {
            return <div onClick={() => fn(item)}>{item.name}</div>
          })
        }
      </>
    )
  }
})
```

### 状态管理(Pinia)

npm安装**[pinia]([Pinia | The intuitive store for Vue.js (vuejs.org)](https://pinia.vuejs.org/zh/))**

`npm install pinia`

main.ts 安装插件

```ts
import { createPinia } from 'pinia'
const app = createApp(App)
app.use(createPinia())
```

配置store（counter.ts）

```ts
import { defineStore } from 'pinia'

// 使用一个对象来定义store
/*
export const useCounterStore = defineStore("counter", {
  state: () => ({count: 0}),
  actions: {
    increment() {
      this.count++
    }
  }
})
*/

// 使用一个函数定义store
/*
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)

  function increment() {
    count.value++
  }

  return {
    count, increment
  }
})
*/

/**
* ts 定义类型
*/
interface State {
  count: number
  items: Item[]
  userinfo: UserInfo | null
}

interface UserInfo {
  username: string
  password: string
}

interface Item {
  name: string
  email: string
}

// 向外导出store
// 组合式
export const useCounterStore = defineStore('counter', {
  state: (): State => ({ count: 0, userinfo: null, items: []}),
  getters: {
    double: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    }
  }
})
```

使用

```vue
<script lang="ts" setup>
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()

// 订阅
counter.$subscribe((mutation, state) => {
  console.log(mutation, state)
  mutation.type // 'direct' | 'patch object' | 'patch function'
  // 和 cartStore.$id 一样
  mutation.storeId // 'cart'
  // 只有 mutation.type === 'patch object'的情况下才可用
  mutation.payload // 传递给 cartStore.$patch() 的补丁对象。

  // 每当状态发生变化时，将整个 state 持久化到本地存储。
  localStorage.setItem('cart', JSON.stringify(state))
}, {
    // 组件销毁时依然订阅
    detached: true,
    // 深度订阅
    deep: true,
    // 改变时机
    flush: 'post'
})

// 直接使用变量进行自加一
counter.count++

// 使用内部方法进行自加一
counter.$patch({ count: counter.count + 1 })

// 使用actions进行自加一
counter.increment()

// 重置值
counter.$reset()

// 同时修改多个值
counter.$patch(state => {
  state.count = 1
  state.items.push({name: '123', email: '123'})
})
    
counter.$state = {
   count: 1
}
    
const unsubscribe = counter.$OnAction(({
    name, // action 名称
    store, // store 实例，类似 `someStore`
    args, // 传递给 action 的参数数组
    after, // 在 action 返回或解决后的钩子
    onError, // action 抛出或拒绝的钩子
  }) => {
    console.log(arg)
    // 这将在 action 成功并完全运行后触发。
    // 它等待着任何返回的 promise
    after((result) => {
        console.log('after')
    })
    
    // 如果 action 抛出或返回一个拒绝的 promise，这将触发
    onError((error) => {
      console.warn(
        `Failed "${name}" after ${Date.now() - startTime}ms.\nError: ${error}.`
      )
    })
})

// 手动取消订阅
unsubscribe()
</script>

<template>
  <div>{{ counter.count }}
    {{ counter.items }}
    <button @click="counter.count ++">+1</button>
  </div>
</template>
```

#### 一些问题

结构store对象的值将不再具备响应式，所以通常将store对象转换成ref

```ts
const {count} = storeToRefs(Test)
```

#### 持久化数据

```ts
type Options = {
  key: string
}

// 默认key
const __piniaKey__: string = 'dongyu'

// 保存数据到本地
const setStorage = (key: string, value: any) => {
  localStorage.setItem(key, JSON.stringify(value))
}

// 从本地获取数据
const getStorage = (key: string) => {
  const value = localStorage.getItem(key)
  return value ? JSON.parse(value as string) : {}
}

// 插件
const piniaPlugin = (options: Options) => {
  return (context: PiniaPluginContext) => {
    const {store} = context
    // 首先先从本地获取数据
    const data = getStorage(`${options?.key ?? __piniaKey__}-${store.$id}`)
    // 监听store数据变化
    store.$subscribe(() => {
      console.log('change')
      // 通过对应id保存至本地
      // 注意，选项式需要使用toRaw(store.$state)
      setStorage(`${options?.key ?? __piniaKey__}-${store.$id}`, store.$state) 
    })
    // 返回数据并挂载到store上
    return {
      ...data
    }
  }
}

const pinia = createPinia()
app.use(pinia)
// 注册一个插件
pinia.use(piniaPlugin({
  key: 'pinia'
}))
```

#### Vuex

/store/index.js

```js
import Vue from "vue";
import Vuex from 'vuex'
import user from "@/store/modules/user";

import {COUNT_MUTATION} from './mutation-types'

// 仓库
// 安装Vuex插件
Vue.use(Vuex)

const store = new Vuex.Store({
  // 共享数据
  state: {
    count: 0
  },
  // 修改数据
  mutations: {
    // state 共享数据，payload 传递的参数
    [COUNT_MUTATION]: (state, payload) => {
      state.count = payload
    }
  },
  // methods方法
  actions: {
    // context表示当前store仓库实例
    // 这里可以将context结构为commit，dispatch，state, 如果是子模块，还附有rootState
    // payload为传递过来的数据，可以为具体数值，也可以为对象
    increment: (context, /* payload */) => {
      context.commit(COUNT_MUTATION, context.state.count + 1)
      context.commit('user/setUserName', 'admin modified')
      context.dispatch('user/totalUserName', '123')
    }
  },
  // 计算属性
  getters: {
    total: (state) => {
      return state.count * 2
    }
  },
  // 子模块
  modules: {
    user
  }
})

export default store

```

/store/modules/user.js

```js
import { COUNT_MUTATION } from "@/store/mutation-types";

export default {
  namespaced: true, // 当设置为true时，除state外，getter、action、mutation访问将额外添加模块名
  state: () => ({
    username: 'admin'
  }),
  mutations: {
    setUserName(state, payload) {
      state.username = payload
    }
  },
  actions: {
    // state: 当前模块的state，commit：提交修改函数，rootState：主仓库的state, payload传递的参数
    // eslint-disable-next-line no-unused-vars
    totalUserName({state, commit, rootState, dispatch, getters, rootGetters}, payload) {
      console.log(payload)
      commit('setUserName', state.username + rootState.count)
      // 调用主仓库中的mutation
      // 第一个参数为type，第二个为传递的参数，第三个指定为根仓库
      commit(COUNT_MUTATION, 10, {root: true})
      // 调用其他模块中的mutation
      // commit('test/mutation', payload, {root: true})
      // 调用主模块的increment方法
      // dispatch('increment', 10, {root: true})
      // 调用其他模块的方法同上
      // 调用当前getters
      // getters.userInfo
      // 调用主仓库的getters
      // rootGetters.total
      // 调用其他模块的getters
      // rootGetters["user/userInfo"]
    },
    // 将待命名空间的actions添加到主仓库中
    testAction: {
      root: true,
      handler(context, payload) {
        console.log(context, payload)
      }
    }
  },
  getters: {
    // state: 当前state， getter当前模块getters，rootState：主仓库state
    userInfo: (state, getters, rootState) => {
      return state.username + getters + rootState.count
    }
  }
}
```

main.js

```js
import Vue from 'vue'
import App from './App.vue'
import store from "@/store";

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  store // 注入vuex实例
}).$mount('#app')

```

App.vue

```vue
<template>
  <div>
    {{ $store.state.count }}, {{ $store.getters.total }}

    <div>{{ $store.state.user.username }}123</div>
<!--    <div>{{ $store.getters.userInfo }}</div>-->
          <!--  子模块中的数据使用此方法获取到dom结构中  -->
    <div>{{ $store.getters["user/userInfo"] }}</div>
    <Counter/>
    <button @click="increment">+1</button>
    <button @click="reset">Reset</button>
  </div>
</template>

<script>

import { mapGetters, mapMutations, mapState } from "vuex";
import Counter from "@/components/Counter.vue";
import { COUNT_MUTATION } from "@/store/mutation-types";

// 统一创建子模块的命名空间
// const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  name: 'App',
  components: {
    Counter
  },
  methods: {
    /* ===========mapMutations================ */
    // 通过mapMutation方法将仓库中的修改数据方法添加到methods中
    ...mapMutations([COUNT_MUTATION]),
    // 子模块user共享，同上
    ...mapMutations('user', ['setUserName']),
    // mapActions同上
    increment() {
      // 调用仓库中increment方法
      this.$store.dispatch('increment')
      // 传递参数
      // this.$store.dispatch('increment', 1)
      // 对象方式传递
      /*this.$store.dispatch('increment', {
        count: 1
      })*/
      /*this.$store.dispatch({
        type: 'increment',
        count: 0, // 取值方式：payload.count
      })*/

      this.$store.dispatch('user/totalUserName', 0)

      // user.js中的actions添加到主仓库中的actions中
      this.$store.dispatch('testAction', 0)
    },
    reset() {
      // 对仓库中的数据进行修改操作
      this.$store.commit(COUNT_MUTATION, 0)
      // 传递的参数可以是一个对象
      // this.$store.commit({type: 'setCount', amount: 0})
    }
  },
  computed: {
    /* ==========使用State=============  */
    // 通过mapState将仓库中的数据共享到计算属性当中
    ...mapState(['count']),
    // 通过对象方式对数据名进行重命名
    ...mapState({'countNum': "count"}),
    // 使用模块中的数据
    ...mapState('user', ['username']),
    /* ==============使用Getters=================== */
    // 通过mapGetters方法将仓库中的Getters共享到计算属性中
    ...mapGetters(['total']),
    ...mapGetters({'totalNum': "total"}),
    ...mapGetters('user', ['userInfo'])
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

Counter.vue

```vue
<script>
import { mapState } from "vuex";

export default {
  name: "Counter",
  // 使用对象方式
  computed: mapState({
    // 将仓库中的count共享到当前组件的计算属性当中
    // count: state => state.count,
    // 以上方法的简写形式
    countNum: "count",
    // 使用函数方式更加灵活
    count(state) {
      return state.count + this.localState
    }
  })
}
</script>

<template>
  <div>
    Count: {{ count }} , CountNum: {{ countNum }}
  </div>
</template>

<style scoped>

</style>
```

##### 表单处理

```VUE
<input :value="message" @input="updateMessage">
```

```vue
computed: {
  ...mapState({
    message: state => state.obj.message
  })
},
methods: {
  updateMessage (e) {
    this.$store.commit('updateMessage', e.target.value)
  }
}
```

mutation

```js
mutations: {
  updateMessage (state, message) {
    state.obj.message = message
  }
}
```

双向绑定

```vue
<input v-model="message">
```

```vue
computed: {
  message: {
    get () {
      return this.$store.state.obj.message
    },
    set (value) {
      this.$store.commit('updateMessage', value)
    }
  }
}
```

### V-Model

**双向数据绑定**

组件中定义

```vue
<script setup lang="ts">
import { computed } from 'vue'

// v-model:title 表示指定一个参数
const props = defineProps({
  modelValue: String,
  // v-model 修饰符
  modelModifiers?: {
    big: boolean,
    // 指定一个默认值
    default: () => ({
      big: false
    })
  }
}) // modelValue是父组件v-model传递的属性，固定写法
const emit = defineEmits(['update:modelValue']) // 更新属性值，固定写法

const value = computed({
  get() {
    return props.modelValue
  },
  set: function(val) {
    console.log(val)
    const { big } = props.modelModifiers
    if (big) {
      // console.log(props.modelModifiers)
    }
    emit('update:modelValue', val)
  }
})

const change = (e: Event) => {
  emit('update:modelValue', (e.target as HTMLInputElement).value)
}

</script>

<template>
  <div>
    <h2>V-Model</h2>
    <input type="text" :value="modelValue" @input="change">
    <input type="text" v-model="value">
  </div>
</template>
```

父组件中用法

```vue
<!--  v-model:title 给v-model指定一个参数，子组件接收属性名将为title  -->
<V-Model v-model.big="searchText" />
<div>App组件: search: {{ searchText }}</div>
```

### 自定义指令

```ts
// 定义一个自定义指令 v-focus
const vFocus: Directive = {
  created: (el, binding, vNode, prevVNode) => {
    // console.log(el, binding, vNode, prevVNode)
  },
  mounted: (el: HTMLInputElement) => {
    el.focus()
  },
  beforeUpdate() {},
  updated() {}
}

// 简写
const vFocus: Directive<HTMLElement, string> = (el, binding) => {
    
}
```

#### 实战用法

可移动的标签

```vue
<script setup lang="ts">
import type { Directive, DirectiveBinding } from 'vue'

const vMove: Directive<any, void> = (el: HTMLElement, binding: DirectiveBinding) => {
  const moveElement: HTMLDivElement = el.firstElementChild as HTMLDivElement
  console.log(moveElement)
  const mouseDown = (e: MouseEvent) => {
    // 鼠标按下时调用这个箭头函数
    let x = e.clientX - el.offsetLeft
    let y = e.clientY - el.offsetTop
    
    const move = (e: MouseEvent) => {
      console.log(e)
       // 具体移动的实现
      el.style.left = e.clientX - x + 'px'
      el.style.top = e.clientY - y + 'px'
    }
    // 监听鼠标移动事件
    document.addEventListener('mousemove', move)
    document.addEventListener('mouseup', () => {
      // 鼠标抬起时删除鼠标移动事件
      document.removeEventListener('mousemove', move)
    })
  }
  // 监听标题区域鼠标按下的事件
  moveElement.addEventListener('mousedown', mouseDown)
}
</script>

<template>
<div v-move class="box">
  <div class="header">这是标题</div>
  <div>内容</div>
</div>
</template>

<style scoped>
.box {
  position: absolute;
  width: 400px;
  height: 200px;
  border: 1px solid black;
}
    
.header {
	cursor: move;
}
</style>
```

图片懒加载

```vue
<script setup lang="ts">
import type {Directive} from "vue"
// glob 是懒加载的模式
// globEager 直接静态加载
const imageList: Record<string, { default: string }> = import.meta.glob('./assets/images/**', {eager: true})
// 对象转换成数组
const arr = Object.values(imageList).map(item => item.default)

const vLazy: Directive<HTMLImageElement, string> = async (el: HTMLImageElement, binding) => {
  // 默认占位图
  const def = await import('./assets/vue.svg')
  // 设置默认图片
  el.src = def.default
  const observer = new IntersectionObserver((entry) => {
    // 监听图片是否完全显示
    if (entry[0].intersectionRatio > 0) {
      setTimeout(() => {
        // 需要加载的图片
        el.src = binding.value
      }, 1000)
      observer.unobserve(el)
    }
  })
  observer.observe(el)
}
</script>

<template>
  <div>
    <img v-lazy="item" v-for="(item, index) in arr" :src="item" :key="index" alt="" height="500" width="350">
  </div>
</template>

<style lang="css">
img {
  display: block;
}
</style>
```

### 自定义hooks

定义转换方法

```ts
import {onMounted} from "vue"

type Options = {
  el: string
}

export default function (options: Options): Promise<{ baseUrl: string }> {
  return new Promise((resolve) => {
    onMounted(() => {
      const img: HTMLImageElement = document.querySelector(options.el) as HTMLImageElement
      img.onload = () => {
        resolve({
          baseUrl: base64(img)
        })
      }
    })

    // 核心实现代码
    const base64 = (el: HTMLImageElement) => {
      const canvas = document.createElement('canvas')
      const context = canvas.getContext('2d')
      canvas.width = el.width
      canvas.height = el.height
      context?.drawImage(el, 0, 0, canvas.width, canvas.height)
      return canvas.toDataURL('image/png')
    }
  })
}
```

使用

```vue
<script setup lang="ts">
import useBase64 from './hooks'

useBase64({
  el: '#img'
}).then(res => {
  console.log(res.baseUrl)
})

</script>

<template>
  <img src="./assets/images/1.png" alt="" id="img">
</template>
```

#### 综合案例

##### 准备工作

1.新建项目

2.创建src目录

3.src目录添加index.ts文件

4.执行命令

```bash
npm init -y #初始化项目
npm install -g typescript # 安装ts
npm install vue -D # 安装vue
npm install vite # 安装vite
tsc --init #初始化ts
```

5.添加文件:

​	`index.d.ts`    `vite.config.ts`

开始 

index.ts

```ts
// MutationObserver 主要侦听子集的变化 还有属性的变化，以及增删改查
// ResizeObserver 主要侦听元素宽高的变化

import type { App } from 'vue'

function useResize(el: HTMLElement, callback: Function) {
  const resize = new ResizeObserver((entries) => {
    callback(entries[0].contentRect)
  })
  resize.observe(el)
}

const install = (app: App) => {
   app.directive('resize', {
     mounted(el, binding) {
       useResize(el, binding.value)
     }
   })
}

useResize.install = install

export default useResize
```

vite.config.ts

```ts
import {defineConfig} from "vite"

export default defineConfig({
  build: {
    lib: {
      entry: 'src/index.ts',
      name: 'useResize'
    },
    rollupOptions: {
      external: ['vue'],
      output: {
        globals: {
          useResize: 'useResize'
        }
      }
    }
  }
})
```

index.d.ts

```ts
import {App} from "vue"

declare const useResize: {
  (el: HTMLElement, callback: Function): void
  install: (app: App) => void
}

export default useResize
```

package.json

> 注意修改 name和version

```json
{
  "name": "v-resize-jdy",
  "version": "0.1.2",
  "description": "2023-9-21",
  "main": "./dist/v-resize.umd.js",
  "module": "dist/v-resize.mjs",
  "scripts": {
    "build": "vite build",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "jdy2002",
  "files": [
    "dist",
    "index.d.ts"
  ],
  "license": "ISC",
  "devDependencies": {
    "vite": "^4.4.9",
    "vue": "^3.3.4"
  }
}
```

运行 `npm run build` 生成dist文件

打包发布到npm

```bash
npm login #登录npm
npm publish #发布
```

##### 在项目中使用

1.安装依赖

```bash
npm install v-resize-jdy
```

2.安装自定义指令插件

```ts
import useResize from "v-resize-jdy"

// 在main.ts中使用插件
createApp(App)
  .use(useResize)
```

3.使用

```vue
<script setup lang="ts">
import useResize from "v-resize-jdy"
import {onMounted} from "vue"

// 1.直接引入使用
/*onMounted(() => {
  useResize(document.querySelector('#resize') as HTMLElement, (e: any) => {
    console.log(e)
  })
})*/

const resizeCallback = (e: any) => {
  console.log(e)
}
</script>

<template>
  <!-- 2.自定义指令实现 -->
  <div v-resize="resizeCallback" id="resize"></div>
</template>

<style lang="css">
#resize {
  width: 300px;
  height: 300px;
  background-color: pink;
  border: 1px solid #ccc;
  resize: both;
  overflow: hidden;
}
</style>
```

### 全局函数和变量

main.ts

```ts
const app = createApp(App)

// 配置全局变量 $dev
app.config.globalProperties.$dev = 'dev'
// 配置全局函数 format
app.config.globalProperties.$filters = {
  format<T> (str: T) {
    return `东宇-${str}`
  }
}

// ts支持
type Filter = {
  format<T>(str: T): string
}

declare module 'vue' {
  export interface ComponentCustomProperties {
    $filters: Filter,
    $dev: string
  }
}
```

组件中使用

```vue
<script setup lang="ts">
import { getCurrentInstance } from 'vue'

// 获取实例对象
const app = getCurrentInstance()
// 使用
console.log(app?.proxy?.$filters.format('ts'))
</script>
<template>
    <!-- 直接使用 -->
	<div>{{$filters.format('的飞机')}}</div>
</template>
```

### 插件(Plugin)

实现全局加载插件

定义加载布局

```vue
<template>
    <!-- 通过v-if实现显示隐藏效果 -->
    <div v-if="isShow">Loading...</div>
</template>
    
<script setup lang='ts'>
import { ref } from 'vue'
// 这个响应式属性控制显示和隐藏
const isShow = ref<boolean>(false)

/**
* 用两个方法来控制
*/
const show = () => {
  isShow.value = true
}

const hide = () => {
  isShow.value = false
}

// 向外导出显示和隐藏方法
defineExpose({
  show,
  hide,
  isShow
})

</script>
    
<style scoped>
div {
    position: absolute;
    left: 50%;
    top: 50%;
    padding: 10px 40px;
    background-color: rgba(255, 255, 255, .3);
    box-shadow: 1px 1px 2px 2px rgba(0, 0, 0, .3);
    border-radius: 16px;
    transform: translate(-50%, -50%);
}
</style>
```

定义插件

```ts
import type { App, VNode } from 'vue'
import Loading from './index.vue'

import { createVNode, render } from 'vue'

export default {
  // 必须使用这个install方法
  install(app: App) {
    // 构建成虚拟dom
    const vNode: VNode = createVNode(Loading)
    console.log(vNode)
    // 渲染到body中
    render(vNode, document.body)
    // 把显示和隐藏方法挂载到vue全局属性中
    app.config.globalProperties.loading = {
        show: vNode.component?.exposed?.show,
        hide: vNode.component?.exposed?.hide
    }
  }
}
```

安装插件

```ts
import Loading from './components/Loading'

const app = createApp(App)
// 安装插件
app.use(Loading)

// 解决找不到属性报错的问题
type Lod = {
  show: () => void,
  hide: () => void
}

declare module 'vue' {
  export interface ComponentCustomProperties {
    loading: Lod
  }
}
```

使用

```vue
<script lang="ts">
import { getCurrentInstance } from 'vue'

// 获取到vue实例
const instance = getCurrentInstance()

// 延迟隐藏
setTimeout(() => {
  instance?.proxy?.loading.hide()
}, 5000)

// 显示
instance?.proxy?.loading.show()
</script>
```

### ElementUI自动导入

准备工作

**npm install -D unplugin-auto-import** # 支持自动导入

```bash
npm install -D unplugin-icons # 自动导入Icon
npm install -D @iconify-json/ep # 安装图标库（https://icones.netlify.app/）
npm install -D unplugin-vue-components # 自动导入组件
```

tsconfig.json

```json
"include": ["env.d.ts", "src/**/*", "src/**/*.vue", "src/**/*.ts"]
"compilerOptions": {
   "types": ["vite/client", "element-plus/global", "unplugin-icons/types/vue"]
}
```

vite.config.ts

```ts
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

import Icons from 'unplugin-icons/vite'
import IconsResolver from 'unplugin-icons/resolver'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      // 需要自动导入的文件
      include: [/\.[tj]sx?$/, /\.vue$/],
      // 解析器，支持UI组件库自动导入
      resolvers: [
        ElementPlusResolver(),
        // 自动导入图标组件
        // IconsResolver({
        //   prefix: 'Icon'
        // })
      ],
      // 自动导入的文件名
      imports: ['vue'],
      // 指定生成.d.ts的位置
      dts: 'src/auto-imports.d.ts'
    }),
    Components({
      resolvers: [
        // 自动注册图标组件
        IconsResolver({
          prefix: '', // 默认前缀是i
          enabledCollections: ['ep'] // 选择图标库集合 ep
        }),
        ElementPlusResolver(),
      ],
      // 需要自动导入组件的目录
      dirs: ['src/components', 'src/views'],
      // 文件后缀
      extensions: ['vue'],
      // 指定.d.ts生成的位置
      dts: 'src/components.d.ts',
      // 深度扫描
      deep: true,
      // 需要自动导入的文件
      include: [/.vue$/, /.vue?vue/]
    }),
    Icons({
      autoInstall: true, // 自动安装
    })
  ],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  }
})
```

组件中使用

```vue
<EpApple />

<ep-add-location />

<EpRefresh />
```

无需进行手动导入

### ElementUI修改默认样式

覆盖ElementUI的默认样式

#### CSS快速覆盖

对应style使用css变量方式进行修改

#### **SCSS变量**

src新建/styles/variables.scss

```scss
@forward 'element-plus/theme-chalk/src/common/var.scss' with (
  $colors: (
    'primary': (
      'base': green,
    ),
    'success': (
      'base': #21ba45,
    ),
    'warning': (
      'base': #f2711c,
    ),
    'danger': (
      'base': #db2828,
    ),
    'error': (
      'base': #db2828,
    ),
    'info': (
      'base': #42b8dd,
    ),
  ),
  $button-padding-horizontal: (
    'default': 80px,
  )
);

// 如果是全局导入，添加ElementUI的样式
// sass推荐使用@use方式进行导入
// @use "element-plus/theme-chalk/src/index.scss" as *;
```

##### 全局导入（不推荐）

main.ts

```ts
import ElementPlus from 'element-plus'
// 已导入scss样式，无需全局导入ElementUI的css样式
// import 'element-plus/dist/index.css'
// 导入已覆盖的样式，进行样式覆盖
import './styles/variables.scss'

// 安装插件，全局导入
app.use(ElementPlus)
```

##### 按需导入（推荐）

修改vite.config.ts文件

```ts
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver({
        importStyle: "sass"
      })],
    }),
  ],
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@use "@/styles/variables.scss" as *;`
      }
    }
  },
})
```

###  CSS相关

#### scope样式穿透

1.给HTML的DOM节点加一个不重复data属性(形如：data-v-123)来表示他的唯一性
2.在每句css选择器的末尾（编译后的生成的css语句）加一个当前组件的data属性选择器（如[data-v-123]）来私有化样式
3.如果组件内部包含有其他组件，只会给其他组件的最外层标签加上当前组件的data属性

不适用:deep

```css
#hello .green {
  background-color: red;
}
```

转换之后

```css
#hello .green[data-v-7a7a37b1] {
  background-color: red;
}
```

------

使用:deep

``` css
#hello :deep(.green) {
  background-color: red;
}
```

转换之后

``` css
#hello[data-v-7a7a37b1] .green {
  background-color: red;
}
```

**使用之后会把data-v属性前移**

#### 插槽选择器

父组件

```vue
<script setup lang="ts">
import A from '@/components/A.vue'
</script>

<template>
  <A>
    <div class="a">我是充八万</div>
  </A>
</template>

<style scoped>

</style>
```

子组件A.vue

```vue
<template>
  <div>
  我是插槽
  <slot></slot>
</div>
</template>

<style scoped>
:slotted(.a) {
  color: red;
}
</style>
```

如果要在子组件中修改插槽中的内容，需要添加`:slotted(选择器)`

#### 全局选择器

```css
<style scoped>
:global(div) {
  color: pink;
}
</style>
```

global修饰的选择器将是全局生效的

#### 动态CSS

使用v-bind动态绑定css属性值

```vue
<script setup lang="ts">
import { ref } from 'vue'

const color = ref('pink')
</script>

<template>
  <div class="div">
    动态css
  </div>
</template>

<style scoped>
.div {
  color: v-bind(color);
}
</style>
```

通过对象形式动态修改

```vue
<script setup lang="ts">
import { ref } from 'vue'

const style = ref({
  color: 'blue'
})

setTimeout(() => {
  style.value.color = 'skyblue'
}, 2000)
</script>

<template>
  <div class="div">
    动态css
  </div>
</template>

<style scoped>
.div {
  color: v-bind('style.color');
}
</style>
```

module形式

```vue
<template>
  <div :class="[dy.div, dy.border]">
    动态css
  </div>
</template>

<style module="dy">
.div {
  color: red;
}

.border {
  border: 1px solid #ccc;
}
</style>
```

script中使用

```vue
<script setup lang="ts">
import { useCssModule } from 'vue'

const css = useCssModule('dy')
console.log(css)
</script>
```

### 集成TailWindCSS

安装

```bash
npm install -D tailwindcss postcss autoprefixer
```

生成配置文件

```bash
npx tailwindcss init -p
```

修改配置文件tailwind.config.js

```js
module.exports = {
  content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

src目录下创建index.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

在main.ts中引入

```ts
import './index.css'
```

使用

```vue
<template>
  <div class="w-screen h-screen bg-red-600 flex justify-center items-center text-8xl text-slate-200">
    hello tailwind
  </div>
</template>
```

重新运行项目即可生效

### nextTick

vue更新dom是异步的 数据更新是同步的
我们本次更新代码是同步代码
当我们操作dom的时候，发现数据读取的是上次的，就需要使用nextTick

具体实现

```vue
<script setup lang="ts">
import { nextTick, reactive, ref } from 'vue'

const list = reactive([
  {name: 'zhangsan', message: 'xxxxxxx'}
])

const box = ref<HTMLDivElement>()

const ipt = ref<string>('123')

// vue更新dom是异步的 数据更新是同步的
// 我们本次更新代码是同步代码
// 当我们操作dom的时候，发现数据读取的是上次的，就需要使用nextTick
const send = async () => {
  list.push({
    name: 'jdy2002',
    message: ipt.value
  })
  // 1.回调函数的模式
  // nextTick(() => {})
  // 2.async await
  await nextTick()
  box.value!.scrollTop = 99999
  // ipt.value = ''
}
</script>

<template>
  <div class="m-auto w-3/5">
    <h3 class="h-16 text-3xl flex items-center bg-amber-400">张三: xxxxxxxx</h3>
    <ul ref="box" class="border border-gray-400 my-2 p-1.5 h-60 overflow-y-scroll">
      <li v-for="(item, index) in list" :key="index">{{item.name}}: {{ item.message }}</li>
    </ul>
    <textarea class="w-full border border-gray-400" v-model="ipt"></textarea>
    <div class="w-full flex justify-end">
      <button @click="send" type="button" class="flex justify-end">send</button>
    </div>
  </div>
</template>
```

### 移动端Ionic

配置`jdk`和`android sdk`环境变量

全局安装 `iconic`

```bash
npm install -g @ionic/cli
```

创建项目

```bash
ionic start xxx tabs --type vue
```

运行项目

```bash
npm run dev
```

构建项目

```bash
npm run build
```

部署到安卓

```bash
ionic capacitor copy android
```

用Android studio打开android文件夹

### H5适配

把所有px单位转换为vw单位

```ts
// postcss的插件
import { Plugin } from 'postcss'

const Options= {
  viewportWidth: 375 // 默认视口宽度
}

interface Options {
  viewportWidth?: number
}

export const PostcssPxToViewport = (options: Options = Options): Plugin => {
  // 防止用户传入空对象
  const opt = Object.assign({}, Options, options)
  return {
    postcssPlugin: 'postcss-px-to-viewport',
    // 钩子函数
    Declaration(node: any) {
      if (node.value.includes('px')) {
        const num = parseFloat(node.value)
        // 将px转换为vw
        node.value = `${((num / opt.viewportWidth) * 100).toFixed(2)}vw`
      }
    }
  }
}
```

vite.config.ts

引入css插件

```ts
export default defineConfig({
  plugins: [
    vue()
  ],
  css: {
    postcss: {
      plugins: [PostcssPxToViewport()]
    }
  }
  ...
})
```

tsconfig.node.json

解决报错问题

```json
"include": "ts插件位置"
```

全局样式设置

```css
<style>
:root {
    --size: '14px';
}

xxx {
  font-size: var(--size);
}
<style>
```

使用VueUse

```ts
import { useCssVar } from "@vueuse/core";

const change = (num: number) => {
  const size = useCssVar('--size')
  size.value = num + 'px'
}
```

### unoCss原子化

```bash
npm install unocss
```

main.ts

```ts
import 'uno.css'
```

vite.config.ts

```ts
export default defineConfig({
  plugins: [
    vue(),
    unoCss({
      rules: [
        [
          'flex',
          {
            display: 'flex'
          }
        ],
        [
          'red',
          {
            color: 'red'
          }
        ],
        [
          /^m-(\d+)$/,
          ([, d]) => ({
            margin: `${Number(d) * 10}px`
          })
        ]
      ],
      // 快捷方式
      shortcuts: {
        fr: ['flex', 'red']
      }
    })
  ]
})
```

具体使用

```vue
<template>
  <div class="flex red m-10">
    div
  </div>
</template>
```

预设

```ts
import {presetIcons, presetAttributify, presetUno} from 'unocss'

export default defineConfig({
  plugins: [
    vue(),
    unoCss({
      presets: [presetIcons(), presetAttributify(), presetUno()],
    })
 ]
```

安装图标库

```bash
npm install -D @iconify-json/ic # ic为图标库名称 google material design
```

具体使用

```vue
<template>
  <!-- presetAttributify -->
  <div class="fr" m="10" flex red>
    div
  </div>
  <!-- presetIcons -->
  <div class="i-ic-baseline-access-alarms"></div>
  <!-- presetUno -->
  <div class="tailwind类名"></div>
</template>
```

### electron桌面程序

安装

```bash
npm install electron electron-builder -D
```

### 编译宏

```vue
<template>
  <div>child</div>
  <ul>
    <li v-for="(item, index) in data" :key="index">
      <slot :item="item" :index="index"></slot>
    </li>
  </ul>
  <!-- <button @click="sned">派发</button> -->
</template>

<script setup lang="ts" generic="T">
// defineSlots只有声明没有参数，没有任何参数，只能接收ts的类型
defineSlots<{
  default(props: {item: T, index: number}): void
}>()

defineProps<{
  data: T[]
}>()
/* const props = defineProps({
  name: {
    type: Array as PropType<string[]>,
    required: true,
  },
}) */
/* const props = defineProps<{
  name: string[]
}>()
console.log(props) */

// vue3.3对defineProps的改进，新增了对泛型支持
/*defineProps<{
  name: T[]
}>()*/

// const emit = defineEmits(['change'])
/* const emit = defineEmits<{
  (e: "change", value: string): void
}>() */
// vue3.3改进
/* const emit = defineEmits<{
  'change': [name: string]
}>()

const send = () => {
  emit("change", "hello")
} */
// vue3.3内置了defineOptions 不需要下载插件
// 他里面属性跟optionsAPI一模一样的
defineOptions({
  name: 'dongyu',
  
})
</script>

<style scoped></style>
```

### 环境变量

**项目开发过程中，至少会经历开发环境、测试环境和生产环境(即正式环境)三个阶段。不同阶段请求的状态(如接口地址等)不尽相同，若手动切换接口地址是相当繁琐且易出错的。于是环境变量配置的需求就应运而生，我们只需做简单的配置，把环境状态切换的工作交给代码。**

开发环境（development）
顾名思义，开发使用的环境，每位开发人员在自己的dev分支上干活，开发到一定程度，同事会合并代码，进行联调。

测试环境（testing）
测试同事干活的环境啦，一般会由测试同事自己来部署，然后在此环境进行测试

生产环境（production）
生产环境是指正式提供对外服务的，一般会关掉错误报告，打开错误日志。(正式提供给客户使用的环境。)

注意:一般情况下，一个环境对应一台服务器,也有的公司开发与测试环境是一台服务器！！！

项目根目录分别添加 开发、生产和测试环境的文件!

```
.env.development
.env.production
.env.test
```

.env.development 创建开发环境变量 

```bash
# 变量必须以 VITE_ 为前缀才能暴露给外部读取
VITE_HTTP = http://www.baidu.com
```

.env.production 创建生产环境变量 

```bash
VITE_HTTP = http://www.jd.com
```

.env.test

```bash
VITE_HTTP = http://www.test.com
```

组件中读取

```ts
console.log(import.meta.env)
```

配置运行命令：package.json

```json
 "scripts": {
    "dev": "vite --open",
    "build:test": "vue-tsc && vite build --mode test",
    "build:pro": "vue-tsc && vite build --mode production",
    "preview": "vite preview"
  },
```

在配置文件中获取

vite.config.ts

```ts
import { defineConfig, loadEnv } from 'vite'

export default ({mode}: any) => {
  console.log(loadEnv(mode, process.cwd()))
  return defineConfig({...})
}
```

### Webpack构建Vue

1.创建一个空项目

2.创建目录public, src

3.执行命令

```bash
npm init -y # 初始化包管理器
tsc --init # 初始化typescript
```

4.在src创建assets，views，App.vue，main.ts

5.在public创建index.html

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport"
        content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>webpack demo</title>
</head>
<body>
<div id="app"></div>
</body>
</html>
```

6.新建webpack.config.js配置文件

7.安装webpack、webpack-cli、webpack-dev-server、html-webpack-plugin

```bash
npm install webpack webpack-cli webpack-dev-server html-webpack-plugin
```

8.配置webpack.config.js

```js
const { Configuration } = require('webpack')
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

/**
 *
 * @type {Configuration} 支持类型提示
 */
const config = {
  mode: "development", // 开发模式
  entry: './src/main.ts', // 入口文件位置
  output: { // 配置输出文件
    filename: "[hash].js", // 打包后的文件名
    path: path.resolve(__dirname, 'dist') // 打包后的文件生成路径
  },
  resolve: {
    // 别名
    alias: {
      // @表示src的路径
      '@': path.resolve(__dirname, 'src')
    },
    // 导入时自动补全后缀
    extensions: ['.vue', '.ts', '.js']
  },
  plugins: [ // 打包插件 生成template模板
    new HtmlWebpackPlugin({
      template: "./public/index.html" // 模板位置
    })
  ]
}

module.exports = config;
```

9.添加命令

package.json

```json
"scripts": {
    "dev": "webpack-dev-server",
    "build": "webpack"
  },
```

10.安装vue

```bash
npm install vue
```

11.解决引入vue组件报错问题

src新建`env.d.ts`

```ts
declare module '*.vue' {
  import { DefineComponent } from 'vue'
  const component: DefineComponent<{}, {}, any>
  export default component
}
```

12.配置入口文件**main.ts**

```ts
import {createApp} from "vue";
import App from "./App.vue";

createApp(App).mount('#app')
```

13.配置loader加载器

安装

```bash
npm install vue-loader@next @vue/compiler-sfc
```

webpack.config.js

```js
module: {
  // loader配置
  rules: [
    {
      test: /\.vue$/,
      use: 'vue-loader'
    }
  ]
},
plugins: [
  ...
  new VueLoaderPlugin()
]
```

支持加载css、less

```bash
npm install css-loader style-loader less less-loader
```

```js
rules: [
    {
        // 支持加载 css文件
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
    },
    {
        test: /\.less$/,
        use: ['style-loader', 'css-loader', 'less-loader']
    }
]
```

14.引入css|less文件

```vue
<template>
  <div>
    App123
  </div>
</template>

<script setup>
import '@/assets/index.css' // css
// import '@/assets/index.less' less
</script>

<style scoped>

</style>
```

15.typescript支持

```bash
npm install typescript ts-loader
```

```js
rules: [
    {
        test: /\.ts$/,
        loader: "ts-loader",
        options: {
          configFile: path.resolve(process.cwd(), "tsconfig.json"),
          appendTsSuffixTo: [/\.vue$/]
        }
    }
]
```

```vue
<script setup lang="ts">
import '@/assets/index.less'
import {ref} from "vue";

const name = ref<string>('冬雨')
</script>
```

16.优化打包日志

```bash
npm install friendly-errors-webpack-plugin
```

webpack.config.js

```js
stats: "errors-only",
plugins: [
   new FriendlyErrorsWebpackPlugin({
    	compilationSuccessInfo: { // 编译成功后打印的内容
       		messages: ['You application is running here: http://localhost:8080']
    	}
	}) 
],
externals: {
   vue: 'Vue' // 不将vue打包进js，使用在线地址引入
}
```

### 性能优化

#### 安装分析插件

```bash
npm install rollup-plugin-visualizer
```

使用

```ts
plugins: [visualizer({open: true})]
```

打包配置优化

```ts
build: {
    // 打包警告大小
    chunkSizeWarningLimit: 2000,
    // css文件拆分
    cssCodeSplit: true,
    // 关闭sourcemap
    sourcemap: false,
    // esbuild打包速度快，terser打包体积小
    minify: 'terser',
    // 图片转base64限制大小
    assetsInlineLimit: 4000
}
```

#### pwa

```bash
npm install vite-plugin-pwa
```

```ts
plugins: [
    VitePWA({
      workbox:{
        cacheId:"XIaoman",//缓存名称
        runtimeCaching:[
          {
            urlPattern:/.*\.js.*/, //缓存文件
            handler:"StaleWhileRevalidate", //重新验证时失效
            options:{
              cacheName:"XiaoMan-js", //缓存js，名称
              expiration:{
                maxEntries:30, //缓存文件数量 LRU算法
                maxAgeSeconds:30 * 24 * 60 * 60 //缓存有效期
              }
            }
          }
        ]
      },
    })
]
```

#### 图片懒加载

```bash
npm install vue3-lazy
```

```ts
plugins: [
    lazyPlugin()
]
```

```vue
<img v-lazy="user avatar">
```

js实现懒加载

```ts
 // intersectionObserver 交叉观察 ： 目标元素和可视窗口会产生交叉区域
  const imagess = [...document.querySelectorAll('img')]

  // 2.1 创建视觉交叉的观察实例
  const observer = new IntersectionObserver(callback)
  // 2.2 给每一个图片绑定观察方法
  imagess.forEach(img => {
    // 2.3 图片进入视野+离开视野时触发 - 回调
    observer.observe(img)
  })

  // callback 接收的参数为带有监听所有图片交叉属性的集合
  const callback = (imgArr) => {
    console.log('视图交叉时触发，离开交叉时也触发', imgArr) // imgArr为
    imgArr.forEach(e => {
      // 判断是否在视野区域
      if (e.isIntersecting) {
        e.target.src = e.target.dataset.src
        // 取消观察追踪，避免重复加载同一张图片
        observer.unobserve(e.target)
      }
    })
  }
```

vue实现懒加载

```ts
// 导入默认图片
import defaultImg from '@/assets/images/01.png'
// 引入监听是否进入视口
import { useIntersectionObserver } from '@vueuse/core'
export default {
  // 需要拿到 main.js 中由 createApp 方法产出的 app 实例对象
  install (app) {
    // app 实例身上有我们想要的全局注册指令方法  调用即可
    app.directive('lazyImg', {
      mounted (el, binding) {
        // el:img dom对象
        // binding.value  图片url地址
        // 使用 vueuse/core 提供的监听 api 对图片 dom 进行监听 正式进入视口才加载
        // img.src = url
        console.log(el, binding)
        const { stop } = useIntersectionObserver(
          // 监听目标元素
          el,
          ([{ isIntersecting }], observerElement) => {
            if (isIntersecting) {
              // ◆图片加载失败显示默认图片
              el.onerror = function () {
                el.src = defaultImg
              }
              // ◆这里显示传过来的图片数据
              el.src = binding.value
              stop()// 中止监听
            }
          })
       }
    })
  }
}
```

### web Components

Web Components 提供了基于原生支持的、对视图层的封装能力，可以让单个组件相关的 javaScript、css、html模板运行在以html标签为界限的局部环境中，不会影响到全局，组件间也不会相互影响 。 再简单来说：就是提供了我们自定义标签的能力，并且提供了标签内完整的生命周期 。

用法

```js
class Btn extends HTMLElement {
  constructor() {
    super();

    const shaDow = this.attachShadow({mode: "open"})

    /*this.p = this.h('p')
    this.p.innerText = 'dongyu'
    this.p.setAttribute('style', 'width: 200px; height: 200px; border: 1px solid red;')*/

    this.template = this.h('template')
    this.template.innerHTML = `
      <style>
        div {
          width: 200px;
          height: 200px;
          border: 1px solid red;
        }
      </style>
      <div>dongyu</div>
    `
    // shaDow.appendChild(this.p)
    shaDow.appendChild(this.template.content.cloneNode(true))
  }

  /**
   * 生命周期
   */
  //当自定义元素第一次被连接到文档 DOM 时被调用。
  connectedCallback() {
    console.log('我已经插入了！！！嗷呜')
  }

  //当自定义元素与文档 DOM 断开连接时被调用。
  disconnectedCallback() {
    console.log('我已经断开了！！！嗷呜')
  }

  //当自定义元素被移动到新文档时被调用
  adoptedCallback() {
    console.log('我被移动了！！！嗷呜')
  }
  //当自定义元素的一个属性被增加、移除或更改时被调用
  attributeChangedCallback() {
    console.log('我被改变了！！！嗷呜')
  }

  h(el) {
    return document.createElement(el)
  }
}

window.customElements.define('dongyu-btn', Btn);
```

网页中引入

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
	<script src="./btn.js"></script>
</head>
<body>
    <dongyu-btn></dongyu-btn>
    <dongyu-btn></dongyu-btn>
    <dongyu-btn></dongyu-btn>
    <dongyu-btn></dongyu-btn>
</body>
</html>
```

在vue中使用

```ts
plugins: [
    vue({
      template: {
        compilerOptions: {
          isCustomElement: (tag) => tag.startsWith('dongyu-')
        }
      }
    })
]
```

新建组件custom-vue.ce.vue（必须.ce.vue结尾）

```vue
<script setup lang="ts">
defineProps<{
  obj: any
}>()
</script>

<template>
  <div>dongyu</div>
  <div>{{JSON.parse(obj)}}</div>
</template>

<style scoped>

</style>
```

在App.vue中使用

```vue
<script setup lang="ts">
import CustomVue from './components/custom-vue.ce.vue'
import {defineCustomElement} from "vue";
	
const Btn = defineCustomElement(CustomVue)

window.customElements.define('dongyu-btn', Btn)

const obj = {name: 'dongyu'}
</script>

<template>
  <dongyu-btn :obj="JSON.stringify(obj)"></dongyu-btn>
</template>
```

### Proxy(代理)

解决请求跨域的问题

vite.config.ts

```ts
server: {
    // 只适合本地开发，生产环境无效
    proxy: {
      // 将代理 /api这个请求地址
      '/api': {
        target: "http://localhost:9999", // 目标服务器请求地址
        changeOrigin: true, // 是否有跨域
        // 实际请求地址没有/api这个路径，所以将/api替换为空
        rewrite: (path) => path.replace(/^\/api/, ''),
      }
    }
  }
```

### 可视化

Api接口

1.安装环境

```bash
npm install ts-node -g # node中使用ts需要(第一次使用全局安装)
npm init -y # 初始化包管理工具
npm install @types/node -D # 添加node类型支持
npm install express -S # 安装express
npm install @types/express -D # 安装express 类型支持
npm install axios -S # 安装axios请求工具
```

代码地址 [github](https://github.com/Yu2002s/COVID-19)

### Router(路由)

安装依赖

```bash
npm install vue-router
```

使用示例

/router/index.js

```ts
import { createRouter, createWebHistory } from 'vue-router'
import Home from '@/views/Home.vue'
import About from '@/views/About.vue'
import User from '@/views/User.vue'
import NotFound from '@/components/NotFound.vue'
import UserProfile from '@/views/UserProfile.vue'
import UserPosts from '@/views/UserPosts.vue'
import UserHome from '@/views/UserHome.vue'
import Register from '@/views/Register.vue'
import LeftSlide from '@/views/LeftSlide.vue'
import RightSlide from '@/views/RightSlide.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      components: {
        default: Home,
        LeftSlide,
        RightSlide
      },
      // 独享守卫
      // 只有在进入时触发，不会在params、query、 hash改变是触发
      //只有在从一个不同的路由导航时才会被触发
      beforeEnter: (to, from) => {
        // console.log('enter')
        // return false
      }
    },
    {
      path: '/about/:id',
      name: 'about',
      // route level code-splitting
      // this generates a separate chunk (About.[hash].js) for this route
      // which is lazy-loaded when the route is visited.
      component: About,
      // 定义元数据
      meta: {
        // 只有身份验证成功后才能进入
        requiresAuth: true
      }
    },
    {
      name: 'user-parent',
      path: '/users/:id',
      component: User,
      sensitive: false,
      children: [
        {
          path: '',
          name: 'user-home',
          component: UserHome
        },
        {
          name: 'user-profile',
          path: 'profile',
          component: UserProfile
        },
        {
          name: 'user-posts',
          path: 'posts',
          component: UserPosts
        }
      ]
    },
    {
      name: 'NotFound',
      // 匹配所有的地址，如果前面规则为匹配，则展示notfound
      path: '/:pathMatch(.*)*',
      component: NotFound
    },
    {
      path: '/user-:afterUser(.*)',
      component: User
    },
    {
      // :orderId 仅匹配数字
      // path: '/:orderId(\\d+)',
      // 匹配 /one, /one/two, /one/two/three
      // path: '/:chapters+',
      // 匹配 /, /one, /one/two
      name: 'chapters',
      path: '/:chapters*',
      // 匹配 /1, /1/2
      // path: '/:chapters(\\d+)+',
      // 匹配 /, /1, /1/2
      // path: '/:chapters(\\d+)*',
      // 匹配 /users/, /users/jdy
      // path: '/users/:userId?',
      // 匹配 /users, /users/12
      // path: '/users/:userId(\\d+)?',
      component: NotFound
    },
    {
      name: 'register',
      path: '/register',
      component: Register
    },
    {
      name: 'search',
      path: '/search/:searchText',
      redirect: (to) => {
        return {
          path: '/users',
          name: 'user-home',
          params: {
            id: to.params.searchText
          }
        }
      }
    },
    {
      // /users/jdy/posts 重定向到 /users/jdy/profile
      path: '/users/:id/posts',
      redirect: (to) => {
        return 'profile'
      }
      // 设置别名, 访问 /jdy 将展示
      // alias: ['/jdy', 'jdy2']
    }
  ],
  // 全局规则
  strict: true, // 末尾不能出现/
  sensitive: true, // 区分大小写
  scrollBehavior(to, from, savedPosition) {
    if (to.hash) {
      return {
        el: '#root',
        top: -100,
       // hash: to.hash
      }
    }
    return savedPosition
  }
})

router.onError(() => {
  console.log('error')
})

/*router.beforeEach(async (to, from, next) => {
  console.log('beforeEach: ', to.path)
  // 访问到定义的元数据，判断是否需要进行身份验证
  console.log(to.meta.requiresAuth)
  if (i >= 3) {
    i = 0
    // console.log('about')
    // 进行重定向跳转到指定页面
    // return {name: 'about', params: {id: 'jdy'}}
    // 也可以使用next方法进行跳转
    // return '/about/jdy'
    next('/about/jdy')
  } else {
    i++
    next()
  }
  // return 可以取消导航
  // return false
})*/

/*router.beforeResolve((to) => {
  if (i >= 3) {
    i = 0
    return false
  } else {
    i++
  }

  console.log('beforeResolve: ', to.path)
})*/

/*router.afterEach((to, from, failure) => {
  console.log('afterEach: ', to.fullPath)
})*/

export default router
```

main.ts

```tsx
import router from './router/index.js'

app.use(router)
```

App.vue

```vue
<script setup lang="ts"></script>

<template>
  <div id="app-container">
    <RouterLink to="/">Home</RouterLink>
    <RouterLink to="/about/id">About</RouterLink>
    <RouterLink to="/users/john">John</RouterLink>
    <RouterLink to="/users/dongyu">Dongyu</RouterLink>
    <RouterLink to="/users/john/profile">Profile</RouterLink>
    <RouterLink to="/users/dongyu/posts">Posts</RouterLink>
    <RouterLink to="/a">测试</RouterLink>
    <RouterLink to="/user-a">前缀匹配</RouterLink>
    <div id="main">
      <RouterView name="LeftSlide" />
      <RouterView v-slot="{ Component, route }">
        <Transition name="fade">
          <component :is="Component" :key="route.path" />
        </Transition>
      </RouterView>
      <RouterView name="RightSlide" />
    </div>
  </div>
</template>

<style scoped lang="less">
#app-container {
  height: 100%;
}

a {
  color: black;
  text-decoration: none;
  padding: 6px;

  &:hover {
    color: skyblue;
  }
}

#main {
  display: flex;
  height: 100%;

  & > div {
    flex: 1;
    height: 100%;
  }
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateX(100px);
}

.fade-enter-active,
.fade-leave-active {
  transform: translateX(100px);
  transition: all 0.8s ease;
}
</style>
```

### pm2(node进程管理)

全局安装依赖

```bash
npm install pm2 -g
```

使用方法

```bash
pm2 start index.js [--watch] -n 指定名称 # 启动进程
pm2 log # 查看日志
pm2 list #查看进程列表
pm2 stop 0 # 根据id停止进程
pm2 restart 0 # 重启进程
pm2 delete 0 # 删除进程
pm2 monit # 进入管理页面
```

### Mock数据

安装依赖:https://www.npmjs.com/package/vite-plugin-mock

```bash
pnpm install -D vite-plugin-mock mockjs
```

在 vite.config.js 配置文件启用插件。

```ts
import { UserConfigExport, ConfigEnv } from 'vite'
import { viteMockServe } from 'vite-plugin-mock'
import vue from '@vitejs/plugin-vue'

export default ({ command }: ConfigEnv): UserConfigExport => {
  return {
    plugins: [
      vue(),
      viteMockServe({
        // default
        mockPath: 'mock',
        enable: command === 'serve',
      }),
    ],
  }
}

/**
* {
    mockPath?: string;
    ignore?: RegExp | ((fileName: string) => boolean);
    watchFiles?: boolean;
    enable?: boolean;
    ignoreFiles?: string[];
    configPath?: string;
}
*/
```

**解决项目报错问题**

在node_modules/vite-plugin-mock/dist/index.mjs这个文件中做如下配置：

```ts
import { createRequire } from 'module';
const require = createRequire(import.meta.url);
```

在根目录创建mock文件夹:去创建我们需要mock数据与接口！！！

在mock文件夹内部创建一个user.ts文件

```ts
//用户信息数据
function createUserList() {
    return [
        {
            userId: 1,
            avatar:
                'https://wpimg.wallstcn.com/f778738c-e4f8-4870-b634-56703b4acafe.gif',
            username: 'admin',
            password: '111111',
            desc: '平台管理员',
            roles: ['平台管理员'],
            buttons: ['cuser.detail'],
            routes: ['home'],
            token: 'Admin Token',
        },
        {
            userId: 2,
            avatar:
                'https://wpimg.wallstcn.com/f778738c-e4f8-4870-b634-56703b4acafe.gif',
            username: 'system',
            password: '111111',
            desc: '系统管理员',
            roles: ['系统管理员'],
            buttons: ['cuser.detail', 'cuser.user'],
            routes: ['home'],
            token: 'System Token',
        },
    ]
}

export default [
    // 用户登录接口
    {
        url: '/api/user/login',//请求地址
        method: 'post',//请求方式
        response: ({ body }) => {
            //获取请求体携带过来的用户名与密码
            const { username, password } = body;
            //调用获取用户信息函数,用于判断是否有此用户
            const checkUser = createUserList().find(
                (item) => item.username === username && item.password === password,
            )
            //没有用户返回失败信息
            if (!checkUser) {
                return { code: 201, data: { message: '账号或者密码不正确' } }
            }
            //如果有返回成功信息
            const { token } = checkUser
            return { code: 200, data: { token } }
        },
    },
    // 获取用户信息
    {
        url: '/api/user/info',
        method: 'get',
        response: (request) => {
            //获取请求头携带token
            const token = request.headers.token;
            //查看用户信息是否包含有次token用户
            const checkUser = createUserList().find((item) => item.token === token)
            //没有返回失败的信息
            if (!checkUser) {
                return { code: 201, data: { message: '获取用户信息失败' } }
            }
            //如果有返回成功信息
            return { code: 200, data: {checkUser} }
        },
    },
]
```

user.ts 模拟例子

```ts
import Mock from 'mockjs'
import type { MockMethod } from 'vite-plugin-mock'

const userList = Mock.mock({
  "data|6": [
    {
      "id|+1": 1,
      username: "@cname",
      password: /.{6,10}/,
      'avatar': "@image('100x100', '#000', '#ffffff', 'Hello')",
      'mobile': /^1[3456789]\d{9}/,
      'a|1-10': '@cname',
    }
  ]
})

export default [
  {
    url: '/api/login',
    method: 'post',
    response: ({ body }) => {
      const { username, password } = body
      return {  
        code: 200,
        username,
        password,
        data: userList,
      }
    },
  },
  {
    url: '/api/user',
    method: 'get',
    response: ({ query }) => {
      return {
        code: 200,
        data: {
          username: query.username
        }
      }
    }
  },
  {
    url: '/api/user/list',
    method: 'get',
    timeout: 2000,
    response: {
      code: 200,
      data: userList
    }
  },
  {
    url: '/api/sign',
    method: 'post',
    rawResponse: async (req, res) => {
      let reqBody = ''
      await new Promise((resolve) => {
        req.on('data', (chunk) => {
          reqBody = chunk
        })
        req.on('end', () => resolve(undefined))
      })
      res.setHeader('Context-Type', "text/plain")
      res.statusCode = 200
      res.end(`hello, ${reqBody}`)
    }
  }
] as MockMethod[]
```

### axios二次封装

在开发项目的时候避免不了与后端进行交互,因此我们需要使用axios插件实现发送网络请求。在开发项目的时候

我们经常会把axios进行二次封装。

目的:

1:使用请求拦截器，可以在请求拦截器中处理一些业务(开始进度条、请求头携带公共参数)

2:使用响应拦截器，可以在响应拦截器中处理一些业务(进度条结束、简化服务器返回的数据、处理http网络错误)

在根目录下创建utils/request.ts

```ts
import axios from "axios";
import { ElMessage } from "element-plus";
//创建axios实例
let request = axios.create({
    baseURL: import.meta.env.VITE_APP_BASE_API,
    timeout: 5000
})
//请求拦截器
request.interceptors.request.use(config => {
    return config;
});
//响应拦截器
request.interceptors.response.use((response) => {
    return response.data;
}, (error) => {
    //处理网络错误
    let msg = '';
    let status = error.response.status;
    switch (status) {
        case 401:
            msg = "token过期";
            break;
        case 403:
            msg = '无权访问';
            break;
        case 404:
            msg = "请求地址错误";
            break;
        case 500:
            msg = "服务器出现问题";
            break;
        default:
            msg = "无网络";

    }
    ElMessage({
        type: 'error',
        message: msg
    })
    return Promise.reject(error);
});
export default request;
```

### API接口统一管理

在开发项目的时候,接口可能很多需要统一管理。在src目录下去创建api文件夹去统一管理项目的接口；

/api/user/index.ts

```ts
// 统一管理项目用户相关的接口
import request from "@/utils/request"
import type { LoginFrom, LoginResponseData, UserResponseData } from "./types"
// 统一管理接口

enum API {
    LOGIN_URL = '/user/login',
    USERINFO_URL = '/user/info',
}

// 对外暴露请求函数
export const login = (data: LoginFrom) => request.post<LoginResponseData>(API.LOGIN_URL, data)
// 暴露获取用户信息函数
export const getUserInfo = () => request.get<UserResponseData>(API.USERINFO_URL)
```

定义类型 /api/user/types.ts

```ts
// 登录接口需要携带参数的ts类型

export interface LoginFrom {
    username: string
    password: string
}

interface DataType {
    token: string
}

// 登录接口返回的数据类型

export interface LoginResponseData {
    code: number,
    data: DataType
}

// 定义服务器返回用户信息相关的数据类型

interface UserInfo {
    userId: number,
    avatar: string,
    username: string,
    password: string,
    desc: string,
    rules: string[],
    buttons: string[],
    routes: string[],
    token: string
}

interface User {
    checkUser: UserInfo
}

export interface UserResponseData {
    code: number,
    data: User
}
```

### Express

全局安装express-generator

```bash
npm install express-generator

express --view=ejs server
```

访问 localhost:3000
