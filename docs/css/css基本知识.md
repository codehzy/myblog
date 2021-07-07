[toc]
# 总结
1. 盒模型宽度计算，offsetWidth
2. margin纵向重叠问题，**重叠**
3. margin负值问题
4. BFC理解和应用,怎么清楚浮动，手写clearfix
5. float布局，圣杯布局和双飞翼布局
6. flex布局，花三个点的色子
7. absolute和relative定位
>水平居中，垂直居中（以及不知道宽高）
8. CSS图文样式
9. line-height如何继承？写具体数值，比例，百分比
10. CSS响应式 media
11. rem是什么？所有长度单位以及异同？px,em,rem,vw/vh
12. flex

## 1.盒模型宽度计算
```css
/* 如下代码：请问div1的offsetWidth是多大? */ 100 + 10 + 10 + 1 + 1 = 122
#div1{
    width: 100px;
    padding: 10px;
    border: 1px solid #ccc;
    margin: 10px;
}
```
```html
<div id="div1">
</div>
```

- offsetWidth = (内容宽度 + 内边距 + 边框 )，无外边距
- 如果让offsetWidth变成100px,box-sizing: border-box;
- box-sizing: content-box 是W3C盒子模型
- box-sizing: border-box 是IE盒子模型

## 2.margin纵向重叠问题
```css
<!-- 如下代码：AAA和BBB之间的距离是多少？ --> 15px
p {
    font-size: 16px;
    line-height: 1;
    margin-top: 10px;
    margin-bottom: 15px;
}
```

```html
<p>AAA</p>
<p></p>
<p></p>
<p></p>
<p>BBB</p>
```

- 相邻元素的margin-top和margin-bottom会发生重叠
- 空白内容的<p></p>也会重叠

## 3.margin负值问题
> 对margin的top、left、right、bottom设置负值，有何效果？
```css
body {
    margin: 20px;
}

.float-left {
    float: left;
}
.clearfix:after {
    content: '';
    display: table;
    clear: both;
}

.container {
    border: 1px solid #ccc;
    padding: 10px;
}
.container .item {
    width: 100px;
    height: 100px;
}
.container .border-blue {
    border: 1px solid blue;
}
.container .border-red {
    border: 1px solid red;
}
```
```html
    <p>用于测试 margin top bottom 的负数情况</p>
    <div class="container">
        <div class="item border-blue">
            this is item 1
        </div>
        <div class="item border-red">
            this is item 2
        </div>
    </div>

    <p>用于测试 margin left right 的负数情况</p>
    <div class="container clearfix">
        <div class="item border-blue float-left">
            this is item 3
        </div>
        <div class="item border-red float-left">
            this is item 4
        </div>
    </div>
```

- margin-top和margin-left负值，元素向上、向左移动
- margin-right负值，右侧元素左移，自身不受影响
- margin-bottom负值，下方元素上移，自身不受影响

## 4.BFC理解和应用
> 什么是BFC？如何应用？
```css
    .container {
        background-color: #f1f1f1;
    }
    .left {
        float: left;
    }
    .bfc {
        overflow: hidden; /* 触发元素 BFC */
    }
```

```html
<div class="container bfc">
    <img src="https://www.imooc.com/static/img/index/logo.png" class="left" style="magin-right: 10px;"/>
    <p class="bfc">某一段文字……</p>
</div>
```
- **块级格式化上下文**
- 一块独立渲染区域，内部元素的渲染不会影响边界以外的元素

- 形成BFC的常用条件
  - float不是none
  - position是absolute或fixed
  - overflow不是visible
  - display是flex inline-block等

- BFC常见应用
  - **清除浮动** ->  overflow:hidden 给父级和自己加上BFC

## 5.float布局
> 如何实现圣杯布局和双飞翼布局
-   **目的**
    -   三栏布局，中间一栏最先加载和渲染（内容最重要）
    -   两侧内容固定，中间内容随着宽度自适应
    -   一般用于PC网页
 
