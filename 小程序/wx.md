vscode开发小程序
打开项目安装插件 wxml-language service（、小程序开发助手、wechat-snippet）



引擎 JSCore（webkit）
双线程模型--消耗更多的内存
开启两个Webview
渲染层（Webview）wxml+wxss
逻辑层（JsCore）js+json
beta版的Skyline，不准备使用Webview了



只有vue实现了数据劫持，在小程序和react中在事件方法里直接修改数据时不会自动更新页面



image元素默认宽高320*240



wxss中的rpx(responsive pixel)：可以根据屏幕宽度进行自适应，规定屏幕宽度750rpx，在小程序中替代rem/vw进行适配
在iphone6上，屏幕宽度375px，共有750个物理像素，750rpx === 375px



wxs是跑在渲染层线程的，如果执行过于复杂的逻辑，会阻塞页面渲染过程
wxs运行环境和其他js代码是隔离的，不能调用其他js文件中定义的函数，也不能调用小程序提供的API
wxs在ios设备上运行比js代码更快



触发事件的元素target
处理事件的元素currentTarget



touches当前屏幕上所有触摸点的列表
changedTouches：触发事件时改变的触摸点的集合



小程序中插槽不支持默认值



### 项目问题

scroll-view让页面内容滚动
height: cac(100% - 225px); 有问题，  100%相对高度
需要设置页面高度

```html
<video class="video" />

<scroll-view class="content" scroll-y>
</scroll-view>
```



```css
page {
  height: 100vh;
}
.video {
  width: 100%;
}
.content {
  height: calc(100% - 225px);
}

```





行内样式动态设置轮播图高度和图片高度一致
bannerHeight
在图片加载完后获取Image组件高度

```js
```





