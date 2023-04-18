## 操作表单

用JavaScript操作表单和操作DOM是类似的，因为表单本身也是DOM树。

不过表单的输入框、下拉框等可以接收用户输入，所以用JavaScript来操作表单，可以获得用户输入的内容，或者对一个输入框设置新的内容。

HTML表单的输入控件主要有以下几种：

+ 文本框，对应的`<input type="text">`，用于输入文本；

+ 口令框，对应的`<input type="password">`，用于输入口令；

+ 单选框，对应的`<input type="radio">`，用于选择一项；

+ 复选框，对应的`<input type="checkbox">`，用于选择多项；

+ 下拉框，对应的`<select>`，用于选择一项；

+ 隐藏文本，对应的`<input type="hidden">`，用户不可见，但表单提交时会把隐藏文本发送到服务器。

### 1. 获取值
如果我们获得了一个`<input>`节点的引用，就可以直接调用value获得对应的用户输入值：
```
// <input type="text" id="email">
var input = document.getElementById('email');
input.value; // '用户输入的值'
```
这种方式可以应用于text、password、hidden以及select。但是，对于单选框和复选框，value属性返回的永远是HTML预设的值，而我们需要获得的实际是用户是否“勾上了”选项，所以应该用checked判断：
```
// <label><input type="radio" name="weekday" id="monday" value="1"> Monday</label>
// <label><input type="radio" name="weekday" id="tuesday" value="2"> Tuesday</label>
var mon = document.getElementById('monday');
var tue = document.getElementById('tuesday');
mon.value; // '1'
tue.value; // '2'
mon.checked; // true或者false
tue.checked; // true或者false
```
### 2. 设置值
设置值和获取值类似，对于text、password、hidden以及select，直接设置value就可以：
```
// <input type="text" id="email">
var input = document.getElementById('email');
input.value = 'test@example.com'; // 文本框的内容已更新
```
对于单选框和复选框，设置checked为true或false即可。

### 3. HTML5控件
HTML5新增了大量标准控件，常用的包括date、datetime、datetime-local、color等，它们都使用`<input>`标签：
```
<input type="date" value="2015-07-01">

2015/07/01
<input type="datetime-local" value="2015-07-01T02:03:04">

2015/07/01 02:03:04
<input type="color" value="#ff0000">
```
不支持HTML5的浏览器无法识别新的控件，会把它们当做type="text"来显示。支持HTML5的浏览器将获得格式化的字符串。例如，type="date"类型的input的value将保证是一个有效的YYYY-MM-DD格式的日期，或者空字符串。

### 4. 提交表单
最后，JavaScript可以以两种方式来处理表单的提交（AJAX方式在后面章节介绍）。

方式一是通过`<form>`元素的`submit()`方法提交一个表单，例如，响应一个`<button>`的click事件，在JavaScript代码中提交表单：
```
<!-- HTML -->
<form id="test-form">
    <input type="text" name="test">
    <button type="button" onclick="doSubmitForm()">Submit</button>
</form>

<script>
function doSubmitForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input...
    // 提交form:
    form.submit();
}
</script>
```
这种方式的缺点是扰乱了浏览器对form的正常提交。浏览器默认点击`<button type="submit">`时提交表单，或者用户在最后一个输入框按回车键。因此，第二种方式是响应`<form>`本身的onsubmit事件，在提交form时作修改：
```
<!-- HTML -->
<form id="test-form" onsubmit="return checkForm()">
    <input type="text" name="test">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input...
    // 继续下一步:
    return true;
}
</script>
```
注意要return true来告诉浏览器继续提交，如果return false，浏览器将不会继续提交form，这种情况通常对应用户输入有误，提示用户错误信息后终止提交form。

在检查和修改`<input>`时，要充分利用`<input type="hidden">`来传递数据。

