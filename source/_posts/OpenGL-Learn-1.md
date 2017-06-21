title: OpenGL学习笔记(一)--window下VS2013环境搭建OpenGL开发环境
tags:
  - 'OpenGL, 图形学'
categories: []
date: 2017-05-29 16:05:00
---

### 1. 环境搭建
> 需要: Win10; VS2013(其他版本的VS未经测试, 但总体流程和操作相差不大); [GLFW3][1](windows 32位版本); [GLEW][2]

- #### 新建C++项目
1. 首先使用VS2013新建一个C++的控制台项目`OpenGL_Lean`, 然后打开生成解决方案(可自己先新建一个main.cpp, 写上main函数,后面会用到)

- #### `GLEW`和`GLFW`安装步骤:

##### 1. 分别解压`glfw`和`glew`得到两个文件夹

> glfw解压后文件夹结构如下图 **(图1.1)**
![解压glfw之后的文件夹结构](/img/201705/2017-05-29_164226.png)

> glew文件夹结构 **(图1.2)**
![解压glew之后的文件夹结构](/img/201705/2017-05-29_164227.png)

##### 2. 复制库文件和头文件

首先找到VS2013的安装目录 (如果是默认安装, 那么应该在`C:\Program Files (x86)\Microsoft Visual Studio 12.0`)
+ **第一步: 复制动态库文件:** 
分别复制glew (`bin\Release\Win32\glew32.dll`) 和glfw (`lib-vc2013\glfw3.dll`) 这两个`DLL`文件到第一步新建的项目的OpenGL_Learn里面的Debug文件夹下(也可以直接复制到c:\windows\system32下), 复制完成后如下图:
![Debug文件夹结构](/img/201705/dll-pic.png)

+ **第二步: 复制静态库文件:**
分别复制glew(`lib\Release\glew32s.lib`) 和glfw (`lib-vc2013\glfw3.lib`)这两个`lib`文件到`[VS2013安装目录]\vc\lib`下面

+ **第三步: 复制头文件:**
分别复制glew: `include\GL`和glfw: `include\GLFW`文件夹到`[VS2013安装目录]\vc\include`. 复制完成之后VS2013的`vc\include`文件夹应该是这样的
![include文件夹内的结构](/img/201705/2017-05-29_190301.png)    

##### 3. 在VS2013中设置依赖库

打开VS的解决方案资源管理器, 在刚才的项目上右键, 点击属性打开属性设置对话框, 如下图
![VS2013属性设置面板](/img/201705/2017-05-30_001220.png)
**然后按照上图展开`链接器`>输入, 在项目依赖库里面填入glfw3.lib;glew32s.lib两个静态库, 如上图所示**

##### 4. 设置项目类型(显示控制台或者不显示)
+ 显示控制台窗口: 个人在开发阶段喜欢同时显示控制台窗口, 因为这样可以看到标准输入输出信息, 方便调试和排查错误.
> 打开方式: 无需特殊打开, 默认如此
+ 不显示控制台窗口: 只显示OpenGL图形窗口
> 打开方式: 依次打开`属性`




[1]: https://github.com/glfw/glfw/releases/download/3.2.1/glfw-3.2.1.zip
[2]: https://downloads.sourceforge.net/project/glew/glew/2.0.0/glew-2.0.0.zipr=http%3A%2F%2Fglew.sourceforge.net%2F&ts=1496045710&use_mirror=nchc
