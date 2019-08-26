**OWindows下安装OpenCV-Python**

## 目标 ##

在本教程中，我们将学习在Windows系统中设置OpenCV-Python。

下面的步骤在Windows 7-64位计算机上使用Visual Studio 2010和Visual Studio 2012进行测试。屏幕截图显示了VS2012。

## pip指令安装 ##

可直接用pip命令安装：

**pip install opencv-python**

然后自动安装完成。

## 从二进制文件安装OpenCV ##

- 下面的Python包将被下载并安装到其默认位置。
	- Python的2.7.x
	- NumPy
	- Matplotlib（Matplotlib是可选的，但推荐，因为我们在教程中使用了很多）。
- 将所有包安装到其默认位置。 Python将安装到C：/ Python27 /。
- 安装完成后，打开Python IDLE。 输入import numpy并确保Numpy工作正常。
- 从sourceforge站点下载最新的OpenCV版本，然后双击以将其解压缩。
- 转到opencv / build / python / 2.7文件夹。
- 将cv2.pyd复制到C：/ Python27 / lib / site-packages。
- 打开Python IDLE并在Python终端中键入以下代码。

		import cv2
		print( cv2.__version__ )

如果结果打印出来没有任何错误，恭喜！ 您已成功安装OpenCV-Python。

## 从源代码构建OpenCV ##

1. 下载并安装Visual Studio和CMake。
	- Visual Studio 2012
	- CMake
1. 下载并安装必要的Python包到其默认位置。
	- Python 2.7.x
	- Numpy
	- Matplotlib (Matplotlib是可选的，但推荐使用，因为我们在教程中使用了很多。)

	注意：在这种情况下，我们使用32位的Python包二进制文件。 但是如果要将OpenCV用于x64，则需要安装64位的Python包二进制文件。 问题是，Numpy没有正式的64位二进制文件。 你必须自己构建它。 为此，您必须使用用于构建Python的相同编译器。 当您启动Python IDLE时，它会显示编译器详细信息。 您可以在此处获取更多信息。 因此，您的系统必须具有相同的Visual Studio版本并从源代码构建Numpy。
	另一种拥有64位Python软件包的方法是使用来自第三方的现成Python发行版，如Anaconda，Enthought等。它的大小会更大，但会有你需要的一切。 一切都在一个壳里。 您也可以下载32位版本。

1. 确保Python和Numpy工作正常。
1. 下载OpenCV源代码。 它可以来自Sourceforge（官方发布版本）或Github（最新来源）。
1. 将其解压缩到一个文件夹，opencv并在其中创建一个新的文件夹。
1. Open CMake-gui (Start > All Programs > CMake-gui)
1. 按如下方式填写字段（请参见下图）：
	- 单击Browse Source ...并找到opencv文件夹。
	- 单击Browse Build ...并找到我们创建的构建文件夹。
	- 单击“Configure”。
![](https://docs.opencv.org/3.3.0/Capture1.jpg)
	- 它将打开一个新窗口来选择编译器。 选择适当的编译器（此处为Visual Studio 11），然后单击“Finish”。
![](https://docs.opencv.org/3.3.0/Capture2.png)
	- 等到分析完成。
1. 您将看到所有字段都标记为红色。 单击WITH字段以展开它。 它决定了您需要的额外功能。 所以标记适当的字段。 见下图：
![](https://docs.opencv.org/3.3.0/Capture3.png)
1. 现在单击BUILD字段将其展开。 前几个字段配置构建方法。 见下图：
![](https://docs.opencv.org/3.3.0/Capture5.png)
1. 剩余字段指定要构建的模块。 由于OpenCV-Python尚不支持GPU模块，因此您可以完全避免使用它来节省时间（但如果您使用它们，请将其保留在那里）。 见下图：
![](https://docs.opencv.org/3.3.0/Capture6.png)
1. 现在单击ENABLE字段将其展开。 确保未选中ENABLE_SOLUTION_FOLDERS（Visual Studio Express版不支持解决方案文件夹）。 见下图：
![](https://docs.opencv.org/3.3.0/Capture7.png)
1. 还要确保在PYTHON字段中，一切都已填满。 （忽略PYTHON_DEBUG_LIBRARY）。 见下图：
![](https://docs.opencv.org/3.3.0/Capture80.png)
1. 最后单击Generate按钮。
1. 现在转到我们的opencv / build文件夹。 在那里你会找到OpenCV.sln文件。 用Visual Studio打开它。
1. 将构建模式检查为Release而不是Debug。
1. 在解决方案资源管理器中，右键单击解决方案（或ALL_BUILD）并构建它。 完成需要一些时间。
1. 再次，右键单击INSTALL并构建它。 现在将安装OpenCV-Python。
![](https://docs.opencv.org/3.3.0/Capture8.png)
1. 打开Python IDLE并输入import cv2。 如果没有错误，则表示安装正确。

注意：我们安装时没有其他支持，如TBB，Eigen，Qt，文档等。这里很难解释。 很快就会添加更详细的视频，或者您可以随意浏览。

## 练习 ##
如果您有一台Windows机器，请从源代码编译OpenCV。 做各种hack。 如果您遇到任何问题，请访问OpenCV论坛并解释您的问题。
