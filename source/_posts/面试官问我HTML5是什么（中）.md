---
title: 面试官问我HTML5是什么（中）
date: 2019-05-25  09:30:54
tags: HTML5
categories: 
	- 前端面试


---

学习链接：
[HTML 5与CSS 3权威指南](https://read.douban.com/ebook/15160963/)

[W3cScholl](https://www.w3cschool.cn/html/)

## Canvas

HTML5 中的一个新增元素，可以在页面绘制出各种漂亮的图形与图像。

### 绘制图形

canvas 元素就是 HTML5 中新增的一个用来绘制图形。在页面上放置一个 `canvas`元素，相当于在页面上放置一块画布，可以在其中进行图形绘制。

### 在页面中放置 canvas元素

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Canvas</title>
</head>

<body onload="draw('canvas')">
  <canvas id="canvas" width="400" height="300"></canvas>
  <script src="canvas.js"></script>
</body>

</html>
<!--script-->
<script>
function draw(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  context.fillStyle = 'red';
  context.strokeStyle = 'blue';
  context.lineWidth = 1;
  context.fillRect(50,50,100,100);
  context.strokeRect(50,50,100,100);
}
</script>
```

首先，要指定 ID,width,height 这三个属性。

#### 绘制矩形

1. 取得 canvas 元素，用 DOM 方法取得要绘制的目标元素
2. 取得上下文（context）。进行绘制的时候需要用到图形上下文（graphics context），图形上下文是一个封装了很多绘图功能的对象。使用 canvas 对象的 `getContext`方法来获得图形上下文。
3. 填充与绘制边框。绘制图形的时候有两种方式：填充（fill）与绘制边框（stroke）。填充是指填充图形内部，绘制边框是指不填充图形内部，值绘制图形的外框。
4. 设定绘图样式（style）。在进行图形绘制的时候，要设定好绘图的样式，然后调用相关的方法进行图形绘制。相关的属性有 fillStyle(填充图形的样式)，strokeStyle(图形边框的样式)
5. 指定线宽。使用图形上下文对象的 lineWidth 属性设置图形边框的宽度。
6. 指定颜色值。颜色名，或者十六进制的颜色值
7. 分别使用 fillRect 与 strokeRect 方法来填充矩形和绘制矩形边框。

```javascript
context.fillRect(x,y,width,height);
context.strokeRect(x,y,width,height);
```

还有一个 clearRect 方法，擦除指定矩形区域中的图形，使得矩形中的颜色全部变成透明。

```javascript
context.clearRect(x,y,width,height);
```

### 使用路径

#### 绘制圆形

使用路径绘制图形的过程：

1. 开始创建路径
2. 创建图形的路径
3. 路径创建完成后，关闭路径
4. 设定绘制样式，调用绘制方法，绘制路径

大概的意思就是，先用路径勾勒图形轮廓，然后设置颜色，进行绘制。

```javascript
function drawCircle(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  for (let i = 0; i < 10; i++) {
    context.beginPath();
    context.arc(i * 25, i * 25, i * 10, 0, Math.PI * 2, true)
    context.closePath();
    context.fillStyle = 'rgba(255,0,0,0.25)';
    context.fill();
  }
}
```

其中，`context.arc(x,y,radius,startAngle,endAngle,anticlockwise)`

这个方法使用了六个参数，`x`为绘制圆形的横坐标，`y`为纵坐标，`radius`为圆形半径，`startAngle`为开始的角度，`endAngle`为结束角度，`anticlockwise`为是否按顺时针方向进行绘制。

关于角度与弧度的计算

```javascript
let radius = degress * Math.PI / 180;
```

`arc`方法不仅可以用阿里绘制圆形，也可以用来绘制圆弧。因此，使用时必须要指定开始角度与结束角度。因为这两个角度决定了弧度。`anticlockwise`为一个布尔值的参数，参数为 true 时，按顺时针绘制，参数为 false时，按逆时针绘制。

绘制完图形后，使用 `context.closePath()`将路径关闭，接着就可以使用 `fill`方法（`stroke`）填充图形与绘制图形边框了。

#### 不关闭路径

如果不关闭路径，已经创建的路径会被永远保留，使用 fill 或者 stroke 绘制的时候会重复绘制已存在的路径

```javascript
function drawCircle(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  for (let i = 0; i < 10; i++) {
    context.arc(i * 25, i * 25, i * 10, 0, Math.PI * 2, true)
    context.fillStyle = 'rgba(255,0,0,0.25)';
    context.fill();
  }
}
```

#### 绘制线

**moveTo 与 lineTo**

moveTo 方法的作用是将光标移动到指定坐标，绘制直线的时候以这个坐标点为起点

```javascript
moveTo(x,y)
```

lineTo方法，也是用两个参数，x ，y表示直线终点的横纵坐标。使用该方法绘制直线后，光标会自动移动到 lineTo 方法参数所指定的直线终点。

```javascript
function drawLine(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  let dx = 150;
  let dy = 150;
  let s = 100;
  context.beginPath();
  context.fillStyle = 'rgb(100,255,100)';
  context.strokeStyle = 'rgb(0,0,100)';
  let dig = Math.PI / 15 * 11;
  for (let i = 0; i < 30; i++) {
    let x = Math.sin(i * dig);
    let y = Math.cos(i * dig);
    context.lineTo(dx + x * s, dy + y * s);
  }
  context.closePath()
  context.fill();
  context.stroke();
}
```

#### 使用 bezierCurveTo 绘制贝塞尔曲线

```javascript
context.bezierCurveTo(cp1x,cp1y,cp2x,cpy2y,x,y);
```

绘制曲线，需要两个控制点以及一点终点。

```javascript
function drawBezierLine(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  let dx = 150;
  let dy = 150;
  let s = 100;
  context.beginPath();
  context.fillStyle = 'rgb(100,255,100)';
  context.strokeStyle = 'rgb(0,0,100)';
  let dig = Math.PI / 15 * 11;
  for (let i = 0; i < 30; i++) {
    let x = Math.sin(i * dig);
    let y = Math.cos(i * dig);
    context.bezierCurveTo(dx + x * s, dy + y * s - 100, dx + x * s + 100, dy + y * s, dx + x * s, dy + y * s);
  }
  context.closePath()
  context.fill();
  context.stroke();
}
```

`quadraticCurveTo`方法绘制二次贝塞尔曲线

```javascript
context.quadraticCurveTo(cpx,cpy,x,y)
```

### 绘制渐变图形

#### 绘制线性渐变

绘制线性渐变需要用到 `lineargradient`对象。

```javascript
context.createLinearGradient(xStart,yStart,xEnd,yEnd);
```

接着使用 addColorStop 方法设定

```javascript
context.addColorStop(offset,color);
```

offset 为所设定的颜色离开渐变起始点的偏移量。参数的值是0到1之间的浮点值，渐变起始点的偏移量是0，渐变结束点的偏移量是1.

```javascript
function drawLineGradient(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  const gl = context.createLinearGradient(0, 0, 0, 300);
  gl.addColorStop(0, 'rgb(255,255,0)');
  gl.addColorStop(1, 'rgb(0,255,255)');
  context.fillStyle = gl;
  context.fillRect(0, 0, 400, 300);
  const g2 = context.createLinearGradient(0, 0, 300, 0);
  g2.addColorStop(0, 'rgba(0,0,255,0.5)');
  g2.addColorStop(0, 'rgba(255,0,0,0.5)');
  for (let i = 0; i < 10; i++) {
    context.beginPath();
    context.fillStyle = g2;
    context.arc(i * 25, i * 25, i * 10, 0, Math.PI * 2, true);
    context.closePath();
    context.fill();
  }
}
```

#### 绘制径向渐变

```javascript
context.createRadialGradient(xStart,yStart,radiusStart,xEnd,yEnd,radiusEnd);
```

分别指定两个圆的大小和位置。从第一个圆的圆心处向外进行扩散渐变，一直扩散到第二个圆的外轮廓处。

```javascript
function drawRadialGradient(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  const g1 = context.createRadialGradient(400, 0, 0, 400, 0, 400);
  g1.addColorStop(0.1, 'rgb(255,255,0)');
  g1.addColorStop(0.3, 'rgb(255,0,255)');
  g1.addColorStop(1, 'rgb(0,255,255)');
  context.fillStyle = g1;
  context.fillRect(0, 0, 400, 300);
  const g2 = context.createRadialGradient(250, 250, 0, 250, 250, 300);
  g2.addColorStop(0.1, 'rgb(255,0,0,0.5)');
  g2.addColorStop(0.7, 'rgb(255,255,0,0.5)');
  g2.addColorStop(1, 'rgb(0,0,255,0.5)');
  for (let i = 0; i < 10; i++) {
    context.beginPath();
    context.fillStyle = g2;
    context.arc(i * 25, i * 25, i * 10, 0, Math.PI * 2, true);
    context.closePath();
    context.fill();
  }
}
```

### 绘制变形图形

#### 坐标变换

主要有三种形式：

- 平移，使用图形上下文的 `translate`方法来移动图形坐标轴原点。

```javascript
context.translate(x,y);
```

- 扩大，使用对象的 `scale`方法将图形放大

```javascript
context.scale(x,y)
```

- 旋转，使用 `rotate`方法将图形进行旋转

```javascript
context.rotate(angle);
```

旋转中西是坐标轴的原点，顺时针方法

````javascript
function drawDeformationGraphics(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
   // 改变圆心位置
  context.translate(200, 50);
  context.fillStyle = 'rgba(255,0,0,0.25)';
  for (let i = 0; i < 50; i++) {
    context.translate(25, 25);
    context.scale(0.95, 0.95);
    context.rotate(Math.PI / 10);
    context.fillRect(0, 0, 100, 50);
  }
}
````

上面的代码使用坐标变换的方法。首先绘制了一个长方形，然后在一个循环中反复使用平移坐标轴，图形缩小，图形旋转三种技巧绘制出来变形图形。

#### 与路径结合使用

```javascript
function drawDeformationGraphicsWithFivePointedStar(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  context.translate(200, 50);
  context.fillStyle = 'rgba(255,0,0,0.25)';
  for (let i = 0; i < 50; i++) {
    context.translate(25, 25);
    context.scale(0.95, 0.95);
    context.rotate(Math.PI / 10);
    createFivePointedStar(context);
    context.fill();
  }
}


function createFivePointedStar(context) {
  context.beginPath();
  context.fillStyle = 'rgba(255,0,0,0.5)';
  const dx = 100;
  const dy = 0;
  const s = 50;
  let dig = Math.PI / 5 * 4;
  for (let i = 0; i < 5; i++) {
    let x = Math.sin(i * dig);
    let y = Math.cos(i * dig);
    context.lineTo(dx + x * s, dy + y * s);
  }
  context.closePath();
}
```

#### 矩阵变换

当图形上下文被创建完毕时，事实上也创建了一个默认的变换矩阵，如果不对这个变换矩阵进行修改，绘制的图形将以画布的最左上角为坐标原点绘制图形，绘制出来的图形也不经过缩放、变形的处理。我们可以对这个变换矩形进行修改

```javascript
context.transform(a , b , c , d , e , f )
```

| 参数 | 描述         |
| :--- | :----------- |
| *a*  | 水平缩放绘图 |
| *b*  | 水平倾斜绘图 |
| *c*  | 垂直倾斜绘图 |
| *d*  | 垂直缩放绘图 |
| *e*  | 水平移动绘图 |
| *f*  | 垂直移动绘图 |

```javascript
function drawTransform(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'navy', 'purple'];
  context.lineWidth = 10;
  context.transform(1, 0, 0, 1, 100, 0);
  for (let i = 0; i < colors.length; i++) {
    // 定义每次向下移动 10个像素的变换矩阵
    context.transform(1, 0, 0, 1, 0, 10);
    context.strokeStyle = colors[i];
    context.beginPath();
    context.arc(50, 100, 100, 0, Math.PI, true);
    context.stroke();
  }
}
```

setTransform 会将变换矩阵重置

```javascript
setTransform(m11,m12,m21,m22,dx,dy)
```

```javascript
function drawSetTransform(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.strokeStyle = 'red';
  context.strokeRect(30, 10, 60, 20);
  let rad = 45 * Math.PI / 180;

  context.setTransform(Math.cos(rad), Math.sin(rad), -Math.sin(rad), Math.cos(rad), 0, 0);
  context.strokeStyle = 'blue';
  context.strokeRect(30, 10, 60, 20);

  context.setTransform(2.5, 0, 0, 2.5, 0, 0);
  context.strokeStyle = 'green';
  context.strokeRect(30, 10, 60, 20);
  
  context.setTransform(1, 0, 0, 1, 40, 80);
  context.strokeStyle = 'gray';
  context.strokeRect(30, 10, 60, 20);
}
```

### 图形组合

设定图形上下文对象的 `globalCompositeOperation`属性能自己决定图形的组合方式

```javascript
context.globalCompositeOperation = type
```

type 的值必须是下面几种字符串之一：

- source-over(默认值)

  表示新图形覆盖杂原有图形上

- destination-over

  表示在原有图形之下绘制新图形

- source-in

  新图形与原有图形作 in 运算，只显示新图形中与原有图像相互重叠的部分，新图形与原有图形的其他部分均变成透明

- destination-in

  原有图形与新图形作 in 运算，只显示原有图形中与新图形相重叠的部分，新图形与原有图形的其他部分均变成透明

- source-out

  新图形与原有图形 out 运算。只显示新图形中与原有图形不重叠的部分，新图形与原有图形的其他部分变成透明

- destination-out

  新图形与原有图形 out 运算。只显示原有图形中与新图形不重叠的部分，原有图形与新图形的其他部分变成透明

- source-atop

  只绘制新图形中与原有图形重叠的部分与未被重叠覆盖的原有图形，新图形的其他部分变成透明

- destination-atop

  只绘制原有图形中被新图形重叠覆盖的部分与新图形的其他部分，原有图形中的其他部分变成透明，不绘制新图形中与原有图形相重叠的部分

- lighter

  原有图形与新图形均绘制，重叠部分做加色处理

- xor

  只绘制新图形中与原有图形不重叠的部分，重叠部分变成透明

- copy

  只绘制新图形，原有图形中未与新图形重叠的部分变成透明

### 给图形绘制阴影

- shadowOffsetX——阴影的横线位移量
- shadowOffsetY——阴影的纵向位移量
- shadowColor——阴影的颜色
- shadowBlur——阴影的模糊范围

```javascript
function drawShadowFifthStar(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300);
  context.shadowOffsetX = 10;
  context.shadowOffsetY = 10;
  context.shadowColor = 'rgba(100,100,100,0.5)';
  context.shadowBlur = 7.5;
  context.translate(0, 50);
  for (let i = 0; i < 3; i++) {
    context.translate(50, 50);
    createFivePointedStar(context);
    context.fill();
  }
}
```

### 使用图像

#### 绘制图像

```javascript
// 宽高 为原图像宽高
context.drawImage(image,x,y);

