---
title: HTML5&CSS基础
date: YYYY-MM-DD HH:mm:ss
categories: 
- web开发
tags:
- HTML5新增标签
    - 1.结构性标签
    - 2.块级性标签
    - 3.行内语义性标签
    - 4.交互性标签
- HTML的标签类型：
- 如何修改标签显示类型
- CSS层叠样式表：
  - 行内样式：
  - 页内样式：
  - 外部样式：
- CSS选择器
- 选择器优先级排序
- CSS盒子模型
- CSS3 新增属性
- CSS布局
  - 标准流布局
  - 脱离标准流的方法有
- 水平居中
- 垂直居中
- box-sizing属性主要用于input标签
---
# HTML5新增标签

HTML5新增了27个标签元素,废弃了16个标签元素,主要包括 结构性标签、级块性标签、行内语义性标签、交互性标签

### 1.结构性标签

负责Web上下文结构的定义，确保HTML文档，包括：

- article 文章主体内容(一篇博客、一篇论坛帖子、一段用户评论、插件)
- header 标记头部区域内容
- footer 标记脚部区域内容
- section 区域章节表述
- nav 菜单导航,链接导航

### 2.块级性标签

完成Web页面区域的划分，确保内容的有效分隔，包括：

- aside 注记,贴士,侧栏,摘要,插入的引用作为补充主体的内容
- figure 对多个元素组合并展示的元素,常与figcaption联合使用
- code 表示一段代码块
- dialog 人与人之间对话,包含dt和dd两个组合元素（dt用于表示说话者、dd用于表示说话者的内容）

### 3.行内语义性标签

完成Web页面具体内容的引用和表述，丰富展示内容，包括：

- meter 特定范围内的数值，如工资、数量、百分比
- time 时间值
- progress 进度条，可用max、min、step进行控制，完成对进度的表示和监听
- video 视频元素，用于视频播放，支持缓冲预载和多种视频媒体格式
- audio 音频元素，用于音频播放，支持缓冲预载和多种音频媒体格式

### 4.交互性标签

功能性内容的表达，有一定的内容和数据的关联，是各种事件的基础，包括：

- details 表示一段具体的内容，默认不显示，通过某种方式（单击）与legend交互才会显示
- datagrid 控制客户端数据与显示，可用于动态脚本及时更新
- menu 用于交互菜单
- command 用来处理命令按钮

# HTML的标签类型：

- 块级标签：

1、独占一行
2、能随时设置宽度和高度，比如div、p、h1、h2、ul、li

- 行内标签

1、多个行内标签同时显示在一行上
2、宽度和高度取决于内容的尺寸，比如span、a、label

- 行内-块级标签

1、多个行内-块级标签可以显示在同一行
2、能随时设置高度和宽度，比如input、button

# 如何修改标签显示类型

设置CSS的display属性

- none：隐藏标签
- block：让标签变成块级标签
- inline：让标签变成行内标签
- inline-block：让标签变成行内-块级标签

# CSS层叠样式表：

CSS编写格式是键值对，CSS书写形式：

## 行内样式：

(内联样式)直接写在标签的style属性中

```
<div style="color: red"></div>
```



## 页内样式：

在本网页的style标签中书写

```
<style>
    body {
        color: red;
    }
</style>
```



## 外部样式：

```
<link rel="stylesheet" href="index.css">
```

# CSS选择器

- 标签选择器 div { … }
- 类选择器 .class { … }
- id选择器 #id { … }
- 并列选择器 div, a { … }
- 复合选择器 div.class { … }
- 后代选择器 div a { … }
- 直接后代选择器 div > p { … }
- 相邻兄弟选择器 div + p { … }
- 属性选择器 div[name] { … } 或 div[name][age] { … } 或 div[name=”jack”] { … }
- 伪类

|     属性     |                  描述                  |
| :----------: | :------------------------------------: |
|   :active    |         向被激活的元素添加样式         |
|    :focus    |    向拥有键盘输入焦点的元素添加样式    |
|    :hover    | 当鼠标悬浮在元素上方时，向元素添加样式 |
|    :link     |        向未被访问的链接添加样式        |
|   :visited   |        向已被访问的链接添加样式        |
| :first-child |      向元素的第一个子元素添加样式      |
|    :lang     |    向带有指定lang属性的元素添加样式    |

- 伪元素

|     属性      |              描述              |
| :-----------: | :----------------------------: |
| :first-letter | 向文本的第一个字母添加特殊样式 |
|  :first-line  |    向文本的首行添加特殊样式    |
|    :before    |       在元素之前添加内容       |
|    :after     |       在元素之后添加内容       |

# 选择器优先级排序

important > 内联 > id > 类 > 标签 | 伪类 | 属性选择 > 伪元素 > 通配符 > 继承

# CSS盒子模型

- 内容 content
- 填充 padding 内边距
- 边框 border
- 边界 margin 外边距

标准盒子模型中CSS属性width和height都是针对content的。

```
|------------------------------|
|	  margin               |
| |--------------------------| |
| |         border           | |
| | |----------------------| | |
| | |       padding        | | |
| | | |------------------| | | |
| | | |     content      | | | |
| | | |------------------| | | |
| | |----------------------| | |
| |--------------------------| |
|------------------------------|
         上   右   下   左
padding: 10px 5px 15px 20px;
         上下  左右
padding: 10px 5px;
         上   左右  下
padding: 10px 5px 15px;
         上下左右
padding: 10px;
```

边框设置：

- border-width
- border-style
- border-color
- border-radius 设置边框圆角

# CSS3 新增属性

- RGBA透明度

RGB(红色R+绿色G+蓝色B),RGBA则在其基础上增加了Alpha通道，可用于设置透明值

- 块阴影与圆角阴影

box-shadow text-shadow

- 圆角

border-radius

- 边框图片

border-image

- 形变

```
transform: none | <transform-function>[<transform-fuction>]
```

# CSS布局

## 标准流布局

默认情况下，所有的网页标签都在标准流布局中

从上到下，从左到右

## 脱离标准流的方法有

- float属性

left：脱离标准流，浮动在父标签的最左边
right：脱离标准流，浮动在父标签的最右边

- position属性 和 left、right、top、bottom属性

|    值    |                             描述                             |
| :------: | :----------------------------------------------------------: |
| absolute | 生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。元素的位置通过 “left”, “top”, “right” 以及 “bottom” 属性进行规定。 |
|  fixed   | 生成绝对定位的元素，相对于浏览器窗口进行定位。元素的位置通过 “left”, “top”, “right” 以及 “bottom” 属性进行规定。 |
| relative | 生成相对定位的元素，相对于其正常位置进行定位。因此，”left:20” 会向元素的 LEFT 位置添加 20 像素。 |
|  static  | 默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。 |
| inherit  |           规定应该从父元素继承 position 属性的值。           |

# 水平居中

适用行内标签和行内-块级标签
text-align: center

适用块级标签
margin:0 auto

# 垂直居中

- 方法一

line-height == height

- 方法二：定位

父标签：position: relative
子标签：position: absolute
top: 50%
left: 50%
transform: translate(-50%, -50%)

# box-sizing属性主要用于input标签

以特定的方式定义匹配某个区域的特定元素，默认为content-box
将box-sizing设置为 “border-box”，可以把边框和内边距放入框中。