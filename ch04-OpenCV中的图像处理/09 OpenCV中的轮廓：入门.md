**OpenCV中的轮廓：入门**

学习查找和绘制轮廓。

## 目标 ##

- 了解轮廓是什么。
- 学习轮廓，绘制轮廓等
- 你会看到这些函数：cv.findContours（），cv.drawContours（）

## 什么是轮廓？ ##

轮廓可以简单地解释为连接所有连续点（沿着边界）的曲线，具有相同的颜色或强度。 轮廓是形状分析和物体检测和识别的有用工具。

- 为了更好的准确性，使用二进制图像 因此，在找到轮廓之前，应用阈值或canny边缘检测。
- 从OpenCV 3.2开始，findContours（）不再修改源图像。
- 在OpenCV中，找到轮廓就像从黑色背景中找到白色物体。 所以请记住，要找到的对象应该是白色，背景应该是黑色。

让我们看看如何找到二进制图像的轮廓：

	import numpy as np
	import cv2 as cv
	im = cv.imread('test.jpg')
	imgray = cv.cvtColor(im, cv.COLOR_BGR2GRAY)
	ret, thresh = cv.threshold(imgray, 127, 255, 0)
	contours, hierarchy = cv.findContours(thresh, cv.RETR_TREE, cv.CHAIN_APPROX_SIMPLE)

参见cv.findContours（）函数中有三个参数，第一个是源图像，第二个是轮廓检索模式，第三个是轮廓近似方法。 它输出轮廓和层次结构。 Contours是图像中所有轮廓的Python列表。 每个单独的轮廓是对象的边界点的（x，y）坐标的Numpy阵列。

**注意：** 我们稍后将详细讨论第二和第三个参数以及层次结构。 在此之前，代码示例中给出的值将适用于所有图像。

## 如何绘制轮廓？ ##

要绘制轮廓，使用cv.drawContours函数。 如果有边界点，它也可以用于绘制任何形状。 它的第一个参数是源图像，第二个参数是应该作为Python列表传递的轮廓，第三个参数是轮廓索引（在绘制单个轮廓时很有用。绘制所有轮廓，传递-1），其余参数是颜色，厚度 等等

- 要绘制图像中的所有轮廓：

		cv.drawContours(img, contours, -1, (0,255,0), 3)

- 要绘制单个轮廓，请说第4个轮廓：

		cv.drawContours(img, contours, 3, (0,255,0), 3)

- 但大多数时候，下面的方法将是有用的：

		cnt = contours[4]
		cv.drawContours(img, [cnt], 0, (0,255,0), 3)

**注意：** 最后两种方法是相同的，但是当你继续前进时，你会发现最后一种方法更有用。

## 轮廓逼近法 ##

这是cv.findContours函数中的第三个参数。 它实际上表示什么？

上面，我们告诉轮廓是具有相同强度的形状的边界。 它存储形状边界的（x，y）坐标。 但是它存储了所有坐标吗？ 这由该轮廓近似方法指定。

如果传递cv.CHAIN_APPROX_NONE，则存储所有边界点。 但实际上我们需要所有的观点吗？ 例如，您找到了直线的轮廓。 你需要线上的所有点来代表那条线吗？ 不，我们只需要该线的两个端点。 这就是cv.CHAIN_APPROX_SIMPLE的作用。 它删除所有冗余点并压缩轮廓，从而节省内存。

下面的矩形图像展示了这种技术。 只需在轮廓阵列中的所有坐标上绘制一个圆圈（以蓝色绘制）。 第一张图片显示了我用cv.CHAIN_APPROX_NONE（734点）获得的点数，第二张图片显示了带有cv.CHAIN_APPROX_SIMPLE（仅4点）的点数。 看，它节省了多少内存！

![](https://docs.opencv.org/4.1.0/none.jpg)