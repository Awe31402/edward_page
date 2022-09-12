---
title: "四元數的基本運算"
slug: "quaternion"
date: 2022-09-02
categories:
- mathematics
- geometry
tags:
- quaternion
- SLAM
keywords:
- quaternion
autoThumbnailImage: false
thumbnailImagePosition: "left"
thumbnailImage: //d1u9biwaxjngwg.cloudfront.net/welcome-to-tranquilpeak/city-750.jpg
coverImage: //d1u9biwaxjngwg.cloudfront.net/welcome-to-tranquilpeak/city.jpg
metaAlignment: center
---

{{< toc >}}

# 從羅德里格旋轉公式開始
---
向量 $\vec x'$  為向量 $\vec x$  以單位向量 $\vec n$ 為旋轉軸旋轉角度 $\psi$ 所得到的向量<br>
如圖可得

<p></p>
{{< image classes="fancybox fig-33" align="left" src="/images/rodrigue.png" thumbnail="/images/rodrigue.png"  title="rodrigue's rotation" >}}


<div>
$$
\begin{align}
\vec{v_2} &= \cos \psi \vec{v_1} - \sin \psi \vec{v_3} \\
\vec{v_1} &= \vec{x} - (\vec{n} \cdot \vec{x})\vec{n} \\
\vec{v_3} &= \vec{v_1} \times \vec{n} \\
&= (\vec{x} - (\vec{n} \cdot \vec{x})\vec{n}) \times \vec{n} \\
&= (\vec x \times \vec n) - (\vec n \cdot \vec x) \mathop{\underline{\vec n \times \vec n}}\limits_0 \\
&= \vec x \times \vec n
\end{align}
$$
</div>

最後可將 $\vec x'$  表示為

<div>
$$
\begin{align}
\vec x' &= (\vec n \cdot \vec x)\vec n + \vec v_2 \\
&=(\vec n \cdot \vec x)\vec n +\cos \psi (\vec{x} - (\vec{n} \cdot \vec{x})\vec{n}) + \sin \psi (\vec n \times \vec x ) \\
&= (1-\cos\psi)(\vec{n} \cdot \vec{x})\vec{n} + \cos \psi \vec x + \sin \psi (\vec n \times \vec x )
\end{align}
$$
</div>

{{< alert info >}}
羅德里格旋轉公式:<br>
$\vec x' = (1-\cos\psi)(\vec{n} \cdot \vec{x})\vec{n} + \cos \psi \vec x + \sin \psi ( \vec n \times  \vec x)$
{{< /alert >}}
 
# 四元數以及四元數的基本運算
---
## 四元數

四元數是一個由四個參數所組成的集合，包含:<br>

<div>
- 實數部 $q_0$
- 虛數部 $q_1i,q_2j, q_3k$ 且 $q_1,q_2, q_3 \in \mathbb{R}$
</div>

一個四元數可以表示成:

<div>
$$
q = q_0 + q_1i + q_2j + q_3k
$$
</div>

其中:

<div>
$$
\begin{split} 
i^2 &= j^2 = k^2 = ijk = -1 \\
ij &= k, ji = -k \\
jk &= i, kj = -i \\
ki &= j, ik = -j
\end{split}
$$
</div>

另外我們可以將四元數分成實數部以及向量化的虛數部進行描述，即:

<div>
$$
q = q_0 + q_1i + q_2j + q_3k = q_0 + \vec q = (q_0, \vec q)
$$
</div>

### 四元數單位元素

<div>
$$
1 = 1 + 0i + 0j +0k
$$
</div>

### 四元數絕對值

<div>
$$
|q| = \sqrt {q_0^2 + q_1^2 + q_2^2 + q_3^2}
$$
</div>

{{< alert info >}}
單位四元數:<br>
若四元數 $q$ 滿足  $|q|=1$ ，則 $q$ 為單位四元數
{{< /alert >}}

### 四元數共軛

<div>
$$
q^* = q_0 - q_1i-q_2j-q_3k
$$
</div>

### 四元數反元素

<div>
$$
q^{-1} = \frac {q^*}{|q|^2}
$$
</div>

### 四元數加法

<div>
$$
\begin{align*}
p + q &= (p_0 + p_1i + p_2j + p_3k) + (q_0 + q_1i +q_2j + q_3k) \\
&= (p_0 + q_0)+(p_1+q_1)i + (p_2+q_2)j + (p_3+q_3)k
\end{align*}
$$
</div>

### 四元數乘法
<div>
$$
\begin{align*}
pq =& (p_0 + p_1i + p_2j + p_3k)(q_0 + q_1i + q_2j + q_3k)\\
=&p_0q_0 - (p_1q_1+p_2q_2+p_3q_3) +
 p_0(q_1i+q_2j+q_3k) + q_0 (p_1i+p_2j+p_3k) \\&+ (p_2q_3-p_3q_2)i + (p_3q_1 - p_1q_3)j+(p_1q_2-p_2q_1)k\\
