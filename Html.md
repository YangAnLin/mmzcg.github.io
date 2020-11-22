# Html
## 标题标签
```html
<body>
    <h1>111111111111</h1>
    <h2>111111111111</h2>
    <h3>111111111111</h3>
    <h4>111111111111</h4>
    <h5>111111111111</h5>
    <h6>111111111111</h6>
</body>
```

![image-20201115195359237](https://raw.githubusercontent.com/YangAnLin/images/master/20201115195401.png)

## 段落标签

```html
<body>
<p>我们一进里面一片漆黑，然后老师给我们每一个人都发了一个眼镜，我们戴上眼镜，后面前有一张大白纸，后面有一个投影仪，放了一个电影，名叫“雪怪大冒险”。我们看着入迷，还有电影里面在下雪，我戴着眼镜，却以为天上正在下雪，我伸出手来去捉雪花，我说怎么也捉不住，我一想这是电影里面的，这不是现实。下一站去野餐。</p>

<p>有几位老师去帮我们买汉堡去了，我们先到那里先吃一些零食，我们作文https://Www.ZuoWEn8.Com/的午餐特别丰富，我们吃饱后，我们一起去玩游戏，第一个游戏的名字叫做动物园里有什么。第二个游戏的名字叫做学校里面有什么。第三个游戏的名字叫做萝卜蹲。最后一个游戏是猜谜语，我们玩的特别开心，我们玩猜谜语时，每答对一个问题，都有一个奖品。高年级是苏家和笔袋儿，低年级是彩铅和笔记本儿，我们玩儿了一会儿，我们又开始吃水果了，我们吃西瓜，那瓜可甜了。吃完后我们收拾了一下东西，去了下一站，鸳鸯湖去参观。</p>
</body>
```
## 换行标签
段落之间的距离比较大,换行的距离较小
```html
<body>
13213123<br/>3123123
</body>
```

## 文本格式化标签
```html
<body>
1<strong>加粗</strong>3
1<b>加粗</b>3

1<em>倾斜</em>3
1<del>删除线</del>3
1<ins>下划线</ins>3

</body>
```

## div
div独占一行
```html
<body>
<div>div单独占一行</div>3213123
<div>div单独占一行</div>
</body>
```

## span
span所有的占一行
```html
<body>
<span>div单独占一行</span>3213123
<span>div单独占一行</span>
</body>
```

## 图像标签
```html
<body>
这是百度的图片<img src="http://www.baidu.com/img/pc_2e4ef5c71eaa9e3a3ed7fa3a388ec733.png"/>
</body>
```

## 超链接
target属性有_self为默认值,当前页面
            _blank 新的页面打开

外连接就要加http,内连接就是路径地址
```html
<body>
<a href="http://baidu.com">这是超裂解</a>
</body>
```

## 锚点
```html
<body>
目录:
<a href="#1">1</a>
<a href="#2">2</a>
<hr>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<h2 id="1">这是锚点1</h2>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
<h2 id="2">这是锚点2</h2>
</body>
```

## 表格标签
```html
<body>
<table border="1" align="center">
    <tr>
        <th>姓名</th>
        <th>性别</th>
    </tr>
    <tr>
        <td>anthony</td>
        <td>南</td>
    </tr>
</table>
</body>
```

## 列表标签
```html
<body>
<!-- 无序列表 -->
<ul>
    <li>无序列表1</li>
    <li>无序列表2</li>
    <li>无序列表3</li>
    <li>无序列表4</li>
</ul>

<!-- 有序列表 -->
<ol>
    <li>1</li>
    <li>2</li>
    <li>4</li>
    <li>3</li>
</ol>

<!-- 自定义 -->
<dl>
    <dt>这是自定义列表</dt>
    <dd>1</dd>
    <dd>2</dd>
    <dd>4</dd>
    <dd>3</dd>
</dl>
</body>
```

## 表单标签
```html
<body>
<form action="url地址" method="提交方式" name="表单名称">
    <!-- 点击lable 就能把光标古交到输入框里面-->
    <label for="usernameId">用户名</label>
    <input type="text" name="username" value="anthony" id="usernameId"> <br>
    密码  :<input type="password" name="pwd"> <br>
    性别 : 男<input type="radio" name="sex" value="男" checked>  女<input type="radio" value="女" name="sex"><br>
    爱好: 吃饭<input type="checkbox" name="hobby" value="1">
    睡觉<input type="checkbox" name="hobby" value="2">
    玩<input type="checkbox" name="hobby" value="3"><br>

    <label for="selectId">选项:</label>
    <select id="selectId">
        <option value="1" >一</option>
        <option value="2" selected>二</option>
        <option value="3">三</option>
    </select><br>

    文本域元素:<textarea>23123</textarea>
</form>

</body>
```

# CSS
## 基础选择器
### 标签选择器
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p{
            color: green;
        }

        div{
            color: red;
        }
    </style>
</head>
<body>
<p>男生</p>
<p>男生</p>
<p>男生</p>
<div>男生</div>
<div>男生</div>
<div>男生</div>
</body>
```

### 类选择器
只有一个class
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .red{
            color: red;
        }
    </style>
</head>
<body>
<ul>
    <li class="red">1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
</ul>
</body>
```

多个class
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        .red{
            color: red;
        }

        .font35{
            font-size: 35px;
        }
    </style>
</head>
<body>
<div class="red font35">刘德华</div>
</body>
```

### 类选择器Demo
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .red {
            /*color 是字体的颜色*/
            color: aliceblue;
            width: 100px;
            height: 100px;
            background-color: red;
        }
        .green{
            width: 100px;
            height: 100px;
            background-color: green;
        }
    </style>
</head>
<body>
<div class="red">1</div>
<div class="green">2</div>
<div class="red">3</div>
</body>
```

### ID选择器
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        #red{
            color: red;
        }

    </style>
