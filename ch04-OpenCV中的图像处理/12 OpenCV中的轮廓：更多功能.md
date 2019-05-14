**OpenCV中的轮廓：更多功能**

学习找到凸性缺陷，pointPolygonTest，匹配不同的形状等。

## 目标 ##

在本章中，我们将了解：

- 凸性缺陷以及如何找到它们。
- 寻找从点到多边形的最短距离
- 匹配不同的形状

## 理论和代码 ##

### 凸性缺陷 ###

我们在第二章中看到了关于轮廓的凸包。 物体与该凸包的任何偏差都可以被认为是凸起缺陷。

OpenCV附带了一个现成的函数来查找它，cv.convexityDefects（）。 基本函数调用如下所示：

	hull = cv.convexHull(cnt,returnPoints = False)
	defects = cv.convexityDefects(cnt,hull)

**注意：** 记住我们必须在找到凸包时传递returnPoints = False，以便找到凸起缺陷。

它返回一个数组，其中每一行包含这些值 -  [起点，终点，最远点，到最远点的近似距离]。 我们可以使用图像将其可视化。 我们绘制一条连接起点和终点的线，然后在最远点绘制一个圆。 请记住，返回的前三个值是cnt的索引。 所以我们必须从cnt中提取这些值。

	import cv2 as cv
	import numpy as np
	img = cv.imread('star.jpg')
	img_gray = cv.cvtColor(img,cv.COLOR_BGR2GRAY)
	ret,thresh = cv.threshold(img_gray, 127, 255,0)
	contours,hierarchy = cv.findContours(thresh,2,1)
	cnt = contours[0]
	hull = cv.convexHull(cnt,returnPoints = False)
	defects = cv.convexityDefects(cnt,hull)
	for i in range(defects.shape[0]):
	    s,e,f,d = defects[i,0]
	    start = tuple(cnt[s][0])
	    end = tuple(cnt[e][0])
	    far = tuple(cnt[f][0])
	    cv.line(img,start,end,[0,255,0],2)
	    cv.circle(img,far,5,[0,0,255],-1)
	cv.imshow('img',img)
	cv.waitKey(0)
	cv.destroyAllWindows()

结果如下：

![](https://docs.opencv.org/4.1.0/defects.jpg)

### 点多边形测试 ###

此功能可查找图像中的点与轮廓之间的最短距离。 当点在轮廓外时返回负值，当点在内部时返回正值，如果点在轮廓上则返回零。

例如，我们可以检查点（50,50）如下：

	dist = cv.pointPolygonTest(cnt,(50,50),True)

在函数中，第三个参数是measureDist。 如果为True，则查找标记的距离。 如果为False，则查找该点是在内部还是外部或在轮廓上（它分别返回+1，-1,0）。

**注意：** 如果您不想找到距离，请确保第三个参数为False，因为这是一个耗时的过程。 因此，将其设为False可提供大约2-3倍的加速。

### 匹配形状 ###

OpenCV附带了一个函数cv.matchShapes（），它使我们能够比较两个形状或两个轮廓，并返回一个显示相似性的度量。 结果越低，匹配就越好。 它是根据hu-moment值计算的。 文档中解释了不同的测量方法。

	import cv2 as cv
	import numpy as np
	img1 = cv.imread('star.jpg',0)
	img2 = cv.imread('star2.jpg',0)
	ret, thresh = cv.threshold(img1, 127, 255,0)
	ret, thresh2 = cv.threshold(img2, 127, 255,0)
	contours,hierarchy = cv.findContours(thresh,2,1)
	cnt1 = contours[0]
	contours,hierarchy = cv.findContours(thresh2,2,1)
	cnt2 = contours[0]
	ret = cv.matchShapes(cnt1,cnt2,1,0.0)
	print( ret )

我尝试匹配下面给出的不同形状的形状：

![](https://docs.opencv.org/4.1.0/matchshapes.jpg)

我得到了以下结果：

- 匹配图像A与其自身= 0.0
- 匹配图像A与图像B = 0.001946
- 匹配图像A与图像C = 0.326911

请注意，即使图像旋转对此比较也没有太大影响。

## 练习 ##

- 查看cv.pointPolygonTest（）的文档，您可以找到红色和蓝色的漂亮图像。 它表示从所有像素到其上的白色曲线的距离。 曲线内的所有像素都是蓝色，具体取决于距离。 同样，外部点是红色的。 轮廓边缘标有白色。 所以问题很简单。 编写代码来创建这种距离表示。
- 使用cv.matchShapes（）比较数字或字母的图像。 （这将是向OCR迈出的一小步）

















