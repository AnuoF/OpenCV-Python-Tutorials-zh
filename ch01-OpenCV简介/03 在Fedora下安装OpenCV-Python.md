**在Fedora下安装OpenCV-Python**

## 目标 ##

在本教程中，我们将学习在Fedora系统中设置OpenCV-Python。以下步骤针对Fedora 18（64位）和Fedora 19（32位）进行了测试。

## 介绍 ##

OpenCV-Python可以通过两种方式安装在Fedora中：1）从fedora存储库中可用的预构建二进制文件安装，2）从源代码编译。 在本节中，我们将看到两者。

另一个重要的是需要额外的库。 OpenCV-Python只需要Numpy（除了其他依赖项，我们稍后会看到）。 但在本教程中，我们还使用Matplotlib进行一些简单而美观的绘图目的（与OpenCV相比，我感觉好多了）。 Matplotlib是可选的，但强烈推荐。 同样，我们也会看到IPython，一个交互式Python终端，这也是强烈推荐的。

## 从预构建的二进制文件安装OpenCV-Python ##

使用以下命令在终端中以root身份安装所有软件包。

	$ yum install numpy opencv*

打开Python IDLE（或IPython）并在Python终端中键入以下代码。

	import cv2
	print( cv2.__version__ )

如果结果打印出来没有任何错误，恭喜！ 您已成功安装OpenCV-Python。

这很容易。 但这有一个问题。 Yum存储库可能始终不包含最新版本的OpenCV。 例如，在编写本教程时，yum存储库包含2.4.5，而最新的OpenCV版本为2.4.6。 对于Python API，最新版本将始终包含更好的支持。 此外，相机支持，视频播放等可能存在问题，具体取决于驱动程序，ffmpeg，gstreamer包等。

所以我个人的偏好是下一个方法，即从源代码编译。 此外，在某个时间点，如果您想为OpenCV做出贡献，您将需要这个。

## 从源代码安装OpenCV ##

从源代码编译起初可能看起来有点复杂，但是一旦你成功了，就没有什么复杂的了。

首先，我们将安装一些依赖项。 有些是必修课，有些是可选的。 可选的依赖项，如果您不想要，可以离开。

### 强制性依赖 ###

我们需要CMake来配置安装，GCC用于编译，Python-devel和Numpy用于创建Python扩展等。

	yum install cmake
	yum install python-devel numpy
	yum install gcc gcc-c++

接下来我们需要GTK支持GUI功能，相机支持（libdc1394，libv4l），媒体支持（ffmpeg，gstreamer）等。

	yum install gtk2-devel
	yum install libdc1394-devel
	yum install libv4l-devel
	yum install ffmpeg-devel
	yum install gstreamer-plugins-base-devel

### 可选的依赖项 ###

以上依赖关系足以在您的fedora机器中安装OpenCV。 但根据您的要求，您可能需要一些额外的依赖项。 下面给出了这种可选依赖项的列表，你可以保留或安装它。

OpenCV附带了PNG，JPEG，JPEG2000，TIFF，WebP等图像格式的支持文件。但它可能有点旧。 如果要获取最新库，可以安装这些格式的开发文件。

	yum install libpng-devel
	yum install libjpeg-turbo-devel
	yum install jasper-devel
	yum install openexr-devel
	yum install libtiff-devel
	yum install libwebp-devel

几个OpenCV功能与英特尔的线程构建模块（TBB）并行化。 但是如果要启用它，则需要先安装TBB。 （同时使用CMake配置安装时，不要忘记传递-D WITH_TBB = ON。更多详细信息如下。）

	yum install tbb-devel

OpenCV使用另一个库Eigen来优化数学运算。 因此，如果您在系统中安装了Eigen，则可以利用它。（同时在使用CMake配置安装时，不要忘记传递-D WITH_EIGEN = ON。更多详细信息如下。）

	yum install eigen3-devel

如果你想构建文档（是的，你可以在你的系统中使用完整的搜索工具在你的系统中创建OpenCV的完整官方文档的离线版本，这样你就不需要在任何问题上访问互联网，而且它非常快！）， 你需要安装Doxygen（文档生成工具）。

	yum install doxygen

### 下载OpenCV ###

接下来我们要下载OpenCV。您可以从sourceforge站点下载最新版本的OpenCV，然后解压缩文件夹。

或者您可以从OpenCV的github repo下载最新的源代码。（如果您想为OpenCV做出贡献，请选择此选项。它始终使您的OpenCV保持最新状态）。为此，您需要先安装Git。

	yum install git
	git clone https://github.com/opencv/opencv.git

它将在主目录（或您指定的目录）中创建一个文件夹OpenCV。 克隆可能需要一些时间，具体取决于您的互联网连接。

现在打开一个终端窗口并导航到下载的OpenCV文件夹。 创建一个新的构建文件夹并导航到它。

	mkdir build
	cd build

### 配置和安装 ###

