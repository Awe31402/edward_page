---
title: "Derivation of Kalman Filter"
slug: "KalmanFilter"
date: 2022-09-06
categories:
- mathematics
- SLAM
tags:
- Recursive Bayes Filter
- SLAM
keywords:
- Kalman Filter
autoThumbnailImage: false
resources:
- name: "featured-image"
  src: "featured-image.jpg"
metaAlignment: center
---


{{< image classes="fancybox fig-50" src="kf_algo.png" thumbnail="kf_algo.png"  title="Kalman Filter" >}}

## Start from Recursive Bayes Filter
---
### Basic Assumptions

1. Observation and state transition matrices are linear:
    <div>
    $$
    \left\{
        \begin{matrix}
            x_t &=& A_tx_{t-1}+B_tu_t + \omega_t\\
            z_t &=& C_tx_t+\nu_t
        \end{matrix}
    \right.
    $$
    </div>

2. All of the noises are Gaussian distributed:
<div>
$$
\omega_t  \sim \mathcal N(0, R_t)\\
\nu_t \sim \mathcal N(0, Q_t)
$$
</div>


### Time Update (Prediction)

Noted that:

1. The belief $bel(x_{t-1})$ obeys Normal distribution $\mathcal N(x_{t-1};\mu_{t-1}, \Sigma_{t-1})$  
2. The motion model $p(x_t|x_{t-1}, u_t)$  obeys Normal distribution  $\mathcal N(x_t;A_tx_{t-1}+B_tu_t, R_t)$ 

The prediction step can be represented as:

<div style="width:1100px;" align="left">
$$
\begin {align}
\overline {bel}(x_t) &= \int \underbrace {p(x_t|x_{t-1}, u_t)}_{\sim \mathcal N(x_t;A_tx_{t-1}+B_tu_t, R_t)} \text{  }\underbrace{bel(x_{t-1}) }_{\sim \mathcal N(x_{t-1};\mu_{t-1}, \Sigma_{t-1})}dx_{t-1}\\
&= \eta \int exp\{ - \frac{1}{2} (x_t - A_t x_{t-1} - B_t u_t)^TR_t^{-1}(x_t - A_t x_{t-1} - B_t u_t) \} exp\{ - \frac{1}{2} (x_{t-1} - \mu _{t-1})^T\Sigma_{t-1}^{-1}(x_{t-1} - \mu _{t-1}) \} dx_{t-1}\\
&= \eta \int exp \{ -L_t\} dx_{t-1}
\end {align}
$$
</div>

where

<div>
$$
\begin{align}
L_t = \frac{1}{2} (x_t - A_t x_{t-1} - B_t u_t)^TR_t^{-1}(x_t - A_t x_{t-1} - B_t u_t) + \frac{1}{2} (x_{t-1} - \mu _{t-1})^T\Sigma_{t-1}^{-1}(x_{t-1} - \mu _{t-1})
\end{align}
$$
</div>

{{< hl-text blue >}}$L_t$ is quadratic in  $x_{t-1}$ and also is quadratic in  $x_{t}${{< /hl-text >}}, so that we can  decompose $L_t$ into two parts,  $L_t(x_{t-1}, x_t)$  and $L_t(x_t)$  

<div>
$$
L_t = L_t(x_{t-1}, x_t) + L_t(x_t)
$$
</div>

So that

<div>
$$
\begin {align}
\overline {bel}(x_t) 
&= \eta \int exp \{ -L_t\} dx_{t-1} \\
&= \eta \int exp\{ -L_t(x_{t-1}, x_t) - L_t(x_t)\} dx_{t-1} \\
&= \eta \text{ } exp\{-L_t(x_t)\} \mathop{\int exp\{ -L_t(x_{t-1}, x_t) \}dx_{t-1}}  \\
\end {align} 
$$
</div>

For seeking the function  $L_t(x_{t-1}, x_t)$   quadratic in $x_{t-1}$ , we calculate the first two derivatives of $L_t$ ,

<div>
$$
\begin {align}
\frac {\partial L_t} {\partial x_{t-1}} &= -A_t^TR_t^{-1}(x_t - A_tx_{t-1} - B_tu_t) + \Sigma_t^{-1} (x_{t-1} - \mu_{t-1}) \\
\frac {\partial^2 L_t} {\partial^2 x_{t-1}} &= A_t^TR_t^{-1}A_t+ \Sigma_{t-1}^{-1} =  \Psi^{-1}_t
\end {align}
$$
</div>

in order to find out the expected value of  $x_{t-1}$ ,  try to solve

<div>
$$
\begin {align}
\frac {\partial L_t} {\partial x_{t-1}} &= -A_t^TR_t^{-1}(x_t - A_tx_{t-1} - B_tu_t) + \Sigma_{t-1}^{-1} (x_{t-1} - \mu_{t-1})  = 0
\end {align}
$$
</div>

we can get

<div>
$$
\begin{align}
&A_t^TR_t^{-1}(x_t - A_tx_{t-1} - B_tu_t) = \Sigma_{t-1}^{-1} (x_{t-1} - \mu_{t-1})  \\
&A^T_t R_t^{-1}x_t - A^T_t R_t^{-1}A_tx_{t-1} - A^T_t R_t^{-1}B_tu_t = \Sigma_{t-1}^{-1} x_{t-1} -  \Sigma_{t-1}^{-1} \mu_{t-1} \\
 &(A^T_t R_t^{-1}A_t + \Sigma_{t-1}^{-1} )x_{t-1} = A^T_t R_t^{-1}x_t - A^T_t R_t^{-1}B_tu_t + \Sigma_{t-1}^{-1} \mu_{t-1}\\
&\Psi_t^{-1}x_{t-1} = A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}\\
&x_{t-1} = \Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]
\end{align}
$$
</div>