</head>
<body>
<div id="red">刘德华</div>
</body>
```

### 通配符选择器
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        * {
            color: red;
        }

    </style>
</head>
<body>

<div>我的</div>
<span>我的</span>
<ul>
    <li>还是我的</li>
</ul>
</body>
```

## 字体属性
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        /* 设置字体*/
        .ziti1 {font-family: system-ui;}

        .ziti2 {font-family: '微软雅黑';}

        /* 设置大小 */
        .div6{font-size: 35px;}

        /* 粗细 */
        .cuxi{font-weight: normal;}

        /* 样式 */
        .yangshi{font-style: italic;}

        /*复合属性 第一种写法 */
        .shuxing {
            font-style: italic;
            font-weight: 700;
            font-size: 16px;
            font-family: '微软雅黑';
        }


    </style>
</head>
<body>
<div class="ziti1">abcdefg</div>
<div class="ziti2">abcdefg</div>
<div class="div6">abcdefg</div>
<div class="cuxi">abcdefg</div>
<div class="yangshi">abcdefg</div>
<div class="shuxing">abcdefg</div>
</body>
```

## 文本属性
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        /* 文本颜色 */
        .yanse {
            /*color: red;*/
            /*color: #FF6000;*/
            color: rgb(255,0,0);
        }

        /* 对齐文本 */
        .duiqi{
            /* 只能让盒子里的文本水平对齐 */
            text-align: center;
        }

        /* 装饰文本,就是加一条线 */
        .jiaxian{
            /* 下划线*/
            /*text-decoration: underline;*/
            /* 删除线 */
            /*text-decoration: line-through;*/
            /* 上划线 */
            text-decoration: overline;
        }

        /* 文本缩进 */
        .suojin{

            /*text-indent: 20px;*/

            /* 缩进两个文字大小的距离 */
            text-indent: 2em;
        }

        /* 行间距 */
        .hangjianju{
            /* 上间距 +  字体大小 + 下间距 =26px*/
            line-height: 26px;
        }
        
    </style>
</head>
<body>
<div class="yanse">abcdefg</div>
<div class="duiqi">abcdefg</div>
<div class="jiaxian">abcdefg</div>
<div class="suojin">abcdefg</div>
<div class="hangjianju">abcdefg</div>

</body>
```

## 引入方式
内部样式:写在`<style>`里
行内:就是写在`<p>`这种标签里的
外部:就是引用单独的css文件

## Emmet语法
* 想生成多个标签:`div*3`
* 父子关系:`ui>li`
* 兄弟关系:`ui+li`
* 带class的,就用`.banne` 就会生成`<div class="banner">`
    * 用`p.demo`就会生成 `<p class="demo">`
    * 带顺序的  `p.demo$5` ,就会生成`<p class="demo1"> <p class="demo2">..<p class="demo5">`
* 生成时候字节带文本:`p{你好}` 就会生成 `<p>你好</p>`

## 复合选择器
### 后代选择器

后代选择器可以直接找孙子,不用通过儿子

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /* 后代选择器 */
        ol li {
            color: red;
        }

        ol div{
            color: yellow;
        }

    </style>
</head>
<body>
<ol>
    <li>我是ol的孩子</li>
    <li>我是ol的孩子</li>
    <li><div>我是ol的孩子</div></li>
</ol>

</body>
```

### 子选择器

只能找儿子

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /* 后代选择器 */
        ol li {
            color: red;
        }

        ol div{
            color: yellow;
        }

        ol > div{
            color: red;
        }

    </style>
</head>
<body>
<ol>
    <li>我是ol的孩子</li>
    <li><div>我是ol的孩子</div></li>
    <div>我是ol的儿子</div>
</ol>

</body>
```
### 复合选择器
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div,p{
            color: yellow;
        }

        div,p,ul>li{
            color: red;
        }

    </style>