例如，很多登录表单希望用户输入用户名和口令，但是，安全考虑，提交表单时不传输明文口令，而是口令的MD5。普通JavaScript开发人员会直接修改`<input>`：
```
<!-- HTML -->
<form id="login-form" method="post" onsubmit="return checkForm()">
    <input type="text" id="username" name="username">
    <input type="password" id="password" name="password">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var pwd = document.getElementById('password');
    // 把用户输入的明文变为MD5:
    pwd.value = toMD5(pwd.value);
    // 继续下一步:
    return true;
}
</script>
```
这个做法看上去没啥问题，但用户输入了口令提交时，口令框的显示会突然从几个*变成32个*（因为MD5有32个字符）。

要想不改变用户的输入，可以利用`<input type="hidden">`实现：
```
<!-- HTML -->
<form id="login-form" method="post" onsubmit="return checkForm()">
    <input type="text" id="username" name="username">
    <input type="password" id="input-password">
    <input type="hidden" id="md5-password" name="password">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var input_pwd = document.getElementById('input-password');
    var md5_pwd = document.getElementById('md5-password');
    // 把用户输入的明文变为MD5:
    md5_pwd.value = toMD5(input_pwd.value);
    // 继续下一步:
    return true;
}
</script>
```
注意到id为md5-password的`<input>`标记了name="password"，而用户输入的id为input-password的`<input>`没有name属性。没有name属性的`<input>`的数据不会被提交。

### 练习
利用JavaScript检查用户注册信息是否正确，在以下情况不满足时报错并阻止提交表单：

用户名必须是3-10位英文字母或数字；

口令必须是6-20位；

两次输入口令必须一致。
```
<!-- HTML结构 -->
<form id="test-register" action="#" target="_blank" onsubmit="return checkRegisterForm()">
    <p id="test-error" style="color:red"></p>
    <p>
        用户名: <input type="text" id="username" name="username">
    </p>
    <p>
        口令: <input type="password" id="password" name="password">
    </p>
    <p>
        重复口令: <input type="password" id="password-2">
    </p>
    <p>
        <button type="submit">提交</button> <button type="reset">重置</button>
    </p>
</form>
```
用户名: 

口令: 

重复口令: 

提交 重置
```
'use strict';
var checkRegisterForm = function () {
    // TODO:
    return false;
}

// 测试:
;(function () {
    window.testFormHandler = checkRegisterForm;
    var form = document.getElementById('test-register');
    if (form.dispatchEvent) {
        var event = new Event('submit', {
    		bubbles: true,
    		cancelable: true
  		});
        form.dispatchEvent(event);
    } else {
        form.fireEvent('onsubmit');
    }
})();
```

## 操作文件

在HTML表单中，可以上传文件的唯一控件就是`<input type="file">`。

注意：当一个表单包含`<input type="file">`时，表单的enctype必须指定为`multipart/form-data`，method必须指定为post，浏览器才能正确编码并以multipart/form-data格式发送表单的数据。

出于安全考虑，浏览器只允许用户点击`<input type="file">`来选择本地文件，用JavaScript对`<input type="file">`的value赋值是没有任何效果的。当用户选择了上传某个文件后，JavaScript也无法获得该文件的真实路径：

<input type="file">

### 待上传文件:

通常，上传的文件都由后台服务器处理，JavaScript可以在提交表单时对文件扩展名做检查，以便防止用户上传无效格式的文件：
```
var f = document.getElementById('test-file-upload');
var filename = f.value; // 'C:\fakepath\test.png'
if (!filename || !(filename.endsWith('.jpg') || filename.endsWith('.png') || filename.endsWith('.gif'))) {
    alert('Can only upload image file.');
    return false;
}
```
### File API
由于JavaScript对用户上传的文件操作非常有限，尤其是无法读取文件内容，使得很多需要操作文件的网页不得不用Flash这样的第三方插件来实现。

随着HTML5的普及，新增的File API允许JavaScript读取文件内容，获得更多的文件信息。

HTML5的File API提供了File和FileReader两个主要对象，可以获得文件信息并读取文件。

下面的例子演示了如何读取用户选取的图片文件，并在一个<div>中预览图像：

图片预览：

