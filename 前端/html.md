## 标签

##### 加粗

```
<strong>加粗</strong>
<b>加粗</b>
```

##### 段落

```
<p>sdhfskjdfdhfjkdsfkjdshkjhfkhkfdh</p>
<p>sdhfskjdfdhfjkdsfkjdshkjhfkhkfdh</p>
```

##### 换行

```
<br>
```

##### 水平线

```
<hr>
```

##### 倾斜

```
<em>倾斜</em>
<i>斜体</i>
```

##### 下划线

```
<ins>下划线</ins>
<u>下划线</u>
```

##### 删除线

```
<del>删除线</del>
<s>删除线</s>
```

##### 图像

```
<img src="D:\images\1.jpg" alt="图片">
```

##### 超链接

target="_blank" 跳转新窗口打开  href="#":空链接

```
<a href="https://www.baidu.com">跳转到百度</a>
<a href="1.jpg">跳转到猫娘</a>
<a href="01.html" target="_blank">跳转到01.html</a>
<a href="#">空链接</a>
```

##### 音频

 在html5里, 如果属性名和属性值完全一样, 可简写为一个单词

controls: 显示音频控制台 loop:循环播放 autoplay:自动播放,浏览器一般禁用

```
<audio src="a.mp3" controls loop>音频</audio>
```

##### 视频

muted:静音 autoplay:自动播放 为了提升用户体验,浏览器支持在静音状态下自动播放

```
<video src="1.mp4" controls loop muted autoplay></video>
```

##### 列表

无序列表

```
<ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>
```

有序列表

```
<ol>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ol>
```

定义列表

```
<dl>
    <dt>服务中心</dt>
    <dd>申请售后</dd>
    <dd>售后政策</dd>
    <dd>常见问题</dd>
</dl>
```

##### 表格

border: 双边框线 "1"表示边框线粗细为1像素

<thead>: 表头 

<tbody>: 表格主体

<tfoot>: 表格页脚

```
<table border="1">
    <thead>
        <tr>
            <th>姓名</th>
            <th>语文</th>
            <th>数学</th>
            <th>英语</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>张三</td>
            <td>80</td>
            <td>90</td>
            <td>85</td>
        </tr>
        <tr>
            <td>李四</td>
            <td>80</td>
            <td>90</td>
            <td>85</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td>总结</td>
            <td>全市第一</td>
            <td>全市第一</td>
            <td>全市第一</td>
        </tr>
    </tfoot>
</table>
```

##### 表单

 占位文本: placeholder

radio的checked表示默认选中

radio的name用于单选绑定

file的multiple用于上传多个文件

```
文本框(单行): <input type="text" placeholder="请输入内容"><br><br>
密码框: <input type="password" placeholder="请输入密码"><br><br>
性别: <input type="radio" checked name="gender">男 <input type="radio" name="gender">女 <br><br>
提交按钮: <input type="submit"><br><br>
多选框: <input type="checkbox" checked name="interest">游戏<input type="checkbox" checked name="interest">唱歌
上传单个文件: <input type="file"><br><br>
上传多个文件: <input type="file" multiple>
```

##### 下拉菜单

```
<select>
    <option>北京</option>
    <option selected>上海</option>
    <option>广州</option> 
</select> 
```

##### 文本域

```
<textarea>请输入文字</textarea>
```

##### label标签-增大点击范围

```
性别:
<input type="radio" id="man" name="gender">
<label for="man">男</label>
<input type="radio" id="women" name="gender">
<label for="women">女</label>
```

```
性别:
<label><input type="radio" name="gender2">男</label>
<label><input type="radio" name="gender2">女</label>
```

##### 按钮

action: 发送数据的地址

```
<form action="">
    用户名:<input type="text" id="username"><br>
    密码:<input type="password" id="password"><br>
    <button type="submit">提交</button>
    <button type="reset">重置</button>
    <button type="button">按钮</button>
</form>
```

##### 标签

```
<div>div 换行标签</div>
<div>div 换行标签</div>
<span>span 不换行标签</span>
<span>span 不换行标签</span>
```

##### 字符实体

```
空格: &nbsp;
<: &lt;
>: &gt;
```

```
小&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;红
<br>
&lt;p&gt;
```

##### 案例-注册页面

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>注册</title>
</head>
<body>
    <form action="http://www.baidu.com">
        <h1>注册信息</h1>
        <h3>个人信息</h3>
        姓名: <input type="text" placeholder="请输入姓名"><br><br>
        密码: <input type="text" placeholder="请输入密码"><br><br>
        确认密码: <input type="text" placeholder="请输入确认密码"><br><br>
        性别: <input type="radio" name="gender" id="man"><label for="man">男</label> 
            <input type="radio" name="gender" id="woman"><label for="woman">女</label><br><br>
        居住城市: 
        <select name="city">
            <option>北京</option>
            <option>上海</option>
            <option>广州</option>
        </select>
        <h3>教育经历</h3>
        最高学历:
        <select>
            <option>博士</option>
            <option>硕士</option>
            <option>本科</option>
            <option>高中</option>
            <option>高中以下</option>
        </select><br><br>
        学校名称: <input type="text" placeholder="请输入学校名称"><br><br>
        所学专业: <input type="text" placeholder="请输入所学专业"><br><br>
        在校时间: <input type="date" value="2021-09-06"> -- <input type="date" value="2025-06-30">
        <h3>工作经历</h3>
        公司名称: <input type="text"><br><br>
        工作描述: <br><textarea></textarea><br>
        <input type="checkbox" id="read"><label for="read">我已阅读并同意以下协议</label><br>
        <ul>
            <li><a href="http://www.baidu.com" target="_blank">用户服务协议</a></li>
            <li><a href="#">隐私政策</a></li>
        </ul>
        <button type="submit">免费注册</button>
        <button type="reset">重新填写</button>
    </form>    
</body>
</html>