Now we get

<div>
$$
L_t(x_{t-1}, x_t) = \frac{1}{2}(x_{t-1}-\Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}])^T\Psi_t^{-1}(x_{t-1}-\Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}])
$$
</div>

and since function 

<div>
$$
det(2\pi\Psi_t)^{-\frac{1}{2}} exp\{-L_t(x_{t-1}, x_t)\} dx_{t-1}
$$
</div>

is a probability density function of $x_{t-1}$, so

<div>
$$
\int det(2\pi\Psi_t)^{-\frac{1}{2}} exp\{-L_t(x_{t-1}, x_t)\} dx_{t-1} = 1
$$
</div>

Now $\overline {bel}(x_t)$ can be re-written as:

<div>
$$
\begin {align}
\overline {bel}(x_t) 
&= \eta \text{ } exp\{-L_t(x_t)\} \int exp\{ -L_t(x_{t-1}, x_t) \}dx_{t-1} \\
&= \eta \text{ } exp\{-L_t(x_t)\} 
\end {align} 
$$
</div>

according to {{< hl-text green >}}$L_t = L_t(x_{t-1}, x_t) + L_t(x_t)${{< /hl-text >}}in previous, we can get

<div style="width:1100px" align="left">
$$
\begin {align*} 
L_t(x_t) =& L_t - L_t(x_{t-1}, x_t)\\ 
=& \frac{1}{2} (x_t - A_t x_{t-1} - B_t u_t)^TR_t^{-1}(x_t - A_t x_{t-1} - B_t u_t) \\ &+ \frac{1}{2} (x_{t-1} - \mu _{t-1})^T\Sigma_{t-1}^{-1}(x_{t-1} - \mu _{t-1}) \\&- \frac{1}{2}(x_{t-1}-\Psi_t^{}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}])^T\Psi_t^{-1}(x_{t-1}-\Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]) \\
\end {align*} 
$$
</div>

separate terms including  $x_{t-1}$ from others

