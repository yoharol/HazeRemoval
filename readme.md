## 去雾算法

#### 程序流程

对于一张带有雾气的原图*src*，首先统计每一个像素点的RGB通道中数值最小者为暗通道值，然后用窗口大小为7的最小值滤波来处理图片，处理后的暗通道示意如下：

![output_dim](https://github.com/LunaElfGaming/HazeRemoval/raw/master/output_origin/3/output_dim.bmp)

计算出暗通道图像*$dim$*后，接下来估计大气光值。将所有像素点的坐标、颜色、暗通道全部存储在结构体*$Pixel$*构成的Vector *$pix$*中，随后对pix按照暗通道值从大到小排序，取前0.1%为参照，找出其中亮度最高的像素点作为大气光值估计，存储在Pixel类的变量*max_pix*中。

随后计算大气透射率图，计算公式为：

$t(x)=1-\omega\ min_{N}[min_{R,G,B}\frac{I_C (y)}{A_C}]$

计算结果存储在float型Mat $trans$中。

对每一个点计算透射率后，按照透射率图和大气光值来计算去雾结果图 $J$，计算公式为：

$J_C(x)=A_C-\frac{A_C-I_C(x)}{t(x)}$

最后分别输出三张图：原图output_origin.bmp，暗通道示意图output_dim.bmp，和去雾结果图output.bmp。

#### 程序结果

所有的处理结果分文件夹存储在output_origin文件夹中。

以第三张为例，原图如下：

![output_origin](https://github.com/LunaElfGaming/HazeRemoval/raw/master/output_origin/3/output_origin.bmp)



图中红色的圈标注了选取为大气光的像素点，最终得到处理结果如下：

![output](https://github.com/LunaElfGaming/HazeRemoval/raw/master/output_origin/3/output.bmp)

图中总体颜色很好地去除了雾气效果，颜色非常显眼且分辨率比较好地保留了。

图二的处理结果则表现出了算法对浓雾处理能力的不足：

![output_origin](https://github.com/LunaElfGaming/HazeRemoval/raw/master/output_origin/2/output_origin.bmp)

可以看到算法非常正确地选取了大气光点，但图中部分浓雾已经几乎与天光相同，因此处理结果也将部分景色处理成了天空：

![output](https://github.com/LunaElfGaming/HazeRemoval/raw/master/output_origin/2/output.bmp)

不仅出现了缺口，部分雾气直接变成了深色。

#### 算法改进

在处理图片之前，先对图片进行T空间转换：

$T=MC=\left[\begin{matrix}0.0255&-0.1275&1.0965\\-0.3315&1.5045&-0.1785\\0.5610&0.3825&0.0510\end{matrix}\right]\left[\begin{matrix}R\\G\\B\end{matrix}\right]$

处理函数为BGRtoT（openCV中RGB图为BGR顺序），处理后图片的色彩不会变化，而图三的处理结果出现了较大的差别，图片色彩更加鲜艳

![output](https://github.com/LunaElfGaming/HazeRemoval/raw/master/output_tspace/3/output.bmp)

可以看到墙壁的颜色相比之前有较大的区别。

所有的处理结果都可以在output_tspace文件夹中查看。
