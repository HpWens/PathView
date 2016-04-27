#PathView

马上要到五一了，提前祝贺大家五一快乐。

今天的主角是路劲，这个词给我的第一印象就是那种弯弯的小路，届时让我想起了小时候的`曲径通幽处`，反正我是忘记了下一句是什么。言归正传，`Android`中的`Path`又有哪一些呢，以及它们的常用方法。

##Path常用方法

| 方法     | 作用  | 备注 |
| ------------- |:-------------:| -----:|
| **moveTo** | 移动起点   | 移动下一次操作的起点位置 |
| **lineTo** | 连接直线    | 连接上一个点到当前点之间的直线 |
| **setLastPoint** | 设置终点 |   重置最后一个点的位置 |   
| **close** | 闭合路劲 |  从最后一个点连接最初的一个点，形成一个闭合区域 |  
| **addRect** | 添加矩形 |   添加矩形到当前Path |   
| **addRoundRect** | 添加圆角矩形 |   添加圆角矩形到当前Path |   
| **addOval** | 添加椭圆 |   添加椭圆到当前Path |   
| **addCircle** | 添加圆 |   添加圆到当前Path |   
| **addPah** | 添加路劲 |   添加路劲到当前Path |   
| **addArc** | 添加圆弧 |  添加圆弧到当前Path  |  
| **arcTo** |   圆弧 |  绘制圆弧，注意和addArc的区别  |  
| **isEmpty** | 是否为空 |  判定Path是否为空  |  
| **isRect** | 是否为矩形 |  判定Path是否是一个矩形  |  
| **set** | 替换路劲 |  用新的路劲替换当前路劲的所有内容  |  
| **offset** | 偏移路劲 |  对当前的路劲进行偏移  |  
| **quadTo** | 贝塞尔曲线 |  二次贝塞尔曲线的方法  |   
| **cubicTo** | 贝塞尔曲线 |  三次贝塞尔曲线的方法  |  
| **rMoveTo,rlineTo,rQuadTo,rCubicTo** | rXxx方法 |  不带r的方法是基于原点坐标系（偏移量），带r的基于当前点坐标系（偏移量）  |  
| **op** | 布尔操作 |  对两个Path进行布尔运算（交集，并集）等操作  |  
| **setFillType** |  填充模式 |  设置Path的填充模式   | 
| **getFillType** |  填充模式 |  获取Path的填充   | 
| **isInverseFillType** |  是否逆填充 |  判断是否是逆填充模式  | 
| **toggleInverseFillType** |  相反模式 |  切换相反的填充模式  | 
| **getFillType** |  填充模式 |  获取Path的填充   | 
| **incReserve** |  提示方法 |  提示Path还有多少个点等待加入   |  
| **computeBounds** |  计算边界 |  计算Path的路劲   |  
| **reset，rewind** |  重置路劲 |  清除Path中的内容（reset相当于new Path , rewind 会保留Path的数据结构）    
| **transform** |  矩阵操作 |  矩阵变换   |  

##Path方法使用详解

使用`Path`不仅可以绘制简单的图形（如圆形，矩形，直线等），也可以绘制复杂一些的图形（如正多边形，五角星等），还有绘制裁剪和绘制文本都会用到`Path`。由于方法比较多，我这里分组来讲下。

###moveTo , lineTo , setLastPoint , close

先创建画笔：

```
        paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setStrokeWidth(10);
        paint.setColor(Color.parseColor("#FF0000"));
```

**注意` paint.setStyle(Paint.Style.FILL);`，设置画笔为实心。一些线条将在画布上看不见。**

####1、lineTo

首先我们来看看`lineTo`,如果你直接`moveTo` 将看不出效果。

```
        Path path = new Path();

        path.lineTo(200,200);
        path.lineTo(400,0);

        canvas.drawPath(path,paint);
```

