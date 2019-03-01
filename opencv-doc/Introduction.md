# Introduction

## module

|模块|描述|
|:---:|:---:|
|Core functionality (**core**) | 一个紧凑的模块，定义基本的数据结构，包括密集的多维数组 Mat 和所有其他模块使用的基本函数。|
|Image Processing (**imgproc**) | 图像处理模块，包括线性和非线性图像滤波，几何图像变换（resize, affine, perspective warping, generic table-based remapping），颜色空间转换，直方图等。|
|Video Analysis (**video**) |视频分析模块，包括运动估计，背景减法和目标跟踪算法。|
|Camera Calibration and 3D Reconstruction (**calib3d**) |基本的多视图几何算法，单一和立体摄像机校准，对象姿态估计，立体对应算法和 3D 重建。|
|2D Features Framework (**features2d**) |出色的特征检测器，描述符和描述符匹配器。|
|Object Detection (**objdetect**) |检测目标和预定义类的实例（例如，人脸，眼睛，马克杯，人，汽车等）。|
|High-level GUI (**highgui**) |简单易用的界面，简单的UI功能。|
|Video I/O (**videoio**) |一个易于使用的视频捕获和视频编解码器接口。|
|**................** |一些其他辅助模块，例如FLANN和Google测试包装器，Python绑定等。|