---
title: Mathematics.for.3D.Game.Programming.and.Computer.Graphics阅读笔记(一)
date: 2017-08-24 14:08:28
tags: OpenGL, 图形学
---
## 第一章: 渲染管线

### 写在前面的话: 
> 本文基本等同于翻译(加上自己的理解), 笔者英语水平有限, 难免会出现错误, 望高手在评论中指出, 谢谢!
> 原书: [Mathematics for 3D Game Programming and Computer Graphics, Third Edition][1_booksite] 

### 一. 渲染管线
#### 1.1 图形处理器
一个3D图形场景通常包含了很多独立的对象.这些对象的几何形式通常是由一些特定类型的`图元`组成的.`图元`指的是顶点以怎样的方式连接构成了这种形状. 图1.1列出了 OpenGL图形库定义了10种图元. 图形硬件可以渲染一系列独立的点, 一些线段, 或者一组已经填充好了的多边形. 在大多数情况下, 3D模型的表面是由一些三角形构成的, 每一个三角形又是由顶点列表中的3个顶点组成.
> ![图1.1, 来自原书截图](/img/201708/2017-08-24 .png)

现代的3D图形卡通常会执行从CPU发出的一些指令, CPU想GPU发出渲染命令, 然后CPU会执行其他的任务, 而GPU会执行这些渲染操作. 这通常被称为***异步操作***. 当需要被渲染的图形信息被提交到渲染库(OpenGL等)的时候, 请求渲染操作的函数将会在渲染操作还未完成前很早就返回. 这些在一个渲染命令的子操作和渲染完成之间的滞后时间将不会产生什么问题, 但是在渲染完成的时刻是需要知道的. 幸运的是, 目前OpenGL的一些扩展可程序以在CPU中运行, 这些程序能够在GPU完成渲染的时候发出通知.但同时会带来性能损耗, 这种异步操作会拖慢3D图形程序的执行速度, 因此, 如果想要性能优先, 那么应该避免这些操作.

那么应用程序和GPU的通信是怎么来进行呢? 通常是通过一些第三方的图形库来实现的, 比如OpenGL. 这些应用将会通过调用OpenGL API方式来向其发出渲染命令. 而OpenGL等图形库又向GPU驱动程序发送渲染命令, 这些驱动程序才知道怎样和GPU交流. OpenGL的这些接口我们称之为***Hardware Abstraction Layer(HAL) 硬件抽象层***, 因为它提供了一些通用的函数可以用来渲染场景, 这些函数可以在任何支持OpenGL架构的图形硬件上运行. 驱动程序将会把这些OpenGL的API转换成GPU可以执行的命令. 3D图形驱动程序通常会直接实现OpenGL函数以最小化渲染命令的开销. ***图1.2*** 展示了CPU和GPU之间的通信过程:
>![图1.2 CPU和GPU之间的通信(来自原书截图)](/img/201708/2017-08-24 3.48.18.png)

一个3D图形卡有自己的存储空间(显存), 通常被称为***VRAM (Video Random Access Memory)***. GPU可以在VRAM中存储任何东西, 当时在3D应用程序运行的时候, 有这么几类数据大多数总可以在显存中找到. VRAM中有两个图像缓冲区, 被称为前缓冲和后缓冲 (传说中的[***双缓冲***][2_double_swap]). 其中, `front image buffers` 包含了当前在可视区域内看到的每一个像素的数据. *可视区域* 指的就是包含了已经被渲染的图象数据的一个区域, 他可能是一个窗口, 也可能是整个屏幕; `back image buffer`是GPU真正渲染的场景的位置. `back buffer` 在渲染完成呈现给用户之前是不可见的. 一旦一个图像完成了渲染, `back buffer` 和 `front buffer` 将会交换数据. 这个操作被称为`缓冲交换`. 他可以通过以下几种方式来实现:
> 1. 直接交换两个缓冲区的内存地址
> 2. 将`back buffer`中的数据拷贝到`fornt buffer`中

缓冲区交换通常和图像显示刷新率保持同步, 这是为了避免发生`画面撕裂现象`. `画面撕裂`通常由于在刷新期间发生了缓冲区交换, 这将会导致一副图像的上半部分和下半部分不同, 因为他们的数据分别来自`front buffer`和`back buffer`.

还有一块存储在VRAM中的数据块被称为`Depth-buffer`或者`z-buffer`. `深度缓冲`存储的数据是图像缓冲中的每一个像素数据的`depth`,`depth`表明这个像素距离屏幕有多远或者这个像素位于图像中的哪个深度. `深度缓冲`通常用来隐藏或表面消除的功能, 它只绘制深度小于图像缓冲去深度的像素. `Depth`同时也表明了从被绘制的场景到虚拟相机(充当人眼的那个位置)的距离. 而`z-buffer`这个名字来自于相机的Z轴, 它是从相机的坐标空间射向屏幕的方向.
随着`image buffer`和`depth buffer`的创建, 应用程序可能会请求`stencil buffer(模板缓冲区)`. `stencil buffer`包含了一个整型的遮罩, 这和`image buffer`中的每一个像素都是一一对应的, 他可以用来控制特定的像素是否被绘制. 更高级的`stencil buffer`可以用来产生实时阴影.
大多数3D渲染应用程序中使用VRAM主要通过`Texture maps(纹理贴图)`, `texture maps`通常被用来附着到物体的表面以达到更好的视觉细节. 在高级渲染程序中, `texture maps`通常包含了比简单像素图像更多的信息. 例如: 一个[`bump map(凹凸贴图)`][3_bumpmap]包含的信息是一个个的向量, 这些向量表明了物体表面对应位置的倾斜程度. 