</head>
<body>
<div>熊大</div>
<p>熊二</p>
<span>光头强</span>
<ul class="pig">
    <li>小猪佩奇</li>
    <li>猪爸爸</li>
    <li>猪妈妈</li>
</ul>
</body>
```
### 连接伪类选择器
* :link 未被访问的连接
* :visited 已经访问的连接
* :hover 鼠标放在上面的连接
* :active 鼠标按下没有谈起的连接
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /* 没有选中的连接 */
        a:link{
            /* 把颜色改成黑色 */
            color: #333333;
            /* 把连接的下划线去掉 */
            text-decoration: none;
        }

        /* 选中的过连接 */
        a:visited{
            color: orange;
        }

        /* 鼠标经过的连接设置成蓝色,不管这个连接有没有已经被点过 */
        a:hover{
            color: skyblue;
        }

        /* 点击按钮,按下没有松开 */
        a:active{
            color: green;
        }


    </style>
</head>
<body>

<a href="#">小猪佩奇</a>
</body>
```
### focus伪类选择器

获取焦点的`表单`选择器

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
       input:focus{
           background-color: skyblue;
           color: red;
       }
    </style>
</head>
<body>

<label for="name">用户名</label>
<input type="text" id="name">
</body>
```
## 元素显示模式
### 块元素
  * div ,h1 p ul ol li  一个占一行
  * 高度,宽度,外边距,内边距都可以控制
  * 宽度默认是容器的100%
  * 是一个容器及盒子,里面可以放行内或者块元素
  * `<p>`和`<h1>` 等用于存放`文字`的标签里面不能放`<div>`

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #a {
            background-color: skyblue;
        }

        #b {
            width: 200px;
            height: 200px;
            background-color: pink;
        }
        #c {
            background-color: pink;
        }
        .d{
            background-color: yellow;
        }

    </style>
</head>
<body>

<div id="a">块状元素,比较霸道,独占一行</div>瑟瑟发抖
<div id="b">块状元素,可以更改宽高</div>瑟瑟发抖
<div id="c">块状元素,宽度默认是容器的100%</div>瑟瑟发抖
<div style="background-color: skyblue">
    块状元素,里面可以放行内或者块元素
    <div class="d">里块元素</div>
    <span style="background-color: pink">行内元素1</span>
    <span style="background-color: gray">行内元素2</span>
</div>
</body>
```
### 行内元素
* span a strong b em i
*  一行可以用多个
*  宽高,设置无效,背景颜色设置有效
* 默认宽度就是它本身内容的宽度
* 行内元素只能放文本或者其他行内元素
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        span {
            /* 行内元素设置宽高无效*/
            width: 100px;
            height: 100px;
            background-color: red;
        }

    </style>
</head>
<body>
<span>中国</span>
<span>美国</span>
<!--宽度本身就是内容的本身-->
<span>中国1111111111111</span>
<span>美国222222222222222</span>
<span>美国2222222<em>123123123</em>22222222</span>
</body>
```

### 特殊情况

链接里面不能再放连接,`<a>`里面可以放块级元素

`<input> <img>`是行内款,一行可以给多个,也能设置宽高

### 元素显示模式的转换

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        a {
            width: 200px;
            height: 50px;
            background-color: orange;
            /* 将行内元素转成块状元素 */
            display: block;
        }
        #b {
            /* 把块元素转成行内元素 */
            display: initial;
        }

        span{
            width: 200px;
            height: 50px;
            background-color: orange;
            /* 将行内元素转成块状元素 */
            display: inline-block;
        }

    </style>
</head>
<body>
<a href="#">这是个按钮和超链接</a>

<div id="a">我是块级元素</div>1
<div id="b">我是块级元素</div>1
<div id="c">我是块级元素</div>1

<span>行内转速转成行内块元素</span>
<span>行内转速转成行内块元素</span>

</body>
```
### 元素显示模式Demo
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        a {
            width: 230px;
            /* 盒子的高度 */
            height: 40px;
            color: white;
            background-color: #55585a;
            display: block;
            text-decoration: none;
            text-indent: 2em;
            line-height: 40px;
        }

        a:hover{
            background-color: #ff6700;
        }

    </style>
