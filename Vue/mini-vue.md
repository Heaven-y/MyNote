Compiler模块：编译模板系统
Runtime模块：Renderer模块，真正渲染的模块
Reactivity模块：响应式系统



真实DOM不利于操作（其具有很多的属性），不利于diff算法
真实DOM容易引起浏览器回流
vnode(js对象)操作更加容易、diff对比效率更高操作
方便实现跨平台



Mini-Vue渲染系统模块：runtime->vnode->真实dom h函数：返回一个VNode对象 mount函数，将VNode挂载到DOM patch函数，用于对两个VNode进行对比，决定如何处理新的VNode可响应式系统模块应用程序入口模块：createApp().mount()



Vue3选择Proxy

Object.defineProperty是劫持对象的属性，如果新增元素 

Vue2需要再次调用defineProperty，而Proxy劫持的是整个对象，不需要做特殊处理

修改对象的不同
使用defineProperty时，修改原来的obj对象触发，而用Proxy则必须修改代理对象才能触发

proxy能观察的类型更丰富，has，delete等等

浏览器厂商更注重proxy的性能优化





组件的VNode和组件的instance有什么区别
VNode是构建虚拟DOM中的虚拟节点组件的
instance是用来保存组件的各种状态data/setup/computed/methods



源码：先观察package.json中的"scripts"
	// --sourcemap生成映射文件，方便调试