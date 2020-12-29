# canvas

+ canvas 以左上角为坐标原点,向右是x轴正向,向下是y轴正向
+ canvas 是基于状态的,先设置状态再stroke
+ beginPath()是开始一段新的线条
+ beginPath()+lineTo() = moveTo()
+ 没有beginPath绘制多条线段时状态会被覆盖,所有线条共用最后一个状态
+ closePath 可以封闭路径
+ 当我们需要描边的填充图案,要先填充,再描边,如果先描边再填充描边的边宽会小一半

## 基础绘制线条:

~~~css
<script type="text/javascript">
			var canvas = document.getElementById('canvas');
			if (canvas.getContext('2d')) {
				var context = canvas.getContext('2d');
			} else {
				alert('当前游览器不支持canvas')
			}
			context.beginPath()
			context.moveTo(100, 180); // 起点
			context.lineTo(500, 500); //终点 下一个的起点
			context.lineTo(200, 200) //终点 下一个的起点
			context.lineTo(100, 180) //终点
			context.fillStyle = "rgb(2,100,30)" //css填充着色状态
			context.fill() //开始着色
			context.lineWidth = 1; //线宽
			context.strokeStyle = "aqua"; //css线条颜色
			context.stroke() //开始绘画线条
			context.closePath()//自动封闭当前的路径 如果没封闭会加线段

			//如果没有begin和close,状态会共享
			context.beginPath()
			context.moveTo(0, 100)
			context.lineTo(100, 100)
			context.lineWidth = 2;

			context.strokeStyle = 'red'
			context.stroke()
			context.closePath()
		</script>
~~~

## 绘制七巧板

~~~javascript
<script type="text/javascript">
			var canvas = document.getElementById('canvas');
			if (canvas.getContext('2d')) {
				var context = canvas.getContext('2d');
			} else {
				alert('当前游览器不支持canvas')
			}
			var tangram = [
				{p:[{x:0,y:0},{x:800,y:0},{x:400,y:400}],color:"#caff67"},
				{p:[{x:0,y:0},{x:400,y:400},{x:0,y:800}],color:"#67becf"},
				{p:[{x:800,y:0},{x:800,y:400},{x:600,y:600},{x:600,y:200}],color:"#ef3d61"},
				{p:[{x:600,y:200},{x:600,y:600},{x:400,y:400}],color:"#f9f51a"},
				{p:[{x:400,y:400},{x:600,y:600},{x:400,y:800},{x:200,y:600}],color:"#a594c0"},
				{p:[{x:200,y:600},{x:400,y:800},{x:0,y:800}],color:"#fa8ecc"},
				{p:[{x:800,y:400},{x:800,y:800},{x:400,y:800}],color:"#f6ca29"},
			]
			var context = canvas.getContext('2d');
			function draw(piece,ctx){
				ctx.beginPath();
				ctx.moveTo(piece.p[0].x,piece.p[0].y)
				for (var j=1;j<piece.p.length;j++){
					ctx.lineTo(piece.p[j].x,piece.p[j].y)
				}
				ctx.fillStyle = piece.color;
				ctx.fill()
				ctx.closePath();
			}
			for(var i=0;i<tangram.length;i++){
				draw(tangram[i],context)
			}
			
		</script>
~~~

## 绘制圆

~~~javascript
<script type="text/javascript">
			var canvas = document.getElementById('canvas');
			if (canvas.getContext('2d')) {
				var context = canvas.getContext('2d');
			} else {
				alert('当前游览器不支持canvas')
			}
			context.lineWidth = 2;
			context.strokeStyle = 'black';
			context.arc(300,300,200,0,0.5*Math.PI,true);//x坐标,y坐标,半径,起始弧度,终止弧度,是否开启逆时针默认false
			context.stroke()
		</script>
~~~

![image-20191221171257160](canvas.assets/image-20191221171257160.png)

## 绘制的矩形API

* 矩形 ctx.rect(x,y,width,height),ctx.fillRect(x,y,width,height),ctx.strokeRect(x,y,width,height)
  * fillRect 会使用当前的fillStyle 绘制有填充颜色的矩形
  * strokeRect 会使用当前的strokeStyle 绘制有边框颜色的矩形
  * fillRect ,strokeRect 一起用就有上面两种效果

## css颜色

