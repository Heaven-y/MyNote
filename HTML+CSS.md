#### URL

URL的格式	[协议类型]：//[服务器地址]：[端口号]/\[文件路径][文件名]？[查询]#[片段ID]

​						scheme			post				port		path                           query	fragment

URL和URI的区别

* URI 统一资源标识符，标识Web技术使用的逻辑或物理资源
* URL 统一资源定位符 URL是一个URI



#### SEO

​	h元素 

​	爬虫：  索引区 - `抓取` - > 网页 < - -`存放`- - > 临时库 ->(符合规则)索引区，在索引区中分类、归档、排序，然后将结果反馈给用户

SSR



性能优化

*  \<link rel="dns-prefetch">
* 精灵图(减少http请求)



#### 全局属性data-\*

data设置的属性可以通过JavaScript的DOM操作在dataset中拿到

通常用于HTML和JS数据间的传递

```html
<div id="box" data-age="18"></div>
<script>
  const d = documen.querySelector("#box")
  d.dataset.age
</script>
```

针对自定义属性:element.getAttribute('属性') 	element.setAttribute('属性','值')	element.removeAttribute('属性')

以data开头的自定义属性

```html
<div data-list-name="andi" />
```

```javascript
let div = document.querySelector('div');
div.getAttribute('data-list-name');
div.dataset.listName;//div.dataset['listName'] -链接时候，用驼峰命名法
```

element.dataset.属性 or element.dataset['属性'] **只能获取data-开头的自定义属性**



#### video兼容性

```html
<video src="./assets/fcrs.mp4">
  <source src="./assets/fcrs.ogg">
  <source src="./assets/fcrs.webm">
  <p>你的浏览器不支持播放资源</p>
</video>
```

当不支持mp4，无法正常播放时，会去查找source中的资源



#### 列表

自定义

```html
<dl>
  <dt>一</dt>
  <dd></dd>
  <dd></dd>
  
  <dt>二</dt>
  <dd></dd>
  <dd></dd>
</dl>
```



#### 表格

```html
<table>
  <tr>
    <td></td>
  </tr>
</table>

<table>
  <thead>
    <tr>
    	<th></th>
 		</tr>
  </thead>
	<tbody>
    <tr>
      <td></td>
    </tr>
	</tbody>
</table>
```

border-collapse 合并边框

colspan 跨列合并		rowspan 跨行合并



#### 表单

```html
<form action="">
  <input type="text">
  <input type="date">
  
  <input type="reset" value="重置按钮">
  <button type="reset">重置按钮</button>
  
  <input type="submit" value="提交按钮">
  <button type="submit">提交按钮</button>
</form>
```

label[for]用来和input[id]绑定

radio单选,chekbox多选需要name相同 提交value值



#### css sprite

制作：https://www.toptal.com/developers/css/sprite-generator

查看：http://www.spritecow.com/



#### 元素隐藏(4)

* display:none	不占据任何空间(不存在一样)
* visibility:hidden  会占据该元素应该占据的空间
* rgba的a设置为0 (alpha)    不影响子元素，设置自身color或者bgc其中颜色的透明度
* opacity  携带所有子元素都设置透明度



#### 水平居中

* 行内级元素
  * text-align:center
* 块级元素
  * 有宽度，margin:0 auto
* 绝对定位
  * 有宽度，left0/right0/margin:0 auto

* flex
  * justify-content:center
* left/translate



#### 垂直居中

* 绝对定位
  * 有高度，top0/bottom0/margin:auto 0

* flex布局
  * align-items:center
* top/translate



#### text-align

 行内级元素相对于它的块父元素对齐  无法对块级产生效果



text-indent对行内非替换元素无效



# vertical-align(day12)

影响行内块级元素在一个行盒中垂直方向的位置



#### line-hight

* 行盒

  两行**文字**基线之间的距离

行高 - 文本高度 = 行距

font-size/line-height   30px/1.5  1.5相对于30px

行内非替换元素的高度无法由line-hight撑起来，但是会继承过来  `display:(inline-)block`

;

#### 选择器

属性选择器：[title=div]    ---<div title='div'></div>    权重 0 0 1 0
兄弟选择器 ：+相邻	~普遍(后面)



#### 伪类:

##### :empty	:root



#### 伪元素::

默认是行内非替换元素，设置宽高不生效	::before、::after



