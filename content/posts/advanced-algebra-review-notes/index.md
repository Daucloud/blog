+++
date = '2024-06-18T00:00:00+08:00'
draft = false
title = '高代选讲期末重点整理'
aliases = ['/2024/06/18/高代选讲期末重点整理/']
tags = ['Linear Algebra', 'Course Notes']
categories = ['Learning']
+++
> 此文为宗正宇老师在最后一节课所划六个重点的整理，权作期末预习(x)

# 求特征多项式和极小多项式

## 特征多项式

$f(\lambda)=\left|\lambda I-A\right|$

## 极小多项式

### 方法 1

**定理 7.4**

极小多项式 $m(\lambda)$ 和特征多项式 $f(\lambda)$ 含有完全相同的根集和不可约因子集。

一般来说考试涉及的矩阵并不复杂，维数也较小，因此可以根据定理 7.4，从小到大尝试$f(\lambda)$的因式$a(\lambda)$是否满足$a(A)=O$以确定最小多项式

### 方法 2

求出矩阵$A$的 Jordan 标准型，则$(\lambda-\lambda_i)$项在$m(\lambda)$中的重数即为特征值为$\lambda_i$的 Jordan 块的最大阶数；对于广义 Jordan 块，可以直接取为相应的不可约因式

# 计算复方阵的 Jordan 标准型及可逆矩阵 P

## Jordan 标准型计算

### 方法 1

1.  求出特征多项式$f(\lambda)$

2.  则$\lambda_i$的个数为$r_i=n-rank(A-\lambda_iI)$，也即$f(\lambda)$中$(\lambda-\lambda_i)$的次数

3.  阶数为$t$的 Jordan 块的个数为$r_i(t)=rank(A-\lambda_iI)^{t+1}+rank(A-\lambda_iI)^{t-1}-2rank(A-\lambda_iI)^t$

    > 解释：注意到$rank(A-\lambda_iI)^m$中仅含有原本阶数大于$m$的$\lambda_i$的 Jordan 块，故$rank(A-\lambda_iI)^m-rank(A-\lambda_iI)^{m+1}$恰好为阶数大于$m$的$\lambda_i$的 Jordan 块的个数，因此$\left(rank(A-\lambda_iI)^{m-1}-rank(A-\lambda_iI)^m\right)-\left(rank(A-\lambda_iI)^m-rank(A-\lambda_iI)^{m+1}\right)$为阶数大于$m-1$的$\lambda_i$的 Jordan 块的个数和阶数大于$m$的 Jordan 块的个数之差，换言之即为阶数为$m$的 Jordan 块的个数

- 一些 tricks:
  1.  由于考试涉及的 Jordan 块通常维数不大，而$m(\lambda_i)$则确定了各$\lambda_i$的 Jordan 块的最大阶数，通过此往往就可求出 Jordan 标准型
  2.  可以通过计算$dim Ker(\lambda_iI-A)$来获知$\lambda_i$的 Jordan 块的个数（$Ker(\lambda_iI-A)$中的每个向量都可以作为一条 Jordan 链的终结）

### 方法 2

求出$A$的初等因子组，则 Jordan 标准型为

$$
J=\begin{bmatrix}J\left(p_1^{k_1}\right)&&\\&\ddots&\\&&J\left(p_s^{k_s}\right)\end{bmatrix}
$$

> 注：该方阵称为第三种相似标准型，除此之外，还有第一类相似标准型（有理标准型）和第二类相似标准型（初等因子友阵型）:

$$
C=\begin{bmatrix}C\left(d_1\right)&&\\&\ddots&\\&&C\left(d_r\right)\end{bmatrix}\\G=\begin{bmatrix}C\left(p_1^{k_1}\right)&&\\&\ddots&\\&&C\left(p_s^{k_s}\right)\end{bmatrix}
$$

## 可逆矩阵 P 的求取

### 方法 1

1.  求出$Ker(A-\lambda_iI)$，其中的向量可以作为$\lambda_i$的 Jordan 链的终结
2.  但是需要注意，并不是每个向量都可以形成阶数正确的 Jordan 块。譬如，设$Ker\left(A-\lambda_iI\right)$由$\alpha_1$和$\alpha_2$张成。如果一个 Jordan 块为二阶，那么我们需要设此 Jordan 链的终结为$s\alpha_1+t\alpha_2$，并确保$\left(A-\lambda_iI\right)x=s\alpha_1+t\alpha_2$有解（通过考虑增广矩阵$\begin{bmatrix}A&s\alpha_1+t\alpha_2\end{bmatrix}$）

> 举一道例题，便于理解
>
> *问：求此复数域上方阵的 Jordan 标准型和可逆矩阵 P：*

$$
\begin{bmatrix}1&-3& 0&3\\-2&-6& 0&13\\0&-3& 1&3\\-1&-4& 0&8\\\end{bmatrix}
$$

*解：*

有

$$
f(\lambda)=(\lambda-1)^4,\quad m(\lambda)=(\lambda-1)^3
$$

故 Jordan 标准型为

$$
\begin{bmatrix}
1&0&0&0\\
1&1&0&0\\
0&1&1&0\\
0&0&0&1
\end{bmatrix}
$$

并且

$$
Ker(A-I)=span\left(
\begin{bmatrix}3\\1\\0\\1\end{bmatrix},
\begin{bmatrix}0\\0\\1\\0\end{bmatrix}
\right)
$$

取

