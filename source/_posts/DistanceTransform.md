---
layout:     post
title:      图像的距离变换（Distance Transform）
category:   Image
---

### 图像的距离变换

> The distance transform of a region of foreground pixels in a background of zeros is the distance from every pixel to the nearest nonzero valued pixel.

背景为零的前景图距离变换指的是：每个像素到最近的非零值像素的距离。比如：

$$
\begin{array}{lllll}{0} & {0} & {0} & {0} & {0} \\\\ {0} & {1} & {1} & {1} & {0} \\\\ {0} & {1} & {1} & {1} & {0} \\\\ {0} & {0} & {0} & {0} & {0}\end{array}\quad\quad\quad\quad\quad
\begin{array}{rrrrrr}{1.41} & {1} & {1} & {1} & {1.41} \\\\ {1} & {0} & {0} & {0} & {1} \\\\ {1} & {0} & {0} & {0} & {1} \\\\ {1.41} & {1} & {1} & {1} & {1.41}\end{array}
$$

<!--more-->

1 对应的值是 0 因为里它最近的非零值就是它自己。

一般感兴趣的是从前景（白色）像素区域的像素到其最近的背景(零)像素的距离，因此，要计算图像的 **补码的距离变换**。

$$
\begin{array}{lllllllll}{0} & {0} & {0} & {0} & {0} & {0} & {0} & {0} & {0} \\\\ {0} & {1} & {1} & {1} & {1} & {1} & {1} & {1} & {0} \\\\ {0} & {1} & {1} & {1} & {1} & {1} & {1} & {1} & {0} \\\\ {0} & {1} & {1} & {1} & {1} & {1} & {1} & {1} & {0} \\\\ {0} & {1} & {1} & {1} & {1} & {1} & {1} & {1} & {0} \\\\ {0} & {1} & {1} & {1} & {1} & {1} & {1} & {1} & {0} \\\\ {0} & {0} & {0} & {0} & {0} & {0} & {0} & {0} & {0}\end{array}\quad\quad\quad\quad\quad
\begin{array}{lllllllll}{0} & {0} & {0} & {0} & {0} & {0} & {0} & {0} & {0} \\\\ {0} & {1} & {1} & {1} & {1} & {1} & {1} & {1} & {0} \\\\ {0} & {1} & {2} & {2} & {2} & {2} & {2} & {1} & {0} \\\\ {0} & {1} & {2} & {3} & {3} & {3} & {2} & {1} & {0} \\\\ {0} & {1} & {2} & {2} & {2} & {2} & {2} & {1} & {0} \\\\ {0} & {1} & {1} & {1} & {1} & {1} & {1} & {1} & {0} \\\\ {0} & {0} & {0} & {0} & {0} & {0} & {0} & {0} & {0}\end{array}
$$

结果是里白色部分越远的距离，值也越大。

![1560329552883](\images\ImageProcessing\距离变换.png)

距离变换的脊线（ridge）相当于这个图像的骨架（skeleton）。

------

### OpenCV 中的距离变换

```c++
void distanceTransform(InputArray src, OutputArray dst, int distanceType, int maskSize)
```

`distanceTransform` 

第一个 Mat 矩阵参数 src 是一个单通道的 8bit 的二值图像；

第二个 Mat 矩阵参数 dst 保存了每一个点与最近的零点的距离信息，图像上越亮的点，代表了离零点的距离越远；

第三个参数是距离类型，有欧式距离、棋盘距离和街区距离等等；

第四个参数是掩膜模板，这个坑日后再填。

```c++
#include <iostream>
#include <opencv2\core\core.hpp>
#include <opencv2\highgui\highgui.hpp>
#include <opencv2\imgproc\imgproc.hpp>

using namespace cv;
using namespace std;

//主函数
int main()
{
    Mat srcImage = imread("1234.jpg");
    if (!srcImage.data)
    {
        cout << "读入图片错误！" << endl;
        system("pause");
        return -1;
    }
    Mat grayImage;
    cvtColor(srcImage, grayImage, CV_BGR2GRAY);
    Mat binaryImage;
    threshold(grayImage, binaryImage, 100, 255, THRESH_BINARY); 
    Mat dstImage;
    // DIST_L2 欧氏距离
    distanceTransform(binaryImage, dstImage, CV_DIST_L2, CV_DIST_MASK_PRECISE);
    normalize(dstImage, dstImage, 0, 1, NORM_MINMAX);
    
    imshow("二值图像", binaryImage);
    imshow("距离变换图像", dstImage);
    waitKey();
    return 0;
}
```



