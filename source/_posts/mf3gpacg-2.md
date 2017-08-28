---
title: Mathematics.for.3D.Game.Programming.and.Computer.Graphics阅读笔记(二)
date: 2017-08-24 14:08:28
tags: OpenGL, 图形学
---
## 第二章: 向量

### 写在前面的话: 
> 本文基本等同于翻译(加上自己的理解), 笔者英语水平有限, 难免会出现错误, 望高手在评论中指出, 谢谢!
> 原书: [Mathematics for 3D Game Programming and Computer Graphics, Third Edition][1_booksite] 

#### 2.1 向量的属性

+ `定义`: 这里将向量定义为: `实数组成的n元组`

不过大多数情况下, 这个n指的是2, 3, 4, 一个n维向量可以记为:
$$V=(v_1,v_2,...,v_n) \tag{2.1}$$
$V_i$表示向量$V$的第i个分量, 通常情况下, 下标是这个分量所对应的坐标轴的名字. 例如: 一个三维向量$P$可以记为: $P_x,P_y,P_z$.
+ `列向量`: 可以使用1列n行来表示和(2.1)式等价的`列向量`
$$V = 
\begin{bmatrix}
v_1\\\\ v_2\\\\ \dots \\\\ v_n
\end{bmatrix} \tag{2.2}$$

+ `行向量`: 可以用1行n列来表示和(2.1)等价的`行向量`, 同时他也是列向量(2.2)的`转置矩阵`(行变列)
$$V^T=
\begin{bmatrix}
v_1 & v_2 & \dots & v_n
\end{bmatrix}\tag{2.3}$$

+ `向量和标量相乘`: 分别用这个标量和向量的每一个分量相乘, 结果是一个向量
$$
aV=Va=(av_1,av_2,\dots,av_n) \tag{2.4}
$$

+ `向量和向量的加法`: 每个分量分别相加, 结果是一个向量. 比如$P + Q$:
$$
P + Q = (p_1 + q_1,p_2 + q_2,\dots,p_n + q_n)
\tag{2.5}$$

+ `向量和向量的减法`: $P - Q$可以认为是$P + (-Q)$

+ #### 向量的运算法则: 给定任意两个标量$a,b$, 任意三个向量$P,Q,R$, 以下定律成立
> (a). $P + Q = Q + P$
> (b). $(P + Q) + R = P + (Q + R)$
> (c). $(ab)P = a(bP)$
> (d). $a(P + Q) = aP + aQ$
> (e). $(a + b)P = aP + bP$

+ `n维向量V的模`是一个标量, 记做$\left \\| V \right \\|$, 计算公式如下:
$$
\left \\| V \right \\| = \sqrt{\sum_{i=1}^{n}V^2_i} \tag{2.6}
$$
向量的模也可以叫做向量的长度. 长度为一的向量叫做`unit vector(单位向量)`. 当$V$代表三维的点或者方向的时候, 公式(2.6)可以记作:
$$
\left \\| V \right \\| = \sqrt{V_x^2 + V_y^2 + V_z^2}\tag{2.7}
$$
一个向量$V$至少有一个分量不为0才能够被单位化, 做法就是用这个向量$V$乘以$\frac{1}{\left \\| V \right \\|}$. 这个操作通常被称为`normalization(单位化)`, 在3D图形学中应用的很广泛. 这里应该注意的就是`单位化`这儿术语和`normal vector(法向量)`这个术语并没有什么联系, `法向量`指的平面中垂直于这个平面的一个向量. 向量的模遵循以下定律:
> **定理2.2 给定一个标量$a$, 任意两个向量$P, Q$.**
> (a). $\left \\| P \right \\| \geq 0$
> (b). $P = 0 当且仅当 P = (0,0,\dots,0)$
> (c). $\left \\| aP \right \\| =\left |a\right | \left \\| P \right \\|$
> (d). $\left \\| P + Q \right \\| \leqslant \left \\|P \right \\| \left \\|Q \right \\|$

#### 2.2 向量的点乘

向量的点乘, 通常也叫做`scalar product(数量积) or inner product(內积)`, 在3D图形学中用的很广泛因为他可以衡量两个向量的方向相似程度

+ `向量的点乘法则`: n维向量$P, Q$的內积记作: $P \cdot Q$, 通过下面的公式来计算
$$
P \cdot Q = \sum^{n}_{i=1}P_iQ_i \tag{2.9}
$$
其实就是两个向量对应分量的乘积的和. 在三维空间中, 有如下公式:
$$P\cdot Q = P_xQ_x + P_yQ_y + P_zQ_z \tag{2.10}$$
$点乘P \cdot \Q$




















[1_booksite]: http://www.mathfor3dgameprogramming.com/