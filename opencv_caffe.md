# Caffe OpenCV4.0 编译问题记录

一直在使用caffe，但是之前都是使用的opencv3.4。最近卸载了OpenCV，重新安装了Opencv4.0，然后再编译caffe时遇到了些错误，本文记录错误，并提供解决方法。

## Caffe安装过程

由于OpenCV4.0使用了C++11特性，所以在编译时，需要指定为C++11。

**1. 安装依赖**

```bash
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
```

如果编译过程中有以下对应包的错误，可以用以下命令安装：

```bash
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
```

**2. 修改Makefile.config**

首先奉上我的[Makefile.config](files/Makefile.config)文件，你可以根据自己的情况进行修改，我这里使用的是Anaconda2, mkl。

```makefile
# 反注释OPENCV，开启Opencv支持
OPENCV_VERSION := 3
# 设置CUDA_DIR
CUDA_DIR := /usr/local/cuda-8.0
# 添加Anaconda目录
ANACONDA_HOME := $(HOME)/anaconda3
PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
        $(ANACONDA_HOME)/include/python3.5 \
        $(ANACONDA_HOME)/lib/python3.5/site-packages/numpy/core/include/ 
# 添加Pythonlib
PYTHON_LIB := $(ANACONDA_HOME)/lib
# 添加指定库文件
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
```

**3. 修改Makefile**

奉上我的[Makefile](files/Makefile)文件，由于需要支持C++11.，所以大致修改如下：

```makefile
NVCCFLAGS +=-ccbin=$(CXX) -Xcompiler-fPIC $(COMMON_FLAGS)
改为：
NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)
```

```makefile
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_hl hdf5
改为：
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial
```

添加c++11支持(OpenCV 4.x+ requires enabled C++11 support需要)

```makefile
将CXXFLAGS += -pthread  -fPIC $(COMMON_FLAGS) $(WARNINGS)
改为:
CXXFLAGS += -pthread -std=c++11 -fPIC $(COMMON_FLAGS) $(WARNINGS)
```

**4. 编译**

```bash
cd CAFFE_ROOT
make -j8  # runs 8 jobs in parallel
make pycaffe  # compile caffe for python 
```

## 问题及解决方法

我在编译的时候遇到的问题是：

----

**Q1:** 

`opencv2/core.hpp: No such file or directory`

**A1:**

找不到opencv的路径，我打开opencv的安装目录，发现安装目录为`/usr/local/include/opencv4/opencv2`，而在编译的时候只是指定到路径`/usr/local/include/`下，所以找不到`opencv2`。

*解决方法：在`Makefile.config`文件中`INCLUDE_DIRS`中添加`/usr/local/include/opencv4`*，具体可以参考我的[Makefile.config](files/Makefile.config)文件

----

**Q2:**

`‘CV_LOAD_IMAGE_COLOR’ was not declared` 或者`‘CV_LOAD_IMAGE_GRAYSCALE’ was not declared`

**A2**

这个问题一般在OpenCV3之后出现，主要原因是`CV_LOAD_IMAGE_COLOR`和`CV_LOAD_IMAGE_GRAYSCALE`已经被弃用了，被改为了`cv::IMREAD_COLOR`和`cv::IMREAD_GRAYSCALE`。

*解决方法：将caffe中所有`CV_LOAD_IMAGE_COLOR`替换为`cv::IMREAD_COLOR`，将`CV_LOAD_IMAGE_GRAYSCALE`替换为`cv::IMREAD_GRAYSCALE`*


## 参考内容

[1] [ubuntu16安装caffe](https://blog.csdn.net/bleedingfight/article/details/80238348)

[2] [OpenCV Tutorial: Load and Display an Image (codeblocks, fedora20)](https://stackoverflow.com/questions/24439548/opencv-tutorial-load-and-display-an-image-codeblocks-fedora20)

[3] [opencv ImreadModes](https://docs.opencv.org/master/d4/da8/group__imgcodecs.html#ga61d9b0126a3e57d9277ac48327799c80)