</head>
<body>
<a href="">手机 电话卡</a>
<a href="">电视 盒子</a>
<a href="">笔记本 平板</a>
<a href="">出行 穿戴</a>
<a href="">职能 路由器</a>
<a href="">健康 儿童</a>
<a href="">耳机 音响</a>
</body>
```
## CSS背景
背景色默认是透明的
```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        /* 背景透明-默认值*/
        .a {background-color: transparent;}

        /* 背景图片*/
        .b {
            height: 300px;
            width: 600px;
            background-image: url("logo.png");
            /* 平铺 */
            background-repeat: no-repeat;
            /* 图片位置 */
            background-position: center center;
        }

        h3 {
            width: 150px;
            height: 40px;
            font-size: 14px;
            font-weight: normal;
            line-height: 40px;
            background-image: url("21_4f16e.png");
            background-position: left center;
            background-repeat: no-repeat;
            text-indent: 3.3em;
        }

        body{
            background-image: url("918481363662609.jpg");
            background-position: center top;
            background-repeat: no-repeat;
        }

        p {
            width: 200px;
            height: 200px;
            /* 最后一个参数是透明 */
            background: rgba(0,10,0,0.5);
            display: block;

        }

    </style>
</head>
<body>
<div class="a"></div>
<div class="b"></div>
<!-- 装饰类扸 不是用这种方式实现-->
<!--<h3><img src="21_4f16e.png"></img>成长守护平台</h3>-->
<!-- 装饰类图片用这种-->
<h3>成长守护平台</h3>
<p>1</p>

</body>
```

## CCS的三大特性
### 层叠性

就近原则的那个生效,有冲突的话,只是冲突相同的属性,不冲突的依然生效

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
       
        div{
            color: red;
            font-size: 16px;
        }
        
        div{
            color: yellow;
        }
    </style>
</head>
<body>
<!-- 最终生效的是  16px 和 yellow-->
<div>这个字体会变色</div>
</body>
```



### 继承性

子标签会继承父标签的样式,通常是继承文本属性

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body{
            color: red;
        }
    </style>
</head>
<body>
<div>这个字体会变色</div>
</body>
```



### 优先级

算了算了,我搞不定这,哈哈哈,我算知道就差不多了

![image-20200424163130735](https://image.yanganlin.com/blog/20200424163132.png)

## 盒子模型
### 边框

还有个表格边框合并的属性:`border-collapse:`两条边合并成一条边 

边框变改变原本盒子的大小

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .a {
            width: 300px;
            height: 200px;
            /* 边框的粗细 */
            border-width: 5px;
            /* 边框的样式 */
            border-style: dotted;
            /* 边框颜色 */
            border-color: red;
        }

        .b {
            width: 300px;
            height: 200px;
            /* 边框的粗细 */
            border-width: 5px;
            /* 边框的样式 */
            border-style: dotted;
            border-top-color: red;
            border-top-style: solid;
        }


    </style>
</head>
<body>
<div class="a"></div>
<hr>
<div class="b"></div>
</body>
```
```html

### 内边距

* 也会影响盒子的实际大小

* padding 值得顺序是 上 右 下 左
* 没有指定width,加了padding就不会改变盒子宽度

​```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .a {
            width: 300px;
            height: 200px;
            /* 边框的粗细 */
            border-width: 5px;
            /* 边框的样式 */
            border-style: dotted;
            /* 边框颜色 */
            border-color: red;
        }

        .b {
            width: 300px;
            height: 200px;
            /* 边框的样式 */
            border-style: solid;
            /* 边框颜色 */
            border-color: red;
            /* 内边距左边 */
            padding-left: 20px;
            /* 内边距上面 */
            padding-top: 30px;
        }

    </style>
</head>
<body>
<div class="a">你好的</div>
<div class="b">你好的</div>
```
### 外边距

margin的语法跟padding用法一样

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            width: 200px;
            height: 200px;
            background-color: red;

        }

        .one{
            margin-bottom: 10px;
        }

    </style>
</head>
<body>

<div class="one"></div>
<div></div>

</body>
```
#### 外边距应用-盒子居中

让盒子居中显示,我想写博客的中间的内容的东西

让盒子居中的前提条件是:

* 盒子必须指定了宽度
* 盒子左右的外边距都设置为auto

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .header {
            width: 900px;
            height: 200px;
            background-color: red;
            
            margin: 1px auto;
            /* 让行内元素或者行内块居中 这样才是起作用的 */
            text-align: center;

        }
        /* 这个是不起作用的,并不能让行内元素或者行内块居中,需要在父级添加 */
        span{
            text-align: center;
        }
    </style>
</head>
<body>
<div class="header">
    <span>里面的蚊子</span>
</div>

<div class="header">
    <img src="logo.png" alt="">
</div>
</body>
```
#### 相邻外边距合并

解决这种,尽量只给一个盒子添加外边距

![image-20200425120828983](https://image.yanganlin.com/blog/20200425120830.png)

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            width: 200px;
            height: 200px;
            background-color: black;
        }

        .a {
            margin-bottom: 100px;
        }

        .b {
            margin-top: 200px;
        }
        /* a和b 之间的最终边距是200px,而不是300px*/
    </style>
</head>
<body>

<div class="a"></div>
<div class="b"></div>
</body>
```