// 增加宽高的自定义
context.drawImage(image,x,y,w,h)

// 将画布中绘制好的图像的全部或者部分复制到画布的另一个位置上
context.drawImage(image,sx,sy,sw,sh,dx,dy,dw,dh)
```

```javascript
function drawCanvasImage(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300)
  image = new Image();
  image.src = '../Desktop/微信图片_20190311090059.jpg'
  image.onload = function () {
    drawBigImage(context, image)
  }
}

function drawImage(context, image) {
  for (let i = 0; i < 7; i++) {
    context.drawImage(image, 0 + i * 50, 0 + i * 25, 100, 100);
  }
}

function drawBigImage(context, image) {
  context.drawImage(image, 0, 0, 100, 100);
  context.drawImage(image, 23, 5, 57, 80, 110, 0, 100, 100);
}
```

#### 图像平铺

```javascript
function drawTileImage(canvas, context, image) {
  console.log(canvas);
  const scale = 5;
  let n1 = image.width / scale;
  let n2 = image.height / scale;
  let n3 = canvas.width / n1;
  let n4 = canvas.height / n2;
  for (let i = 0; i < n3; i++) {
    for (let j = 0; j < n4; j++) {
      context.drawImage(image, i * n1, j * n2, n1, n2);
    }
  }
}
```

上面使用变量和循环来实现图像平铺。还可以用更简便的图形上下文的 `createPattern`方法。

```javascript
context.createPattern(image,type)
```

image 参数为要平铺的图像，type 参数：

- no-repeat：不平铺
- repeat-x：横方向平铺
- repeat-y：纵方向平铺
- repeat：全方向平铺

```javascript
function drawCanvasImage(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = '#EEEEFF';
  context.fillRect(0, 0, 400, 300)
  image = new Image();
  image.src = '../Desktop/微信图片_20190311090059.jpg';
  image.width = 10;
  image.onload = function () {
    const canvasTemp = document.createElement('canvas');
    const contextTemp = canvasTemp.getContext('2d');
    canvasTemp.width = 100;
    canvasTemp.height = 100;
    contextTemp.drawImage(this,0,0,100,100);
    let ptrn = context.createPattern(canvasTemp,'repeat');
    // context.drawImage(image, 23, 5, 57, 80);
    context.fillStyle = ptrn;
    context.fillRect(0, 0, 400, 300)
  }
}
```

当图像太大的时候，可以通过把图像绘制在一个临时的 canvas 里面，然后再用主canvas 重复这个临时 canvas 

#### 图像裁剪

使用 clip 方法设置裁剪区域

```javascript
function cutImage(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  const gr = context.createLinearGradient(0, 400, 300, 0);
  gr.addColorStop(0, 'rgb(255,255,0)');
  gr.addColorStop(1, 'rgb(0,255,255)');
  context.fillStyle = gr;
  context.fillRect(0, 0, 400, 300);
  image = new Image();
  image.src = '../Desktop/微信图片_20190311090059.jpg';
  image.onload = function () {
    createFivePointedStar(context);
    context.drawImage(image, -50, -150, 400, 300);
  }
}