=& p_0q_0 - \vec p \cdot \vec q + p_0 \vec q + q_0 \vec p + \vec p \times \vec q
\end{align*} 
$$
</div>

{{< alert info >}}
證明:<br> 
<div>
$$
\begin{align*}
q q^{-1} &= q\frac{q^*}{|q|^2} \\
&= \frac{1}{|q|^2} (q_0, \vec q)(q_0, -\vec q) \\
&= \frac{1}{|q|^2} (q_0^2 + \vec q \cdot \vec q + \mathop {\underline{q_0\vec q - q_0\vec q + \vec q \times \vec q}}\limits_0) \\
&= \frac {1}{|q|^2} |q|^2 =1
\end{align*}
$$
</div>
{{< /alert >}}

 # 單位四元數與旋轉變換的關係
---
我們可以將以單位向量 $\vec n$ 為旋轉軸旋轉角度 $\psi$ 的變換以一個單位四元數描述，即

<div>
$$
\begin{align*}
q = \cos \frac{\psi}{2} + \vec n \sin \frac{\psi}{2}\\
q_0 = \cos \frac {\psi}{2}, \vec q = \sin  \frac{\psi}{2} \vec n
\end{align*}
$$
</div>


其中 $v = (0, \vec v)$ 為一個實數部為 0 的單純四元數，映射關係如圖<br>
<p></p>
{{< image classes="fancybox fig-100" src="/images/vec_to_quaternion.png" thumbnail="/images/vec_to_quaternion.png"  title="mapping vector to quaternion" >}}
並且將一向量$\vec v$ 進行旋轉變換可以表示為函數

<div>
$$
\begin{align*}
L_q(v) &= qvq^{-1} = qvq^* \\
&=(q_0, \vec q)(0, \vec v)(q_0, -\vec q) = (-\vec q \cdot \vec v, q_0\vec v+\vec q \times \vec v)(q_0, - \vec q) \\
&=0+q_0^2 \vec v +(\vec q \cdot \vec v)\vec q + q_0(\vec q \times \vec v)  - \vec q \times \vec v \times \vec q - q_0(\vec v \times \vec q) \\
&= q_0^2\vec v + (\vec q \cdot \vec v)\vec q +q_0(\vec q \times \vec v)+(\vec q \cdot \vec v)\vec q -|q|^2\vec v +q_0(\vec q \times \vec v) \\
&= (q_0^2 -|q|^2 )\vec v + 2(\vec q \cdot \vec v)\vec q + 2q_0(\vec q \times \vec v)
\end{align*}
$$
</div>

{{< alert info >}}
證明:<br>
<div>
$$
\begin{align*}
L_q(v) &= qvq^{-1} = qvq^* \\
=& (q_0^2 -|q|^2 )\vec v + 2(\vec q \cdot \vec v)\vec q + 2q_0(\vec q \times \vec v) \\
=& (\cos^2 \frac{\psi}{2} - \sin^2 \frac{\psi}{2}) \vec v+2\sin^2 \frac{\psi}{2}(\vec n \cdot \vec v)\vec n \\&+ 2\cos\frac{\psi}{2}\sin\frac{\psi}{2}(\vec n \times \vec v) \\
=&\cos \psi \vec v + (1-\cos \psi)(\vec n \cdot \vec v)\vec n+ \sin \psi(\vec n \times \vec v)
\end{align*}
$$
</div> 
{{< /alert >}}

# 單位四元數與旋轉矩陣的關係
---
<div>
$$
\begin{align*}
L_q(v) = (q_0^2 -|q|^2 )\vec v + 2(\vec q \cdot \vec v)\vec q + 2q_0(\vec q \times \vec v)
\end{align*}
$$
</div>

<div>
$$
\begin{align*}
Rv &= (q_0^2 -|q|^2 )I v + 2(q^T v)q + 2q_0(q_\times v) \\
&= [(q_0^2 -|q|^2 )I + 2qq^T + 2 q_0 q_\times]v
\end{align*}
$$
</div>

<div>
$$
q = \begin{bmatrix}
q_1 \\
q_2 \\
q_3
\end{bmatrix}
, q_\times = \begin{bmatrix}
0 & -q_3 & q_2\\
q_3 & 0 & -q_1 \\
-q_2 & q_1 & 0
\end{bmatrix}
$$
</div>