#### 继承

继承的是计算值、强制继承 inherit



#### 块，行内(块)

##### 行内级非替换元素

* 设置width、height不生效
* **border、padding的上下会撑大内容，但是不会占据空间**
* margin的上下不生效

img、video、input能设置宽高：行内替换元素

`开启绝对定位的元素(absolute/fixed)能随意设置宽高`

​	

#### margin

`值为百分比时，相对于包含块的宽度`

* 包含块的宽度为1600px时，margin-top:50% == 800px

行内级设置margin-top无效

##### 传递/折叠/负值(day06)

1.传递

左右不传递、margin的上下传递

​	marin-top：块级元素的顶部线和父元素的顶部线重叠，那么块级元素的margin-top会传递给父元素

​	marin-bottom：块级元素的底部线和父元素的底部线重叠，且父元素高度为auto



如何防止出现传递

* 给父元素设置padding-top
* 给父元素设置border
* 触发BFC



2.折叠

水平方向不折叠	上下折叠

折叠计算：取大值



3.负值



#### outline

外轮廓不占用空间



#### margin与padding

```html
<div class='box'>
  <div class='content'></div>
</div>
```

让content移动，给box设置padding时，会撑大内容	解决：

* box设置box-sizeing:border-box

让content下移，给content设置margin-top时，父元素也会下来  解决：

* 给box触发BFC
  * overflow:auto



#### box-sizing

content-box

​	padding、border都布置在width、height外边

border-box

​	width=内容+padding+border



前景色会在border没有设置颜色的情况下, 显示出来color颜色



box-shadow :https://html-css-js.com/css/generator/box-shadow/



#### bgimg和img对比

|                        |    img     | background-image |
| :--------------------: | :--------: | :--------------: |
|          性质          |  HTML元素  |     CSS样式      |
|    图片是否占用空间    |     √      |        ×         |
| 浏览器右键直接查看地址 |     √      |        ×         |
|     支持CSS Sprite     |     ×      |        √         |
| 更有可能被搜索引擎收录 | √(结合alt) |        ×         |

* img，作为网页内容的重要组成部分，比如广告图片、LOGO图片、文章配图、产品图片
*  background-image，有-能让网页更加美观。无-不影响用户获取完整的网页内容信息



#### 单行...显示

white-space:nowrap

overflow:hidden

text-overflow:ellipsis 

多行

```css
display: -webkit-box; /*弹性盒*/
-webkit-line-clamp: 2; /*显示行数*/
-webkit-box-orient: vertical; /*设置伸缩盒对象的子元素的排列方式*/
overflow: hidden;
text-overflow: ellipsis;
```



background:color  url  position/size  style  attachement



#### 定位

定位元素会层叠在浮动元素上面

relative 相对于原来位置

fixed 脱标  相对于视口

absolute 脱标 参照最邻近的定位祖先元素，没有找到则为视口

sticky 相对定位，到阈值时变为绝对定位  相对于最近的滚动祖先包含滚动视口的



当position为absolute/fixed时

* 随意设置宽高
* 宽高默认由内容决定
* 不再受标准流的约束
  * 不再严格按照从上到下、从左到右排布
  * 不再严格区分块级(block)、行内级(inline)，行内块级(inline-block)的很多特性都会消失
* 不再给父元素汇报宽高

* **定位参照对象的宽度=left + right +margin-left + margin-right +绝对定位元素的实际占用宽度(width)**

* **定位参照对象的高度=top + bottom +margin-top + margin-bottom +绝对定位元素的实际占用高度(height)**(可设置居中效果)
* 如果希望绝对定位元素的宽高和定位参照对象一样，可以给绝对定位元素设置以下属性
  *  **left: 0、right: 0、top: 0、bottom: 0、margin:0**

* 如果希望绝对定位元素在定位参照对象中居中显示，可以给绝对定位元素设置以下属性
  * **left: 0、right: 0、top: 0、bottom: 0、margin: auto**
  * 另外，还得设置具体的宽高值（宽高小于定位参照对象的宽高）



#### 浮动

浮动元素脱离了标准流，变成了浮动元素，不再向父元素汇报高度。所以父元素在计算高度时并没有将浮动元素的高度计算进来，因此就造成了高度塌陷的问题

清除浮动clear