function createFivePointedStar(context) {
  context.beginPath();
  context.translate(100,150);
  context.fillStyle = 'rgba(255,0,0,0.5)';  
  const dx = 100;
  const dy = 0;
  const s = 50;
  let dig = Math.PI / 5 * 4;
  for (let i = 0; i < 5; i++) {
    let x = Math.sin(i * dig);
    let y = Math.cos(i * dig);
    context.lineTo(dx + x * s, dy + y * s);
  }
  context.closePath();
  context.clip();
}
```

#### 像素处理

使用 getImageData 方法可以获取图像中的像素,获得像素组依次 rgba 值。

```javascript
imageData = context.getImageData(x,y,width,height);
```

| 参数     | 描述                            |
| :------- | :------------------------------ |
| *x*      | 开始复制的左上角位置的 x 坐标。 |
| *y*      | 开始复制的左上角位置的 y 坐标。 |
| *width*  | 将要复制的矩形区域的宽度。      |
| *height* | 将要复制的矩形区域的高度。      |

对于 ImageData 对象中的每个像素，都存在着四方面的信息，即 RGBA 值：

- R - 红色 (0-255)
- G - 绿色 (0-255)
- B - 蓝色 (0-255)
- A - alpha 通道 (0-255; 0 是透明的，255 是完全可见的)

利用 putImageData 将图像数据放回画布,期间做一些颜色处理，可以模拟滤镜。

```javascript
  image.onload = function () {
    // createFivePointedStar(context);
    context.drawImage(image, 0, 0, 400, 300);
    let imagedata = context.getImageData(0, 0, image.width, image.height)
    for (let i = 0, n = imagedata.data.length; i < n; i += 4) {
      imagedata.data[i + 0] = 255 - imagedata.data[i + 0];
      imagedata.data[i + 1] = 255 - imagedata.data[i + 2];
      imagedata.data[i + 2] = 255 - imagedata.data[i + 1];
    }
    context.putImageData(imagedata, 0, 0, 400, 300);
  }
```

```
context.putImageData(imgData,x,y,dirtyX,dirtyY,dirtyWidth,dirtyHeight);
```

| 参数          | 描述                                                  |
| :------------ | :---------------------------------------------------- |
| *imgData*     | 规定要放回画布的 ImageData 对象。                     |
| *x*           | ImageData 对象左上角的 x 坐标，以像素计。             |
| *y*           | ImageData 对象左上角的 y 坐标，以像素计。             |
| *dirtyX*      | 可选。水平值（x），以像素计，在画布上放置图像的位置。 |
| *dirtyY*      | 可选。水平值（y），以像素计，在画布上放置图像的位置。 |
| *dirtyWidth*  | 可选。在画布上绘制图像所使用的宽度。                  |
| *dirtyHeight* | 可选。在画布上绘制图像所使用的高度。                  |

### 绘制文字

fillText 填充方式绘制字符串

```javascript
context.fillText(text,x,y,maxWidth);
```

maxWidth，允许最大的宽度，像素单位

strokeText 用轮廓方式绘制字符串

```javascript
context.strokeText(text,x,y,maxWidth);
```

一些关于文字绘制的属性：

- font：字体
- textAlign：水平对齐方式，start(默认)、end、left、right、center
- textBaseline：垂直对齐方式，top、hanging、middle、alphabetic(默认)、ideographic、bottom

获得文字的宽度

```javascript
context.measureText(text)
```

接受一个参数 text，该参数为需要绘制的文字，返回一个 TextMetrics 对象，TextMetrics对象的 width 属性表示使用当前指定的文字后  text 参数中指定的文字的总文字宽度

```javascript
function drawText(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.font = 'italic 20px sans-serif';
  let txt = '字符串的宽度为：';
  let tml = context.measureText(txt);
  context.fillText(txt, 10, 30);
  context.fillText(tml.width, tml.width + 10, 30);
  context.font = 'bold 30px sans-serif';
  let tm2 = context.measureText(txt);
  context.fillText(txt, 10, 70);
  context.fillText(tm2.width, tm2.width + 10, 70);
}
```

### 其他

#### 保存与恢复状态

```javascript
// 将当前状态保存到 栈中
context.save();
// 从栈中取出之前保存的图形上下文的状态进行恢复 
context.restore();
```

绘画状态包括坐标的原点、变形时的变换矩阵，图形上下文对象的当前属性值。

可以应用在下面但不仅仅下面的场景：

- 图像或图形变形
- 图像裁剪
- 改变图形上下文的以下属性的时候：fillStyle、font、globalAlpha、globalComposite、Operation、lineCap、lineJoin、lineWidth、miterLimit、shadowBlur、shadowColor、shadowOffsetX、shadowOffsetY、strokeStyle、textAlign、textBaseline

#### 保存文件

 原理实际上是把当前的绘画状态输出到一个 data URL 地址所指向的数据中的过程。data URL指的是目前大部分浏览器能够识别的一种 base64编码的 URL，主要用于小型的、可以在网页中直接嵌入的，而不需要从外部文件嵌入的数据，例如 img 元素中的图像文件等。

toDataURL 方法把绘画状态输出到一个 data URL中，然后重新装载，客户可以直接把装载后的文件进行保存。

```javascript
canvas.toDataURL(type);
// type 表示输出输出类型的 MIME 类型
```

```javascript
function drawDataURL(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  const context = canvas.getContext('2d');
  context.fillStyle = 'rgb(0,0,255)';
  context.fillRect(0, 0, canvas.width, canvas.height);
  context.fillStyle = 'rgb(255,255,0)';
  context.fillRect(10, 20, 50, 50);
  console.log(canvas.toDataURL("image/jepg"))
}
```

#### 简单动画的制作

canvas 画布中制作的动画实际上就是一个不断擦除（clearRect）、重绘的过程。

```javascript
function animationOnCanvas(id) {
  const canvas = document.getElementById(id);
  if (!canvas) return false;
  context = canvas.getContext('2d');
  width = canvas.width;
  height = canvas.height;
  i = 0;
  setInterval(rotate, 100);
}

