# 常用元素

## 文本

```html
<h1>这是一个 h1 标题</h1>
<h2>这是一个 h2 标题</h2>
<h3>这是一个 h3 标题</h3>
<h4>这是一个 h4 标题</h4>
<h5>这是一个 h5 标题</h5>
<h6>这是一个 h6 标题</h6>

<p>段落1，测试测试</p>
<p>段落2，br标签后面插入换行，但仍属于同一段落
    <br>
    测试测试<strong>此处加粗</strong>测试测试
    <br>
    测试测试<em>此处斜体</em>测试测试
    <br>
    测试测试<u>此处加下划线</u>测试测试
    <br>
    测试测试<del>此处加删除线</del>测试测试
    <br>
    测试测试<mark>此处高亮</mark>测试测试
</p>
<p>段落3，这是一个<sub>下标</sub>和<sup>上标</sup>的例子</p>
```

![20240304110315](https://image.zuoright.com/20240304110315.png)

## 列表

```html
<!-- 无序列表 Unordered list -->
<ul>
    <li>BTC</li>
    <li>ETH</li>
    <li>SOL</li>
</ul>

<!-- 有序列表 Ordered list -->
<ol>
    <li>BTC</li>
    <li>ETH</li>
    <li>SOL</li>
</ol>

<!-- 定义列表 Definition list -->
<dl>
    <dt>Title</dt>
    <dd>desc1</dd>
    <dd>desc2</dd>

    <dt>Title</dt>
    <dd>desc1</dd>
    <dd>desc2</dd>
</dl>
```

![20240304111612](https://image.zuoright.com/20240304111612.png)

相同或不同列表间可以互相嵌套，需要注意的是，子列表要嵌套在父列表的列表项中

```html
<ul>
    <li>水果
		<ul>
			<li>苹果</li>
			<li>香蕉</li>
			<li>橙子
				<ul>
					<li>纽瓦尔</li>
					<li>瓦伦西亚</li>
				</ul>
			</li>
		</ul>
    </li>
</ul>
```

![20240304123758](https://image.zuoright.com/20240304123758.png)

```html
<ul>
    <li>水果
		<!-- 在无序列表项内嵌套有序列表 -->
		<ol>
			<li>苹果</li>
			<li>香蕉</li>
			<li>橘子</li>
		</ol>
    </li>
    <li>蔬菜
		<!-- 在无序列表项内嵌套有序列表 -->
		<ol>
			<li>胡萝卜</li>
			<li>西红柿</li>
			<li>菠菜</li>
		</ol>
    </li>
</ul>

<hr>

<ol>
    <li>做饭步骤
		<!-- 在有序列表项内嵌套无序列表 -->
		<ul>
			<li>准备食材</li>
			<li>烹饪食材</li>
			<li>装盘</li>
		</ul>
    </li>
    <li>烘焙蛋糕
      	<!-- 在有序列表项内嵌套无序列表 -->
    	<ul>
			<li>预热烤箱</li>
			<li>混合原料</li>
			<li>烘烤</li>
      	</ul>
    </li>
</ol>

<hr>

<dl>
    <dt>术语1</dt>
    <dd>
	术语1的定义
		<ul>
			<li>无序列表项</li>
			<li>无序列表项</li>
			<li>无序列表项</li>
		</ul>
    </dd>

    <dt>术语2</dt>
    <dd>
    术语2的定义
		<ol>
			<li>有序列表项</li>
			<li>有序列表项</li>
			<li>有序列表项</li>
		</ol>
    </dd>
</dl>
```

![20240304122947](https://image.zuoright.com/20240304122947.png)

## 表格

```html
<!DOCTYPE html>
<html>
<head>
    <title>表格示例</title>
    <style>
        table, th, td {
            border: 1px solid black;  /* 指定某一条边，比如下边：border-bottom */
            border-collapse: collapse;  /* 表格边框是否合并：separate(默认), collapse */
        }
        th, td {
            padding: 10px;
            text-align: center;  /* 表格文本水平对其方式：left(默认), center, right */
            vertical-align: center;  /* 表格文本垂直对其方式：top, center(默认), bottom */
        }
		/* hover 表格某一横行时高亮显示，不设置的话默认没有 */
        tr:hover {
          background-color: #f5f5f5;
        }
    </style>
</head>
<body>

<h2>用户信息表</h2>

<!-- 可以不加 thead、tfoot、tbody -->
<table>
	<!-- 表头 -->
	<thead>
		<tr>
			<!-- 表头这里要使用：<th> -->
			<th>姓名</th>
			<th>性别</th>
			<th>年龄</th>
			<th>身高</th>
			<th>语文</th>
			<th>数学</th>
		</tr>
	</thead>
	<!-- 表尾 -->
	<tfoot>
		<tr>
			<td>统计</td>
			<td colspan="3">--</td>  <!-- 横向合并3个单元格 -->
			<!-- <td>--</td> -->
			<!-- <td>--</td> -->
			<td>220</td>
			<td>240</td>
		</tr>
	</tfoot>
	<!-- 表身 -->
	<tbody>
		<tr>
			<td>张三</td>
			<td>男</td>
			<td>28</td>
			<td>170</td>
			<td rowspan="2">70</td>  <!-- 纵向合并2个单元格 -->
			<td>75</td>
		</tr>
		<tr>
			<td>李四</td>
			<td>女</td>
			<td>25</td>
			<td>165</td>
			<!-- <td>70</td> -->
			<td>85</td>
		</tr>
		<tr>
			<td>王五</td>
			<td>男</td>
			<td>30</td>
			<td>180</td>
			<td>80</td>
			<td>80</td>
		</tr>
	</tbody>
</table>
</body>
</html>
```

![20240306162605](https://image.zuoright.com/20240306162605.png)

## 表单

使用 `<form>` 来创建表单，当用户完成数据输入并点击提交按钮时，浏览器会收集表单内所有控件的 `name` 和 `value` 属性，将它们作为请求参数发送到表单的 `action` 属性指定的路由。

```html
<!DOCTYPE html>
<html>
<body>
<h2>表单示例</h2>
<form action="/submit-your-form" method="post">
	<!-- 单行文本 -->
    <label for="fname">名字：</label>
    <input type="text" id="fname" name="firstname" placeholder="你的名字...">
    <br><br>

    <label for="lname">姓氏：</label>
    <input type="text" id="lname" name="lastname" placeholder="你的姓氏...">
    <br><br>

    <label for="email">邮箱：</label>
    <input type="email" id="email" name="email" placeholder="你的邮箱...">
    <br><br>

    <label for="password">密码：</label>
    <input type="password" id="password" name="password" placeholder="设置密码...">
    <br><br>

	<!-- 单选框 -->
	性别：
	<label for="male">男</label>
    <input type="radio" id="male" name="gender" value="male">
    <label for="female">女</label>
    <input type="radio" id="female" name="gender" value="female">
    <br><br>

	<!-- 复选框 -->
    <input type="checkbox" id="subscribe" name="subscribe" value="newsletter">
    <label for="subscribe">订阅新闻邮件</label>
    <br><br>

	<!-- 下拉选择框 -->
    <label for="country">国家：</label>
    <select id="country" name="country">
        <option value="none" selected>选择国家</option>
        <option value="china">中国</option>
        <option value="usa">美国</option>
        <option value="uk">英国</option>
    </select>
    <br><br>

	<!-- 多行文本 -->
	<label for="note">自我介绍：</label>
	<textarea id="note" name="note" rows="4" cols="15"></textarea>
	<br><br>

    <input type="submit" value="提交">
</form>
</body>
</html>
```

![20240307115246](https://image.zuoright.com/20240307115246.png)

### form

```text
action: 提交表单后，数据发送到的位置，不指定则默认发送到当前页面
method: 定义提交表单时使用的HTTP方法
	get
	post
	...
enctype: 使用POST方法提交表单时定义数据的编码类型
	application/x-www-form-urlencoded 默认，所有支付都会被编码，空格转换为+号，特殊符号转换为ASCII HEX值
	text/plain 数据以纯文本形式发送，不对特殊字符编码，但空格转换为+号，主要用于调试
	multipart/form-data 用于发送包含文件上传的表单，不对字符编码
target: 提交表单后，响应返回的位置
	_self 默认，在当前页打开
	_blank 在新标签页打开响应结果
	_parent 在父框架中加载，不存在则等同于_self
	_top 在整个窗口中加载，替换掉所有的框架结构
	在指定 name 框架中加载
```

### label

用于定义用户界面中表单项的标签，点击标签会将焦点移至并激活关联的控件，使表单更易于点击，提高表单的可用性和无障碍访问性。

```html
<form>
    <!-- <label> 的 for 属性应与表单控件的 id 属性相匹配，这样就建立了它们之间的联系 -->
    <label for="email">电子邮件：</label>
    <input type="email" id="email" name="email">
    <br>
    
	<!-- 将表单控件直接放在 <label> 元素内部也可以建立关联 -->
    <label>
        密码：
        <input type="password" name="password">
    </label>
    <br>

    <input type="submit" value="提交">
</form>
```

### input

`<input type=text name="" value="">`

```text
type: 创建不同类型的输入字段
	text: 单行文本，用户可以输入文本
	password: 密码输入框，输入的文本会被掩码处理
	radio: 单选框，用户只能从一组选项中选择一个
	checkbox: 复选框，用户可以从一组选项中选择多个
	email: 专门用于输入电子邮件地址，有基本的格式验证
	number: 允许用户输入数字，可以设置最大、最小值和步长
	date: 允许用户选择日期，显示为日历形式
	url: 用于输入网址，有基本的格式验证
	color: 颜色选择器，用户可以选择颜色
	file: 允许用户选择一个或多个文件从他们的存储设备上传
	hidden: 隐藏的输入字段，用户不可见，但表单数据会被发送到服务器
name: 字段名称，用于在表单提交时标识不同的输入字段
value: 输入字段的值，也可以是按钮上的文字
placeholder: 在输入字段为空时显示的提示文本
min 和 max: 为 number、date 等类型的输入定义可接受的最小和最大值
step: 为 number 类型的输入定义合法的数字间隔
pattern: 定义一个正则表达式，输入字段的值必须与之匹配
autofocus 页面加载完后自动获得焦点
autocomplete:
	on 默认，点击时浏览器基于用户之前输入的值提供完成建议
	off 禁止
required: 必填，提交表单时必须输入值
readonly: 只读，用户不能修改，但值会被提交
disabled: 禁用该字段，其值也不会被提交到服务器
```

## 按钮

`<input>` 和 `<button>` 标签均可创建按钮

```html
<!-- input 属于空元素，按钮上显示的文本由 value 属性指定，通常用于简单的提交操作 -->
<input type="button" value="Click Me" onclick="alert('Hello!')">

<!-- button 属于容器元素，可以包含图标或文本等其他元素，更加灵活 -->
<button type="button" onclick="alert('Hello!')">
	Click Me
</button>
```

type 属性都有三个值

```text
reset: 重置按钮，将表单的所有字段重置为其初始值
submit: 提交按钮，用于将表单数据发送到服务器
button: 没有默认行为，通常用于执行脚本代码
```

其他属性

```text
onclick：当用户点击按钮时触发
onfocus：当按钮获得焦点时触发
onblur：当按钮失去焦点时触发
onmouseover：当鼠标指针移动到按钮上时触发
onmouseout：当鼠标指针移出按钮时触发
onmousedown、onmouseup：分别在用户按下和释放鼠标按钮时触发
```

## 超链接

- `<base>`

放在 head 中，用于指定页面中所有相对 URL 的基础 URL，一个文档中只能有一个

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document with Base Tag</title>
    <base href="https://www.example.com/" target="_blank">
</head>
<body>
    <p>This is a paragraph with a link to a 
		<a href="docs/document.html">document</a>.
	</p>
</body>
</html>
```

- 普通链接

```html
<!-- hover 时显示 title内容 -->
<a href="https://zuoright.com/" title="demo" target="_blank">ZuoRight</a>
```

- 锚点链接

```html
<!-- 使用 name 或 id 属性来设置锚点 -->
<a name="section1"></a>
<a href="#section1">跳转到指定锚点</a>
```

- 邮箱链接

```html
<a href="mailto:example@demo.com">发送邮件</a>
```

- 下载链接

```html
<a href="https://xxx.demo/img.png" download="logo">下载</a>
```

## 图片

可以设置图片的宽度和高度，但为了不影响图片原本的比例，通常只设置其中一个，另一个自动

```html
<img src="https://demo.com/img.png" width="200" alt="图片加载失败时显示"/>

<!-- 图片还可以加链接 -->
<a href="https://google.com/">
	<img src="https://demo.com/img.png" class="img" alt="logo" />
</a>
```

图片默认是行内元素，可以设置 `display: block` 变为块级元素

另外图片默认有 5px 边距

```css
img {
	display: block;
	margin: 0 auto;  /* 块级元素时居中 */
	width: 200px;
	height: 100px;
	opacity: 0.5;  /* 设置透明度，0全透明，0.5半透明 */
	border-radius: 8px;  /* 圆角，设置50%为圆形 */
	box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);  /* 阴影 */
}

img {
	text-align: center;  /* 行内元素时居中 */
}
```

## 富媒体

HTML5 新增了一些富媒体元素，意味着不需要再装 Flash 等插件即可操作媒体文件，极大的增强了用户体验。

### 音视频

`<audio>` 和 `<video>` 分别用于嵌入音频和视频文件，并提供控件来控制播放。

共有属性

其中 `controls`, `autoplay`, `loop`, `muted` 都是布尔值，设置则有效，不设置（即为空置）则无效

```text
src: 指定媒体文件的URL
controls: 显示浏览器提供的媒体控制面板，允许用户播放、暂停、调整音量、跳转等操作。
autoplay: 自动播放媒体（谷歌浏览器中禁止）
loop: 循环播放媒体
muted: 将媒体的音量设置为静音。
preload: 指示浏览器是否应该在页面加载时预加载媒体
	auto 自动预加载
	metadata 仅预加载元数据
	none 不预加载
```

如果有多个不同格式的源可以使用 `<source>` 标签指定多个音频或视频源，浏览器将会遍历这些源直到找到一个合适的格式，都不支持则显示 audio 元素中间的文本。

```html
<audio controls>
	<source src="audio.mp3" type="audio/mpeg"/>
	<source src="audio.ogg" type="audio/ogg"/>
	Your browser does not support the audio tag.
</audio>

<video controls width="500">
	<source src="movie.mp4" type="video/mp4"/>
	<source src="movie.ogg" type="video/ogg"/>
	Your browser does not support the video tag.
</video>
```

`<audio> ` 特有属性

```text
controlsList: 指定显示的控制面板
currentTime: 获取或设置当前播放的时间
duration: 媒体文件的总时长
paused: 返回媒体是否处于暂停状态
```

`<video> ` 特有属性

```text
poster: 指定视频加载时显示的图片，可以是视频的封面。
width、height: 指定视频的宽度和高度。
videoWidth、videoHeight: 视频的实际宽度和高度。
playsInline: 在iOS设备上，指示视频是否应该在内联播放（在页面中直接播放），而不是全屏播放。
```

音频标签更多的是通过标签自带属性来控制

视频标签可设置的一些 css 属性

```css
/* 去除进度条 */
video:-webkit-media-controls-timeline {
	display: none;
}

/* 去除观看的当前时间 */
video:-webkit-media-controls-current-time-display {
	display: none;
}

/* 去除剩余时间 */
video:-webkit-media-controls-time-remaining-display {
	display: none;
}

/* 去掉全部控制控件 */
video:-webkit-media-controls {
	display: none !important;
}
```

### canvas

用于绘制图形，动画以及进行图形处理，为图像提供了一个可编程的环境。

```html
<canvas id="myCanvas" width="200" height="100" style="border:1px solid #000;">
	Your browser does not support the HTML5 canvas tag.
</canvas>

<script>
	var canvas = document.getElementById("myCanvas");
	var ctx = canvas.getContext("2d");
	ctx.fillStyle = "#000000";
	ctx.fillRect(0, 0, 80, 80);
</script>
```

### embed

用于嵌入外部资源，如 PDF、Flash 等

```html
<embed src="document.pdf" type="application/pdf" width="600" height="400">
```