![path](http://img.blog.csdn.net/20160426092050219)

为了方便大家好观察坐标的变化，我在屏幕上画出了网格，每块网格的宽高都是100。由于第一次之前没有过操作，所以默认点就是原点（屏幕左上角），第一次`lineTo`就是坐标原点到(200,200)之间的直线。第二次`lineTo`就是上一次结束点位置(200,200)到(400,0)点之间的直线。

####2、moveTo 和setLastPoint 

方法预览：
```
moveTo(float x, float y) 

setLastPoint(float dx, float dy)
```
这两个方法在作用上有相似之处，却是两个不同的东西，具体参考下表：

| 方法名         | 作用  | 是否影响之前的操作 |是否影响之后的操作|
| ------------- |:-------------:| -----:|-----:|
| **moveTo**    | 移动下一次操作的起点位置   | 否 | 是|
| **setLastPoint**   | 改变上一次操作点的位置   | 是 | 是|


----------
来看看下面的例子：

```
        Path path = new Path();

        path.lineTo(200, 200);
        path.moveTo(300,300);
        path.lineTo(400, 0);

        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426094923466)

```
        Path path = new Path();

        path.lineTo(200, 200);
        path.setLastPoint(300,100);
        path.lineTo(400, 0);

        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426095229795)

当我们绘制线条之前，调用`moveTo` 和 `setLastPoint`效果是一样的，都是对坐标原点(0,0)进行操作。`setLastPoint`是重置上一次操作的最后一点，在执行完第一次`lineTo`的时候，最后一个点就是(200,200),`setLastPoint`更改(200,200)为(300,100)，所以在执行的时候就是(300,100)到(400, 0)之间的连线了。

####3、close 

方法预览
```
public void close()
```
`close`方法连接最后一个点和最初一个点（如果两个点不重合）形成一个闭合的图形。
```
        path.moveTo(100,100);
        path.lineTo(500,100);
        path.lineTo(300,400);
        path.close();
        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426105730065)

上图中可以看到`lineTo(500,100)`直线和`lineTo(300,400)`直线，而`close`方法就是连接(300,400)，(100,100)两点，形成一个闭合的区域。

**注意：close的作用的封闭路径，如果连接最后一个点和最初一个点任然无法形成闭合的区域，那么close什么也不做。**

###quadTo,cubicTo

二次贝塞尔曲线以及三次贝塞尔曲线。

####1、quadTo

方法预览
```
public void quadTo(float x1, float y1, float x2, float y2)
```
`quadTo`方法其中 (x1,y1) 为控制点，(x2,y2)为结束点。

```
        path.moveTo(100,400);
        path.quadTo(300, 100, 400, 400);

        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426111419867)

####2、cubicTo

方法预览：
```
public void cubicTo(float x1, float y1, float x2, float y2, float x3, float y3)
```
`cubicTo`方法比`quadTo`方法多了一个点坐标，那么其中(x1,y1) 为控制点，(x2,y2)为控制点，(x3,y3) 为结束点。

```
        path.moveTo(100, 400);
        path.cubicTo(100, 400, 300, 100, 400, 400);

        canvas.drawPath(path, paint);
```
绘制的图形和上面的`quadTo`绘制的图形是一样的。我们去掉`moveTo`来看看运行的效果图：