function rotate() {
  context.clearRect(0, 0, width, height);
  context.fillStyle = 'red';
  context.fillRect(i, 0, 20, 20);
  i += 20;
}
```

## 多媒体播放

### 关于 video 与 audio元素

HTML4页面中播放视频或者音频数据

```html
  <object classid="clsid:F08DF954-8592-11D1-B16A-00C0F0283628" id="Slider1" width="100" height="50">
    <param name="BorderStyle" value="1" />
    <param name="MousePointer" value="0" />
    <param name="Enabled" value="1" />
    <param name="Min" value="0" />
    <param name="Max" value="10" />
    <param name="allowFullScreen" value="true" />
    <param name="allowscriptaccess" value="always" />
    <embed type="application/x-shockwave-flash" width="425" height="344" src="p.swf" allowscriptaccess="always"
      allowfullscreen="true"></embed>
  </object>
```

可以看出上面代码的一些缺点：冗长，需要使用 Flash 插件，用户没有安装的话，则视频看不了。需要结合多个元素，并且需要添加很多属性。

HTML5 中的则是：

```html
<audio src='xxx.mp3'>
    您的浏览器器不支持 audio 元素
</audio>
<video width="640" height="360" src='xxx.mp4'>
    您的浏览器器不支持 video 元素
</video>

<!--结合 source元素为同一个媒体数据指定多个播放格式与编码方式，浏览器会从中找到一种自己支持的播放格式来播放 -->
<video>
    <source src="sample.ogv" type="video/ogg";codecs="theora,vorbis">
	<source src="sample.mov" type="video/quicktime">
</video>
<!--type指播放媒体的MIME类型，codes表示媒体的编码格式-->
```

### 属性

- src

  指定媒体数据的 URL 地址

- autoplay

  指定媒体是否在页面加载后自动播放

- preload

  指定数据是否预加载。预加载浏览器会预先将视频或者音频数据缓冲，加快播放速度，因为播放时数据已经缓冲完毕。该属性有 none（不进行预加载）、metadata（只预加载媒体的元数据：媒体字节数、第一帧、播放列表、持续时间等）、auto(默认，表示预加载全部视频或者音频)。

- poster(video独有)

  当视频不可用的时候，可以使用该元素向用户展示一副替代用的图片。用户体验良好

- loop

  循环播放

- controls

  添加浏览器自带的播放用的控制条，具有播放、暂停等按钮。

- width 与 height（video独有）

  该属性中指定视频的宽度和高度（像素）

- error

  读取使用媒体数据的过程中出错的话，返回一个 MediaError对象，该对象的code返回对应的错误状态，有4个值

  MEDIA_ERR_ABORTED(数字值为1)：媒体数据的下载过程由于用户操作的原因而被中止

  MEDIA_ERR_NETWORK(数字值为2)：确认媒体资源可用，但是在下载时出现网络错误，媒体数据的下载过程被中止

  MEDIA_ERR_DECODE(数字值为3)：确认媒体资源可用，但是解码时发生错误。

  MEDIA_ERR_SRC_NOT_SUPPORTED(数字值为4)：媒体资源不可用媒体格式不被支持，error属性为只读属性

```javascript
  const video = document.getElementById('video element');
  video.addEventListener('error', function () {
    let { error } = video;
    switch (error.code) {
      case 1:
        console.log('视频的下载过程被中止');
        break;
      case 2:
        console.log('网络发生故障，视频的下载过程被中止');
        break;
      case 3:
        console.log('解码失败');
        break;
      case 4:
        console.log('不支持的播放格式');
        break;
      default:
        console.log('无');
    }
  }, false);
```

- networkState,4个可能值
  - NETWORK_EMPTY(数字值为0)：音频/视频尚未初始化
  - NETWORK_IDLE(1)：音频/视频是活动的且已选取资源，但并未使用网络
  - NETWORK_LOADING(2)：浏览器正在下载数据
  - NETWORK_NO_SOURCE(3)：未找到音频/视频来源

```javascript
  video.addEventListener('progress', function (e) {
    const { networkState } = video;
    if (networkState === 2) {
      x.innerHTML = `加载中...[${e.loaded}/${e.total} byte]`
    } else if (networkState === 3) {
      x.innerHTML = '记载失败'
    }
  }, false);  
