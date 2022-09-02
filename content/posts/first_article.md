---
title: "Quaternion"
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
thumbnailImagePosition: "top"
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
{{< image classes="fancybox fig-33" align="right" src="/images/rodrigue.png" thumbnail="/images/rodrigue.png"  title="rodrigue's rotation" >}}


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

# 四元數以及四元數的基本運算
---
## 四元數