<div style="width:1100px" align="left">
$$
\def\doubleunderline#1{\underline{\underline{#1}}}
\begin{align*} 
L_t(x_t) 
=& \frac{1}{2} (x_t - A_t x_{t-1} - B_t u_t)^TR_t^{-1}(x_t - A_t x_{t-1} - B_t u_t) \\ &+ \frac{1}{2} (x_{t-1} - \mu _{t-1})^T\Sigma_{t-1}^{-1}(x_{t-1} - \mu _{t-1}) \\&- \frac{1}{2}(x_{t-1}-\Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}])^T\Psi_t^{-1}(x_{t-1}-\Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}])\\
=& \doubleunderline{\frac{1}{2} x_{t-1}^TA_t^TR_t^{-1}A_tx_{t-1}} -\underline{x_{t-1}^TA_t^{T}R_t^{-1}(x_t - B_tu_t)} +  \frac{1}{2}(x_t - B_tu_t)^T R_t^{-1}(x_t-B_tu_t) \\
&+ \doubleunderline{\frac{1}{2}x_{t-1}^T\Sigma_{t-1}^{-1}x_{t-1}}-\underline{x_{t-1}^T\Sigma_{t-1}^{-1}\mu_{t-1}} + \frac{1}{2} \mu_{t-1}^T  \Sigma_{t-1}^{-1}\mu_{t-1}- \doubleunderline{\frac{1}{2}x_{t-1}^T \Psi_{t}^{-1} x_{t-1}}\\
&+ \underline{x_{t-1}^T \Psi_t^{-1}\Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}])} \\
&-\frac{1}{2}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]^T\Psi_t^{T}\Psi_t^{-1}\Psi_t[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}] \\
=& \doubleunderline{\frac{1}{2} x_{t-1}^TA_t^TR_t^{-1}A_tx_{t-1}} -\underline{x_{t-1}^TA_t^TR_t^{-1}(x_t - B_tu_t)} +  \frac{1}{2}(x_t - B_tu_t)^T R_t^{-1}(x_t-B_tu_t) \\
&+ \doubleunderline{\frac{1}{2}x_{t-1}^T\Sigma_{t-1}^{-1}x_{t-1}}-\underline{x_{t-1}^T\Sigma_{t-1}^{-1}\mu_{t-1}} + \frac{1}{2} \mu_{t-1}^T  \Sigma_{t-1}^{-1}\mu_{t-1}- \doubleunderline{\frac{1}{2}x_{t-1}^T (A_t^TR_t^{-1}A_t+ \Sigma_{t-1}^{-1}) x_{t-1}}\\
&+ \underline{x_{t-1}^T [A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}])} \\
&-\frac{1}{2}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]^T(A_t^TR_t^{-1}A_t+ \Sigma_{t-1}^{-1})^{-1}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]\\
=& \frac{1}{2}(x_t - B_tu_t)^T R_t^{-1}(x_t-B_tu_t)+ \frac{1}{2} \mu_{t-1}^T  \Sigma_{t-1}^{-1}\mu_{t-1}\\
&-\frac{1}{2}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]^T(A_t^TR_t^{-1}A_t+ \Sigma_{t-1}^{-1})^{-1}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]
\end{align*} 
$$
</div>

{{< admonition tip >}}
Seperate all items about $x_{t-1}$ from others
{{< /admonition >}}

we can find that $L_t(x_t)$  is a quadratic function only related to variable $x_t$ 

<div style="width:1100px" align="left">
$$
\begin{align} 
\begin{split}
L_t(x_t) 
=& \frac{1}{2}(x_t - B_tu_t)^T R_t^{-1}(x_t-B_tu_t)+ \frac{1}{2} \mu_{t-1}^T  \Sigma_{t-1}^{-1}\mu_{t-1} \\
&-\frac{1}{2}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]^T(A_t^TR_t^{-1}A_t+ \Sigma_{t-1}^{-1})^{-1}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]
\end{split}
\end{align} 
$$
</div>

Then the mean and covariance of this distribution can be obtained by computing first and second order derivative of $L_t(x_t)$  