#### 嵌套外边距塌陷

在`浮动`,固定,绝对定位的盒子就不会有塌陷问题

![image-20200425122026566](https://image.yanganlin.com/blog/20200425122028.png)

```html
 <head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .father {
            width: 200px;
            height: 200px;
            background-color: black;
            /* 问题:第一步先让父盒子移动50px*/
            margin-top: 50px;
            /* 解决方法1: 给父元素定义上边框*/
            /*border-top: 1px solid transparent;*/
            /* 解决方法2:指定一个上内边距*/
            /*padding: 1px;*/
            /* 解决方法三:为父元素添加 overflow*/
            overflow: hidden;
        }
        .son{
            width: 100px;
            height: 100px;
            background-color: burlywood;
            /* 问题:第二步 又想让子盒子下移50px,就造成了塌陷的问题*/
            margin-top: 100px;
        }
    </style>
</head>
<body>
<div class="father">
    <div class="son"></div>
</div>
</body>
```

#### 清除内外边距

* 不同的浏览器都带有默认的内外边距

* 行内元素不要设置上下外边距,设置了也没有用

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /* 把所有的元素的内外边距都设置为0 */
        * {
            margin: 0 0;
        }
    </style>
</head>
<body>
123
</body>
```

### 圆角边框

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            height: 300px;
            width: 300px;
            background-color: black;
            border-radius: 10px;
        }
    </style>
</head>
<body>

<div></div>
</body>
```

### 盒子阴影

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            height: 300px;
            width: 300px;
            background-color: black;
            box-shadow: 10px 10px 10px 10px black;
        }
    </style>
</head>
<body>

<div></div>
</body>
```

### 文字阴影

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            text-shadow: 5px 5px 5px red;
        }
    </style>
</head>
<body>

<div>
    文字阴影
</div>
</body>
```

## 浮动

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            float: left;
            width: 150px;
            height: 200px;
            background-color: red;
            /* 可以让三个盒子并排排列,但是盒子之间会有缝隙 */
            /*display:inline-block;*/
        }
    </style>
</head>
<body>

<div>1</div>
<div>2</div>
<div>3</div>
</body>
```

### 标准流

就是按照标签规定好的默认方式排列

多个块级元素纵向排列找标准流,多个块级元素横向排列找浮动

加入了浮动之后:

* 浮动元素会脱离标准流

  * 浮动的盒子不会保留原先的位置,会让其他的标准流占有

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .box1 {
                float: left;
                width: 150px;
                height: 200px;
                background-color: red;
            }
    
            .box2 {
                width: 300px;
                height: 400px;
                background-color: burlywood;
            }
        </style>
    </head>
    <body>
    <div class="box1">1</div>
    <div class="box2">2</div>
    </body>
    ```

* 浮动的元素会一行内显示并且元素顶部对齐
* 浮动的元素会具有行内块元素的特性
* 行内元素添加浮动之后,就具有行内块元素相似的特性
* 浮动的盒子只会影响盒子后面的标准流,不会影响前面的标准流

### 布局Demo

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box {
            width: 1200px;
            height: 460px;
            background-color: pink;
            margin: 0 auto;
        }

        .div1 {
            float: left;
            width: 230px;
            height: 460px;
            background-color: purple;
        }

        .div2 {
            float: left;
            width: 970px;
            height: 460px;
            background-color: skyblue;
        }
    </style>
</head>
<body>

<div class="box">
    <div class="div1">左侧</div>
    <div class="div2">右侧</div>
</div>
</body>
```

### 清除浮动的原因

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box {
            width: 800px;
            border: 1px solid blue;
            margin: 0 auto;
        }

        .damao {
            float: left;
            width: 300px;
            height: 200px;
            background-color: purple;
        }

        .ermao {
            float: left;
            width: 200px;
            height: 200px;
            background-color: pink;
        }
        .footer {
            height: 200px;
            background-color: black;
        }
    </style>
</head>
<body>

<div class="box">
    <div class="damao">大毛</div>
    <div class="ermao">二毛</div>
</div>
<div class="footer"></div>
</body>
```

由于父级盒子很多情况下,不方便给高度,但是子盒子浮动又不占位置,最后父级盒子高度为0时,就会影响下面的标准被盒子

* 比如淘宝的一直往下托,就一直会与产品,这个时候就不方便给父盒子高度
* 比如新闻,有的新闻字数比较多,有的字数比较少,也不方便给告诉

如果不给高度,父级盒子就会变成一条线,子盒子会浮起来,然后父级盒子下面的标准元素,就会被浮起来的子元素挡住,如下图:

