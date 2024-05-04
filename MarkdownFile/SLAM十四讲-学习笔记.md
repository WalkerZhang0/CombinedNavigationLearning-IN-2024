### SLAM十四讲-学习笔记

### ch2

#### SLAM的构成

1. 传感器获取信息；
2. 前端视觉里程计；
3. 后端（非线性）优化：接收来自前端视觉里程计所获取的相机的位姿信息的同时，接收回环检测的信息，从而确定全局一致的轨迹或者地图；
4. 回环检测：检测是否发生过回环，从而能够改正漂移误差；
5. 建图：将后端优化所得到的轨迹和地图根据需要生成一个适当的地图类型；

#### SLAM的数学表达

1. 运动方程：简单的讲，就是利用前一个状态的位置+运动传感器所输入的信息+运动噪声模拟去估计在下一个状态的位置，继而得到机器人运动的轨迹，实现所谓Localization功能；
2. 观测方程：当机器运动时，还会返回路标观测数据，这个数据可以由机器的位置和路标的位置结合可能的观测噪声来确定；

这样，我们可以估计在当前状态下的机器位置实现定位，路标位置实现Mapping.

上述两个方程，可以写成一个通用的表达形式：

> 注意，在不同的运动系统中，SLAM的状态方程具有不同的参数化形式，如何准确的表达状态方程，需要结合系统进行相应的分析。

$$
x_k=f(x_{k-1},u_k,w_k),k=1,...,K\\z_{k,j}=h(y_j,x_k,v_{k,j}),(k,j)\in O
$$

此时呢，我们就有了前一个状态中的位置$x_{k-1}$、运动传感器所返回的相机的位姿信息$u_k$、相机和路标之间的观测数据$z_{k,j}$，而两个方程之中的噪声和当前状态下的机器位置、路标位置仍然有待确定。此时，SLAM问题转换成了一个状态估计问题。而根据状态方程的线性与否和噪声所满足的分布，存在如下的分类。

| 是否线性 | 噪声分布 | 状态估计                                                    |
| -------- | -------- | ----------------------------------------------------------- |
| 线性     | 高斯     | Kalman Filter                                               |
| 线性     | 非高斯   |                                                             |
| 非线性   | 高斯     | Extended Kalman Filter                                      |
| 非线性   | 非高斯   | Extended Kalman Filter/Partical Filter/非线性优化（图优化） |

#### 习题

1. 阅读文献1、14

   - *L. Haomin, Z. Guofeng, and B. Hujun, “A survey of monocular simultaneous localization and mapping,”*
     *Journal of Computer-Aided Design and Compute Graphics, vol. 28, no. 6, pp. 855–868, 2016. in Chinese.*

     基于单目视觉的同时定位与地图构建方法综述：看不懂一点！

   - 

2. 阅读SLAM综述文献，这些文献关于SLAM的看法与本书有何不同？

3. g++中命令有哪些参数，如何填写参数从而更改生成的程序文件名：gcc具有相当多的参数设置。

   ```
   -o <output_file>
   -x <language>: 指定源文件的语言类型，例如-c++用于指定C++语言类型
   -c 分阶段编译
   -Wall 开启所有警告信息
   -Werror 将所有警告信息视为错误
   -std=c++xx: 指定C++的标准版本，xx可以是98、11、14、17、20等
   -g: 生成调试信息，便于调试程序
   -E: 只进行预处理，输出预处理后的代码
   -S: 只进行编译，生成汇编代码而不进行汇编
   
   ```

   > 在编译过程中，通过包括三个步骤：预处理、编译和链接。预处理完成#符所标记的内容，生成一个经过与处理后的源文件，而编译是将预处理后的源文件翻译成汇编语言，生成一个或者多个目标文件，但是此时并没有连接到最终的可执行文件。
   >
   > 先进行预处理和编译，而不进行链接，在一些大型项目的进行中相当有用，往往能够显著减少编译时间。