<div style="width:1100px" align="left">
$$
\begin{align}
\frac {\partial L_t(x_t)}{\partial x_t} &= R_t^{-1}(x_t - B_tu_t)-R_t^{-1}A_t(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}[A^T_t R_t^{-1}(x_t - B_tu_t) + \Sigma_{t-1}^{-1} \mu_{t-1}]\\
&= [R_t^{-1}-R_t^{-1}A_t(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}A^T_t R_t^{-1}](x_t - B_tu_t)-R_t^{-1}A_t(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}\Sigma_{t-1}^{-1} \mu_{t-1}
\end{align}
$$
</div>

Then apply the inversion lemma

<div style="width:1100px" align="left">
$$
\begin{align}
R_t^{-1}-R_t^{-1}A_t(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}A^T_t R_t^{-1} = (R_t+A_t\Sigma_{t-1}A_t^T)^{-1}
\end{align}
$$
</div>

So that

<div style="width:1100px" align="left">
$$
\begin{align*}
\frac {\partial L_t(x_t)}{\partial x_t} 
&= [R_t^{-1}-R_t^{-1}A_t(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}A^T_t R_t^{-1}](x_t - B_tu_t)-R_t^{-1}A_t(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}\Sigma_{t-1}^{-1} \mu_{t-1}\\
&= (R_t+A_t\Sigma_{t-1}A_t^T)^{-1}(x_t - B_tu_t)-R_t^{-1}A_t(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}\Sigma_{t-1}^{-1} \mu_{t-1} = 0
\end{align*}
$$
</div>

The expected value of the distribution is

<div>
$$
\begin{align*}
x_t&= B_tu_t + \underbrace{(R_t+A_t\Sigma_{t-1}A_t^T)R_t^{-1}A_t}_{A_t + A_t\Sigma_{t-1}A_t^TR_t^{-1}A_t}\text{ }\underbrace{(A_t^TR_t^{-1}A_t + \Sigma_{t-1}^{-1})^{-1}\Sigma_{t-1}^{-1} }_{(\Sigma_{t-1}A_t^TR_t^{-1}A_t + I)^{-1}}\mu_{t-1}\\
&=B_tu_t + A_t\underbrace{(\Sigma_{t-1}A_t^TR_t^{-1}A_t + I)(\Sigma_{t-1}A_t^TR_t^{-1}A_t + I)^{-1}}_{I}\mu_{t-1} \\
&= B_tu_t + A_t\mu_{t-1} = \overline \mu_t
\end{align*}
$$
</div>

And we can get covariance by second order derivative

<div>
$$
\begin{align*}
\frac {\partial^2 L_t(x_t)}{\partial^2 x_t}
&= (R_t+A_t\Sigma_{t-1}A_t^T)^{-1} =\overline\Sigma_t^{-1}
\end{align*}
$$
<div>

{{< alert info >}}
Summary:<br>
Time update procedure for Kalman Filter
<div>
$$
\begin{align}
\overline \mu_t &= A_t\mu_{t-1} + B_tu_t \\
\overline\Sigma_t &= A_t\Sigma_{t-1}A_t^T + R_t
\end{align}
$$
</div>
{{< /alert >}}

### Measurement Update (Correction)

Noted that:

<div>
$$
\begin{align}
bel(x_t) &= \eta \underbrace{p(z_t|x_t)}_{\sim \mathcal N(z_t; C_tx_t, Q_t)} \text{ } \underbrace{\overline {bel}(x_t)}_{\sim \mathcal N(x_t; \overline \mu_t, \overline \Sigma_t)} \\
&=\eta \text { } exp\{-\frac{1}{2}(z_t-C_tx_t)^TQ_t^{-1}(z_t-C_tx_t)\} \text{ } exp\{-\frac{1}{2}(x_t-\overline \mu_t)^T\overline\Sigma_t^{-1}(x_t-\overline \mu_t)\} \\
&= \eta \text { } exp\{-\frac{1}{2}(z_t-C_tx_t)^TQ_t^{-1}(z_t-C_tx_t)-\frac{1}{2}(x_t-\overline \mu_t)^T\overline\Sigma_t^{-1}(x_t-\overline \mu_t)\} \\
&= \eta \text { } exp\{-J_t\}
\end{align}
$$
</div>

