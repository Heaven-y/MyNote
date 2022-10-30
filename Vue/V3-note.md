创建项目

* 方式一：Vue CLI：vue create app-name

* 方式二：npm init vue@latest
  * 1.安装一个本地工具：create-vue
  * 2.使用create-vue创建一个vue项目



template -> createVNode -> VNode -> VDOM

* runtime-only
  * 代码中不可以有任何template
  * .vue文件中的template已经由vue-template-compiler渲染成render函数了
    * **webpack中的vue-loader已经完成了template -> createVNode 过程**
* runtime-compiler
  * 由Vue中源码的代码进行处理template



props{validator}

非props的attribute

* 没有定义对应的prop或者emits时，常见的class，id，style
* 当组件有单个根节点，**该属性会默认添加到子组件的根组件上**
* 禁用：inheritAttrs: false
  * 访问：$attrs.xxx
* 组件有多个根节点，手动指定 v-bind="$attrs"



作用域插槽

父组件通过插槽拿到传入的值

```vue
<!-- TabControl.vue -->
<template>
	<slot :ti="title">
    <span>{{ title }}</span>
  </slot>
</template>
<!-- App.vue -->
<template>
	<tab-controle :titles=["衣服", "裤子", "鞋子"]>
    <template #default="props">
      <button>{{ props.ti }}</button>
		</template>
  </tab-controle>
</template>
```





组件的v-model

两者是等价的

```vue
<!-- App.vue -->
<template>
	<my-cpn v-model="appCounter" />
	<my-cpn :modelValue="appCounter" @update:modelValue="appCounter = $event"></my-cpn>
	<!-- 起别名
	<my-cpn v-model:counter="appCounter"></my-cpn>
	-->
</template>
```



* reactive定义复杂类型的数据
  * 必须传入一个数组或者对象类型

* ref定义简单类型的数据(也可以定义复杂)
  * 在template中使用ref，vue会自动解包





### nexttick

  将回调推迟到下一个DOM更新周期之后执行。在更改了一些数据以等待DOM更新后立即使用它
    需求：点击一个按钮后，会修改在h2中的message
    message被修改后，获取h2的最新高度
      1.updated周期中获取（其他数据更新也会执行操作）
      2.nextTick(callback)

  原理：将callback加入到微任务队列的最后(等到watch、组件的更新，生命周期等微任务执行完后再执行)

  好处:
  ```js
    const increment = () => {
      for(let i = 0; i < 100; i++) {
        counter.value++
      }
    }
  ```
  假如有watch(counter,() => {})...时 监听的函数并不会执行一百次。真正的更新并不是同步更新，而是加入微任务队列中。当先把这次的宏任务做完时，再去执行微任务