![image-20200427164126306](https://image.yanganlin.com/blog/20200427164129.png)

### 清除浮动的方法

* 额外标签法,是W3C推荐的做法

* 父级添加overflow属性

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .box {
                width: 800px;
                border: 1px solid blue;
                margin: 0 auto;
                /* 清除浮动 */
                overflow: hidden;
            }
    
            .damao {
                float: left;
                width: 300px;
                height: 200px;
                background-color: purple;
            }
    
            .ermao {
                float: left;
                width: 200px;
                height: 200px;
                background-color: pink;
            }
            .footer {
                height: 200px;
                background-color: black;
            }
        </style>
    </head>
    <body>
    
    <div class="box">
        <div class="damao">大毛</div>
        <div class="ermao">二毛</div>
    </div>
    <div class="footer"></div>
    </body>
    ```

  * 优点:代码简洁

  * 缺点:无法显示溢出的部分

* 父级添加after元素

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .clearfix:after {
                content: "";
                display: block;
                height: 0;
                clear: both;
                visibility: hidden;
            }
            .clearfix {
                /* IE6 ,7 专用*/
                *zoom: 1;
            }
    
            .box {
                width: 800px;
                border: 1px solid blue;
                margin: 0 auto;
            }
    
            .damao {
                float: left;
                width: 300px;
                height: 200px;
                background-color: purple;
            }
    
            .ermao {
                float: left;
                width: 200px;
                height: 200px;
                background-color: pink;
            }
            .footer {
                height: 200px;
                background-color: black;
            }
        </style>
    </head>
    <body>
    
    <div class="box clearfix">
        <div class="damao">大毛</div>
        <div class="ermao">二毛</div>
    </div>
    <div class="footer"></div>
    </body>
    ```

  * 优点:没有添加标签,结构更简单

  * 缺点:照顾低版本浏览器

* 父级添加双伪元素

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
    
            .clearfix:before,
            .clearfix:after {
                content: "";
                display: table;
            }
            .clearfix:after{
                clear: both;
            }
            .clearfix{
                *zoom:1
            }
    
            .box {
                width: 800px;
                border: 1px solid blue;
                margin: 0 auto;
            }
    
            .damao {
                float: left;
                width: 300px;
                height: 200px;
                background-color: purple;
            }
    
            .ermao {
                float: left;
                width: 200px;
                height: 200px;
                background-color: pink;
            }
            
            .footer {
                height: 200px;
                background-color: black;
            }
        </style>
    </head>
    <body>
    
    <div class="box clearfix">
        <div class="damao">大毛</div>
        <div class="ermao">二毛</div>
    </div>
    <div class="footer"></div>
    </body>
    ```

  * 优点:代码简单

  * 缺点:照顾低版本浏览器

  * 代表网站:小米,腾讯

## 定位

  标准流或者浮动无法快速实现,需要定位来实现

### 定位总结

| 定位模式         | 是否脱标       | 移动位置       | 是否常用   |
| ---------------- | -------------- | -------------- | ---------- |
| static静态定位   | 否             | 不能使用边偏移 | 很少       |
| relative相对定位 | 否(占有位置)   | 相对于自身偏移 | 常用       |
| absolute绝对定位 | 是(不占有位置) | 带有定位的父级 | 常用       |
| fixed固定定位    | 是(不占有位置) | 浏览器可视区   | 常用       |
| sticky粘性定位   | 否(占有位置)   | 浏览器可视区   | 当前阶段少 |

### 定位组成

  将盒子定再某一个位置,按照定位的方式移动盒子

  定位=定位模式+边偏移

  定位模式用于指定一个元素再文档中的定位方式,边偏移则决定了该元素的最终位置

### 定位模式

| 值       | 语义     |
| -------- | -------- |
| static   | 静态定位 |
| relative | 相对定位 |
| absolute | 绝对定位 |
| fixed    | 固定定位 |

### 边偏移

  边偏移就是定位的盒子移动到最终的位置,有top,bottom left 和 right4个属性

  是相对于父级盒子的偏移

### 静态定位`static`

  静态定位,是元素默认的定位方式,无定位的意思

  * 按照标准流的特性摆放位置它没有边偏移

### 相对定位`relative`

* 它是相对于自己原来的位置移动的

* 原来再标准流的位置继续占有,后面的盒子仍然以标准占位

* ```html
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <style>
  
          * {
              padding: 0;
              margin: 0;
          }
          div {
              width: 200px;
              height: 200px;
          }
  
          .box1 {
              position: relative;
              top: 100px;
              left: 100px;
              background-color: pink;
          }
  
          .box2{
              background-color: black;
          }
      </style>
  </head>
  <body>
  <div class="box1"></div>
  <div class="box2"></div>
  </body>
  ```

### 绝对定位`absolute`

元素在移动位置的时候,是相对于它祖先元素来说的

如果没有祖先元素或者祖先元素没有定位,则以浏览器为准定位

* 没有祖先元素

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            * {
                padding: 0;
                margin: 0;
            }
    
            div {
                width: 200px;
                height: 200px;
            }
    
            .box1 {
                position: absolute;
                top: 10px;
                left: 10px;
                background-color: pink;
            }
        </style>
    </head>
    <body>
    <div class="box1"></div>
    </body>
    ```

* 祖先元素没有定位

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
           .father {
               width: 500px;
               height: 500px;
               background-color: skyblue;
           }
    
            .son{
                position: absolute;
                left: 0;
                bottom: 0;
                width: 200px;
                height: 200px;
                background-color: pink;
            }
        </style>
    </head>
    <body>
    <div class="father">
        <div class="son"></div>
    </div>
    </body>
    ```

* 父级有定位

  * 祖先元素有定位(相对,绝对,固定定位),都可以作为参考点,并且是以最进一级的有定位i元素,如果爸爸没有定位,爷爷有定位,就以爷爷的为参考点

  * 绝对定位,像浮动一样,`不占有原来的位置`

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
           .father {
               position: relative;
               width: 500px;
               height: 500px;
               background-color: skyblue;
           }
    
            .son{
                position: absolute;
                left: 0;
                bottom: 0;
                width: 200px;
                height: 200px;
                background-color: pink;
            }
        </style>
    </head>
    <body>
    <div class="father">
        <div class="son"></div>
    </div>
    </body>
    ```

### 固定定位

  * 以浏览器的可视窗口为定位,不收滚动条的影响,也跟父级元素没有关系

  * 不占有原先的位置

*   固定定位是特殊的绝对定位

* 小技巧

  * ```html
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .w {
                width: 800px;
                height: 800px;
                background-color: pink;
                margin: 0 auto;
            }
          .fixed {
              position: fixed;
              /* 1.先浏览器宽度的一半 */
              left: 50%;
              /* 2.margin,走版心盒子宽度的一半*/
              margin-left: 405px;
              width: 50px;
              height: 50px;
              background-color: skyblue;
          }
        </style>
    </head>
    <body>
    <div class="fixed"></div>
    <div class="w">版心盒子,像素800</div>
    </body>
    ```

### 粘性定位

以浏览器的可视窗口为参考

占有原先的位置

必须添加top left right bottom中的一个才有效

### 定位叠放顺序

z-index 数值越大,盒子月考上

如果属性相同,则按照书写顺序,后来居上

只有定位的盒子才有z-index属性

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

        .box {
            height: 200px;
            width: 200px;
            position: absolute;
            top: 0;
            left: 0;
        }

        .xiongda{
            background-color: red;
            z-index: 1;
        }

        .xionger{
            background-color: green;
        }

        .qinangge{
            background-color: blue;
        }
    </style>
</head>
<body>
<div class="box xiongda">xiongda</div>
<div class="box xionger">xionger</div>
<div class="box qinangge">qinangge</div>
</body>
```

## 显示隐藏
### display

display:block 除了转换为块级元素之外,同时还有显示元素的意思

display:none 隐藏之后,也不会占用位置

### visibility

visibility:visible 元素可见

visibility:hidden 元素隐藏,但是占用位置

### overflow

| 属性值  | 描述                                      |
| ------- | ----------------------------------------- |
| visible | 不剪切内容也不添加滚动条                  |
| hidden  | 不显示超过对象尺寸的内容,超出的部分隐藏掉 |
| scroll  | 不管超出的内容否总是显示滚动条            |
| auto    | 超出自动显示滚动条,不超出不显示滚动条     |

## CSS高级

### 精灵图

background-position 

### 字体

# 好玩的

## 1.

![image-20200916094553973](https://raw.githubusercontent.com/YangAnLin/images/master/20200916094555.png)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #loading{
            position: absolute;
            left: 50%;
            top:50%;
            transform: translate(-50%,-50%);
        }

        #loading .gear1{
            position: relative;
            width: 100px;
            height: 100px;
        }

        #loading .gear1>div{
            position: absolute;
            width: 100px;
            height: 100px;
            top: 0;
            left: 0;
        }

        #loading .gear1 div:first-child{
            background: pink;
            transform: rotate(130deg);
        }

        #loading .gear1 div:nth-child(2){
            background: blueviolet;
            transform: rotate(240deg);
        }

        #loading .gear1 div:nth-child(3){
            background: yellowgreen
        }
    </style>
</head>
<body>
    <div id="loading">
        <div class="gear1">
            <div></div>
            <div></div>
            <div></div>
        </div>
    </div>
</body>
</html>
```

# Vue2.x教程

### v-text

设置标签里的内容,默认会替换全部内容

如果只是想替换部分内容,使用``{{message}}``

支持表达式

```html
<body>
    <div id="app" v-for>
        <h1 v-text="message">深圳</h1>
        <h1 v-text="message">{{message}}深圳</h1>
    </div>

    <!-- 开发环境版本，包含了有帮助的命令行警告 -->
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data: {
                message: 'Hello'
            }
        })
    </script>
</body>
```

### v-html

设置标签里的innerHTML

内容有html结构会被解析成标签,而`v-text`会解析成文本

```html
<body>
    <div id="app" v-for>
        <h1 v-text="message"></h1>
        <h1 v-html="message">{{message}}</h1>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data: {
                message: '<p2>黑马程序猿</p2>'
            }
        })
    </script>
</body>
```

### v-on事件绑定

`v-on:click` ==> `@click`

绑定按键事件,Vue还是有问题的,获取不到焦点,得手动去搞

`<input value="事件绑定" @keyup.enter="mydo">`

```html
<body>
    <div id="app">
        <input type="button" value="事件绑定" v-on:click="mydo">
        <input type="button" value="事件绑定缩写" @click="mydo">
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            methods: {
                mydo:function(){
                    alert("点击事件")
                }
            },
        })
    </script>
</body>
```

例子:

这里如果还没有学属性绑定,所以{{number}} 不能用input标签展示

```html
<body>
    <div id="app">
        <input type="button" value="-" @click="less">
        {{number}}
        <input type="button" value="+" v-on:click="add">
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data:{
                number:0
            },
            methods: {
                add:function(){
                    this.number++
                },
                less:function(){
                    this.number--
                }
            },
        })
    </script>
</body>
```

### v-show和v-if

v-show指令的作用:根据真假切换元素的显示状态,原理就是修改``display`

v-if 操作的dom树,如果隐藏了,就把节点删掉

```html
<body>
    <div id="app">
        <input type="button" value="v-show 切换" @click="mychange">
        <p v-show="show">xxx</p>
        <hr>
        <input type="button" value="v-if 切换" @click="mychange">
        <p v-if="show">xxx</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data:{
                show:true
            },
            methods: {
                mychange:function(){
                    this.show = !this.show
                
                }
            },
        })
    </script>
