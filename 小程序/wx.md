页面生命周期 https://developers.weixin.qq.com/miniprogram/dev/framework/app-service/page-life-cycle.html

![img](https://res.wx.qq.com/wxdoc/dist/assets/img/page-lifecycle.2e646c86.png)

组件生命周期：https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/lifetimes.html



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

- 使用:empty+display进行设置

```html
<!--components/nav-bar/nav-bar.wxml-->
<view class="nav-bar">
  <view class="status" style="height: {{statusBarHeight}}px" />
  <view class="nav">
    <view class="left" bindtap="onNavLeftTap">
      <view class="slot">
        <slot name="left"></slot>
      </view>
      <view class="default">
        <image class="icon" src="/assets/images/icons/arrow-left.png"></image>
      </view>
    </view>
    <view class="center">
      <view class="slot">
        <slot name="center"></slot>
      </view>
      <view class="default">
        {{title}}
      </view>
    </view>
    <view class="right"></view>
  </view>
</view>
```

```css
/* components/nav-bar/nav-bar.wxss */
.nav {
  display: flex;
  height: 44px;
  color: #fff;
}
.nav .left, .nav .right, .nav .center {
  display: flex;
  justify-content: center;
  align-items: center;
}
.nav .left, .nav .right {
  width: 120rpx;
}
.nav .center {
  flex: 1;
}
.left .icon {
  width: 40rpx;
  height: 40rpx;
}
.center {
  text-align: center;
}


.default {
  display: none;
}

.slot:empty + .default {
  display: flex;
}
```





 openid: 小程序中识别某一个帐号

 unionid: 用于跨应用的，小程序、公众号

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





 detail-menu，获取所有歌单信息。**promise.all**

```js
const allPromises = []
for (const tag of tagRes.tags) {
  const promise = getSongMenuList(tag.name)
  allPromises.push(promise)
}

Promise.all(allPromises).then(res => {
  this.setData({ songMenus: res })
})
```







播放工具栏的图片旋转暂停

animation-play-state

```html
<image 
  class="album album-anim" 
  src="{{currentSong.al.picUrl}}"
  style="animation-play-state: {{ isPlaying? 'running': 'paused' }};"
  bindtap="onPlayBarAlbumTap"
/>
```

