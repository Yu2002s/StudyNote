### 构建项目	

vite

```bash
npm init vite@latest
```

vue

```bash
npm init vue@latest
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