未选择任何文件
```
var
    fileInput = document.getElementById('test-image-file'),
    info = document.getElementById('test-file-info'),
    preview = document.getElementById('test-image-preview');
// 监听change事件:
fileInput.addEventListener('change', function () {
    // 清除背景图片:
    preview.style.backgroundImage = '';
    // 检查文件是否选择:
    if (!fileInput.value) {
        info.innerHTML = '没有选择文件';
        return;
    }
    // 获取File引用:
    var file = fileInput.files[0];
    // 获取File信息:
    info.innerHTML = '文件: ' + file.name + '<br>' +
                     '大小: ' + file.size + '<br>' +
                     '修改: ' + file.lastModifiedDate;
    if (file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
        alert('不是有效的图片文件!');
        return;
    }
    // 读取文件:
    var reader = new FileReader();
    reader.onload = function(e) {
        var
            data = e.target.result; // 'data:image/jpeg;base64,/9j/4AAQSk...(base64编码)...'            
        preview.style.backgroundImage = 'url(' + data + ')';
    };
    // 以DataURL的形式读取文件:
    reader.readAsDataURL(file);
});
```
上面的代码演示了如何通过HTML5的File API读取文件内容。以DataURL的形式读取到的文件是一个字符串，类似于`data:image/jpeg;base64,/9j/4AAQSk...(base64编码)...`，常用于设置图像。如果需要服务器端处理，把字符串base64,后面的字符发送给服务器并用Base64解码就可以得到原始文件的二进制内容。

### 回调
上面的代码还演示了JavaScript的一个重要的特性就是单线程执行模式。在JavaScript中，浏览器的JavaScript执行引擎在执行JavaScript代码时，总是以单线程模式执行，也就是说，任何时候，JavaScript代码都不可能同时有多于1个线程在执行。

你可能会问，单线程模式执行的JavaScript，如何处理多任务？

在JavaScript中，执行多任务实际上都是异步调用，比如上面的代码：
```
reader.readAsDataURL(file);
```
就会发起一个异步操作来读取文件内容。因为是异步操作，所以我们在JavaScript代码中就不知道什么时候操作结束，因此需要先设置一个回调函数：
```
reader.onload = function(e) {
    // 当文件读取完成后，自动调用此函数:
};
```
当文件读取完成后，JavaScript引擎将自动调用我们设置的回调函数。执行回调函数时，文件已经读取完毕，所以我们可以在回调函数内部安全地获得文件内容。

## Canvas

Canvas是HTML5新增的组件，它就像一块幕布，可以用JavaScript在上面绘制各种图表、动画等。

没有Canvas的年代，绘图只能借助Flash插件实现，页面不得不用JavaScript和Flash进行交互。有了Canvas，我们就再也不需要Flash了，直接使用JavaScript完成绘制。

一个Canvas定义了一个指定尺寸的矩形框，在这个范围内我们可以随意绘制：
```
<canvas id="test-canvas" width="300" height="200"></canvas>
```
由于浏览器对HTML5标准支持不一致，所以，通常在<canvas>内部添加一些说明性HTML代码，如果浏览器支持Canvas，它将忽略<canvas>内部的HTML，如果浏览器不支持Canvas，它将显示<canvas>内部的HTML：
```
<canvas id="test-stock" width="300" height="200">
    <p>Current Price: 25.51</p>
</canvas>
```
在使用Canvas前，用canvas.getContext来测试浏览器是否支持Canvas：
```
<!-- HTML代码 -->
<canvas id="test-canvas" width="200" heigth="100">
    <p>你的浏览器不支持Canvas</p>
</canvas>
'use strict';
var canvas = document.getElementById('test-canvas');
if (canvas.getContext) {
    console.log('你的浏览器支持Canvas!');
} else {
    console.log('你的浏览器不支持Canvas!');
}
```
 
getContext('2d')方法让我们拿到一个CanvasRenderingContext2D对象，所有的绘图操作都需要通过这个对象完成。
```
var ctx = canvas.getContext('2d');
```
如果需要绘制3D怎么办？HTML5还有一个WebGL规范，允许在Canvas中绘制3D图形：
```
gl = canvas.getContext("webgl");
```
本节我们只专注于绘制2D图形。