![path](http://img.blog.csdn.net/20160426112325207)

如果你想了解[贝塞尔曲线公式，请链接这里](http://baike.baidu.com/link?url=jRgMYu81F4aEnyyWXUB65Ihj0fV6rbDhzEZhO4xE6ZU7r98F-fzRewd4-Os88iX_gBlHguZWeB4k97VGKmqvVq)

###addXxx和arcTo

主要是向`Path`中添加基本图形以及区分`addArc`和`arcTo`

####1、添加基本图形

方法预览：
```
//圆形
addCircle(float x, float y, float radius, Path.Direction dir)
//椭圆
addOval(RectF oval, Path.Direction dir)
addOval(float left, float top, float right, float bottom, Path.Direction dir)
//矩形
addRect(RectF rect, Path.Direction dir)
addRect(float left, float top, float right, float bottom, Path.Direction dir)
//圆角矩形
addRoundRect(RectF rect, float rx, float ry, Path.Direction dir) 
addRoundRect(float left, float top, float right, float bottom, float rx, float ry, Path.Direction dir)
addRoundRect(RectF rect, float[] radii, Path.Direction dir)
addRoundRect(float left, float top, float right, float bottom, float[] radii, Path.Direction dir)
```
我们仔细观察上面的方法，在最后都有一个`Path.Direction`，这是个什么东东呢？`Direction`的意思是方向，指导，趋势。点进去跟一下你会发现`Direction`是一个枚举类型（Enum）分别有CW（顺时针），CCW（逆时针）两个常量。那么它的作用主要有以下两点：

| 序号        |         作用           |
| ------------- |:-------------:|
| **1**    | 在添加图形时确定闭合顺序(各个点的记录顺序)|
| **2**   | 对自相交图形的渲染结果有影响|


----------
我们先来看看闭合顺序的问题，添加一个矩形看看：
```
        path.addRect(100, 200, 500, 400, Path.Direction.CW);

        canvas.drawPath(path, paint);
```

![path](http://img.blog.csdn.net/20160426135209234)

我将上面的代码`CW`改成`CCW`再运行一次，结果一模一样，尼玛区别在哪里啊。稍安勿躁，想看到区别就要用到`setLastPoint`（重置最后一个点的坐标）。我们来这样变变代码：
```
        path.addRect(100, 200, 500, 400, Path.Direction.CW);
        path.setLastPoint(200,400);

        canvas.drawPath(path, paint);
```
效果立马现行：

![path](http://img.blog.csdn.net/20160426135826519)

为什么图形会发生奇怪的变化呢。我们先来分析一下，绘制一个矩形至少需要对角线的两个点，根据这两个点计算出四条边然后把四条边按照顺序连接起来。上图的起始坐标是（100,200）按着顺时针的方向连接（500,200），（500,400），（100,400）最后连接（100,200）形成一个矩形。`setLastPoint`是重置上一个操作点坐标及改变（100,400）为（200,400），所以出现了上图的效果。

接下来我们看看逆时针的情况：
```
        path.addRect(100, 200, 500, 400, Path.Direction.CCW);
        path.setLastPoint(400,300);

        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426141156197)

我们理清楚了闭合的问题，相交问题与设置填充模式有关。

我以`addCircle`方法来讲解添加图形，并不会逐一讲解。

```
        path.addCircle(300,300,200, Path.Direction.CW);

        canvas.drawPath(path, paint);
```

![path](http://img.blog.csdn.net/20160426141658220)

绘制圆形，（300,300）点表示圆心坐标，200 表示半径长度。

####2、addPath

方法预览：
```
public void addPath(Path src)
public void addPath(Path src, float dx, float dy)
public void addPath(Path src, Matrix matrix)
```
**addPath**方法就是将两个路径合并到一起。第二个方法的`dx,dy`指的是偏移量，第三个方法是添加到当前path之前先使用Matrix进行变换。来看看下面例子：
```
        Path path = new Path();
        path.addRect(100,100,400,300, Path.Direction.CW);

        Path src=new Path();
        src.addCircle(300,300,100, Path.Direction.CW);
        path.addPath(src,0,100);

        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426144022285)

####3、addArc与arcTo

方法预览：
```
addArc(RectF oval, float startAngle, float sweepAngle)
addArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle)

arcTo(RectF oval, float startAngle, float sweepAngle)
arcTo(RectF oval, float startAngle, float sweepAngle, boolean forceMoveTo)
arcTo(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean forceMoveTo)
```
从方法名字上面看，这两个方法都是与圆弧有关，那么他们之间肯定是有区别的：

| 名称       |         作用           | 区别 |
| ------------- |:-------------:|:-------------:|
| **addArc**    | 添加一个圆弧到Path|直接添加一个圆弧到path中，和上一次操作点无关|
| **arcTo**   | 添加一个圆弧到Path|添加一个圆弧到path中，如果圆弧的起点和上次操作点坐标不同就连接两个点|


----------
`startAngle`表示开始圆弧度数（0度与X轴方向对齐，顺时针移动，弧度增大）。

注意：`sweepAngle`表示运动了多少弧度，并不是结束弧度。

**forceMoveTo**表示“是否强制使用moveTo”，也就是说是否使用moveTo将上一次操作点移动到圆弧的起点坐标。默认是false。

| forceMoveTo       |         含义          |
| ------------- |:-------------:|
| **true**    | 将最后一个点移动到圆弧起点，即不连接最后一个点与圆弧起点|
| **false**   | 不移动，而是连接最后一个点与圆弧起点(注意之前没有操作的话，不会连接原点)|


----------
示例：
```
        path.lineTo(200, 200);
        RectF rectF = new RectF(100, 100, 400, 400);
        path.arcTo(rectF, 0, 270, true);
        // path.addArc(rectF,0,270);和上面一句等价

        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426151036609)

我们把 `path.arcTo(rectF, 0, 270, true);`改成 `path.arcTo(rectF, 0, 270, false);`，来看看效果图：

![path](http://img.blog.csdn.net/20160426151602625)

从上面两张图可以看出明显的变化。

###isEmpty、 isRect、 set 和 offset

####isEmpty

判断path中是否包含内容。
```
        Path path = new Path();
        Log.e("-----","----"+path.isEmpty());//-----: ----true
        path.lineTo(100,100);
        Log.e("-----","----"+path.isEmpty());//-----: ----false
        
        canvas.drawPath(path, paint);
```
####isRect

方法预览：
```
isRect(RectF rect)
```
判断path是否是一个矩形，如果是一个矩形的话，会将矩形的信息存放进参数rect中。
```
        Path path = new Path();
        RectF rectF = new RectF();
        rectF.left = 100;
        rectF.top = 100;
        rectF.right = 400;
        rectF.bottom = 300;
        path.addRect(rectF, Path.Direction.CW);
        boolean isRect = path.isRect(rectF);
        Log.e("-----","------"+isRect);//-----: ------true
```
####set 

方法预览：
```
public void set(Path src)
```
将新的path赋值到现有path。相当于运算符中的“=”，如`a=b`,把`b`赋值给`a`

还是一起来看个例子：
```
        Path path = new Path();
        path.addRect(100,100,400,300, Path.Direction.CW);
        Path src=new Path();
        src.addCircle(300,200,100, Path.Direction.CW);
        path.set(src);
        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426153502089)

####offset

方法预览：
```
public void offset(float dx, float dy)
public void offset(float dx, float dy, Path dst)
```
这个方法就是对`Path`进行一段平移，正方向和X轴，Y轴方向一致（如果dx为正数则向右平移，反之向左平移；如果dy为正则向下平移，反之向上平移）。我们看到第二个方法多了一个`dst`，这个又是一个什么玩意呢，其实参数das是存储平移后的path的。

用例子来说明一下：
```
        Path path = new Path();
        path.addCircle(300, 200, 100, Path.Direction.CW);

        Path dst = new Path();
        dst.addCircle(500, 200, 200, Path.Direction.CW);

        path.offset(-100, 100, dst);
        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426155358974)

从运行效果图可以看出，虽然我们在dst中添加了一个圆形，但是并没有表现出来，所以，当dst中存在内容时，dst中原有的内容会被清空，而存放平移后的path。

###FillType

方法预览：
```
public void setFillType(Path.FillType ft)
public Path.FillType getFillType()
```
`setFillType`方法中的参数`Path.FillType`为枚举类型：

| FillType值     |         含义          |
| ------------- |:-------------:|
| **FillType.WINDING**  |**取path所有所在区域 默认值** |
| **FillType.EVEN_ODD** | **取path所在并不相交区域**|
| **FillType.INVERSE_WINDING** | **取path所有未占区域**|
| **FillType.INVERSE_EVEN_ODD** | **取path未占或相交区域**|


----------
path所在区域
path所在区域
path所在区域

####setFillType

**WINDING**
```
        Path path = new Path();
        path.addCircle(300,200,100, Path.Direction.CW);
        path.addCircle(200,200,100, Path.Direction.CW);
        path.setFillType(Path.FillType.WINDING);
        canvas.drawPath(path, paint);
```
效果图：

![path](http://img.blog.csdn.net/20160426162224606)

**EVEN_ODD**

代码我就不贴了，直接上图（上mis）：

![path](http://img.blog.csdn.net/20160426162503342)

**INVERSE_WINDING**

![path](http://img.blog.csdn.net/20160426162855848)

**INVERSE_EVEN_ODD**

![path](http://img.blog.csdn.net/20160426163225033)

####isInverseFillType

是否是逆填充模式：WINDING 和 EVEN_ODD 返回false；
INVERSE_WINDING 和 INVERSE_EVEN_ODD 返回true；

####toggleInverseFillType

切换相反的填充模式，如果填充模式为`WINDING`则填充模式为`INVERSE_WINDING`，反之为`WINDING`模式；如果填充模式为`EVEN_ODD`则填充模式为`INVERSE_EVEN_ODD`，反之为`EVEN_ODD`模式。

举个例子：
```
        Path path = new Path();
        path.addCircle(300,200,100, Path.Direction.CW);
        path.addCircle(200,200,100, Path.Direction.CW);
        path.setFillType(Path.FillType.INVERSE_EVEN_ODD);
        path.toggleInverseFillType();
        canvas.drawPath(path, paint);
```
效果图和上面`EVEN_ODD`模式一模一样。

![path](http://img.blog.csdn.net/20160426162503342)

就写到这里，文中有什么写错，写偏的请给我留言。