$$
\alpha_4=\begin{bmatrix}3\\1\\0\\1\end{bmatrix},\quad
\alpha_3=\begin{bmatrix}3s\\s\\t\\s\end{bmatrix}
$$

欲使 $(A-I)\alpha_2=\alpha_3$ 有解，考虑增广矩阵：

$$
\begin{bmatrix}
0&-3&0&3&3s\\
-2&-7&0&13&s\\
0&-3&0&3&t\\
-1&-4&0&7&s
\end{bmatrix}
$$

可知 $3s=t$。不妨取 $s=1$，故

$$
\alpha_3=\begin{bmatrix}3\\1\\3\\1\end{bmatrix},\quad
\alpha_2=\begin{bmatrix}3u+3\\v-1\\0\\0\end{bmatrix}
$$

再令 $(A-I)\alpha_1=\alpha_2$ 有解，考虑增广矩阵：

$$
\begin{bmatrix}
0&-3&0&3&3u+3\\
-2&-7&0&13&u-1\\
0&-3&0&3&v\\
-1&-4&0&7&w
\end{bmatrix}
$$

有 $3u+3=v,\ 2w-\frac{v}{3}=u-1$。不妨取 $u=1,v=6,w=1$，故

$$
\alpha_2=\begin{bmatrix}6\\0\\6\\1\end{bmatrix},\quad
\alpha_1=\begin{bmatrix}7\\-2\\0\\0\end{bmatrix}
$$

故知

$$
P=\begin{bmatrix}
7&6&3&3\\
-2&0&1&1\\
0&6&3&0\\
0&1&1&1
\end{bmatrix}
$$

### 方法 2

由于$\lambda I-A$和$\lambda I-J$有相同的 Smith 标准型，而 Smith 标准型有固定的算法求解，因此可以求$P_1^{-1}\left(\lambda I-A\right)P_1=P_2^{-1}\left(\lambda I-J\right)P_2=S$，则$P=P_2P_1^{-1}$

# lambda-矩阵对角化；初等因子、不变因子和 Jordan 标准型的关系

## lambda-矩阵对角化

课本有详尽的算法。简而言之即是不断降低$a_{11}$的次数  
![3663133d2ba182b3d2aca82db4134e2.jpg](https://img.picgo.net/2024/06/18/2024061812322236777062ff2476a40.jpg)  
![9b55172bde8fb4579a9083312526854.jpg](https://img.picgo.net/2024/06/18/202406181233913c52ac3b6584e7b8d.jpg)

## 初等因子、不变因子与 Jordan 标准型的关系

### 三种基本因子的概念

- 对于$\lambda-$矩阵

1.  不变因子：Smith 标准型对角线上的所有非零元素：$d_1(\lambda),…,d_r(\lambda)$

2.  行列式因子：所有 k 阶非零子行列式的首 1 最大公因子：$D_1,…,D_r$

3.  初等因子：不变因子的准素因子全体（1 不计入）

    > 不变因子和行列式因子互相决定：$D_k=\prod_{i=1}^k d_i,\ d_k=\frac{D_k}{D_{k-1}}$

- 对于数字矩阵，其三种基本因子指的是$\lambda I-A$的三种基本因子（但是 1 均不计入）

### 从初等因子求 Jordan 标准型

$$
J=\begin{bmatrix}J\left(p_1^{k_1}\right)&&\\&\ddots&\\&&J\left(p_s^{k_s}\right)\end{bmatrix}
$$

# Gram-Schmidt 正交化

设$A=\begin{bmatrix}v_1&v_2&\cdots&v_n\end{bmatrix}$  
则其正交化结果$Q=\begin{bmatrix}w_1&w_2&\cdots&w_n\end{bmatrix}$  
其中：

$$
\begin{aligned}&w_1=v_1\\&w_2=v_2-\frac{\left<w_1,v_2\right>}{\left<w_1,w_1\right>}w_1\\&w_3=v_3-\frac{\left<w_1,v_3\right>}{\left<w_1,w_1\right>}w_1-\frac{\left<w_2,v_3\right>}{\left<w_2,w_2\right>}w_2\\&\vdots\\&w_n=v_n-\frac{\left<w_1,v_n\right>}{\left<w_1,w_1\right>}w_1-\frac{\left<w_2,v_n\right>}{\left<w_2,w_2\right>}w_2-\cdots-\frac{\left<w_n,v_n\right>}{\left<w_n,w_n\right>}w_n\\\end{aligned}
$$

上学期的内容，不过多赘述

# 规范方阵谱分解

1.  求出规范方阵 $A$ 的特征多项式，并解出特征值则$U^{-1}AU=diag(\lambda_{1},\cdots,\lambda_n)$
2.  求出$Ker(\lambda_iI-A)$的一组基，对其施行 Gram-Schimidt 正交化
3.  将上一步得到的所有向量排列起来，即得到酉方阵$U$

# Jordan 标准型的应用和可对角化条件

设 $A$ 为 n 阶复方阵，则如下命题等价：

1.  $A$ 在$\mathbb{C}$可对角化
2.  $A$ 的几何重数为 n（有 n 个线性无关的特征向量）
3.  $A$ 在复数域上的初等因子均为 1 次（即初等因子均无重根）
4.  $A$ 的不变因子均无重根
5.  $A$ 的极小多项式无重根
6.  $\forall c\in\mathbb{C},rank(cI-A)=rank\left((cI-A)^2\right)$

此外，以上命题的一个充分条件是$f(\lambda)$无重根