* #ffffff

- #642 相当于#664422 这个是简写形式
- rgb
- rgba
- hsl
- hsla
- red

## 线条衔接方式

* 圆角 context.lineJoin='round'
* 折叠 context.lineJoin='bevel'
* 默认尖角 context.lineJoin='miter' 默认的miter的尖角有限制
  * context.miterLimit = 10 内角和外角的距离的最大值是10,超过最大值自动转化为bevel
  * <img src = "./img/miterLimit.bmp" zoom="30" style="zoom:80%"> 

## 图形变换

### context.transla(x,y)平移位置

* 必须在绘画 或者填充前使用才能平移

  ~~~javascript
  			context.fillStyle = 'red';
  			context.translate(100,100)
  			context.fillRect(0,0,400,400);
  ~~~

* translate 是叠加的

  ~~~javascript
  			context.fillStyle = 'red';
  			context.translate(100,100)
  			context.fillRect(0,0,400,400);
  			context.fillStyle = 'green';
  			context.translate(300,300)
  			context.fillRect(0,0,400,400);
  			//红色矩形不会变,绿色变成平移(400,400)
  ~~~

  * 解决办法

    ~~~javascript
    			context.fillStyle = 'red';
    			context.translate(100,100)
    			context.fillRect(0,0,400,400);
    
    			//增加这一行
                context.translate(-100,-100);
    			
    			context.fillStyle = 'green';
    			context.translate(300,300)
    			context.fillRect(0,0,400,400);
    			//红色矩形不会变,绿色变成平移(400,400)
    ~~~

  * 另一种解决办法 推荐使用

    ~~~
    			context.save();//存储状态
    			context.fillStyle = 'red';
    			context.translate(100,100)
    			context.fillRect(0,0,400,400);
    			context.restore();//读取状态
    			
    			context.fillStyle = 'green';
    			context.translate(300,300)
    			context.fillRect(0,0,400,400);
    			
    ~~~

### context.rotate(deg)旋转角度

~~~javascript
	context.rotate(rotate/180*Math.PI)
~~~

### context.scale(sx,sy)缩放

* 对x方向缩放sx倍
* 对y方向缩放sy倍
* 有副作用
  1. 对左上角的距离也会缩放
  2. 边框宽度也会缩放
  3. 解决办法
     * 放弃外边框的绘制

### 图形变换矩阵

![image-20191220231956352](canvas.assets/image-20191220231956352.png)

有了变换矩阵可以不适用上面三种方法,平移旋转缩放,直接改变换矩阵参数

**context.transform(a,b,c,d,e,f)**

主对角线是缩放,主对角线缩放元素上或者下一个是rotate,剩下最右边的是位移

tramsform 也可以叠加

setTramsform 是覆盖之前叠加的效果,context.setTransorm(a,b,c,d,e,f)

### context.save()

context.save()能保存状态

context.restore()可以恢复图形变换原先保存的状态

## 线性渐变 fillStyle

~~~javascript
			var grd= context.createLinearGradient(0,0,100,100) //x,y同时渐变
            //context.createLinearGradient(x0,y0,x1,y1)代表了从哪到哪渐变
			grd.addColorStop(0.01,'red')//这个addColorStop可以添加无数个,是渐变的颜色和范围
			grd.addColorStop(0.3,'black')
			grd.addColorStop(0.5,'blue')
			grd.addColorStop(1,'white')
			context.fillStyle = grd;//改变fillstyle状态
			context.fillRect(0,0,100,100); //填充
			

~~~

![x,y同时渐变](canvas.assets/image-20191221142727763.png)

<img src="canvas.assets/image-20191221143006404.png" alt="剩下的canvas区域是最后渐变的颜色,这里是白色" style="zoom:50%;" />

## 径向渐变

在同心圆才有的渐变,放射状

<img src="canvas.assets/image-20191221150729432.png" alt="image-20191221150729432" style="zoom:50%;" />

## createPattern  纹理

![image-20191221154222444](canvas.assets/image-20191221154222444.png)

createPattern(canvas,repeat-style)

功能强大不仅可以放图片,也可以放canvas作为重复的对象

## arcTo 做亮条直线的切边圆

![image-20191221204319701](canvas.assets/image-20191221204319701.png)

