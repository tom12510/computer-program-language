## OpenCV

> 图片模式（色彩空间）：
>
> 1. 灰度图像：图像只包含亮度值（0表示黑色，255表示白色）
>
> 2. 三色图（BGR/RGB）
>
> 3. 透明图（BGRA）：Alpha通道用于表示透明度
>
> 4. HSV：色调、饱和度和明度。H：颜色的色调或纯度，S：颜色的强度或鲜艳程度，V： 颜色的亮度或明暗程度（人类感知颜色非常相似，适合图像分割和对象识别等任务）
>
> 5.  LAB：亮度，A与B颜色对比（比较光源变化）
>
> 6. YCrCb：亮度信息（Y），两个色度分量（Cr和Cb），视频压缩和传输
>
> 
>
> 图像增强
>
> - 对比度（Contrast）：图像中亮部和暗部之间的差异程度，高对比度的图像有明显的明暗对比，低对比度的图像则显得灰暗模糊。
>
> - 色彩饱和度（ColorSaturation）：体现颜色的强度或纯度（高饱和度的图像颜色鲜艳，低饱和度的图像颜色显得灰暗）
>
> - 亮度（Brightness）：图像的整体明暗程度
>
> 
>
>  图像增强算法
>
> - 直方图均衡化：重新分配图像的灰度值，使图像的直方图分布更加均匀，从而增强对比度
> - 自适应直方图均衡化（CLAHE）：图片局部进行直方图均衡化
> - 拉普拉斯增强（Laplacian）：检测图像中的边缘，增强图像细节
> - 高斯滤波（Gaussian）：对图像进行加权平均，减少图像中的噪声和细节
> - 中值滤波：将像素值替换为其邻域像素值的中值，减少图像中的椒盐噪声
> - 边缘增强：通过像素值的梯度，增强图片边缘
> - 伽玛校正：通过应用非线性变换调整图像的亮度
> - 锐化：通过减去模糊图像来增强边缘和细节，使图像更加清晰
> - 直方图拉伸：线性拉伸图像的灰度值范围，使图像的对比度增强
> - 动态范围压缩：通过对数变换或其他技术减少图像的动态范围，使图像的亮度更均匀
>
> 
>
>  算子：用于图像分析和处理的数学工具或函数
>
> - 平滑算子（减少图像中的噪声和细节，使图像更平滑）
>
>   1. 均值滤波器：通过将每个像素值替换为其邻域像素值的平均值来平滑图像
>   2. 高斯滤波器：通过高斯函数对图像进行加权平均，减少噪声并保持边缘平滑（保留高频像素，去除低频/正态分布）
> - 边缘检测算子（检测图像中灰度变化剧烈的区域）
>
>   1. Sobel：计算图像在x，y方向的梯度
>
>   2. Canny：边缘检测器（高斯滤波器，Sobel，双阈值处理，边缘连接）
>
>   3. Prewitt：类似于Sobel算子
> - 锐化算子：增强图像的细节和边缘，使图像看起来更加清晰
>
>   1. 拉普拉斯算子：检测图像中的二阶导数，用于增强边缘
> 2. Unsharp Masking：通过减去模糊图像来增强边缘细节
> - 形态学算子：用于处理图像的形状和结构，主要用于二值图像
>   1. 膨胀：扩展图像中的前景区域，使物体变大
>   2. 腐蚀：缩小图像中的前景区域，使物体变小
>   3. 开运算：先进行腐蚀再进行膨胀，用于去除小的噪点
>   4. 闭运算：先进行膨胀再进行腐蚀，用于填补小的空洞
>