#### 1.2 向量变换

几何数据以3维空间的形式传给了图形硬件. 接下来图形硬件需要做的就是就是将这些数据转换到2维空间的视口内. 在`rendering pipeline(渲染管线)` 过程中有很多与之相关联的坐标系统, 他们之间的转换过程如`图1.3`. 一个3D 模型的顶点通常存储在对象的`object space(模型坐标空间)`内, 这个坐标系统取决于特定的模型.每一个模型的位置通常被存储在`word space(世界坐标空间)`中, 这个坐标空间将所有的对象连接到一个全局的坐标空间中. 在每一个对象被渲染之前, 它的顶点必须被转换到`camera space(相机空间)`中, 这个坐标空间的x和y轴适合显示区域的x和y平行的. 模型的顶点是可以直接从`模型空间`转换到`相机空间`的,具体做法就是通过矩阵乘法将 `模型坐标`->`世界坐标`的变换矩阵M<sub>obj-word</sub> 和 `世界坐标`->`相机坐标`的变换矩阵M<sub>word-camera</sub> 连接起来,得到M<sub>obj-camera</sub>称之为 `model-view`变换.
一旦模型的顶点被转换到了`相机空间`,他们会经历一个投影变换, 这个变换将会达到人眼视觉系统近大远小的视觉效果. 这个投影是通过四维空间其次坐标来完成的, 在这些顶点完成投影后的坐标空间被称为`homogeneous clip space(齐次裁剪空间)`, 之所以这么叫它, 是因为在这个坐标空间,图元会被裁剪在可视区域内,确保落在可视区域外面的图元不会被渲染.
> ![图1.3(来自原书截图)](/img/201708/2017-08-24 5.29.50.png)

在齐次坐标空间中, 每个顶点的坐标称为`normalized device coordinates(规格化设备坐标, 简称NDC)`, 这里出现了一个新的术语`normalized(规格化或标准化)`, 它的意思其实就是将每个顶点的x,y,z分量映射到[-1,1]区间内, 它表示顶点最终出现在`视口`中的位置. 顶点还要经历再一次的变换`viewport transformation(视口变换)`, 这个变换的结果其实就是最终顶点呈现在屏幕上的位置, `z分量`通常会被映射到[-1,1]区间内, 但是随后它会被缩放到深度缓冲所使用的每一个像素位对应的整型范围.最后, 顶点呈现在了我们的屏幕上.
除了从`模型空间`转换到`屏幕空间`外, 图形处理器还会针对每一个顶点做一些运算.例如: OpenGL的光照模型将会计算到达每一个顶点的光照强度的该顶点反射颜色以及最终会有多少颜色会被反射进入人眼(相机的位置). 这中运算被称为`per-vertex lighting(逐顶点光照)`. 一些高级的3D程序会使用逐顶点光照处理, 这将会在物体表面产生很细腻的光照表现(像素级别的).
每一个顶点还会携带一个甚至更多的`texture coordinates(纹理坐标)`. 通常情况下, `纹理坐标`是由应用程序明确指定的或者是GPU自动生成的. 图元在渲染的时候, 将会根据`纹理坐标`从对应的纹理中查询到对应的颜色值插入到图元对应的位置. 这些颜色和其他在每个像素被插入的颜色将会决定最终在视口中的显示效果.

#### 1.3 光栅化和片元操作

一旦模型的顶点完成了坐标转换最终呈现在了屏幕中, GPU必须决定这些在视口中的像素应该用什么样的图元来呈现. 水平扫描固定间隔的像素填充图元的处理过程叫做`raserization(光栅化)`. GPU计算每一个像素的深度, 插入顶点颜色和纹理坐标. 这些信息和像素本身的位置结合起来叫做`fragment(片元)`.
图元到片元的转换如`图1.4`. 应用程序必须在这个过程的第一阶段决定是否进行`face culling(表面剔除)`.`表面剔除`只会对多边形图元生效, 它会移除背对着相机或者面对着相机的多边形图元. 但是大多数情况下,`表面剔除`被用来优化性能, 因为它会去除背对着相机的多边形, 这些不需要被渲染, 因为我们看不到它.
> ![图1.4(来自原书截图)](/img/201708/2017-08-25 11.28.13.png)

图形应用程序指定片元数据在光栅化过程中怎样来确实每一个像素的最终颜色和深度. 这个过程叫做`fragment shading 或者 pixel shading(片元着色)`. 最终的颜可可能是从纹理获取来的颜色和顶点插入的颜色的成绩, 也可能是复杂的逐顶点光照的结果. 最终的深度通常不会变化, 但是一些高级的3D图形硬件允许我们来按照自己的意愿替换最终计算的深度值. `图1.5`展示了在光栅化过程中每一个片元产生所执行的操作. 大部分操作都会决定一个图元最终绘制到屏幕上或者被丢弃. 从逻辑上来讲,虽然这些操作会在`片元着色`之后再进行, 但大多数GPU会在执行`片元着色`前进行一些测试, 这些测试能够带来一些性能和时间上的优势: 有一些最终根本不会被显示到屏幕上的图元将不会通过测试, 这就节省了不必要的性能开销和时间开销.
> ![图1.5(来自原书截图)](/img/201708/2017-08-25 2.19.10.png)







[1_booksite]: http://www.mathfor3dgameprogramming.com/
[2_double_swap]: https://baike.baidu.com/item/%E5%8F%8C%E7%BC%93%E5%86%B2/10953356?fr=aladdin
[3_bumpmap]: https://en.wikipedia.org/wiki/Bump_mapping