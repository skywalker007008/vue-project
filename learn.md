# Vue Learning

## Vue Commands

## Ref

用const标记的内容无法响应式变化，必须加上ref()
```javascript
const var = ref({
    name: "skywalker"
})
```

也可以自定义类型

```typescript
import type {Ref} from 'vue'

type T = {
    name:string
}

const var: ref<T> = ref({
    name: "skywalker"
})
```

修改ref的值，必须要使用.value
```typescript
var.value.name = "Luke"
```

**isRef**: 判断是否是ref对象，用isRef(obj)判断

**shallowRef**: 浅层次的ref对象

+ 必须针对.value整体复制，才可以改动显示的值。
+ Ref和shallowRef不能一块写吗，会影响shallowRef的更新

**triggerRef**: 强制更新shallowRef的更新

**customRef**: 自定义ref响应式函数

```typescript
    function myref<T> (value:T) {
        return customRef((track, trigger) => {
            return {
                get() {
                    track() // 自定义追踪变量的过程
                    return value
                }
                set(newValue) {
                    // 可以实现一些自定义功能，比如防抖操作
                    value = newValue
                    trigger() // 触发依赖的操作
                }
            }
        }) 
    }
```

**ref+dom**: ref可以获取dom的信息。

```typescript
 <div ref='dom'>This is a dom</div>

 const dom = ref<HTMLDivElement>()
 console.log(dom.value?.innerText)
```

## Reactive

+ **reactive只能支持Object类型，比如Array，Map，Set等**
+ **reactive**也接受范式<>
+ **reactive**不需要.value
+ reactive是proxy代理对象，不能直接赋值，否则会破坏响应对象，除非本身的reactive内容是一个object，其他内容作为一个属性

```typescript

    let list = reactive([])
    const add = () => {
        let res = ['a','b']
        list = res //这样无法响应式赋值 XXX
        list.push(...res) // 这样是正确的写法，对于list用push进行操作
    }
```

+ readonly: 对reactive对象进行只读操作。
+ shallowReactive: 浅层响应式

## toRef etc.

+ toRef: 修改响应式对象值，非响应式对象试图无变化。一般可用于传参时，从响应式对象中提取索引信息。

```typescript
//const man = {name: "skywalker", like:"naruto"} // 无法修改
const man = reactive({name: "skywalker", like:"naruto"}) //可以修改视图

const like = toRef(man, 'like')

const change = () => {
    //man.like = 'bleach' // reactive的修改方法
    like.value = 'bleach'
}
```

+ toRefs: 将对象的每个属性都转换为对应的ref对象
+ toRaw: 解除对象的响应式

## Computed

+ 对某个属性的值进行及时更新。

```typescript
<template>
<div>
    <input v-model = "firstName" type="text">
    <input v-model = "lastName" type="text">
    <div>{{fullName}}</div>
</div>

</template>

<script>
    let firstName = ref("Luke")
    let lastName = ref("Skywalker")
</script>

```
- 选项式写法：支持一个对象传入get函数以及set函数自定义操作 (vue2)

```typescript
    let fullName = computed({
        get() {
            return firstName.value + "-" + lastName.value
        },
        set(newValue) {
            [firstName.value, lastName.value] = newValue.split("-")
        }
    })
```

+ 函数式写法:实现getter，但是不允许修改值

```typescript
    let fullName = computed(()=>firstName.value + "-" + lastName.value)
```

## Watch

+ 监听响应式对象变化，从而触发某些效果

```typescript
<div>
    <input v-model="message" type = "text">
</div>

let message = ref<string>("skywalker")
let message1 = ref<string>("skywalker2")
```

+ 写法：watch(obj, callback)，即回调函数
```typescript
watch(message, (newValue, oldValue)=> { // 一个新值，一个旧值
    console.log(newValue, oldValue);    
})
//obj可以为某个值，也可以是某个响应对象，也可以是对应数组，这样的话newValue和oldValue也是对应的数组
watch([message,message2], (newValue, oldValue)=> { // 一个新值，一个旧值
    console.log(newValue, oldValue);    
})
```
+ 深度监听：如果obj是一个复杂对象而不是普通的值，若希望监听某些具体值变化，需要启动深度监听。
```typescript
let message = ref({
    foo: {
        bar: {
            name: "skywalker"
        }
    }
})

watch(message, (newValue, oldValue)=> { // 一个新值，一个旧值
    console.log(newValue, oldValue);    
}, {
    deep: true // 添加了一个深度监听设置。如果是reactive则不需要这个配置
})
```

+ 如果监听某一个值的变化，需要做一个触发函数。
```typescript
watch(() => message.foo.bar.name, (newValue, oldValue)=> { // 一个新值，一个旧值
    console.log(newValue, oldValue);    
})
```

+ 其他配置项
```typescript
watch(obj,callback,{
    deep: false, //深度监听
    immediate: false, //触发函数先不触发，如果设置true，会在监听到后立即执行一次
    flush: 'pre', //组件更新前调用watch，若为sync是同步执行, post是组件更新后执行

})
```

+ watchEffect: 非惰性的，不需要指定变了什么，只要响应函数里有东西变了，就会调用。








