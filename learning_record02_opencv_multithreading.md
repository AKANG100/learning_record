# 学习笔记

## 2021

### 9.21

##### 1.并发、进程、线程的基本概念和综述

* 并发：两个或更多的任务（独立的活动）同时发生（进行）

* 进程：运行起来了的可执行程序

* 线程：线程是用来执行代码的；每个进程都有一个主线程，该主线程是唯一的；除了主线程，我们自己可以写代码创造其他线程；

* 并发实现的方法：多进程并发；多线程并发（速度更快、开销小，但注意数据一致性等问题）

* C++11新标准标准线程库：C++11本身增加对多线程的支持，意味着可移植性（跨平台）

##### 2.创造线程

包含头文件

```
#include<thread>
```

**【补充】线程类参数是一个可调用对象。一组可执行的语句称为可调用对象，c++中的可调用对象可以是函数、函数指针、lambda表达式、bind创建的对象或者重载了函数调用运算符的类对象。**

(1)用函数作为可调对象：

```c++
void myprint()
{}
int main()
{
    thread mytobj(myprint);////创造了线程,线程执行起点（入口）myprint();myprint()开始执行
    
    thread mytobj(myprint, mvar, mybuf);
	if (mytobj.joinable())
	{
		cout << "可以调用可以调用join()或者detach()" << endl;
	}
	else
	{
		cout << "不能调用可以调用join()或者detach()" << endl;
	}
    
    mytobj.join();//主线程在这里受到阻塞等待myprint()执行完，当子线程执行完毕，join（）就执行完毕，主线程就继续往前走
    //设置断点可看到主线程等待子线程的过程
	//F11逐语句，就是每次执行一行语句，如果碰到函数调用，它就会进入到函数里面
	//F10逐过程，碰到函数时，不进入函数，把函数调用当成一条语句执行
    
    //mytobj.detach();//与主线程关联的thread对象失去与主线程的关联，子线程驻留在后台运行，相当于被c++运行时库接管

    return 0;
}
```

(2)用类作为可调对象：

```c++
class TA
{
public:
	void operator()()//不能带参数-->两个括号？？
	{
	}
};

int main()
{
	TA ta;
	thread mytobj3(ta);
	mytobj3.join();
    
	return 0;
}
```

(3)用lambda表达式

```c++
int main()
{
	auto mylamthread = []
	{
	};
	thread mytobj4(mylamthread);
	mytobj4.join();
	return 0;
}
```

小结：1.thread、join()、detach()、joinable()。三种创造线程的方式。

2. 有两个线程在跑，相当于整个程序中有两条线在同时走，即使一条被阻塞，另一条也能运行 。

   

   

------



### 9.22

##### 1.传临时对象作为线程参数

```c++
void myPrint(const int i, const string& pmybuf)
{}

thread mytobj(myprint, mvar, mybuf);//第一个参数是函数名，后两个参数是函数的参数
```

【注】在使用detach时注意：

 1.对线程使用detach()时，有可能会出现主线程结束，但是子线程还没有执行结束，那么此时会出现一个问题：主线程结束后，变量i以及pmybuf会被销毁，那么子线程还可以正常使用吗 ?

---> 如果使用的是引用（例如myPrint函数中的const int &i），那么子线程会将主线程中的变量复制过去，而不是直接引用原本的i地址；如果使用的是指针（例如myprint函数中的char* *pmybuf），那么子线程函数中的指针会指向原变量地址，那么此时会产生问题。

---> 使用类型转换将char *pmybuf转换成string类型，这时会产生另外一个陷阱: 会出现主线程中的变量空间都已经被回收了，子线程才开始进行类型转换。 

2.总结：

* 在传递int等简单类型时，尽量使用值传递，尽量不使用引用

+ 在传递类对象时尽量避免隐式类型转换，要在创建线程的时候同时构造临时对象来传递参数，然后在函数参数里，使用引用来接受临时对象。

- <u>尽量使用join()，不建议使用detach(</u>)。

**2.智能指针做线程参数**

**3.成员函数指针做线程参数**



------



### 9.23

**1.创造多个线程**