```

- currentSrc

  读取播放中的媒体数据的 URL地址，为只读属性

- buffered（只读）

  buffered 属性返回 TimeRanges 对象。该对象表示用户的音视频缓冲范围，缓冲范围指的是已缓冲音视频的时间范围。如果用户在音频中跳跃播放，会得到多个缓冲范围。

语法

```
audio|video.buffered
```

返回值

| 值              | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| TimeRanges 对象 | 表示音视频的已缓冲部分。TimeRanges 对象属性：length - 获得音视频中已缓冲范围的数量<br>start(index) - 获得某个已缓冲范围的开始位置<br/>end(index) - 获得某个已缓冲范围的结束位置<br/>**注释：**首个缓冲范围的下表是 0。 |

- readyState（只读）

  返回媒体当前的播放位置的就绪状态值：

  HAVE_NOTHING(0)：没有获取到媒体的任何信息，当前的播放位置没有可播放数据

  HAVE_METADATA(1)：已经获取到足够的媒体数据，但是当前播放位置没有有效的媒体数据（获取到的媒体数据无效）

  HAVE_CURRENT_DATA(2)：当前播放位置已经有数据可以播放，但没有获取到可以让播放器前进的数据。当媒体为视频，即当前帧的数据已经获得，也获得了下一帧大数据，或者当前帧已经是最后一帧

  HAVE_FUTURE_DATA(3)：当前播放位置已经有数据可以播放，而且也获得到了可以让播放器前进的数据。当媒体为视频时，即当前帧的数据已经获得，而且也获得了下一帧的数据，当前帧是播放的最后一帧时，readyState 属性不可能为 HAVE_FUTURE_DATA

  HAVE_ENOUGH_DATA(4)：当前播放位置已经有数据可以播放，同时也获得了可以让播放器前进的数据，而且浏览器确认媒体数据以某一种速度可以加载，可以保证有足够的后续数据进行播放。

- seeking属性与seekable（只读）

  seeking属性返回一个布尔值，表示浏览器是否正在请求某一特定播放为孩子的数据，true表示浏览器正在请求数据，false表示浏览器正在停止请求。

  seekable返回一个 TimeRanges对象，该对象表示请求到的数据的时间范围。当媒体为视频时，开始时间为请求到视频数据第一帧的时间结束为请求到视频数据最后一帧的时间。

- currentTime（可读写）/startTime（只读）/duration（只读）

  currentTime获取当前播放位置，也可以通过修改currentTime来修改当前播放位置。如果修改的位置上没有可用的媒体数据时，将抛出 INVALID_STATE_ERR 异常。如果修改的位置超出了浏览器在一次请求中可以请求的数据范围，将抛出 INDEX_SIZE_ERR  异常。

  startTime 来读取媒体播放的开始时间，通常为0

  duration 读取媒体文件总的播放时间

  三者单位均为秒

- played/paused/ended（均为只读）

  played 返回一个 TimeRanges 对象，从该对象中可以去读媒体文件的以播放部分的时间段。开始时间为已播放部分的开始时间，结束时间为已播放部分的结束时间。

  paused 返回一个布尔值，表示是否处于暂停播放中，true 表示媒体暂停播放，false 表示媒体正在播放

  end 返回一个布尔值，表示是否播放完毕，true表示播放完毕，false表示还没有播放完毕

- defaultPlaybackRate/playbackRate

  前者读取或者修改默认的播放速率，后者可以读取修改当前的播放速率

- volume与 muted

  Volume 读取或者修改媒体的播放音量，范围为0到1。0为静音，1为最大音量

  muted读取或者修改媒体的静音状态，布尔值。true表示静音状态，false表示非静音状态

### 方法

video与 audio都有以下四种方法：

- play。播放媒体，将 paused变为 false
- pause。暂停媒体，将 paused 变为 true
- load。重新载入媒体进行播放，将 playbackRate变为 defaultPlaybackRate，error变为 null
- canPlayType。来测试浏览器是否支持指定的媒体类型。`support = videoElement.canPlayType(type)`。该方法使用一个参数type，该参数的指定方法与 source元素的type参数的指定方法相同，都用播放文件的 MIME 类型来指定，可以在指定的字符串中加上表示媒体编码格式的 codes参数。该方法返回3个可能值：
  - 空字符串：表示浏览器不支持此种媒体类型
  - maybe：表示浏览器可能支持此种媒体类型
  - probably：表示浏览器确定支持此种媒体类型

### 事件

事件处理方式分为两种：

- addEventListener
- 获取事件句柄的方式



| 事件           | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| loadstart      | 浏览器开始在网上寻找媒体数据                                 |
| progress       | 浏览器正在获取媒体数据                                       |
| suspend        | 浏览器暂停获取媒体数据，但是下载过程并没有正常结束           |
| abort          | 浏览器在下载完全部媒体数据之前中止获取媒体数据，但是并不是由错误引起的 |
| error          | 获取媒体数据过程中出错                                       |
| emptied        | video元素或者 audio元素所在网络突然变为未初始状态。可能引起的原因有两个：载入媒体过程中突然发生一个致命错误和在浏览器正在选择支持的播放格式时，又调用了 load 方法重新载入媒体 |
| stalled        | 浏览器尝试获取媒体数据失败                                   |
| play           | 即使开始播放，当执行了 play 方法时触发，或者数据下载完之后元素被谁 autoplay |
| pause          | 播放暂停，当执行了 pause 方法时触发                          |
| loadedmetadata | 浏览器获取完毕媒体的时长好字节数                             |
| loadeddata     | 浏览器已经加载完毕当前播放位置的媒体数据，准备播放           |
| waiting        | 播放过程由于得不到下一帧而暂停播放（例如下一帧尚未加载完毕），但很快就能够播放下一帧 |
| playing        | 正在播放                                                     |
| canplay        | 浏览器能够播放媒体，但估计以前播放速率不能直接将媒体播放完毕，播放需要缓冲 |
| canplaythrough | 浏览器能够播放媒体，而且以当前播放速率能够将媒体播放完毕，不再需要进行缓冲 |
| seeking        | seeking属性变为 true，浏览器正在请求数据                     |
| seeked         | seeking属性变成false，浏览器停止请求数据                     |
| timeupdate     | 当前播放位置被改变，可能是播放过程中的自然改变，也可能是人为地改变，或由于播放不能连续而发生的跳变 |
| ended          | 播放结束后停止播放                                           |
| ratechange     | defaultplaybackRate属性（默认播放速率）或 playbackRate属性（当前播放速率）被改变 |
| durationchange | 播放时长被改变                                               |
| volumechange   | volume（音量）被改变或者 muted（静音状态） 被改变            |

### 事件捕捉示例

```javascript
  video.addEventListener('timeupdate', function () {
    const timer = document.getElementById('time');
    timer.innerHTML = Math.floor(video.currentTime) + '/' + Math.floor(video.duration) + '(秒)';
  }, false);
```

## 本地存储

### Web Storage

我们可以使用 cookies 在客户端保存注入用户等简单的用户信息，但它有一些限制：

- 大小：cookies大小被限制在 4KB
- 带宽：cookies 随 HTTP 事务一起被发送，会浪费一部分发送 cookies使用的带宽
- 复杂度

在 HTML5中重新提供了一种在客户端本地保存数据的功能，就是 Web Storage 功能。它分为两种

- sessionStorage

  将数据保存在 seesion对象中。所谓的 session，是指用户在浏览某个网站的时候，从进入网站到浏览器关闭所经过这段时间，也就是用户浏览器这个网站所花费的时间，session 对象可以用来保存这时间所要求保存的任何数据、

- localStorage

  将数据保存在客户端本地的硬件设备（通常值硬盘，但也可以是其他硬件设备）中，即使浏览器被关闭了，该数据仍然存在，下次打开浏览器访问网站仍然可以使用。

```javascript
// sessionStorage
// 保存数据
sessionStorage.setItem(key,value);

//读取数据
变量 = seesionStorage.getItem(key)

// localStorage
// 保存数据
localStorage.setItem(key,value);

// 保存数据
变量 = localStorage.getItem(key)

// 保存时不允许重复键名。保存后可以修改键值，但是不允许修改键名（只能重新取键名，然后保存键值）
```

### 示例：简单 web 留言本

```html
<body>
  <h1>简单 Web 留言本</h1>
  <textarea name="memo" id="memo" cols="60" rows="10"></textarea>
  <input type="button" value="追加" onclick="saveStorage('memo')">
  <input type="button" value="初始化" onclick="clearStorage('msg')">
  <hr>
  <p id="msg"></p>
</body>
<script>
  function saveStorage(id) {
    const data = document.getElementById(id).value;
    const time = new Date().getTime();
    localStorage.setItem(time, data);
    console.log('数据已经保存了');
    loadStorage('msg');
  }

  function loadStorage(id) {
    let result = `<table border="1">`;
    for (let i = 0; i < localStorage.length; i++) {
      let key = localStorage.key(i);
      let value = localStorage.getItem(key);
      let date = new Date();
      date.setTime(key);
      let datestr = date.toGMTString();
      result += `<tr><td>${value}</td><td>${datestr}</td></tr>`
    }
    result += `</table>`;
    let target = document.getElementById(id);
    target.innerHTML = result;
  };

  function clearStorage() {
    localStorage.clear();
    console.log('全部数据被清除');
    loadStorage('msg');
  }
</script>
```

### 示例：简易数据库

```html
<body>
  <h1>简易数据库</h1>
  <table>
    <tr>
      <td>姓名：</td>
      <td><input type="text" id="name"></td>
    </tr>
    <tr>
      <td>EMAIL:</td>
      <td><input type="text" id="email"></td>
    </tr>
    <tr>
      <td>电话号码:</td>
      <td><input type="text" id="tel"></td>
    </tr>
    <tr>
      <td>备注:</td>
      <td><input type="text" id="memo"></td>
    </tr>
    <tr>
      <td></td>
      <td><input type="button" onclick="saveStorage()" value="保存"></td>
    </tr>
  </table>
  <hr>
  <p>检索：
    <input type="text" id="find">
    <input type="button" value="检索" onclick="findStorage('msg')">
  </p>
  <p id="msg"></p>