现在我们已经安装了所有必需的依赖项，让我们安装OpenCV。必须使用CMake配置安装。它指定要安装的模块，安装路径，要使用的其他库，是否要编译的文档和示例等。下面的命令通常用于配置（从build文件夹执行）。

	cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..

它指定构建类型为“Release Mode”，安装路径为/ usr / local。 在每个选项之前观察-D并在结尾处观察.. 简而言之，这是格式：

	cmake [-D <flag>] [-D <flag>] ..

您可以指定所需的标志，但每个标志应以-D开头。

因此，在本教程中，我们将使用TBB和Eigen支持安装OpenCV。 我们还构建了文档，但我们排除了性能测试和构建示例。 我们还禁用GPU相关模块（因为我们使用OpenCV-Python，我们不需要GPU相关模块。它节省了我们一些时间）。

*（以下所有命令都可以在单个cmake语句中完成，但为了更好地理解，将其拆分为此。）*

- 启用TBB和Eigen支持 

		cmake -D WITH_TBB=ON -D WITH_EIGEN=ON ..

- 启用文档并禁用测试和示例

		cmake -D BUILD_DOCS=ON -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF -D BUILD_EXAMPLES=OFF ..

- 禁用所有GPU相关模块

		cmake -D WITH_OPENCL=OFF -D WITH_CUDA=OFF -D BUILD_opencv_gpu=OFF -D BUILD_opencv_gpuarithm=OFF -D BUILD_opencv_gpubgsegm=OFF -D BUILD_opencv_gpucodec=OFF -D BUILD_opencv_gpufeatures2d=OFF -D BUILD_opencv_gpufilters=OFF -D BUILD_opencv_gpuimgproc=OFF -D BUILD_opencv_gpulegacy=OFF -D BUILD_opencv_gpuoptflow=OFF -D BUILD_opencv_gpustereo=OFF -D BUILD_opencv_gpuwarping=OFF ..

- 设置安装路径和构建类型

		cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..

	每次输入cmake语句时，都会打印出结果配置设置。 在最后的设置中，确保填写以下字段（下面是我得到的配置的一些重要部分）。 这些字段也应在您的系统中适当填充。 否则出现了一些问题。 因此，请检查您是否正确执行了上述步骤。

		...
		--   GUI:
		--     GTK+ 2.x:                    YES (ver 2.24.19)
		--     GThread :                    YES (ver 2.36.3)
		--   Video I/O:
		--     DC1394 2.x:                  YES (ver 2.2.0)
		--     FFMPEG:                      YES
		--       codec:                     YES (ver 54.92.100)
		--       format:                    YES (ver 54.63.104)
		--       util:                      YES (ver 52.18.100)
		--       swscale:                   YES (ver 2.2.100)
		--       gentoo-style:              YES
		--     GStreamer:
		--       base:                      YES (ver 0.10.36)
		--       video:                     YES (ver 0.10.36)
		--       app:                       YES (ver 0.10.36)
		--       riff:                      YES (ver 0.10.36)
		--       pbutils:                   YES (ver 0.10.36)
		--     V4L/V4L2:                    Using libv4l (ver 1.0.0)
		--   Other third-party libraries:
		--     Use Eigen:                   YES (ver 3.1.4)
		--     Use TBB:                     YES (ver 4.0 interface 6004)
		--   Python:
		--     Interpreter:                 /usr/bin/python2 (ver 2.7.5)
		--     Libraries:                   /lib/libpython2.7.so (ver 2.7.5)
		--     numpy:                       /usr/lib/python2.7/site-packages/numpy/core/include (ver 1.7.1)
		--     packages path:               lib/python2.7/site-packages
		...

还有许多其他标志和设置。 它留待您进一步探索。

现在使用make命令构建文件并使用make install命令安装它。 make install应该以root身份执行。

	make
	su
	make install

安装结束了。 所有文件都安装在/ usr / local /文件夹中，但要使用它，你的Python应该能够找到OpenCV模块，你有两个选择：

1. 将模块移动到Python Path中的任何文件夹：输入import sys可以找到Python路径; 在Python终端中打印（sys.path）。 它会打印出许多地方。 将/usr/local/lib/python2.7/site-packages/cv2.so移动到此文件夹中的任何一个。 例如，

		su mv /usr/local/lib/python2.7/site-packages/cv2.so /usr/lib/python2.7/site-packages

	但是每次安装OpenCV时都必须这样做。

1. 将/usr/local/lib/python2.7/site-packages添加到PYTHON_PATH：它只需要执行一次。 只需打开/ .bashrc并添加以下行，然后注销并返回。

		export PYTHONPATH=$PYTHONPATH:/usr/local/lib/python2.7/site-packages

	因此OpenCV安装完成。 打开终端并尝试导入cv2。

要构建文档，只需输入以下命令：

	make doxygen

然后打开opencv / build / doc / doxygen / html / index.html并在浏览器中将其添加为书签。

## 练习 ##

在Fedora机器中从源代码编译OpenCV。