```c++
void TextThread()
{
     cout << "我是线程" << this_thread::get_id() << endl;
    
     cout << "线程" << this_thread::get_id() << "执行结束" << endl; 
}
 //main函数里     vector threadagg;
     for (int i = 0; i < 10; ++i)
     {
         threadagg.push_back(thread(TextThread));
     }
     for (int i = 0; i < 10; ++i)
     {
         threadagg[i].join();
     }

```

- 把thread对象放入到容器中管理，看起来像个thread对象数组，对一次创建大量的线程并对大量线程进行管理有好处
- 多个线程执行顺序是乱的，跟操作系统内部对线程的运行调度机制有关



**2.数据共享问题**



------



### 9.24

**opencv中更多函数的学习**

* **inRange():**

```c++
void inRange(InputArray src, InputArray lowerb,InputArray upperb, OutputArray dst);
//参数1：输入要处理的图像，可以为单通道或多通道。
//参数2：包含下边界的数组或标量。
//参数3：包含上边界数组或标量。
//参数4：输出图像，与输入图像src 尺寸相同且为CV_8U 类型。

```

作用：可实现二值化功能（这点类似threshold()函数），更关键的是可以同时针对多通道进行操作，使用起来非常方便！ 主要是**将在两个阈值内的像素值设置为白色（255），而不在阈值区间内的像素值设置为黑色（0）**，该功能类似于之间所讲的双阈值化操作。

* **blur()**

```c++
void blur(InputArray src, OutputArray dst, Size ksize, Point anchor=Point(-1,-1), int borderType=BORDER_DEFAULT );
//第一个参数，InputArray类型的src，输入图像，即源图像，填Mat类的对象即可。该函数对通道是独立处理的，且可以处理任意通道数的图片，但需要注意，待处理的图片深度应该为CV_8U, CV_16U, CV_16S, CV_32F 以及 CV_64F之一。
//第二个参数，OutputArray类型的dst，即目标图像，需要和源图片有一样的尺寸和类型。比如可以用Mat::Clone，以源图片为模板，来初始化得到如假包换的目标图。
 //第三个参数，Size类型（对Size类型稍后有讲解）的ksize，内核的大小。一般这样写Size( w,h )来表示内核的大小( 其中，w 为像素宽度， h为像素高度)。Size（3,3）就表示3x3的核大小，Size（5,5）就表示5x5的核大小
//第四个参数，Point类型的anchor，表示锚点（即被平滑的那个点），注意他有默认值Point(-1,-1)。如果这个点坐标是负值的话，就表示取核的中心为锚点，所以默认值Point(-1,-1)表示这个锚点在核的中心。
//第五个参数，int类型的borderType，用于推断图像外部像素的某种边界模式。有默认值BORDER_DEFAULT，我们一般不去管它。
```

作用：对输入的图像src进行均值滤波后用dst输出。



* **GaussianBlur()**

```c++
void GaussianBlur(InputArray src, OutputArray dst, Size ksize, double sigmaX, double sigmaY=0, int borderType=BORDER_DEFAULT ) ;
//src和dst当然分别是输入图像和输出图像。
//Ksize为高斯滤波器模板大小，
//sigmaX和sigmaY分别为高斯滤波在横线和竖向的滤波系数。
//borderType为边缘扩展点插值类型,有默认值BORDER_DEFAULT，如果没有特殊需要不用更改。
```

作用：对输入的图像src进行高斯滤波后用dst输出。



* **erode()**

```c++
void dilate( const Mat& src, Mat& dst, const Mat& element,Point anchor=Point(-1,-1), int iterations=1,int borderType=BORDER_CONSTANT,
const Scalar& borderValue=morphologyDefaultBorderValue() );

//第一个参数：输入图像
//第二个参数：输出图像，要和原图像有一样的尺寸和类型
//第三个参数：原图像类型的element，膨胀操作的核，当为NULL时表示使用的是参考点位于中心的3x3的核（通常使用函数getStructuringElement来计算）
//第四个参数：锚点位置，有默认值Point(-1, -1)
//第五个参数：迭代使用dilate的次数，默认值为1
//第六个参数：有默认值BORDER_DEFAULT
//第七个参数：const Scalar类型的 borderValue，一般不用管他
//其实一般我们使用的时候，大部分只需要填前三个参数，后面四个都有默认值
```