</body>
<script>
  function saveStorage() {
    let data = new Object;
    data.name = document.getElementById('name').value;
    data.email = document.getElementById('email').value;
    data.tel = document.getElementById('name').value;
    data.memo = document.getElementById('memo').value;
    let str = JSON.stringify(data);
    localStorage.setItem(data.name, str);
    console.log('数据已经保存了');
  }

  function findStorage(id) {
    let find = document.getElementById('find').value;
    let str = localStorage.getItem(find);
    let data = JSON.parse(str);
    let result = `姓名： ${data.name}<br>`;
    result += `EMAIL： ${data.email}<br>`;
    result += `电话号码： ${data.tel}<br>`;
    result += `备注： ${data.memo}<br>`;
    let target = document.getElementById(id);
    target.innerHTML = result;
  }
</script>
```

## 本地数据库(新的推荐IndexedDB实现)

HTML5中内置了一个可以通过 SQL 语言来访问的数据库

```javascript
let db = openDatabase('mydb','1.0','Test DB',2*1024*1024)
```

openDatabase接收五个参数：

1. 数据库名字
2. 数据库版本号
3. 显示名字
4. 数据库保存数据的大小（以字节为单位 )
5. 回调函数（非必须)

transaction 方法来执行事务处理，防止在对数据库进行访问以及有关操作的时候收到外界的干扰。当一条语法执行失败的时候，整个事务会回滚

```javascript
db.transaction(function(context){
    context.executeSql('CREATE TABLE IF NOT EXISTS tesTable (id unique,name)');
    context.executeSql('INSERT INTO testTable(id,name) VALUES (0,"haha")')
})
```

executeSql 方法

```javascript
transaction.executeSql(sqlquery,[],dataHandler,errorHandler);
```

executeSql 接收四个参数：

1. 查询字符串
2. 用以替换查询字符串中问号的参数
3. 执行成功回调函数（可选）
4. 执行失败回调函数（可选）

```javascript
transaction.executeSql("UPDATE people set age=?where name=?;",[age,name],(transaction,result)=>{},(transaction,errmsg)=>{})
```

### 实例：留言本

```html
<body onload="init()">
  <h1>使用数据库实现的 Web 留言本</h1>
  <table>
    <tr>
      <td>姓名：</td>
      <td><input type="text" id="name"></td>
    </tr>
    <tr>
      <td>留言：</td>
      <td><input type="text" id="memo"></td>
    </tr>
    <tr>
      <td></td>
      <td><input type="button" value="保存" onclick="saveData()"></td>
    </tr>
  </table>
  <hr>
  <table id="datatable" border="1"></table>
  <p id="msg"></p>
</body>
<script>
  let datatable = null;
  let db = openDatabase('MyData', '', 'My Database', 102400);
  function init() {
    console.log(datatable.childNodes);
    datatable = document.getElementById('datatable');
    showAllData();
  }
  function removeAllData() {
    for (let i = datatable.childNodes.length - 1; i >= 0; i--) {
      datatable.removeChild(datatable.childNodes[i]);
    }
    let tr = document.createElement('tr');
    let th1 = document.createElement('th');
    let th2 = document.createElement('th');
    let th3 = document.createElement('th');
    th1.innerHTML = '姓名';
    th2.innerHTML = '留言';
    th3.innerHTML = '时间';
    tr.appendChild(th1);
    tr.appendChild(th2);
    tr.appendChild(th3);
    datatable.appendChild(tr);
  }

  function showData(row) {
    let tr = document.createElement('tr');

    let td1 = document.createElement('td');
    td1.innerHTML = row.name;

    let td2 = document.createElement('td');
    td2.innerHTML = row.message;

    let td3 = document.createElement('td');
    let t = new Date();
    t.setTime(row.time);
    td3.innerHTML = t.toLocaleDateString() + " " + t.toLocaleTimeString();
    tr.appendChild(td1);
    tr.appendChild(td2);
    tr.appendChild(td3);
    datatable.appendChild(tr);
  }

  function showAllData() {
    db.transaction(function (tx) {
      tx.executeSql('CREATE TABLE IF NOT EXISTS MsgData(name TEXT,message TEXT,time INTEGER)', []);
      tx.executeSql('SELECT * FROM MsgData', [], function (tx, rs) {
        removeAllData();
        for (let i = 0; i < rs.rows.length; i++) {
          showData(rs.rows.item(i));
        }
      })
    });
  }

  function addData(name, message, time) {
    db.transaction(function (tx) {
      tx.executeSql('INSERT INTO MsgData VALUES(?,?,?)', [name, message, time], function (tx, rs) {
        console.log('成功保存数据');
      }, function (tx, error) {
        console.log(`${error.source}:${error.message}`);
      })
    })
  }

  function saveData() {
    let name = document.getElementById('name').value;
    let memo = document.getElementById('memo').value;
    let time = new Date().getTime();
    addData(name, memo, time);
    showAllData();
  }

</script>
```

不过，Web SQL Database规范已经被废弃。因为每个浏览器都有自己的实现，浏览器的兼容性就不重要了。

[关于 IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API)

## 离线应用程序（**已废弃**）

HTML5 引入了应用程序缓存，这意味着 web 应用可进行缓存，并可在没有因特网连接时进行访问。

应用程序缓存为应用带来三个优势：

- 离线浏览 - 用户可在应用离线时使用它们
- 速度 - 已缓存资源加载得更快
- 减少服务器负载 - 浏览器将只从服务器下载更新过或更改过的资源。

**本地缓存与浏览器网页缓存的区别**

本地缓存为整个 Web 程序服务的，浏览器的网页缓存只服务于单个网页。任何网页都有网页缓存，而本地缓存值缓存指定的网页。本地缓存可以控制缓存更新，利用缓存对象的各种属性、状态和事件来开发出离线应用程序

### manifest文件

首先在 index.html 中引入

```html
<！DOCTYPE html>
<html mainfest ='index.appcache'>
</html>
```

每个指定了 manifest 的页面在用户对其访问时都会被缓存。如果未指定 manifest 属性，则页面不会被缓存（除非在 manifest 文件中直接指定了该页面）。

manifest 文件的建议的文件扩展名是：".appcache"。

请注意，manifest 文件需要配置*正确的 MIME-type*，即 "text/cache-manifest"。必须在 web 服务器上进行配置。

Web 应用程序的本地缓存是通过每个页面的 `manifest`文件来管理的。是一个简单的文件文本，清单列举了需要被缓存或者不需要被缓存的资源文件的文件名以及访问路径。

manifest 文件可分为三个部分：

- *CACHE MANIFEST* - 在此标题下列出的文件将在首次下载后进行缓存
- *NETWORK* - 在此标题下列出的文件需要与服务器的连接，且不会被缓存
- *FALLBACK* - 在此标题下列出的文件规定当页面无法访问时的回退页面（比如 404 页面）

```appcache
CACHE MANIFEST
# 文件开头必须要书写 CACHE MANIFEST
# 以下是需要缓存的文件

other.html
images/1.jpg
js/jquery.js
css/bootstrap.css

NETWORK：
login.asp

FALLBACK:
online.js locale.js
# 如果无法建立因特网连接，用 404页面替换/html/目录所有的文件，第一个是 URI资源，第二个是替补
/html5/ /404.html