### 绘制形状
我们可以在Canvas上绘制各种形状。在绘制前，我们需要先了解一下Canvas的坐标系统：

![图裂了](./imgs/img02.png)

Canvas的坐标以左上角为原点，水平向右为X轴，垂直向下为Y轴，以像素为单位，所以每个点都是非负整数。

CanvasRenderingContext2D对象有若干方法来绘制图形：
```
'use strict';

var
    canvas = document.getElementById('test-shape-canvas'),
    ctx = canvas.getContext('2d');
ctx.clearRect(0, 0, 200, 200); // 擦除(0,0)位置大小为200x200的矩形，擦除的意思是把该区域变为透明
ctx.fillStyle = '#dddddd'; // 设置颜色
ctx.fillRect(10, 10, 130, 130); // 把(10,10)位置大小为130x130的矩形涂色
// 利用Path绘制复杂路径:
var path=new Path2D();
path.arc(75, 75, 50, 0, Math.PI*2, true);
path.moveTo(110,75);
path.arc(75, 75, 35, 0, Math.PI, false);
path.moveTo(65, 65);
path.arc(60, 65, 5, 0, Math.PI*2, true);
path.moveTo(95, 65);
path.arc(90, 65, 5, 0, Math.PI*2, true);
ctx.strokeStyle = '#0000ff';
ctx.stroke(path);
```
 
### 绘制文本
绘制文本就是在指定的位置输出文本，可以设置文本的字体、样式、阴影等，与CSS完全一致：
```
'use strict';

var
    canvas = document.getElementById('test-text-canvas'),
    ctx = canvas.getContext('2d');
ctx.clearRect(0, 0, canvas.width, canvas.height);
ctx.shadowOffsetX = 2;
ctx.shadowOffsetY = 2;
ctx.shadowBlur = 2;
ctx.shadowColor = '#666666';
ctx.font = '24px Arial';
ctx.fillStyle = '#333333';
ctx.fillText('带阴影的文字', 20, 40);
```
 
Canvas除了能绘制基本的形状和文本，还可以实现动画、缩放、各种滤镜和像素转换等高级操作。如果要实现非常复杂的操作，考虑以下优化方案：

通过创建一个不可见的Canvas来绘图，然后将最终绘制结果复制到页面的可见Canvas中；

尽量使用整数坐标而不是浮点数；

可以创建多个重叠的Canvas绘制不同的层，而不是在一个Canvas中绘制非常复杂的图；

背景图片如果不变可以直接用<img>标签并放到最底层。

### 练习
请根据从163获取的JSON数据绘制最近30个交易日的K线图，数据已处理为包含一组对象的数组：
```
'use strict';

window.loadStockData = function (r) {
    var
        NUMS = 30,
        data = r.data;
    if (data.length > NUMS) {
        data = data.slice(data.length - NUMS);
    }
    data = data.map(function (x) {
        return {
            date: x[0],
            open: x[1],
            close: x[2],
            high: x[3],
            low: x[4],
            vol: x[5],
            change: x[6]
        };
    });
    window.drawStock(data);
}

window.drawStock = function (data) {
    var
        canvas = document.getElementById('stock-canvas'),
        width = canvas.width,
        height = canvas.height,
        ctx = canvas.getContext('2d');
    console.log(JSON.stringify(data[0])); // {"date":"20150602","open":4844.7,"close":4910.53,"high":4911.57,"low":4797.55,"vol":62374809900,"change":1.69}
    ctx.clearRect(0, 0, width, height);
    ctx.fillText('Test Canvas', 10, 10);

};

// 加载最近30个交易日的K线图数据:
var js = document.createElement('script');
js.src = 'http://img1.money.126.net/data/hs/kline/day/history/2015/0000001.json?callback=loadStockData&t=' + Date.now();
document.getElementsByTagName('head')[0].appendChild(js);
```