作用：取周围元素的最小值，也就是图像越来越暗，亮的部分被腐蚀



* **dilate()**

```c++
void dilate( const Mat& src, Mat& dst, const Mat& element,Point anchor=Point(-1,-1), int iterations=1,int borderType=BORDER_CONSTANT,
const Scalar& borderValue=morphologyDefaultBorderValue() );

//第一个参数：原图像
//第二个参数：输出图像
//第三个参数：和输入图像一样类型的核，通常配合getStructuringElement函数使用
//第四个参数：Point类型的锚点的位置，有默认值(-1, -1)
//第五个参数：int类型的迭代使用dilate函数的次数，默认值为1
//第六个参数：有默认值BORDER_DEFAULT
//第七个参数：const Scalar类型的 borderValue，一般不用管他
```

作用：取周围元素的最大值，也就是图像越来越亮，亮的部分膨胀



* **getStructuringElement()**

```c++
CV_EXPORTS_W Mat getStructuringElement(int shape, Size ksize, Point anchor = Point(-1,-1));

//1）shape:Element shape that could be one of #MorphShapes
    //结构元素形状可以是MorphShapes中的一个。
	//! shape of the structuring element
    enum MorphShapes {
        MORPH_RECT    = 0, //结构元素是矩形的
        MORPH_CROSS   = 1, //结构元素是十字形的       
        MORPH_ELLIPSE = 2 //结构元素是椭圆形的
    };
//（2）ksize:Size of the structuring element.
    //结构元素的大小。
//（3）anchor：Anchor position within the element. The default value \f$(-1, -1)\f$ means that the anchor is at the center. Note that only the shape of a cross-shaped element depends on the anchor position. In other cases the anchor just regulates how much the result of the morphological operation is shifted.
     //结构元素内的锚点位置。默认值为Point（-1，-1）表示锚在结构元素的中央。对于其它形状的元素，锚点规定了形态学处理结果的偏移量

```

作用：返回用于形态学操作的指定大小和形状的结构元素。



* **Sobel()**

```c++
void Sobel(InputArray src, OutputArray dst, int ddepth, int dx, int dy, int ksize=3, double scale=1, double delta=0, int borderType=BORDER_DEFAULT ) 

//1.InputArray src：输入的原图像，Mat类型
//2.OutputArray dst：输出的边缘检测结果图像，Mat型，大小与原图像相同。
//3.int ddepth：输出图像的深度，针对不同的输入图像，输出目标图像有不同的深度，具体组合如下：
- 若src.depth() = CV_8U, 取ddepth =-1/CV_16S/CV_32F/CV_64F
- 若src.depth() = CV_16U/CV_16S, 取ddepth =-1/CV_32F/CV_64F
- 若src.depth() = CV_32F, 取ddepth =-1/CV_32F/CV_64F
- 若src.depth() = CV_64F, 取ddepth = -1/CV_64F
注：ddepth =-1时，代表输出图像与输入图像相同的深度。
//4.int dx：int类型dx，x 方向上的差分阶数，1或0
//5.int dy：int类型dy，y 方向上的差分阶数，1或0
其中，dx=1，dy=0，表示计算X方向的导数，检测出的是垂直方向上的边缘；dx=0，dy=1，表示计算Y方向的导数，检测出的是水平方向上的边缘。
//6.int ksize：为进行边缘检测时的模板大小为ksize*ksize，取值为1、3、5和7，其中默认值为3。特殊情况：ksize=1时，采用的模板为3*1或1*3。
当ksize=3时，Sobel内核可能产生比较明显的误差，此时，可以使用 Scharr 函数，该函数仅作用于大小为3的内核。具有跟sobel一样的速度，但结果更精确，其内核为：
这里写图片描述
其调用格式为：
Scharr( src_gray, grad_x, ddepth, 1, 0, 1, 0, BORDER_DEFAULT );
Scharr( src_gray, grad_y, ddepth, 0, 1, 1, 0, BORDER_DEFAULT );
等价于：
/// 求 X方向梯度
Sobel(src_gray,grad_x,ddepth, 1, 0, CV_SCHARR, scale, delta, BORDER_DEFAULT );
/// 求 Y方向梯度
Sobel(src_gray,grad_y,ddepth, 0, 1, CV_SCHARR, scale, delta, BORDER_DEFAULT );

//7.double scale：默认1。
//8.double delta：默认0。
//9.int borderType：默认值为BORDER_DEFAULT。

sobel算法代码实现过程为：
/// 求 X方向梯度
Sobel( src_gray, grad_x, ddepth, 1, 0, 3, scale, delta, BORDER_DEFAULT );
/// 求 Y方向梯度
Sobel( src_gray, grad_y, ddepth, 0, 1, 3, scale, delta, BORDER_DEFAULT );
convertScaleAbs( grad_x, abs_grad_x );
convertScaleAbs( grad_y, abs_grad_y );
addWeighted( dst_x, 0.5, dst_y, 0.5, 0, dst); //一种近似的估计
```

