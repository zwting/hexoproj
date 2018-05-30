---
title: Unity3D Shader学习(二)--表面着色器结构
date: 2018-05-30 19:41:03
tags:  [Unity3D, Shader]
---
# Unity3D Shader学习(二)--表面着色器结构

写在前面的话：
> 本博客是我学习Unity shader过程中所记录的笔记，前期的学习都是跟随unity官方文档来学习的

#### 一.表面着色器的工作方式
在表面着色器中，把需要的数据作为"表面函数"的输入，计算后用来初始化结构`SurfaceOutput`。下面就先来看看它的结构是什么样的
```c
struct SurfaceOutput
{
    fixed3 Albedo;  // diffuse color
    fixed3 Normal;  // tangent space normal, if written
    fixed3 Emission;
    half Specular;  // specular power in 0..1 range
    fixed Gloss;    // specular intensity
    fixed Alpha;    // alpha for transparencies
};
```
**特别说明一下: ** 在unity5中，表面着色器也可以使用基于物理的光照模型。内置的标准光照和标准镜面反射模型会使用下面的输出结构
```c
struct SurfaceOutputStandard
{
    fixed3 Albedo;      // base (diffuse or specular) color
    fixed3 Normal;      // tangent space normal, if written
    half3 Emission;
    half Metallic;      // 0=non-metal, 1=metal
    half Smoothness;    // 0=rough, 1=smooth
    half Occlusion;     // occlusion (default 1)
    fixed Alpha;        // alpha for transparencies
};
struct SurfaceOutputStandardSpecular
{
    fixed3 Albedo;      // diffuse color
    fixed3 Specular;    // specular color
    fixed3 Normal;      // tangent space normal, if written
    half3 Emission;
    half Smoothness;    // 0=rough, 1=smooth
    half Occlusion;     // occlusion (default 1)
    fixed Alpha;        // alpha for transparencies
};
```
#### 二.表面着色器的编译指令
表面着色器是放在`CGPROGRAM..ENDCG`代码块中的，和其他shader 所不同的主要有以下两点：
+ 必须被放置在`SubShader`代码块中，而不是在`Pass`块中
+ 必须使用`#pragma surface ...`指令来指定它的`surface`函数

`#pragma surface`指令的具体格式如下面所示：
```c
#pragma surface surfaceFunction lightModel [optionalparams]
```

***下面将会来细致介绍参数***

##### 必选参数
+ `surfaceFunction`: 这个参数就是我们的表面函数了，它的函数签名是`void surf (Input IN, inout SurfaceOutput o)`。这里的`Input`类型是一个需要自己去定义的结构体(需要遵循它的标准，准确地说是去定制)。这个结构里面应该包含纹理坐标和表面函数所需要的一些额外字段。


（未完，待续）