其中x0,y0 是一开始的moveTo或者lineTo的点.arcTo前面要有一个点,arcTo会连接这个点并且做圆,相切圆的半径为radius

半径过大的情况是这样的

![image-20191221210244972](canvas.assets/image-20191221210244972.png)

## 贝塞尔曲线

贝塞尔曲线和acrTo相似也是起始点控制点终止点

不过贝塞尔曲线是与起始点和终止点相切,而arcTo不是相切,arcTo是找到圆弧与直线相切,贝塞尔曲线是找到曲线和两点相切,曲线不一定是圆弧

quadraticCurveTo()二次贝塞尔曲线 第一个和第二个参数是控制点坐标

![image-20191222105642852](canvas.assets/image-20191222105642852.png)

三次贝塞尔曲线

![image-20191222105733954](canvas.assets/image-20191222105733954.png)

x1,x2,y1,y2是两个控制点的坐标x3y3是终止点的坐标



 //贝塞尔曲线
       //context.moveTo(x0,y0);
       //context.quadraticCurveTo(x1,y1,x2,y2);贝塞尔二次曲线
       //https://tinyurl**.com**/html5quadratic此页面形象直观的展示了此函数的绘制效果
       //context.bezierCurveTo(x1,y1,x2,y2,x3,y3);贝塞尔三次次曲线
       //https://tinyurl.com/html5bezier

## 文字渲染

context.font = "blod 40px Arial" //字体设置

context.fillText(string,x,y) //实心字

context.strokeStyle = "#058"

context.lineWidth = 1

context.strokeText('欢迎大家学习canvas')//空心字

<img src="canvas.assets/image-20191222150738185.png" alt="image-20191222150738185" style="zoom:50%;" />

maxlen 单位是px,代表最大长度,超过的话会被压缩



### 字和渐变色结合的效果

<img src="canvas.assets/image-20191222151059497.png" alt="image-20191222151059497" style="zoom:50%;" />

![image-20191222151214506](canvas.assets/image-20191222151214506.png)



## 也可以和图像纹理结合,把字变成纹理画出来的字<img src="canvas.assets/image-20191222150958491.png" alt="image-20191222150958491" style="zoom:80%;" />



![image-20191222151337473](canvas.assets/image-20191222151337473.png)







### 字体默认信息

![image-20191222151458000](canvas.assets/image-20191222151458000.png)

#### font-style

![image-20191222151553003](canvas.assets/image-20191222151553003.png)

italic 是如果有设置的斜体就用斜体 没有的时候和normal一样

oblique是把字体倾斜

#### font-variant

![image-20191222151802450](canvas.assets/image-20191222151802450.png)

把小写字母换成大写字母,但是比普通大写字母小一点,和小写字母一样大

#### font-weight

![image-20191222152012653](canvas.assets/image-20191222152012653.png)

#### font-size

<img src="canvas.assets/image-20191222152051932.png" alt="image-20191222152051932" style="zoom:67%;" />

<img src="canvas.assets/image-20191222152126495.png" alt="image-20191222152126495" style="zoom:67%;" />

### 文本对齐

挺多种方式的,看网上

## 阴影

![image-20191222152918402](canvas.assets/image-20191222152918402.png)

![image-20191222152955819](canvas.assets/image-20191222152955819.png)

## 全局透明度

### context.globalAlpha= 0~1

### context.globalCompositeOperation 这个是后绘制的图形盖在前绘制的图形的图形是怎么样的

默认 source-over 后覆盖前面的

destination-over 是前面绘制的覆盖后面的



![image-20191222154614985](canvas.assets/image-20191222154614985.png)

## 获取canvas的鼠标位置

![image-20191222160450943](canvas.assets/image-20191222160450943.png)

标准的获得canvas鼠标位置的方法,不变得方法,从来都是这样获得鼠标位置



# canvas本身不是透明的,会遮挡其他元素

## canvas图形库

![image-20191222174040820](canvas.assets/image-20191222174040820.png)





## 小球update状态

![image-20191222175253649](canvas.assets/image-20191222175253649.png)

## canvas图像渲染

### drawImage

![image-20200224151441681](canvas.assets/image-20200224151441681.png)

dx,dy是起始的x,y值

![image-20200224151658405](canvas.assets/image-20200224151658405.png)

sx,sy,sw,sh都是对原图像操作