with

<div>
$$
J_t = \frac {1}{2} (z_t-C_tx_t)^TQ_t^{-1}(z_t-C_tx_t) + \frac{1}{2}(x_t-\overline \mu_t)^T\overline\Sigma_t^{-1}(x_t-\overline \mu_t)
$$
</div>

In order to calculate mean and covariance of $bel(x_t)$, we need to take first and second order derivative of $J_t$:

<div>
$$
\begin{align}
\frac{\partial J_t}{\partial x_t} &= -C_t^TQ_t^{-1}(z_t-C_tx_t)+\overline \Sigma_t^{-1}(x_t - \overline \mu_t) \\
\frac{\partial^2 J_t}{\partial^2 x_t} &= C_t^TQ_t^{-1}C_t +\overline \Sigma_t^{-1} = \Sigma_t^{-1}
\end{align}
$$
</div>

To calculate first order derivative:

<div>
$$
\begin{align}
&-C_t^TQ_t^{-1}(z_t-C_t\mu_t) + \overline \Sigma_t^{-1}(\mu_t - \overline \mu_t) = 0 \\
&C_t^TQ_t^{-1}(z_t-C_t\mu_t) = \overline \Sigma_t^{-1}(\mu_t - \overline \mu_t) \\
&C_t^TQ_t^{-1}(z_t-C_t\mu_t) + C_t^TQ_t^{-1}C_t\overline\mu_t - C_t^TQ_t^{-1}C_t\overline\mu_t = \Sigma_t^{-1}(\mu_t - \overline \mu_t) \\
&C_t^TQ_t^{-1}(z_t-C_t\overline\mu_t) - C_t^TQ_t^{-1}C_t(\mu_t - \overline\mu_t) = \Sigma_t^{-1}(\mu_t - \overline \mu_t) \\
&C_t^TQ_t^{-1}(z_t-C_t\overline\mu_t) = \underbrace{(C_t^TQ_t^{-1}C_t + \Sigma_t^{-1})}_{\Sigma_t^{-1}}(\mu_t - \overline \mu_t) \\
&\underbrace{\Sigma_tC_t^TQ_t^{-1}}_{K_t}(z_t-C_t\overline\mu_t) =\mu_t - \overline\mu_t \\
&\mu_t = K_t(z_t-C_t\overline \mu_t) + \overline \mu_t
\end{align}
$$
</div>

The term $K_t$, so called Kalman gain can be represented as

<div>
$$
\begin{align*}
K_t &= \Sigma_t C_t^TQ_t^{-1} \\
&= \Sigma_t C_t^TQ_t^{-1}(C_t^T\overline \Sigma_tC_t + Q_t)(C_t^T\overline\Sigma_t C_t +  Q_t)^{-1} \\
&= \Sigma_t(C_t^TQ_t^{-1}C_t^T\overline\Sigma_tC_t+C_t^T\underbrace{Q_t^{-1}Q_t}_{I})(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1}\\
&= \Sigma_t(C_t^TQ_t^{-1}C_t^T\overline\Sigma_tC_t+C_t^T)(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1} \\
&= \Sigma_t(C_t^TQ_t^{-1}C_t^T\overline\Sigma_tC_t+\overline\Sigma_t^{-1}\overline\Sigma_tC_t^T)(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1} \\
&= \Sigma_t\underbrace{(C_t^TQ_t^{-1}C_t^T+\overline\Sigma_t^{-1})}_{\Sigma_t^{-1}}\overline\Sigma_tC_t^T(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1} \\
&= \overline\Sigma_tC_t^T(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1}
\end{align*}
$$
</div>

from $\frac{\partial^2 J_t}{\partial^2 x_t} = C_t^TQ_t^{-1}C_t +\overline \Sigma_t^{-1} = \Sigma_t^{-1}$ , we can get