```

**更新缓存**

一旦应用被缓存，它就会保持缓存知道下列的情况发生：

- 用户清空浏览器缓存
- manifest 文件被修改
- 由程序来更新应用缓存

### 浏览器与服务器的交互过程

场景：访问 网站A,以 index.html 为主页，使用 manifest 缓存了 index.html、1.js、1.css、1.jpg几个资源文件。

第一次访问：

1. 浏览器请求访问网站 A
2. 服务器访问 index.html
3. 浏览器解析 index.html，请求页面上的所有资源，包括 HTML/图像文件/CSS/JS/manifest文件等
4. 服务器返回所有资源
5. 浏览器处理 manifest文件，请求 manifest指定的本地缓存的文件，即使3中已经请求过了。如果要求缓存所有文件也是一个比较大的重复过程。
6. 服务器返回所有要求缓存的文件
7. 浏览器对本地缓存文件进行缓存，触发一个事件，通知本地缓存被更新

manifest文件没有被修改，第二次访问：

1. 浏览器再次请求网站A
2. 浏览器发现这个页面被本地缓存，于是使用本地缓存中的 index.html页面
3. 浏览器解析 index.html，使用所有本地缓存中的资源文件
4. 浏览器向服务器请求 manifest 文件
5. 服务器返回一个 304，通知浏览器 manifest没有发生变化

manifest文件被修改了，第三次访问：

1. 浏览器再次请求网站A
2. 浏览器发送这个页面被本地缓存了，于是使用本地缓存中的 index.html页面
3. 浏览器解析 index.html 文件，使用所有本地缓存中的资源文件
4. 浏览器向服务器请求 manifest文件
5. 服务器返回更新过的 manifest文件
6. 浏览器处理 manifest文件，发送文件已经更新了，于是请求所有要求进行本地缓存的资源文件，包括 index.html 本身
7. 浏览器返回要求进行本地缓存的资源文件
8. 浏览器对本地缓存进行更新，存入所有新的资源文件，并且触发一个事件，通知本地缓存被更新了

浏览器缓存过程中会触发一系列事件，该事件处理程序注册在ApplicationCache对象上，此对象是window的applicationCache属性的值。

下面详细描述了加载文档与更新应用缓存的流程：

1. 当浏览器访问一个包含 `manifest` 特性的文档时，如果应用缓存不存在，浏览器会加载文档，然后获取所有在清单文件中列出的文件，生成应用缓存的第一个版本。
2. 对该文档的后续访问会使浏览器直接从应用缓存(而不是服务器)中加载文档与其他在清单文件中列出的资源。此外，浏览器还会向 `window.applicationCache 对象发送一个` `checking` 事件，在遵循合适的 HTTP 缓存规则前提下，获取清单文件。
3. 如果当前缓存的清单副本是最新的，浏览器将向 `applicationCache 对象发送一个` `noupdate` 事件，到此，更新过程结束。注意，如果你在服务器修改了任何缓存资源，同时也应该修改清单文件，这样浏览器才能知道它需要重新获取资源。
4. 如果清单文件*已经*改变，文件中列出的所有文件—也包括通过调用 `applicationCache.add() 方法添加到缓存中的那些文件`—会被获取并放到一个临时缓存中，遵循适当的 HTTP 缓存规则。对于每个加入到临时缓存中的文件，浏览器会向 `applicationCache 对象发送一个` `progress` 事件。如果出现任何错误，浏览器会发送一个 `error` 事件，并暂停更新。
5. 一旦所有文件都获取成功，它们会自动移送到真正的离线缓存中，并向  `applicationCache `对象发送一个 `cached` 事件。鉴于文档早已经被从缓存加载到浏览器中，所以更新后的文档不会重新渲染，直到页面重新加载(可以手动或通过程序).

```javascript
//载入的时候，检查该清单文件。
window.applicationCache.onchecking = function(){
    $("san").innerHTML = "checking for a new version";
    return false;
}
//如果清单文件没有动，同时应用程序也已经缓存了，该事件执行。
window.applicationCache.onnoupdate = function(){
    $("san").innerHTML = "This version is up-to-date";
    return false;
}
//如果还未缓存应用程序，或者清单有改动
window.applicationCache.ondownloading = function(){
    $("san").innerHTML = "Downloading new version";
    window.progresscount = 0; //在下面的事件中用到
    return false;
}
//下载过程不断调用progress事件，通常在每个文件下载完的时候。
window.applicationCache.onprogress = function(e){
    var progress = ""; 
    if(e && e.lengthComputable){
        progress = "" + Math.round(100*e.loaded / e.total) + "%"; //计算下载完成比例
    }
    else{
        progress = "(" + ++progresscount + ")";  //输出调用次数。
    }
    $("san").innerHTML = "Downloading new version" + progress;
    return false;
}
//当下载完成并且首次将应用程序下载到缓存中时
window.applicationCache.oncached = function(){
    $("san").innerHTML = "This application is now cached locally" ;
    return false;
}
//下载完成并且首次将应用程序下载到缓存中。
window.applicationCache.oncached = function() {
    status("This application is now cached locally");
    return false;
};
//下载完成并缓存的程序更新后触发，注意触发此事件时，用户任然看到老版本，只有当用户再次载入时才会访问最新版。
window.applicationCache.onupdateready = function() {
    status("A new version has been downloaded.  Reload to run it");
    return false;
};
//处于离线时，检查清单失败触发。
 
window.applicationCache.onerror = function() {
    status("Couldn't load manifest or cache application");
    return false;
};
 
//程序引用一个不存在的清单文件触发，同时将应用从缓存中删除。
window.applicationCache.onobsolete = function() {
    status("This application is no longer cached. " + 
           "Reload to get the latest version from the network.");
    return false;
};
```



## 通信 API

### 跨文档消息传输

可以在不同网页文档、不同端口、不同域之间进行消息传递。

HTML5 提供了在网页文档之间互相接受与发送消息的功能，只要获取到网页所在窗口对象的实例，不仅同源（域+端口号）的 Web 之间可以互相通信，甚至可以实现跨域通信。

```javascript
window.postMessage();
// 这个方法可以安全实现跨域通信。提供了一个受控禁止来规避同源策略的限制。这个方法被调用时，会在所有页面脚本执行完毕之后向目标窗口派发一个 MessageEvent 消息。这个消息有四个属性：message属性表示 message类型，data属性为 window.postMessage的第一个参数；origin属性表示调用 window.postMessage方法调用页面的当前状态；source属性记录调用 window.postMessage方法的窗口消息

otherWindow.postMessage(message,targetOrigin,[transfer]);
/**
* otherWindow 其他窗口的一个引用，比如 iframe的contentWindow属性，执行 window.open返回的窗口对象，或者是命名过或数值索引的 window.frames
* message 将要发送到其他 window的数据，会被结构化克隆算法序列化。意味着不受什么限制将数据对象安全传送给目标窗口不用自己序列化
* targetOrigin 通过窗口的 origin 属性来指定哪些窗口能接受到消息时间。可以是“*”或者一个URI.如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配targetOrigin提供的值，那么消息就不会被发送；只有三者完全匹配，消息才会被发送。这个机制用来控制消息可以发送到哪些窗口；
* transfer 可选 是一串和 message同时传递的 Transferable 对象，这些对象的所有权将被转移给消息的接收方，而发送一方不再保有所有权。
*/
```

**派发事件**

```javascript
window.addEventListener('message',receiveMessage,false);
function receiveMessage(event){
    let { origin,data,source} = event;
    if(origin !== 'http://example.com:8080') return;
    console.log(source+':'+data);
}

/**
* message的属性;
* data 从其他 window 传递来的对象
* origin 调用 postMessage 时消息发送方窗口的 origin，不能保证是该窗口当前的 或者 未来的origin，因为 postMessage被调用后可能被导航到不同的位置
* source 对发送消息窗口的引用，可以使用这个在具有不同 origin 的两个窗口建立双向通信
*/
```

**示例**

```javascript
// A窗口域名是 http://example.com:8080 下面是 A 窗口 script 里面代码
let popup = window.open(...popup details...);
// 如果弹出框没有被阻止且加载完成
// 这行语句没有发送信息出去，即使假设当前页面没有改变location（因为targetOrigin设置不对）
popup.postMessage("The user is 'bob' and the password is 'secret'","https://secure.example.net");
// 假设当前页面没有改变location，这条语句会成功添加message到发送队列中去（targetOrigin设置对了）
popup.postMessage("hello there!","http://example.com");

