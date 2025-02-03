### 引入

- 内部样式表: <style>标签里面

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        p {
            /* 文字颜色 */
            color: red;
            /* 文字大小 */
            font-size: 20px;
            /* 文字粗细 */
            font-weight: bold;
            /* 文字斜体 */
            font-style: italic;
            /* 下划线 */
            text-decoration: underline;
        }
    </style>
</head>
<body>
    <p>css 初体验</p>
</body>
</html>
```

- 外部样式表: 单独.css文件, html使用link引入

  ```
  <link rel="stylesheet" href="my.css"> 放在<head>里面
  ```

- 行内样式

  ```
  <span style="color: chartreuse;">span标签</span>
  ```


### 选择器

- 标签选择器

```
p {
    color: red;
}
```

- 类选择器

  ```
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          .my-class {
              color: red;
          }
          p {
              color: blue;
          }
          .size {
              font-size: 50px;
          }
      </style>
  </head>
  <body>
      <p class="my-class">This is a paragraph.</p>
      <p>This is a paragraph.</p>
      <!-- 一个标签用多个类名 -->
      <div class="my-class size">This is a paragraph.</div>
  </body>
  </html>
  ```

- id选择器

  一般配合JavaScript使用, 很少用来设置css样式

  规则: 同一个id选择器在一个页面只能使用一次

  ```
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Document</title>
      <style>
          #p1 {
              color: red;
          }
      </style>
  </head>
  <body>
      <p id="p1">dfjkdks</p>
  </body>
  </html>
  ```

- 优先级

  类选择器 > 标签选择器

- 开发习惯

​	多个单词用-连接, eg: news-hd

- 通配符选择器

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            color: red;
        }
    </style>
</head>
<body>
    <p id="p1">dfjkdks</p>
    <div>jfkds</div>
    <span>dhfsjk</span>
    <ul>
        <li>l1</li>
        <li>l2</li>
        <li>l3</li>
    </ul>
</body>
</html>
```

### 画盒子

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .red {
            width: 50px;
            height: 50px;
            background-color: red;
        }
        .blue {
            width: 100px;
            height: 100px;
            background-color: blue;
        }
    </style>
</head>
<body>
    <div class="red"></div>
    <div class="blue"></div>
</body>
</html>
```

### 文字控制属性

```
/* 文字颜色 */
color: red;
/* 文字大小 */
font-size: 20px;
/* 文字粗细 */
font-weight: bold;
/* 文字斜体 */
font-style: italic;
/* 下划线 */
text-decoration: underline;
/* 行高 */
line-height: 1.5;
/* 字体族 */
font-family: 楷体;
/* 字体复合属性 */
font: italic bold 20px/1.5 fixed;
/* 文本缩进 */
text-indent: 2em;
/* 文本对齐 */
text-align: center;
/* 修饰线 */
text-decoration: line-through;
```

- 行高 line-height

  num px / num, num表示为font-size的num倍

  line-height = 上间距 + font-size + 下间距

- font复合属性的字号和字体值必须书写, 否则font属性不生效

  ```
  font: 是否倾斜 是否加粗 字号/行高 字体 (必须按顺序写)
  ```

  一般用于开发初期设置网页的公共样式

- 文本缩进

  text-indent: x px/em (px表示精确值, em表示当前标签的字号大小)

```
text-indent: 2em;
```

- 文本对齐

  rext-align: left/center/right

- 修饰线

```
a {
    text-decoration: none;  /* 去掉a标签的下划线 */
}
div {
    text-decoration: underline; /* 给div标签添加下划线 */
}
p {
    text-decoration: underline; /* 给p标签添加下划线 */
}
span {
    text-decoration: overline; /* 给span标签添加顶划线 */
}
```

- 颜色表示法

  | 颜色表示法 |        属性值        |                 说明                 |         使用场景         |
  | :--------: | :------------------: | :----------------------------------: | :----------------------: |
  | 颜色关键字 |       英文单词       |             red blue...              |         学习测试         |
  |    rgb     |     rgb(r, g, b)     | r,g,b表示红绿蓝三原色, 取值**0-255** |           了解           |
  |    rgba    | **rgba(r, g, b, a)** |     a表示**透明度**, 取值**0-1**     | 开发使用, 实现**透明色** |
  |  十六进制  |     #**RRGGBB**      |   #000000 #ffcc00 简写: #000 #fc0    |  开发使用(从设计稿复制)  |

  
