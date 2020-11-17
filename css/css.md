[toc]

- 前言
  -  [参考链接](https://www.html.cn/interview/17442.html)

# 1.css实现div内文字显示一行，超出部分用省略号显示

```css
    white-space: nowrap;//规定段落中的文本不进行换行
    overflow: hidden;//内容溢出隐藏
    text-overflow: ellipsis;//	当文本溢出包含它的元素时,显示省略符号来代表被修剪的文本。

```

# 2.div内显示两行或三行，超出部分用省略号显示（只兼容Chrome内核浏览器）

```css
 overflow: hidden;
 text-overflow: ellipsis;
 display: -webkit-box;
 -webkit-line-clamp: 2;（行数）
 -webkit-box-orient: vertical;
```

- webkit-line-clamp 是一个 不规范的属性（unsupported WebKit property），它没有出现在 CSS 规范草案中。限制在一个块元素显示的文本的行数。 为了实现该效果，它需要组合其他外来的WebKit属性。常见结合属性：
- display: -webkit-box; 必须结合的属性 ，将对象作为弹性伸缩盒子模型显示 。
- -webkit-box-orient 必须结合的属性 ，设置或检索伸缩盒对象的子元素的排列方式 。

# 3.介绍一下标准的 CSS 的盒子模型？低版本 IE 的盒子模型有什么不同的？

1. 有两种盒子模型：

   - IE盒模型（box-sizing:border-box）---怪异盒模型

   - W3C标准盒模型（content-box）

        

2. 盒模型由四部分组成

   - 分为内容（content）
   - 填充（padding）
   - 边界（margin）
   - 边框（border）

3. W3C标准盒模型

   - 属性width，height只包含内容content，不包含border和padding,设置border和padding会增加盒模型的大小

4. IE盒模型   

   - 属性width，height包含content、border和padding，指的是content+padding+border。设置border和padding不会增加盒模型的大小

# 4. 如何居中div



## 4.1 margin:0 auto

1. 利用margin:0auto来实现元素的水平居中

   ```html
   div {
     width: 200px;
     margin: 0auto;
   }
   ```

## 4.2  text-align:center  

1. 水平居中，利用 text-align:center 实现  

   ```html
   <style>
       .container {
           background: rgba(0, 0, 0, 0.5);
           text-align: center;
           font-size: 0;
       }
   
       .box {
           display: inline-block;
           width: 500px;
           height: 400px;
           background-color: pink;
       }
   </style>
   
   <body>
       <div class="container">
           <div class="box">1111</div>
       </div>
   </body>  
   ```

## 4.3 绝对定位+margin：auto

1. 利用绝对定位，设置四个方向的值都为0，并将margin设置为auto，由于宽高固定，因此对应方向实现平分，可以实现水平和垂直方向上的居中。

```html

<style>
    div {
        position: absolute;
        width: 300px;
        height: 300px;
        margin: auto;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        background-color: pink;
        /*方便看效果*/
    }
</style>

<body>
    <div>1111</div>
</body>
```

## 4.4 绝对定位+margin值

1. 利用绝对定位，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过margin负值来调整元素的中心点到页面的中心

   ```html
   
    <style>
       /*确定容器的宽高宽500高300的层设置层的外边距*/
       div {
           position: absolute;
           /*绝对定位*/
           width: 500px;
           height: 300px;
           top: 50%;
           left: 50%;
           margin: -150px 0 0 -250px;
           /*外边距为自身宽高的一半*/
           background-color: pink;
           /*方便看效果*/
       }
   </style>
   
   <body>
       <div>1111</div>
   </body>
   ```

## 4.5 绝对定位+translate

1. 利用绝对定位，先将元素的左上角通过top:50%和left:50%定位到页面的中心，然后再通过translate来调整元素的中心点到页面的中心----宽高不固定的时候可以使用

   ```html
   <style>
       /*未知容器的宽高，利用`transform`属性*/
       div {
           position: absolute;
           /*相对定位或绝对定位均可*/
           width: 500px;
           height: 300px;
           top: 50%;
           left: 50%;
           transform: translate(-50%, -50%);
           background-color: pink;
           /*方便看效果*/
       }
   </style>
   
   <body>
       <div>1111</div>
   </body>
   ```

## 4.6 flex布局

1. 使用flex布局，通过align-items:center和justify-content:center设置容器的垂直和水平方向上为居中对齐，然后它的子元素也可以实现垂直和水平的居中----宽高不固定的时候可以使用

   ```html
   <style>
       /*利用flex布局实际使用时应考虑兼容性*/
       .container {
           display: flex;
           align-items: center;
           /*垂直居中*/
           justify-content: center;
           height: 300px;
           /*水平居中*/
       }
   
       .containerdiv {
           width: 100px;
           height: 100px;
           background-color: pink;
           /*方便看效果*/
       }
   </style>
   
   <body>
       <div class="container">
           <div class="containerdiv"></div>
       </div>
   </body>
   ```

## 4.7 text-align:center和vertical-align:middle属性

1. 利用text-align:center和vertical-align:middle属性

```html
<style>
    .container {
        position: fixed;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
        background: rgba(0, 0, 0, 0.5);
        text-align: center;
        font-size: 0;
        white-space: nowrap;
        overflow: auto;
    }

    .container::after {
        content: "";
        display: inline-block;
        height: 100%;
        vertical-align: middle;
    }

    .box {
        display: inline-block;
        width: 500px;
        height: 400px;
        background-color: pink;
        white-space: normal;
        vertical-align: middle;
    }
</style>

<body>
    <div class="container">
        <div class="box"></div>
    </div>
</body>
```



# 5.用纯 CSS 创建一个三角形的原理是什么？

- 采用的是相邻边框连接处的均分原理。

```css
 div {
            width: 0;
            height: 0;
            border-width: 20px;
            border-style: solid;
            border-color: yellow black red pink;
   }
```

# 6.CSS 选择符有哪些？

（1）id选择器（#myid）
（2）类选择器（.myclassname）
（3）标签选择器（div,h1,p）
（4）包含选择符（h1 p）
（5）相邻后代选择器（子）选择器（ul>li）
（6）兄弟选择器（li~a）
（7）相邻兄弟选择器（li+a）
（8）属性选择器（a[rel="external"]）
（9）伪类选择器（a:hover,li:nth-child）
（10）伪元素选择器（::before、::after）
（11）通配符选择器（*）

# 7. CSS3 新增伪类

（1）elem:nth-child(n)选中当前元素的兄弟元素,n可以接受具体的数

​      值，也可以接受函数。

		- odd:奇数行
		- even:偶数行

```html
<style>
    li:nth-child(2) {
        color: red;
    }
</style>

<body>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</body>
```

（2）elem:nth-last-child(n)作用同上，不过是从后开始查找。

（3）elem:last-child选中最后一个子元素。

```html
<style>
    li:last-child {
        color: red;
    }
</style>

<body>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</body>
```

（4）elem:only-child如果elem是父元素下唯一的子元素，则选中之。

（5）elem:nth-of-type(n)选中父元素下第n个elem类型元素，n可以接受具体的数值，也可以接受函数。

（6）elem:first-of-type选中父元素下第一个elem类型元素。

```html
<style>
    li:first-of-type {
        color: red;
    }
</style>

<body>
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</body>
```

（7）elem:last-of-type选中父元素下最后一个elem类型元素。

（8）elem:only-of-type如果父元素下的子元素只有一个elem类型元素，则选中该元素。

（9）elem:empty选中不包含子元素和内容的elem类型元素。

（10）elem:target选择当前活动的elem元素。

（11）:not(elem)选择非elem元素的每个元素。

（12）:enabled 控制表单控件的禁用状态。

（13）:disabled 控制表单控件的禁用状态。

（14）:checked单选框或复选框被选中。

# 8. flex布局

| Flex 布局教程：实例篇 | http://www.ruanyifeng.com/blog/2015/07/flex-examples.html |
| --------------------- | --------------------------------------------------------- |
| Flex 布局教程：语法篇 | http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html  |