function receiveMessage(event){
    if(event.origin !== 'http://example.org') return
}

window.addEventListener('message',receiveMessage,false);
```

```javascript
// 弹出页 popup域名是 http://example.org 下面是 script 里面代码

//当A页面postMessage被调用后，这个function被addEventListenner调用
function receiveMessage(event){
    if(event.origin !== 'http://example.com:8080') return
     // event.data 是 "hello there!"
    // event.source 就当前弹出页的来源页面
    event.source.postMessage("hi there yourself!  the secret response " +"is: rheeeeet!",event.origin);
}
window.addEventListener("message", receiveMessage, false);
```

### Web Sockets 通信

使用 Web Sockets API 可以在服务器与客户端之间建立一个非 HTTP 的双向连接。这个连接是实时的，也是要永久的，除非某一方显示关闭。

**常量**

| **Constant**           | **Value** |
| ---------------------- | --------- |
| `WebSocket.CONNECTING` | `0`       |
| `WebSocket.OPEN`       | `1`       |
| `WebSocket.CLOSING`    | `2`       |
| `WebSocket.CLOSED`     | `3`       |

以上是WebSocket 构造函数的原型中存在的一些常量，可通过 `WebSocket.readyState` 对照上述常量判断 WebSocket 连接 当前所处的状态

**用法：**

```javascript
// URL字符串以 ws 或者 wss（加密通信时）文字开头
const socket = new WebSocket('ws://localhost:8080');

socket.addEventListener('open',function(event){
    // send方法对服务器发送数据，只能发送文本数据，可以使用 JSON对象把任何 js对象转换为文本数据后发送
    socket.send('Hello Server!');
});

socket.addEventListerner('message',function(event){
    console.log('Message from server',event.data);
});

//事件句柄
// 接受服务器传过来的数据
socket.onmessage = function(event){
    let { data } = event
}
socket.onopen = function(event){
    // 开始通信
}
// 监听 socket 关闭事件
socket.onclose = function(event){
    // 通信结束时的处理
}
// 关闭 socket，切断通信连接0000
socket.close()
```

## 使用 Web Workers 处理线程

web worker 是运行在后台的 javaScript，不会影响页面的性能。

创建后台线程的步骤很简单。将需要在后台线程中指定的脚本文件的 URL 地址作为参数，然后创建 Worker对象就可以了

```javascript
let worker = new Worker('worker.js');
// 后台线程是不能访问到页面或者窗口对象的，所以如果使用到 window对象或者 document对象会以前你错误的发生
```

可以通过发送和接收消息来与后台线程互相传递数据。通过 Worker 对象的 onmessage 事件句柄活期户后台线程之间的消息

```javascript
worker.onmessage = function(event){
    // 处理收到的消息
}
// message 文本数据
worker.postMessage(message);
```

### 示例：求和计算

```html
<body>
  <h1>从1到给定数值求和</h1>
  输入数值：<input type="text" id="num">
  <button onclick="calculate()">计算</button>
  <script>
    let worker = new Worker('SumCalculate.js');
    worker.onmessage = function(event){
      console.log(event.data);
    }

    function calculate(){
      let num = parseInt(document.getElementById('num').value,10);
      worker.postMessage(num);
    }
  </script>
</body>

```

```javascript
// SumCalculate.js
onmessage = function (event) {
  let num = event.data;
  let result = 0;
  for (let i = 0; i <= num; i++) {
    result += i;
  }
  postMessage(result);
}
```

### 示例：与线程进行数据的交互

```html
<body>
  <h1>从随机生成的数字中抽取3的倍数并显示</h1>
  <table id="table"></table>
  <script>
    // 随机数组
    let intArray = new Array(100);
    let intStr = '';
    // 生成100个随机数
    for (let i = 0; i < 100; i++) {
      intArray[i] = parseInt(Math.random() * 100);
      if (i != 0) {
        intStr += ';';       
      }
      intStr += intArray[i]
    }
    let worker = new Worker('script.js');
    worker.postMessage(intStr);

    worker.onmessage = function (event) {
      if (event.data != '') {
        let j;
        let k;
        let tr;
        let td;
        let intArray = event.data.split(';');
        let table = document.getElementById('table');
        for (let i = 0; i < intArray.length; i++) {
          j = parseInt(i / 10, 0);
          k = i % 10;
          // 该行不存在
          if (k == 0) {
            // 添加行
            tr = document.createElement('tr');
            tr.id = 'tr' + j;
            table.appendChild(tr);
          } else {
            tr = document.getElementById('tr' + j);
          }
          td = document.createElement('td');
          tr.appendChild(td);
          td.innerHTML = intArray[j * 10 + k];
          td.style.backgroundColor = 'blue';
          td.style.color = 'white';
          td.width = 30;
        }
      }
    }
  </script>
</body>
```

```javascript
// script.js
onmessage = function (event) {
  let { data } = event;
  let returnStr="";
  let intArray = data.split(';');
  for (let i = 0; i < intArray.length; i++) {
    if (parseInt(intArray[i]) % 3 == 0) {
      if (returnStr != '') {
        returnStr += ';';
      }
      returnStr += intArray[i]
    }
  }
  postMessage(returnStr)
}
```

### 线程嵌套

**单层嵌套**

```javascript
// html script
let worket = new Worker('script.js')
worket.onmessage = function(event){
    console.log(event.data)
}
// script.js

onmessage = function(event){
    let { data } = event;
    let worker = new Worker('script2.js')
    // 把数据提交给子线程处理
    worker.postMessage(JSON.stringfy(data))
    worker.onmessage = function(event){
        // 把结果返回主页面
        postMessage(event.data);
    }
}

// script2.js
onmessage = function(event){
    let { data } = event;
    result = someMethod(data);
    // 将处理好的处理返回
    postMessage(result)
    // 如果不再使用则关闭子线程
    close();
}
```

**多个线程中进行数据的交互**

实现子线程与子线程之间数据交互的，大致需要下面步骤：

1. 先创建发送数据的子线程
2. 执行子线程中的任务，然后把要传递的数据发送给主线程
3. 在主线程接受到子线程传回来的消息时，创建接受数据的子线程，然后把发送数据的子线程中返回的消息传递给接受数据的子线程
4. 执行接受数据子线程中的代码

```javascript
onmessage = function(event){
	let worker;
    worker = new Worker('worker1.js');
    worker.onmessage = function(event){
        // 接受子线程中的数据
        let { data } = event;
        worker = new Worker('worker2.js');
        // 把从发送数据的子线程中发回的消息传递给接受数据的子线程
        worker.postMessage(data);
        worker.onmessage = function(event){
            // 获取接受数据的子线程中传回的数据
            let {data} = event;
            // 把结果发送到主页面
            postMessage(data);
        }
    }
}

// worker1.js 发送数据的子线程
onmessga = function(event){
    let {someData} = event;
    result = someMethod(someData);
    postMassage(result);
    // 关闭子线程
    close();
}
```

### 线程中可用的变量、函数与类

- self

  表示本线程范围内的作用域

- postMessage(message)

  向创建线程的源窗口发送信息

- onmessage

  获取接受消息的事件句柄

- importScripts(urls)

  导入其他脚本文件，参数为文件的 URL地址，可以导入多个

- navigator

  与 window.navigator对象类似，具有 appName、platform、userAgent、appVersion这些属性

- sessionStorage/localStorage

  可以在线程中使用 Web Storage

- XMLHttpRequest

  在线程中处理 Ajax请求

- setTimeout/setInterval

  在线程中实现定时处理

- close

  结束本线程

- eval/isNaN/escape

  使用 javascipt 的核心函数

- object

  可以创建对象

- WebSockets

  使用 WebSockets API 来想服务器发送和接收信息