<div>
$$
\begin{align*}
\Sigma_t &= (C_t^T Q_t C_t + \overline \Sigma_t^{-1})^{-1}\\
&= \overline \Sigma_t - \overline \Sigma_t  C_t^T(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1}C_t\overline \Sigma_t &\text{(inversion lemma)}\\
&= [I - \underbrace{\overline\Sigma_t C_t^T(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1}}_{K_t}C_t]\overline \Sigma_t \\
&= (I - C_tK_t)\overline \Sigma_t
\end{align*}
$$
</div>

{{< alert info >}}
Summary:<br>
The measurement update (corrrection) of Kalman filter:
<div> 
$$
\begin{align}
K_t &= \overline\Sigma_tC_t^T(C_t^T\overline\Sigma_tC_t +  Q_t)^{-1} \\
\mu_t &=  \overline \mu_t + K_t(z_t-C_t\overline \mu_t) \\
\Sigma_t &=(I - C_tK_t)\overline \Sigma_t
\end{align}
$$
</div>
{{< /alert >}}

## Video Lecture
---

{{< youtube E-6paM_Iwfc >}}

## Reference
---

- [Probabilistic Robotics](https://www.amazon.com/Probabilistic-Robotics-INTELLIGENT-ROBOTICS-AUTONOMOUS/dp/0262201623) , Ch3
- [Kalman and Bayesian Filters in Python](https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python)
    
## Example in Python
---

```python
import numpy as np
import math
import matplotlib.pyplot as plt

velocity = 70
sample_count = 50
t_end = 10
dt = t_end / sample_count
g = 9.81

angle = math.pi / 4.0
time = np.linspace(0, t_end, sample_count)
sensor_deviation = 3.0

x_truth = velocity * math.cos(angle) * time
y_truth = velocity * math.sin(angle) * time - 0.5 * g * time * time

x_sensor = x_truth + np.random.normal(0.0, sensor_deviation, sample_count)
y_sensor = y_truth + np.random.normal(0.0, sensor_deviation, sample_count)

mu_0 = np.array([[0],
                 [0],
                 [0],
                 [0]])
mu_t = mu_0

A = np.array([[1, 0, dt, 0],
              [0, 1, 0, dt],
              [0, 0, 1, 0],
              [0, 0, 0, 1]])

B = np.array([[0],
              [0],
              [0],
              [1]])

u = -g * dt
R = 0.05 * 0.05 * np.eye(4)
Q = sensor_deviation * sensor_deviation * np.eye(2)
sigma_t = 10 * 10 * np.eye(4)
C = np.array([[1, 0, 0, 0],
     [0, 1, 0, 0]])

x_kalman = []
y_kalman = []
vx_kalman = []
vy_kalman = []

for index, t in enumerate(time):
    mu_bar_t = np.matmul(A, mu_t) + u * B
    sigma_bar_t = np.matmul(np.matmul(A, sigma_t), A.transpose()) + R
    ct_sig_ct_trans_plus_q = np.matmul(np.matmul(C, sigma_bar_t), C.transpose()) + Q

    Kt = np.matmul(np.matmul(sigma_bar_t, C.transpose()),
                   np.linalg.inv(ct_sig_ct_trans_plus_q))
    zt = np.array([[x_sensor[index]],
                   [y_sensor[index]]])

    zt_minus_ct_mu_bar_t = zt - np.matmul(C, mu_bar_t)
    mu_t = mu_bar_t + np.matmul(Kt, zt_minus_ct_mu_bar_t)
    sigma_t = np.matmul((np.eye(4) - np.matmul(Kt, C)), sigma_bar_t)

    x_kalman.append(mu_t[0])
    y_kalman.append(mu_t[1])

plt.plot(x_truth, y_truth, 'k--')
plt.plot(x_sensor, y_sensor, 'rx')
plt.plot(x_kalman, y_kalman, 'go')
plt.legend(['ground truth', 'sensor data', 'kalman'])
plt.grid()
plt.show()
```

{{< image classes="fancybox fig-100" src="kf_example.png" thumbnail="kf_example.png"  title="Kalman Filter Example" >}}
{{< hl-text blue >}}
{{< disqus >}}
