---
title: Unity surface shader 学习（一）-- Material, Shader, Texture扫盲篇
date: 2018-02-10 23:16:46
tags: [Unity3D, Shader]
---

# Unity surface shader 学习（一）

写在前面的话：
> 本博客是我学习Unity shader过程中所记录的笔记，前期的学习都是跟随unity官方文档来学习的

#### 一. unity shader的分类
+ 表面着色器
+ 定点着色器和片段着色器
+ 固定功能着色器

其中，固定功能着色器不做深入学习，这里的学习主要指的是**表面着色器**和**顶点、片段着色器**

#### 二. unity中的渲染方式
在unity中，渲染是通过**材质**，**shader**和**纹理**来实现的。下面，简单来介绍一下这三者在渲染中各自的功能以及相互之间的联系。
1.  **材质(Material): ** 
	描述了一个模型表面的渲染细节和方式。包括了引用的**纹理(Texture)**, 以及纹理是如何覆盖在模型表面的(通过UV来实现)，以及颜色，等各种表现出来的视觉效果。具体有多少，这跟这个材质引用的**shader**有关。这里我觉得和我们生活中所说的材质很类似：生活中我们分辨一个物体的材质最直观快捷的办法就是通过它的外在表现形式。
2. **着色器(Shader): **
	先来看看官网是如何描述的
> Shaders are small scripts that contain the mathematical calculations and algorithms for calculating the colour of each pixel rendered, based on the lighting input and the Material configuration.
意思就是说着色器就是负责计算每个被渲染的像素的颜色，它是基于光照的，并且可以通过**材质**来调节一些细节和配置。其实，经常在3D引擎中听到的渲染就是通过shader来实现的。
3. **纹理(Textuer): **
	一般说的纹理，其实就是一张2D位图(bitmap)。没什么深层次的含义。

**他们之间的区别：**
	一个**材质**通常需要指定一个**shader**，而**shader**却决定了材质可用的选项（指的是材质的功能和那些可调节的部分，在**Material**的**Inspector面板**中可调节的部分），shader还指定了一个或者多个**纹理**。
***结论：*** **unity中的材质是shader的载体，提供了一种可视化的方式来对shader的一些参数做调节。材质所表现出来的渲染效果是shader通过计算和处理了它所引用纹理之后所达成的。纹理则是渲染中必不可少的一部分（如果你需要华丽的效果）。**