- **双飞翼和圣杯技术总结**
  - 使用float布局
  - 两侧使用margin负值，以便和中间内容横向重叠
  - 防止中间内容被两侧覆盖，一个用padding 一个用margin

- **圣杯布局**
```css
/* *
    1. 对container容器中元素设置float
    2. 给container设置padding-left和padding-right
    3. left区域: margin-left: -100% , 设置left为position: relative; , right: 200px
    4. 给right设置margin-right: -150px
*/
    body {
        min-width: 550px;
    }
    #header {
        text-align: center;
        background-color: #f1f1f1;
    }

    #container {
        padding-left: 200px;
        padding-right: 150px;
    }
    #container .column {
        float: left;
    }

    #center {
        background-color: #ccc;
        width: 100%;
    }
    #left {
        position: relative;
        background-color: yellow;
        width: 200px;
        margin-left: -100%;
        right: 200px;
    }
    #right {
        background-color: red;
        width: 150px;
        margin-right: -150px;
    }

    #footer {
        text-align: center;
        background-color: #f1f1f1;
    }

    /* 手写 clearfix */
    .clearfix:after {
        content: '';
        display: table;
        clear: both;
    }
```

```html
    <div id="header">this is header</div>
    <div id="container" class="clearfix">
        <div id="center" class="column">this is center</div>
        <div id="left" class="column">this is left</div>
        <div id="right" class="column">this is right</div>
    </div>
    <div id="footer">this is footer</div>
```

- **双飞翼布局**
```css
    /* 
        1.设置三个区域都float:left
        2.middle设置margin空出左右margin, margin: 0 200px 0 200px;
        3. 设置left: margin-left: -100%
        4. 设置margin-left: -200px
    
    */
    body {
        min-width: 550px;
    }
    .col {
        float: left;
    }

    #main {
        width: 100%;
        height: 200px;
        background-color: #ccc;
    }
    #main-wrap {
        margin: 0 190px 0 190px;
    }

    #left {
        width: 190px;
        height: 200px;
        background-color: #0000FF;
        margin-left: -100%;
    }
    #right {
        width: 190px;
        height: 200px;
        background-color: #FF0000;
        margin-left: -190px;
    }
```

```html
<div id="main" class="col">
    <div id="main-wrap">
        this is main
    </div>
</div>
<div id="left" class="col">
    this is left
</div>
<div id="right" class="col">
    this is right
</div>
```

> 手写clearfix
```HTML
<!-- clearfix -->

.clearfix:after{
    content: '';
    display:table;
    clear:both;
}

.clearfix{
    <!-- 兼容ie低版本 -->
    *zoom: 1; 
}
```

## 6.flex布局
> flex实现一个三点的色子


- flex-direction : 横向 + 纵向
- justify-content : 主轴对齐方式
- align-items : 与主轴垂直轴对齐方式
- flex-wrap: 是否换行
- align-self: 子元素在交叉轴对齐方式

## 7.absolute和relative定位
- **relative**: 依据自身定位,相对定位
- **absolute**: 依据最近一层的定位元素定位，绝对定位
- 定位元素： absolute -> relative -> fixed -> body

## 8. 水平居中，垂直居中
- **水平居中**
  - inline元素： 使用text-align: center
  - block元素: 使用margin:auto
  - absolute: left: 50% + margin-left负值

- **垂直居中**
  - inline元素： line-height的值等于height值
  - absolute： top：50% + margin-top负值 -> 必须知道子元素宽和高
  - absolute元素: tranform(-50%,-50%)
  - aosolute元素： top、left、bottom、right = 0 + margin: auto

## 9.CSS图文样式
- 写具体数值，如30px，则继承改成该值
- 写成比例，如果2 / 1.5,则继承该比例 -> 直接继承比例，与自己font-size相乘
- **写百分比，如200%，则继承body计算出来的值** ->先计算 后继承