<div>
$$
\begin{align*}
R =&(q_0^2 -|q|^2 )I + 2qq^T + 2 q_0 q_\times \\
=&  \begin{bmatrix}
q_0^2 - q_1^2 - q_2^2 - q_3^2 & 0 & 0\\
0 & q_0^2 - q_1^2 - q_2^2 - q_3^2 & 0 \\
0 & 0 & q_0^2 - q_1^2 - q_2^2 - q_3^2
\end{bmatrix} \\&+ 2\begin{bmatrix}
 q_1^2  & q_1q_2 & q_1q_3\\
q_2q_1 & q_2^2  & q_2q_3 \\
q
_3q_1 & q_3q_2 & q_3^2
\end{bmatrix} + 2q_0\begin{bmatrix}
0 & -q_3 & q_2\\
q_3 & 0 & -q_1 \\
-q_2 & q_1 & 0
\end{bmatrix} \\
=& \begin{bmatrix}
q_0^2 + q_1^2 - q_2^2 - q_3^2 & 2(q_11_2-q_0q_3) & 2(q_1q_3 + q_0q_2)\\
2(q_1q_2+q_0q3) & q_0^2 - q_1^2 + q_2^2 - q_3^2 & 2(q_2q_3 - 2q_0q_1) \\
2(q_1q_3-q_0q_2) & 2(q_2q_3+q_0q_1) & q_0^2 - q_1^2 - q_2^2 + q_3^2
\end{bmatrix}\\
=&\begin{bmatrix}
2(q_0^2 + q_1^2) - 1 & 2(q_11_2-q_0q_3) & 2(q_1q_3 + q_0q_2)\\
2(q_1q_2+q_0q_3) & 2(q_0^2  + q_2^2) - 1 & 2(q_2q_3 - 2q_0q_1) \\
2(q_1q_3-q_0q_2) & 2(q_2q_3+q_0q_1) & 2(q_0^2 + q_3^2) - 1
\end{bmatrix}
\end{align*}
$$
</div>

# 參考資料
---
- [Quaternions (Com S 477/577 Notes), Yan-Bin Jia](https://physique.cmaisonneuve.qc.ca/svezina/mat/note_mat/Quaternions-Yan-Bin_Jia-2019.pdf)

# 範例程式
---
C++ example:
```cpp
#include <iostream>
#include <cmath>
#include <Eigen/Core>
#include <Eigen/Geometry>

using namespace std;
using namespace Eigen;

int main(int argc, char* argv[])
{
    cout.precision(4);
    // Declare a rotation vector which rotate 45 degree around Z axis
    AngleAxisd rotation_vector(M_PI / 4, Vector3d(0, 0, 1));
    cout << "rotation matrix = " << endl << rotation_vector.matrix() << endl;

    Matrix3d rotation_matrix = rotation_vector.toRotationMatrix();
    Vector3d v(1, 0, 0);
    
    // rotate the vector by AngileAxis
    Vector3d v_rotated = rotation_vector * v;
    cout << "rotate (1, 0, 0) by angleaxis = " << endl << v_rotated << endl;

    // rotate the vector by rotation matrix
    v_rotated = rotation_matrix * v;
    cout << "rotate (1, 0, 0) by matrix = " << endl << v_rotated << endl;

    // Get XYZ euler angle
    Vector3d euler_angles = rotation_matrix.eulerAngles(0, 1, 2);
    cout << "XYZ euler: " << endl << euler_angles << endl;

    // Get Quaternion from rotation vector
    Quaterniond q = Quaterniond(rotation_vector);
    cout << "quaternion from rotation vector: " << endl << q.coeffs() << endl;
    
    q = Quaterniond(rotation_matrix);
    cout << "quaternion from rotation matrix: " << endl << q.coeffs() << endl;
    // Declare Quaternion(q0, q1, q2, q3)
    Quaterniond v_q = Quaterniond(0, 1, 0, 0);
    
    // rotate vector with quaternion way
    v_rotated = q * v; // qvq^{-1}
    cout << "rotate vector with quaternion: " << endl << v_rotated << endl;
    
    Quaterniond q_rotated = q * v_q * q.inverse();
    cout << "rotate vector with quaternion q(q1, q2, q3, q0): " << endl
         << q_rotated.coeffs() << endl;
    return 0;
}
```
Output of sample code:
```
rotation matrix =
 0.7071 -0.7071       0
 0.7071  0.7071       0
      0       0       1
rotate (1, 0, 0) by angleaxis =
0.7071
0.7071
     0
rotate (1, 0, 0) by matrix =
0.7071
0.7071
     0
XYZ euler:
    -0
     0
0.7854
quaternion from rotation vector:
     0
     0
0.3827
0.9239
quaternion from rotation matrix:
     0
     0
0.3827
0.9239
rotate vector with quaternion:
0.7071
0.7071
     0
rotate vector with quaternion q(q1, q2, q3, q0):
0.7071
0.7071
     0
     0
```

{{< disqus >}}