</body>
```

### v-bind 绑定元素属性

v-bind绑定元素里的属性,可以简写成`属性:value`

```html
<body>
    <div id="app">
        <input type="button" value="-" @click="less">
        <input type="text" v-bind:value="number">
        <input type="text" :value="number">
        <input type="button" value="+" v-on:click="add">
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data:{
                number:0
            },
            methods: {
                add:function(){
                    this.number++
                },
                less:function(){
                    this.number--
                }
            },
        })
    </script>
</body>
```

例子:

```html
<body>
    <div id="app" style="background-color: bisque;">
        <img :src="imgArr[index]">
        <br>

        <!-- 第一种实现方法 -->
        <button @click="prev" v-show="index != 0">上一张</button>
        <button @click="next" v-show="index != imgArr.length-1">下一张</button>

        <br>
        
        <!-- 第二种实现方法 -->
        <button @click="prev" :disabled="index == 0">上一张</button>
        <button @click="next" :disabled="index == imgArr.length-1">下一张</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data:{
                imgArr:["1.png","2.png","3.png"],
                index :0
            },
            methods: {
                prev:function(){
                    this.index--
                },
                next:function(){
                    this.index++
                }
            },
        })
    </script>
</body> 
```

### v-for

```html
<body>
    <div id="app" style="background-color: bisque;">
       <ul>
           <li v-for="(img,index) in imgArr">
               {{img}}-{{index}}
           </li>
       </ul>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data:{
                imgArr:["1.png","2.png","3.png"],
            },
            methods: {
                prev:function(){
                    this.index--
                },
                next:function(){
                    this.index++
                }
            },
        })
    </script>
</body>
```



### v-model

```html
<body>
    <div id="app">
        <input v-model:value="myvalue">
        {{ myvalue }}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        var app = new Vue({
            el: '#app',
            data:{
                myvalue:"1"
            },
            methods: {
                mydo:function(){
                    alert("点击事件")
                }
            },
        })
    </script>
</body>
```







# element-ui

## 级联选择器
新版本props的使用
级联选择器太高可以在全局样式里给.el-cascader-panel设置高度为200px
:props="{ expandTrigger: 'hover', value: 'cat_id', label: 'cat_name', children: 'children' }"



# Node.js

npm和node.js更换版本

```shell
# npm更换
npm install npm@6.10.1 -g 

# node更换
sudo npm install n -g
sudo n 版本号
n # 选择版本号
```

node.js升级

```shell
# 更新npm
npm install -g npm

# 清空npm缓存
npm cache clean -f

# 安装n模块
npm install -g n

# 升级node.js到最新稳定版
n stable

# 有时候需要sudo权限,或者安装好了之后要开启一个新的shell才能用到新的版本
```



  

  

  

  

  