## 10.CSS响应式

### rem是什么？
- rem：是一个长度单位
  - px是绝对长度单位，最常用
  - em，相对长度单位，相对于父元素，不常用
  - rem:，相对长度单位，相对于**根元素**，常**用于响应式布局**
  
### 响应式布局的方案是什么?
- media-query： 根据不同的屏幕的宽度设置根元素font-size
- rem: 基于根元素的相对单位

```css
    @media only screen and (max-width: 374px) {
        /* iphone5 或者更小的尺寸，以 iphone5 的宽度（320px）比例设置 font-size */
        html {
            font-size: 86px;
        }
    }
    @media only screen and (min-width: 375px) and (max-width: 413px) {
        /* iphone6/7/8 和 iphone x */
        html {
            font-size: 100px;
        }
    }
    @media only screen and (min-width: 414px) {
        /* iphone6p 或者更大的尺寸，以 iphone6p 的宽度（414px）比例设置 font-size */
        html {
            font-size: 110px;
        }
    }

    body {
        font-size: 0.16rem;
    }
    #div1 {
        width: 1rem;
        background-color: #ccc;
    }
```

```html
<div id="div1">
    this is div
</div>
```

## 11.vw/vh
出现原因: 1. rem弊端 2.网页视口尺寸 3. vw/vh

**rem的弊端**:‘阶梯性’，根据iphone的不同型号进行划分台阶。随着每1px的增长不会立刻改变，只有跨越阶梯平台才改变吗。

- **网页视口尺寸**
  - window.screen.height // 屏幕高度
  - window.innerHeight // 网页视口高度（不含工具栏）
  - document.body.clientHeight // body高度

- **vw/vh**
  - vh网页视口高度1/100
  - vw网页视口宽度1/100
  - vmax去两者最大值；vmin取最小值

## 12. flex
[阮一峰Flex](https://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
### 12.1 什么是Flex布局
1. flex布局是Flex BOX的缩写，是弹性布局。将任何一个容器定义为dispaly: flex;
2. 行内元素也可以定义为flex，dispaly: inline-flex;

### 12.2 主要概念
1. 我们首先要知道主轴，交叉轴。我们通常将水平方向的上叫做主轴，垂直方向上的叫做交叉轴。
- 容器属性
  - flex-direction
    - 定义主轴方向
    - row , row-resverse , colum , colum-reverse
  - flex-wrap
    - 如果第一条轴线排列不下
    - nowrap
    - wrap
    - wrap-reverse
  - flex-flow
    - 是flex-direction和flex-wrap
  - justify-content
    - 定义项目在主轴上的对齐方式
    - flex-start
    - flex-end
    - center
    - space-between
    - space-around
  - align-items
    - 定义项目在交叉轴上对齐方式
    - flex-start
    - flex-end
    - center
    - stretch
    - baseline
  - algn-content
    - 定义多根轴线的对齐方式。如果项目只有一根轴线，则该属性失效。
    - flex-start
    - flex-end
    - center
    - stretch
    - space-betweem
    - space-around
- 项目属性
  - order
    - 定义项目的排列顺序，数值越少，排名越靠前，默认为0，负数有效
  - flex-grow
    - 定义了项目的放大比例，默认值为0，存在剩余空间则不放大。
  - flex-shrink
    - 缩小比例，默认值为1，空间不足，项目则缩小。
    - 因为默认值为1，所以0的时候，就缩小。
  - flex-basis
    - 显示项目在分配多余空间之前，项目占据的主轴空间。通常我们可以来计算主轴是否有多余空间。
  - flex
    - felx-grow , flex-shrink , flex-basis , 默认值为 0 1 auto
    - 常用默认值 auto: 1 1 auto,none: 0 0 auto, 1: 1 1 auto
  - align-self
    - 属性允许单个项目可以与其他项目不一样的对齐方式，可以覆盖align-items属性，默认值为auto。
