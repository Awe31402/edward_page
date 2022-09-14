---
title: "單位四元數的動態性質"
slug: "quaternion_dynamic"
date: 2022-09-14
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
thumbnailImage: //d20aeo683mqd6t.cloudfront.net/zh-hant/articles/title_images/000/039/096/original/shutterstock_613997048.jpg
coverImage: //d20aeo683mqd6t.cloudfront.net/zh-hant/articles/title_images/000/039/096/original/shutterstock_613997048.jpg
metaAlignment: center
---

{{< toc >}}
# 公式推導

---

設一個 $q(t)$ 為一個隨時間 $t$  變動的單位四元數函數，並且在時間 $t$  以絕對坐標系表示的瞬時角速度為 $\omega(t)$，可簡記為 $\omega$

因此在極短的時間片段 $\Delta t$ ，物體進行一個沿單位向量$\hat\omega$ 旋轉角度$\Delta \theta =  ||\omega|| \Delta t$ 的旋轉運動

並且可以以單位四元數將運動描述為

<div>
$$
\begin{align}
\Delta q &= cos\frac{\Delta \theta}{2} + \hat \omega sin \frac{\Delta \theta}{2} \\
&= cos\frac{||\omega||\Delta t}{2} + \hat \omega sin \frac{||\omega||\Delta t}{2}
\end{align} 
$$
</div>

於是在時間 $t + \Delta t$  的單位四元數函數可表示為我們可以取得 q(t))

<div>
$$
q(t + \Delta t) = \Delta q\text  { }q(t)
$$
</div>

我們可以取得 $q(t)$ 的瞬時變化量

<div>
$$
\begin{align}
q(t + \Delta t) - q(t) &=  \Delta q\text  { }q(t) - q(t)\\
&= (cos\frac{||\omega||\Delta t}{2} + \hat \omega sin \frac{||\omega||\Delta t}{2})q(t) - q(t) \\
&= (cos\frac{||\omega||\Delta t}{2} - 1)q(t) + \hat \omega sin \frac{||\omega||\Delta t}{2}q(t)\\
&= -2 sin^2 \frac{||\omega||\Delta t}{4}q(t) +  \hat \omega sin \frac{||\omega||\Delta t}{2}q(t)
\end{align}
$$
</div>

因此 $q(t)$ 的導數為

<div>
$$
\begin{align}
\dot q(t) &= \lim_{\Delta t \to 0} {\frac{q(t + \Delta t) - q(t)}{\Delta t}}\\
&=\lim_{\Delta t \to 0}{(-2 sin^2 \frac{||\omega||\Delta t}{4}q(t) +  \hat \omega sin \frac{||\omega||\Delta t}{2}q(t))} \\
&= \hat \omega  \lim_{\Delta t \to 0}sin \frac{||\omega||\Delta t}{2}q(t) \\
&= \hat \omega \frac{||\omega||}{2}q(t) = \frac{1}{2} \omega(t)q(t)
\end{align}
$$
</div>

# 角速度與單位四元數導數的關係

---

由於

<div>
$$
\dot q = \frac{1}{2} \omega q
$$
</div>

可得

<div>
$$
\omega = 2 \dot qq^*
$$
</div>

根據單位四元數的旋轉公式，我們可以取得角速度在附體坐標系的描述為

<div>
$$
 \begin{align}
\widetilde \omega &= q^* \omega q 
\end{align}
$$
</div>

因此 $\dot q$ 可以利用附體坐標系的角速度表示如下:

<div>
$$
\dot q = \frac{1}{2} q \widetilde \omega
$$
</div>

# 範例程式

---

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

    double delta_t = 0.01;

    AngleAxisd omega_mult_delta_t(M_PI / 6.0 * delta_t, Vector3d(0, 1, 0));
    AngleAxisd rotation_vector(M_PI / 4, Vector3d(0, 0, 1));

    Vector3d omega_vec = Vector3d(0.0, M_PI /6.0, 0);

    // Get Quaternion from rotation vector
    Quaterniond q_t = Quaterniond(rotation_vector);
    Quaterniond delta_q = Quaterniond(omega_mult_delta_t);
    Quaterniond q_t_plut_delta_t = delta_q * q_t;

    Quaterniond omega_quat = Quaterniond(0.0, 0.0, M_PI / 6.0, 0.0);
    Quaterniond q_dot_mut_2 = omega_quat * q_t;
    Quaterniond omega_quat_form = q_dot_mut_2 * q_t.conjugate();

    cout << "(q(t + delta_t) - q_t) / delta_t:" << endl;
    cout << 1.0 / delta_t * (q_t_plut_delta_t.coeffs() - q_t.coeffs()) << endl;

    cout << "1 / 2 omgega q(t):" << endl;
    cout << 0.5 * q_dot_mut_2.coeffs() << endl;

    cout << "Omega in quat form (x, y, z, w):" << endl;
    cout << omega_quat_form.coeffs() << endl;

    cout << "Omega in vec form (x, y, z)" << endl;
    cout << omega_vec << endl;
    return 0;
}
```

```
(q(t + delta_t) - q_t) / delta_t:
    0.1002
    0.2419
-0.0001311
-0.0003166
1 / 2 omgega q(t):
0.1002
0.2419
     0
     0
Omega in quat form (x, y, z, w):
2.776e-17
   0.5236
        0
        0
Omega in vec form (x, y, z)
       0
0.5236
       0
```
