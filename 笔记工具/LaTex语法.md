+++
date = '2025-05-23T20:12:04+08:00'
draft = true
title = 'LaTex语法'
math = true
license = ""
description = ""
categories = [
    "开发工具使用"
]
tags = [
    "Markdown"
]
image = ""
+++


## 公式
### 上标与下标
用^来输出上标，使用_来输出下标，使用{}包含作用范围。  

### 空格表示

两个quad空格---------a \qquad b---------两个m的宽度  
quad空格---------a \quad b---------一个m的宽度  
大空格---------a\ b---------1/3m宽度  
中等空格---------a\;b---------2/7m宽度  
小空格	a\,b---------a\,b---------1/6m宽度  
没有空格---------ab  
紧贴---------a\!b---------缩进1/6m宽度  

### 数学结构
1. 分式 \frac{a}{b}  :  $\frac{a}{b}$  
2. 根式 \sqrt[n]{a}  :  $\sqrt[n]{a}$  
3. 求导 f' : $f'$
4. 累加$\sum$ ：\sum
5. 累乘$\prod$：`\prod`  
6. 极限 $\lim\limits_{x\to\infty}$：\lim\limits_{x\to\infty}
7. 积分号$\int$：\int
8. 其它 上划线:\overline{a},下划线\underline{a},上右箭头\overrightarrow{a}:$\overrightarrow{a}$  



### 数学函数

1.三角函数 $\sin$：\sin ; $\cos$：\cos;

### 数学符号
1. 偏导数 $\partial$  ：\partial  

### 数学运算

1. 点乘 $a \cdot b$：\cdot  

2. 叉乘 $a \times b$：\times  
3. 除以 $a \div b$：\div  

### 希腊字母

1. $\alpha \beta \gamma \delta \epsilon \zeta \eta \theta$：\alpha \beta \gamma \delta \epsilon \zeta \eta \theta  
2. $\iota \kappa \lambda \mu \nu \omicron \xi \pi$  ：\iota \kappa \lambda \mu \nu \omicron \xi \pi  
3. $\rho \sigma \tau \upsilon \phi \chi \psi \omega$：\rho \sigma \tau \upsilon \phi \chi \psi \omega  
4. $\Alpha \Beta \Gamma \Delta \Epsilon \Zeta \Eta \Theta$：\Alpha \Beta \Gamma \Delta \Epsilon \Zeta \Eta \Theta  
5. $\Iota \Kappa \Lambda \Mu \Nu \Xi \Omicron \Pi$：\Iota \Kappa \Lambda \Mu \Nu \Xi \Omicron \Pi  
6. $\Rho \Sigma \Tau \Upsilon \Phi \Chi \Psi \Omega$：\Rho \Sigma \Tau \Upsilon \Phi \Chi \Psi \Omega

### 关系符号

1. $\leqslant ， \geqslant ，\le ，\ge$：\leqslant ， \geqslant ，\le ，\ge
2. `\in` ：$\in$
3. `\ne`：$\ne$
4. `\approx`：$\approx$

### 括号

1. 大括号 `\{`
1. 向下取整 `\lfloor x \rfloor` $\lfloor x \rfloor$
1. 向上取整`\lceil x \rceil` $\lceil x \rceil$

### 其它

1. \underset{123}{abc} : $\underset{123}{abc}$
2. 使用 **\limits** 将下标放在某个文字或者符号的正下方。 
3. 用**\mathop**{文本}命令将文本转化成数学符号
4. **省略符号**： `\cdots` $\cdots$ , `\ddots` $\ddots$ , `\vdots` $\vdots$ 
5. $\widetilde{x}$：`\widetilde{x}`



### 矩阵、条件表达式、方程组

语法：

```
\begin{类型}
	公式内容
\end{类型}
```

#### 无框矩阵 matrix

```
\begin{matrix}
x & y \\
z & v
\end{matrix}
```

$$
\begin{matrix}
x & y \\
z & v
\end{matrix}
$$

#### 有框矩阵 pmatrix、bmatrix、vmatrix

1. vmatrix

```
\begin{vmatrix}
x & y \\
z & v
\end{vmatrix}
```

$$
\begin{vmatrix}
x & y \\
z & v
\end{vmatrix}
$$

2. **V**matrix

$$
\begin{Vmatrix}
x & y \\
z & v
\end{Vmatrix}
$$

3. bmatrix

$$
\begin{bmatrix}
0      & \cdots & 0      \\
\vdots & \ddots & \vdots \\
0      & \cdots & 0
\end{bmatrix}
$$

4. **B**matrix

$$
\begin{Bmatrix}
a      & b      \\
c      & d
\end{Bmatrix}
$$



5. pmatrix

$$
\begin{pmatrix}
x & y \\
z & v
\end{pmatrix}
$$

#### 条件表达式、方程组：cases

```
f(n) =
\begin{cases} 
1,  & \text{if n is 1} \\
2, & \text{if n is 2}
\end{cases}
```

$$
f(n) =
\begin{cases} 
1,  & \text{if n is 1} \\
2, & \text{if n is 2}
\end{cases}
$$

#### 多行等式 aligned

```
\begin{aligned}
f(x) & = 2 \\
     & = 1+1 \\
\end{aligned}
```

$$
\begin{aligned}
f(x) & = 2 \\
     & = 1+1 \\
\end{aligned}
$$

