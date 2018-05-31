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
+ `lightModel`: 光照模型，就是指定shader所使用的光照模型。内置的有基于物理的`Standard`和`StandardSpecular`,当然，也可以使用不基于物理的`Lambert(diffuse)`和`BlinnPhong(specular)`。

##### 可选参数
可选参数比较多，这里不一一列出来，需要[点我查看][1_unity_surface_shader_ref]

##### 表面着色器的输入结构
表面着色器的输入结构`Input`通常有一个shader所需要的纹理。需要注意的是，纹理坐标字段的名字必须以`"uv"`开头(或者以`"uv2"`开头标识第二个纹理坐标)。
下面的列表展示了`Input`结构的可选额外字段
+ `float3 viewDir`: 顾名思义，这个字段表示了当前视角方向，即表示摄像机的正前方的向量。通常用来计算一些视差效果和边缘光的效果等。
+ `float4 : COLOR`: 带有`COLOR`语义的字段，表示每个顶点的颜色，顶点之间采用差值来确定颜色。
+ `float4 screenPos`: 表示屏幕空间的位置，主要用来反射效果或者屏幕空间的效果实现。
+ `float3 wordPos`: 在世界空间的位置。
+ `float3 worldRefl`: 如果`surface shader`不写`o.Normal`那么将表示世界空间中的反射向量。
+ `float3 worldNormal`: 如果`surface shader`不写`o.Normal`,那么用来表示世界空间中的法向量。
+ `float3 wroldRefl`: 如果`surface shader`写了`o.Normal`, 那么这个字段表示世界空间中的反射向量。如果想要获取逐像素法线贴图的反射向量，使用`WorldReflectionVector (IN, o.Normal)`
+ `float3 worldNormal`: 如果`surface shader`写了`o.Normal`, 那么这个字段表示世界空间的法向量。如果想获取逐像素法线贴图的法向量，使用`WorldNormalVector (IN, o.Normal)` 




（未完，待续）



[1_unity_surface_shader_ref]: https://docs.unity3d.com/Manual/SL-SurfaceShaders.html