4. 使用build文件夹编译cmake工程，然后在KDevelop中试试；

5. 代码中如果存在错误，g++的错误信息是什么？如何看懂g++的error report；

6. 阅读《cmake实践》，了解cmake的其他语法；

   cmake基本语法：

   - 变量名称的表达：

     - 变量使用`${}`方式取值，但是在 IF 控制语句中是直接使用变量名；

   - 指令(参数1 参数2...)

     - 参数使用括弧括起，参数之间使用空格或分号分开。

       以上面的 `ADD_EXECUTABLE` 指令为例，如果存在另外一个 func.c 源文件，就要写成：

       ADD_EXECUTABLE(hello main.c func.c)或者

       ADD_EXECUTABLE(hello main.c;func.c)

   - 指令是大小写无关的，参数和变量是大小写相关的。但，推荐全部使用大写指令。

   内部构建和外部构建：简单地说，其实就是生成的中间文件是否会对原工程目录内容产生影响；

   在第四节中所提到的INSTALL指令，我属实有点不是非常理解。准确的说，所谓的安装指令其实就是将写好的内容根据需要放置在适当的位置，从而能够供用户使用。通常，可以将其安装在一些家目录的文件当中，像是家目录的bin文件夹，其中就存储着在Linux终端所使用的系统命令的接口文件。

### ch3

#### 旋转矩阵

本章学习我将其理解为是描述刚体运动的数学基础。主要介绍了点、向量和向量的坐标表示，以及向量之间的运算。点积、叉积等。不同坐标系的之间的转换可以使用欧式变换描述，其中包括旋转和平移。旋转SO（3）三阶特殊正交群可以使用一个行列式为一的正交矩阵表述，平移则直接使用一个三阶向量进行表述，将平移和旋转表达在同一个矩阵当中，就变成了特殊欧式群。

*不清楚的点*：

- 在描述坐标转换时，这里的旋转矩阵和平移向量的定义有点模糊，比如从坐标系1中的向量$\boldsymbol{a_1}$转换到坐标系2中的向量$\boldsymbol{a_2}$.存在如下的变换：
  $$
  \boldsymbol{a_2} = S_{21}\boldsymbol{a_1}+t_{21}
  $$
  其中，旋转矩阵$S_{21}$表示坐标系1旋转到坐标系2，而平移向量$t_{21}$则表示从坐标系2的原点指向坐标系1的原点的向量，在坐标系2下的坐标。这里是理解的不够清楚，简单点记的话，最终获取的时2坐标系终得坐标，所以平移向量必然也是也应该是在2坐标系下的。

#### 旋转向量

旋转向量的构建是因为旋转矩阵的元素达到9个，而一个旋转仅仅包含三个自由度，因此，旋转矩阵存在所谓冗余的表达方式，因此，使用旋转向量来减小冗余。具体而言，使用一个与旋转轴方向保持一致的单位向量作为旋转向量，然后使用向量的长度等于旋转角。这里的关键问题在于如何实现旋转向量和旋转矩阵的之间的转换。针对同一个变换，以旋转矩阵的方式进行表示$\boldsymbol R$，而使用旋转向量可以表示为旋转角为$\theta$，旋转的单位向量为$n$。，从旋转向量到旋转矩阵之间的转换过程中由罗德里格斯公式表明：
$$
\boldsymbol{R}=cos\theta\boldsymbol{I}+(1-cos\theta)\boldsymbol{nn^{T}}+sin\theta\boldsymbol{\hat{n}}
$$

> 这里需要注意每一个向量都有与之唯一对应的一个反对称矩阵。所谓反对称矩阵，强调的是矩阵的转置等于矩阵乘以-1.并且反对称矩阵的迹必为零，这是由其特点所确定的。

