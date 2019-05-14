**OpenCV中的轮廓：特征**

学习找到轮廓的不同特征，如区域、周长、边界矩形等。

## 目标 ##

在本文中，我们将学习：

- 找到轮廓的不同特征，如面积、周长、质心、边界框等
- 您将看到许多与轮廓相关的功能。

## 1. 图像矩 ##

图像矩可以帮助你计算物体的质心、面积等特征。查看维基百科关于图片时刻的页面
函数cv.moments()给出了一个所有计算得到的矩值的字典。见下文:

	import numpy as np
	import cv2 as cv
	img = cv.imread('star.jpg',0)
	ret,thresh = cv.threshold(img,127,255,0)
	contours,hierarchy = cv.findContours(thresh, 1, 2)
	cnt = contours[0]
	M = cv.moments(cnt)
	print( M )

从这这些图像矩中，您可以提取有用的数据，如面积、质心等。质心由关系给出，Cx = M10/M00和Cy = M01/M00。 这可以按如下方式完成：

	cx = int(M['m10']/M['m00'])
	cy = int(M['m01']/M['m00'])

## 2. 轮廓区域 ##

轮廓区域由函数cv.contourArea（）或从矩M ['m00']给出。

	area = cv.contourArea(cnt)

## 3. 轮廓周长 ##

它也被称为弧长，可以使用cv.arcLength（）函数找到它。 第二个参数指定形状是闭合轮廓（如果传递为True），还是仅仅是曲线。

	perimeter = cv.arcLength(cnt,True)

## 4. 轮廓逼近 ##

它根据我们指定的精度将轮廓形状近似为具有较少顶点数的另一个形状。 它是Douglas-Peucker算法的一种实现方式。 查看维基百科页面以获取算法和演示。

要理解这一点，假设你试图在图像中找到一个正方形，但由于图像中的某些问题，你没有得到一个完美的正方形，而是一个“坏形状”（如下面第一张图所示）。 现在您可以使用此功能来近似形状。 在此，第二个参数称为epsilon，它是从轮廓到近似轮廓的最大距离。 这是一个准确度参数。 需要明智的选择epsilon才能获得正确的输出。

	epsilon = 0.1*cv.arcLength(cnt,True)
	approx = cv.approxPolyDP(cnt,epsilon,True)

下面，在第二张图像中，绿线表示epsilon =弧长的10％的近似曲线。 第三幅图像显示相同的epsilon =弧长的1％。 第三个参数指定曲线是否关闭。

![](https://docs.opencv.org/4.1.0/approx.jpg)


## 5. 凸壳 ##

凸壳看起来类似于轮廓近似，但事实并非如此（两者在某些情况下可能会提供相同的结果）。 这里，cv.convexHull（）函数检查曲线是否存在凸性缺陷并进行修正。 一般而言，凸曲线是总是凸出或至少平坦的曲线。 如果它在内部膨胀，则称为凸性缺陷。 例如，检查下面的手形图像。 红线表示手的凸包。 双面箭头标记显示凸起缺陷，即船体与轮廓的局部最大偏差。

![](https://docs.opencv.org/4.1.0/convexitydefects.jpg)

有一些事情要讨论它的语法：

	hull = cv.convexHull(points[, hull[, clockwise[, returnPoints]]

参数详情：

- points： 我们传入的轮廓。
- hull： 是输出，通常不需要。
- clockwise : 方向标志。 如果为True，则输出凸包顺时针方向。 否则，它逆时针方向。
- returnPoints : 默认为True。 然后它返回壳的坐标。 如果为False，则返回与船体点对应的轮廓点的索引。

因此，要获得如上图所示的凸包，以下就足够了：

	hull = cv.convexHull(cnt)

但是如果你想找到凸性缺陷，你需要传递returnPoints = False。 为了理解它，我们将采用上面的矩形图像。 首先，我发现它的轮廓为cnt。 现在我发现它的凸包有returnPoints = True，我得到以下值：[[[234 202]]，[[51 202]]，[[51 79]]，[[234 79]]]这四个角落 矩形点。 现在如果使用returnPoints = False做同样的事情，我得到以下结果：[[129]，[67]，[0]，[142]]。 这些是轮廓中对应点的索引。 例如，检查第一个值：cnt [129] = [[234,202]]，它与第一个结果相同（依此类推）。

当我们讨论凸性缺陷时，你会再次看到它。

## 6. 检查凸性 ##

有一个功能可以检查曲线是否凸起，cv.isContourConvex（）。 它只返回True或False。 

	k = cv.isContourConvex(cnt)

## 7. 边界矩形 ##

有两种类型的边界矩形。

### 直边矩形 ###

它是一个直的矩形，它不考虑对象的旋转。 因此，边界矩形的面积不会最小。 它由函数cv.boundingRect（）找到。

设（x，y）为矩形的左上角坐标，（w，h）为宽度和高度。

	x,y,w,h = cv.boundingRect(cnt)
	cv.rectangle(img,(x,y),(x+w,y+h),(0,255,0),2)

### 旋转矩形 ###

这里，以最小面积绘制边界矩形，因此它也考虑旋转。 使用的函数是cv.minAreaRect（）。 它返回一个Box2D结构，其中包含以下detals  - （center（x，y），（width，height），rotation of rotation）。 但是要绘制这个矩形，我们需要矩形的4个角。 它是通过函数cv.boxPoints（）获得的。

	rect = cv.minAreaRect(cnt)
	box = cv.boxPoints(rect)
	box = np.int0(box)
	cv.drawContours(img,[box],0,(0,0,255),2)

两个矩形都显示在单个图像中。 绿色矩形显示正常的边界矩形。 红色矩形是旋转的矩形。

![](https://docs.opencv.org/4.1.0/boundingrect.png)

## 8. 最小封闭圈 ##

接下来，我们使用函数cv.minEnclosingCircle（）找到对象的外接圆。 它是一个圆圈，以最小的面积完全覆盖物体。

	(x,y),radius = cv.minEnclosingCircle(cnt)
	center = (int(x),int(y))
	radius = int(radius)
	cv.circle(img,center,radius,(0,255,0),2)

![](https://docs.opencv.org/4.1.0/circumcircle.png)

## 9. 拟合椭圆 ##

接下来是将椭圆拟合到一个物体上。 它返回刻有椭圆的旋转矩形。

	ellipse = cv.fitEllipse(cnt)
	cv.ellipse(img,ellipse,(0,255,0),2)

![](https://docs.opencv.org/4.1.0/fitellipse.png)

## 10. 拟合一条线 ##

同样，我们可以在一组点上拟合一条线。 下图包含一组白点，可以近似直线。

	rows,cols = img.shape[:2]
	[vx,vy,x,y] = cv.fitLine(cnt, cv.DIST_L2,0,0.01,0.01)
	lefty = int((-x*vy/vx) + y)
	righty = int(((cols-x)*vy/vx)+y)
	cv.line(img,(cols-1,righty),(0,lefty),(0,255,0),2)

![](https://docs.opencv.org/4.1.0/fitline.jpg)
