# Chapter 3 灰度变换与空间滤波

<mark>空间域中进行变换，比较简单直观，消耗资源少</mark>

$$g(x,y)=T[f(x,y)]$$
f 输入图像， g 输出图像， T 在点(x,y)的邻域定义的一种关于f的算子（邻域通常选择中心在(x,y)的矩形）

空间滤波器（也称空间掩模，核，模板或窗口）

## 一些基本的灰度变换函数

图像增强的常用的三类基本函数：
* 线性函数（反转，恒等变换）
* 对数函数（对数，反对数变换）
* 幂律变换（n次幂，n次根变换）

### 图像反转
$$s=L-1-r$$
特别适合处理 增强嵌入图像暗色区域的白色或灰色细节

```
cv::Mat img1, img2;
img1 = cv::imread("\load...", 0);
img2 = 255 - img1;
cv::imshow("original", img1);
cv::imshow("求反", img2);
cv::waitKey(0);
cv::destroyAllWindows();
```

### 对数变换
$$s=c\; log(1+r)$$
式中，c为常数，并假定r大于等于0
用于扩展图像的暗像素值，同时压缩更高灰度级的值

```
cv::Mat img1, img2, img3;
    img1 = cv::imread("images/Chapter3\\Fig0305(a)(DFT_no_log).tif",0);
    if (img1.empty()) return -1;
    
    img1.convertTo(img3,CV_32FC1);
    cv::log(img3 + 1.0, img2);
    cv::normalize(img2, img2, 0, 255, cv::NORM_MINMAX, CV_8UC1);
    
    cv::namedWindow("original", cv::WINDOW_NORMAL);
    cv::namedWindow("log", cv::WINDOW_NORMAL);
    
    cv::imshow("original", img1);
    cv::imshow("log", img2);
    
    cv::waitKey(0);
    cv::destroyAllWindows();


// Assertion failed (depth == CV_32F || depth == CV_64F) in log
// cv::log函数的参数得是32f或64f类型的，不是8u
// Bad argument (Matrix operand is an empty matrix.) in checkOperandsExist
// 不能对空矩阵进行运算
```