而也可以根据旋转矩阵反过来计算旋转向量，对上述取迹，可以得到旋转角度$\theta$​和旋转矩阵的迹之间的关系。
$$
\theta=arccos(\frac{tr(R)-1}{2})
$$
而对于旋转轴的选取，可以从旋转矩阵的特征向量出发。因为旋转矩阵表示该旋转变换，但是在旋转过程中，旋转轴是保持不变的。因此，存在
$$
Rn=n
$$
故旋转向量可以看做是旋转矩阵特征值为1所对应的特征向量。

> 注意旋转矩阵的特点是：1. 正交；2. $\det(\theta)=1$

#### 欧拉角

旋转矩阵和旋转向量能够表示一次刚体运动中的旋转变换，但是二者都具有不直观的缺点。而欧拉角提供了很好的直观性，但是欧拉角并不唯一，针对围绕不同的轴所确定的旋转以及后续的旋转轴的选择顺序都会产生不同的欧拉角。具体统一地说，转角主要包括：

1. 绕物体的Z轴旋转，得到偏航角yaw；
2. 绕旋转之后的Y轴旋转，得到俯仰角pitch；
3. 绕旋转之后的X轴旋转，得到滚转角roll；

注意欧拉角会存在一个万向锁的问题，具体而言就是当俯仰角为±90度时，那么第一次旋转和第三次旋转将处于同一个旋转轴上，此时，系统就损失了一个自由度，这种问题称之为奇异性问题。而旋转向量也存在这种问题，因此，欧拉角并不能直接用于迭代和插值的计算之中。

#### 四元数

当旋转矩阵存在冗余表示的问题，而旋转向量又有奇异性的问题，使用四元数，则具有即紧凑又不存在奇异性，是一种很好的表示旋转的方式。

其实直观上看，我对于四元数的理解并没有和复数产生多大的联系，因为我对复数空间的概念也非常模糊。但是涉及到一些四元数的运算，我则类比到了矩阵和向量：加减乘除、模长、取逆、共轭以及数乘。

四元数如何表达旋转：使用虚部表达坐标，当存在四元数$q$表达的矩旋转时，可以有如下的旋转：
$$
p'=qpq^{-1}
$$
四元数和其他旋转表示之间的变换：

**四元数和旋转矩阵之间的变换**：这里的关系推导借助了变换矩阵，因为四元数的乘法可以将其写成矩阵之间的运算，这里具体的转换推导见书本P59. 由此我们可以推导了四元数和旋转矩阵之间的关系，然后借助矩阵的迹实现四元数和旋转向量之间的转换，旋转轴向量具体如何确定还是**懵的**。

**疑问**：

在书中的实践阶段提到了四元数的单一化，仔细看了一下前面的内容，确实提到表示旋转的四元数是单位四元数，而归一化则是为了获取单位四元数，但是我不是很理解chatgpt所说单位四元数表示的是确定的旋转，而非单位四元数则可能是多个相同的旋转。**解答：**理解为什么要使用单位四元数表达旋转的关键，在于理解复数的概念，四元数是复数的扩展，将复数从二维的平面扩展到了三维的空间。针对复数而言，我们可以将其表达在一个平面上，虚部和实部分别可以看做是横轴的取值和纵轴的取值，而当其与另外一个复数进行乘法运算，得到的复数表达在平面上，可以看做是原来的向量发生了旋转和缩放，而缩放的尺度正是乘数的模长，但是在欧式变换或者说刚体运动中，点与点之间的角度和长度是固定不变的，所以为了满足这样的要求，将四元数进行归一化，即转换为单位四元数来表达旋转。

#### Eigen库的实践

Eigen库是基于C++开发的开源线性代数库，提供了有关矩阵的线性代数的运算，以及一些解方程的功能。

基本的用法：