clear 属性可以指定一个元素是否必须移动(清除浮动后)到在它之前的浮动元素下面



## BFC(day15)

Block Formating Context 块级格式化上下文

#### 创建BFC的具体情况

* 根元素
* 浮动元素、绝对定位元素(absolute,fixed)
* 行内块元素(inline-block)
* 表格单元格、表格标题、匿名表格单元格元素
* overflow计算值不为visible的块元素
* 弹性元素
* 网格元素(grid/inline-grid元素的直接子元素)
* display为flow-root的元素



在一个BFC中，盒子在垂直方向上从顶往下一个一个排布。盒子间的距离通过margin设置。**在同一个BFC中**，相邻两个块级元素垂直方向上的margin会折叠。  每个元素的左边缘是紧挨着包含块的左边缘的



#### 作用

* 解决margin的折叠问题
  * 让元素在不同的BFC中

* 解决浮动高度塌陷问题
  * 浮动元素的父元素的height为auto
  * 浮动元素的父元素触发BFC，形成独立的块级格式化上下文
    * BFC的高度是auto情况下，如下计算高度：1.如果只有inline-level，是行高的顶部和底部的距离
    * 2.如果有block-level，由最顶层的块上边缘和最底层块盒子的下边缘之间的距离
    * 3.**如果有绝对定位元素，将被忽略**
    * 4.如果有浮动元素，那么会增加高度以包括这些浮动元素的下边缘



### transform

通常行内级元素不能进行形变，所以transform对于行内级非替换元素是无效的(span,a)



transform = translate() scale() rotate()

rotate 旋转

transform-origin



### transition

transition = property  duration  timing-function  delay



### Animation

keyframs定义动画序列

name  duration  timing-function delay  iteration-count  diraction  fill-mode(停留位置)  play-state



### blur

毛玻璃(高斯模糊) 需要有background-color:rgba()

backdrop-filter:blur()



### gradient

颜色渐变函数 生成的是image

background-image:...-gradient()



## flex布局

主轴，交叉轴

flex-direction:`主轴方向`

container中的属性

* flex-flow = flex-direction + flex-wrap
* justify-content
* align-items
  * height为auto时，值为stretch，会产生拉伸效果，占据整个高度
* align-content



item中的属性

* order
* flex = flex-grow + flex-shrink +flex-basis
* align-self



### 常见问题解决

最后一行的排布

* 添加i/span/strong元素 个数为列数-2，设置宽度(因为没有高度，所以不显示)

flex和white-space的冲突(10-4)

* 给设置了flex的元素添加overflow



## 媒体查询

* 通过@media和@import使用不同的css规则

  * @import url() (max-widht:800px)

* 使用media属性为\<style>\<link>\<source>和其他HTML元素指定特定的媒体类型

  * \<link rel="stylesheet" media="(max-width:800px)" href="./body.css">

* ```html
  <style>
    @media (max-width:800px) {
      body {
        background-color:orange;
      }
    }
  </style>
  ```

* 使用Window.matchMedia()和MediaQueryList.addListener()方法来测试和监控媒体状态



## 单位

em 相对于自己的font-size，如果自身没有设置，那么会继承父元素的font-size

rem 根元素的字体大小

vw，vh



DPR

​	device pixel ratio设备像素比   一个逻辑像素在长度上对应两个物理像素

PPI 每英寸像素



## 视口

PC端:浏览器可视区域

移动端

* 布局视口  相对于宽为980px来布局页面的视口
* 视觉视口  显示在可见区域的视口，称为视觉视口
* 理想视口  布局视口=视觉视口

### 适配方案

* 视口+rem+动态html的font-size
  * 动态计算 HTML font-zise
    * 用媒体查询来修改HTML font-size( 缺点不能实时改变font-size的大小 )
    * 自己编写JS来实现修改HTML font-size的大小(可以实时修改字体大小)
    * 引用lib-flexiable库来实现（原理是JS动态改HTML font-size大小）

  * px 转成rem
    * Less的映射来计算
    * postcss-pxtorem插件来实现（webpack阶段会学到）
    * cssrem VSCode插件来实现

* vw单位
  * Less的映射来计算
  * Postcss-px-to-viewport的插件(Wepack 阶段学)
  * ccsrem VSCode插件

* flex的弹性布局





npm install typescript ts-node tslib @types/node -g
