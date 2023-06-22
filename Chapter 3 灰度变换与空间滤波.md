# Chapter 3 灰度变换与空间滤波

空间域中进行变换，比较简单直观，消耗资源少

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

```cpp
cv::Mat img1, img2;
img1 = cv::imread("\\load...", 0);
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

```cpp
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

### 幂律（伽马）变换

$$
s = c r^\gamma
$$

``` cpp
    //增强对比度 
    cv::Mat img, img1, img2, img3, img4, img5;
    img = cv::imread("images\\Chapter3\\Fig0308(a)(fractured_spine).tif",0);
    if(img.empty()){
        return -1;
    }
    img.convertTo(img1,CV_32FC1);
    img2.create(img1.size(), CV_32FC1);
    img3.create(img1.size(), CV_32FC1);
    img4.create(img1.size(), CV_32FC1);
    img5.create(img1.size(), CV_32FC1);
    cv::pow(img1,0.6,img2);
    cv::pow(img1,0.4,img3);
    cv::pow(img1,0.3,img4);
    cv::pow(img1,0.2,img5);
    cv::normalize(img2, img2, 0, 255, cv::NORM_MINMAX,CV_8UC1);
    cv::normalize(img3, img3, 0, 255, cv::NORM_MINMAX,CV_8UC1);
    cv::normalize(img4, img4, 0, 255, cv::NORM_MINMAX,CV_8UC1);
    cv::normalize(img5, img5, 0, 255, cv::NORM_MINMAX,CV_8UC1);
    
    cv::namedWindow("gamma=0.6",cv::WINDOW_AUTOSIZE);
    cv::namedWindow("gamma=0.4",cv::WINDOW_AUTOSIZE);
    cv::namedWindow("gamma=0.3",cv::WINDOW_AUTOSIZE);
    cv::namedWindow("gamma=0.2",cv::WINDOW_AUTOSIZE);

    cv::imshow("original", img);
    cv::imshow("gamma=0.6",img2);
    cv::imshow("gamma=0.4",img3);
    cv::imshow("gamma=0.3",img4);
    cv::imshow("gamma=0.2",img5);

    cv::waitKey(0);
    cv::destroyAllWindows();

    return(0);
```

```cpp
    \\ 压缩灰度级
    cv::Mat img, img1, img2, img3, img4, img5;
    img = cv::imread("images\\Chapter3\\Fig0309(a)(washed_out_aerial_image).tif",0);
    if(img.empty()){
        return -1;
    }
    
    img1.create(img.size(), CV_32FC1);
    img2.create(img.size(), CV_32FC1);
    img3.create(img.size(), CV_32FC1);
    img4.create(img.size(), CV_32FC1);

    img.convertTo(img1, CV_32FC1);

    cv::pow(img1, 3.0, img2);
    cv::pow(img1, 4.0, img3);
    cv::pow(img1, 5.0, img4);

    cv::normalize(img2,img2,0, 255, cv::NORM_MINMAX, CV_8UC1);
    cv::normalize(img3,img3,0, 255, cv::NORM_MINMAX, CV_8UC1);
    cv::normalize(img4,img4,0, 255, cv::NORM_MINMAX, CV_8UC1);

    cv::namedWindow("gamma = 3.0", 1);
    cv::namedWindow("gamma = 4.0", 1);
    cv::namedWindow("gamma = 5.0", 1);

    cv::imshow("original", img);
    cv::imshow("gamma = 3.0", img2);
    cv::imshow("gamma = 4.0", img3);
    cv::imshow("gamma = 5.0", img4);

    cv::waitKey(0);
    cv::destroyAllWindows();

    return(0);
```

增强对比度，伽马矫正

### 分段线性变换函数

（对比度拉伸， 阈值处理）

灰度级分层
突出特定灰度范围的的亮度（可选，并将非ROI设置成同一个灰度级）

**比特平面分层**
例如8bit，01001011，每一位上的值 都表明着一些数据
低阶比特位 贡献细节
高阶比特位 轮廓（大部分数据）
