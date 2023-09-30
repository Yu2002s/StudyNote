### 构建项目	

vite

```bash
npm init vite@latest
```

vue

```bash
npm init vue@latest
```

npx

```bash
npx create-vue demo
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
const props = defineOptions({
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

### 状态管理

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
</script>

<template>
  <div>{{ counter.count }}
    {{ counter.items }}
    <button @click="counter.count ++">+1</button>
  </div>
</template>
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

.env.development 创建开发环境变量 

```tex
VITE_HTTP = http://www.baidu.com
```

.env.production 创建生产环境变量 

```tex
VITE_HTTP = http://www.jd.com
```

组件中读取

```ts
console.log(import.meta.env)
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
]
```