```c++
#include <iostream>
using namespace std;
#include <ctime>
// 关键库的引用
#include <eigen/core>
#include <eigen/dense>

using namespace eigen;

#define MATRIX_SIZE 50

int main(){
    /*声明方式*/
    Matrix<float,2,3> matrix_23; // 2row3column
    Vector3d v_3d; // 3行的向量声明，等价于下方声明3*1矩阵，但是在一些不同行列的矩阵运算过程中，下式不需要进行额外的类型转换
    Matrix<float,3,1> vd_3d; 
    Matrix3d matrix_33 = Matrix3d::Zero(); // 3*3矩阵生声明，并初始化为零矩阵
    Matrix<double, Dynamic,Dynamic> matrix_dynamic; // 动态大小
    MatrixXd matrix_x;
    
    matrix_23 << 1,2,3,4,5,6;  // 矩阵元素的输入
    cout<<"matrix 2x3 from 1 to 6: \n" << matrix_23<<endl; 
    cout << "print matrix 2x3:\n"<<endl;
    for(int i = 0; i <2;i++) // 通过(i,j)索引
    {
        for(int j = 0;j <3;j++)cout<<matrix_23(i,j)<<endl;
        cout<< endl;
    }
    v_3d << 3,2,1;
    vd_3d << 4,5,6;
    // 运算过程中的转换
    Matrix<double ,2,1> result = matrix_23.cast<double>()*v_3d;
    cout<<"[1,2,3,4,5,6]*[3,2,1]="<<result.transpose()<<endl;
    
    Matrix<float,2,1> result2 = matrix_23*vd_3d;
    cout<<"[1,2,3,4,5,6]*[3,2,1]="<<result2.transpose()<<endl;
    
    // 基础的计算
    matrix_33 = Matrix3d::Random();
    cout<<"random matrix: \n"<<matrix_33<<endl;
    cout<<"transpose:\n" <<matrix_33.transpose()<<endl;
    cout<<"sum:\n"<<matrix_33.sum()<<endl;
    cout<<"trace:\n"<<matrix_33.trace()<<endl;
    cout<<"times 10 : \n" <<matrix_33 * 10 <<endl;
    cout<<"inverse: \n" <<matrix_33.inverse()<<endl;
    cout<<"det:"<<matrix_33.determinant()<<endl;
    
    // 解方程
    SelfAdjointEigenSolver<Matrix3d> eigen_solver(matrix_33.transpose()*matrix_33);
    cout<<"eigen values = \n"<<eigen_solver.eigenvalues()<<endl;
    cout<<"eigen vectors = \n"<<eigen_solver.eigenvectors()<<endl;
    
    Matrix<double,MATRIX_SIZE,MATRIX_SIZE> matrix_NN = MatrixXd::Random(MATRIX_SIZE,MATRIX_SIZE);
    matrix_NN = matrix_NN*matrix_NN.transpose();
    Matrix<double,MATRIX_SIZE,1> v_Nd = MatrixXd::Random(MATRIX_SIZE,1);
    
    clock_t time_stt = clock();
    
    // directly inverse
    Matrix<double, MATRIX_SIZE,1> x = matrix_NN.inverse()*v_Nd;
    cout<<"time of normal inverse is " << 1000*(clock()-time_stt)/(double)CLOCKS_PER_SEC<<"ms"<<endl;
    cout<<"x="<<x.transpose()<<endl;
    
    // QR 
    time_stt = clock();
    x = matrix_NN.colPivHouseholderQr().slove(v_Nd);
    cout<<"time of QR decomposition is " << 1000*(clock()-time_stt)/(double)CLOCKS_PER_SEC<<"ms"<<endl;
    cout<<"x="<<x.transpose()<<endl;
    
     // cholesky
    time_stt = clock();
    x = matrix_NN.ldlt().slove(v_Nd);
    cout<<"time of cholesky decomposition is " << 1000*(clock()-time_stt)/(double)CLOCKS_PER_SEC<<"ms"<<endl;
    cout<<"x="<<x.transpose()<<endl;
    
    return 0;
    
}  
                                            
```