作用：sobel算子是一种常用的边缘检测算子，是一阶的梯度算法。对噪声具有平滑作用，提供较为精确的边缘方向信息，边缘定位精度不够高。



* ##### Laplacian()

```c++
dst = cv2.Laplacian(src, ddepth[, dst[, ksize[, scale[, delta[, borderType]]]]])
   
//前两个是必须的参数：
	第一个参数是需要处理的图像；
    第二个参数是图像的深度，-1表示采用的是与原图像相同的深度。目标图像的深度必须大于等于原图像的深度；

//其后是可选的参数：
	dst不用解释了；
    ksize是算子的大小，必须为1、3、5、7。默认为1。
    scale是缩放导数的比例常数，默认情况下没有伸缩系数；
    delta是一个可选的增量，将会加到最终的dst中，同样，默认情况下没有额外的值加到dst中；
    borderType是判断图像边界的模式。这个参数默认值为cv2.BORDER_DEFAULT。

```



* **floorfill()**

```C++
 //1.
int floodFill(InputOutputArray image, Point seedPoint, Scalar newVal, Rect* rect=0, Scalar loDiff=Scalar(), Scalar upDiff=Scalar(), int flags=4 )  
 //2.
int floodFill(InputOutputArray image, InputOutputArray mask, Point seedPoint,Scalar newVal, Rect* rect=0, Scalar loDiff=Scalar(), Scalar upDiff=Scalar(), int flags=4 )

//第一个参数：InputOutputArray类型的image, 输入/输出1通道或3通道，8位或浮点图像，具体参数由之后的参数具体指明。
//第二个参数：InputOutputArray类型的mask，这是第二个版本的floodFill独享的参数，表示操作掩模,。它应该为单通道、8位、长和宽上都比输入图像 image 大两个像素点的图像。第二个版本的floodFill需要使用以及更新掩膜，所以这个mask参数我们一定要将其准备好并填在此处。需要注意的是，漫水填充不会填充掩膜mask的非零像素区域。例如，一个边缘检测算子的输出可以用来作为掩膜，以防止填充到边缘。同样的，也可以在多次的函数调用中使用同一个掩膜，以保证填充的区域不会重叠。另外需要注意的是，掩膜mask会比需填充的图像大，所以 mask 中与输入图像(x,y)像素点相对应的点的坐标为(x+1,y+1)。
//第三个参数：Point类型的seedPoint，漫水填充算法的起始点。
//第四个参数：Scalar类型的newVal，像素点被染色的值，即在重绘区域像素的新值。
//第五个参数：Rect*类型的rect，有默认值0，一个可选的参数，用于设置floodFill函数将要重绘区域的最小边界矩形区域。
//第六个参数：Scalar类型的loDiff，有默认值Scalar( )，表示当前观察像素值与其部件邻域像素值或者待加入该部件的种子像素之间的亮度或颜色之负差（lower brightness/color difference）的最大值。
//第七个参数：Scalar类型的upDiff，有默认值Scalar( )，表示当前观察像素值与其部件邻域像素值或者待加入该部件的种子像素之间的亮度或颜色之正差（lower brightness/color difference）的最大值。
```

原理：就是从一个点（这个点称为种子像素/参考像素）开始遍历，满足其限制条件的区域（一般用颜色值作为限制），填充成新的颜色，直到封闭区域内的所有像素点都被填充成新颜色为止。
