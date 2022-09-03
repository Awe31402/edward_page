---
title: "Recursive Bayes Filter"
slug: "RecursiveBayesFilter"
date: 2022-09-03
categories:
- mathematics
- SLAM
tags:
- Recursive Bayes Filter
- SLAM
keywords:
- Recursive Bayes Filter
autoThumbnailImage: false
thumbnailImagePosition: "top"
thumbnailImage: //d1u9biwaxjngwg.cloudfront.net/chinese-test-post/vintage-140.jpg
coverImage: //d1u9biwaxjngwg.cloudfront.net/chinese-test-post/vintage-140.jpg
metaAlignment: center
---

{{< toc >}}

# Recursive Bayes Filter
---
Estimate the state $x$ of a system given observations $z$ and controls $u$ :
{{< image classes="fancybox fig-50" src="/images/bayes_intro.png" thumbnail="/images/bayes_intro.png"  title="Bayes Filter" >}}
<br>
<br>
<br>
<br>
<br>

<div style="width:1000px" align="left">
$$
\begin{align*}
bel(x_t) &= p(x_t|z_{1:t}, u_{1:t})= \frac{p(z_t|x_t,z_{1:t-1},u_{1:t})p(x_t|z_{1:t-1},u_{1:t})}{p(z_t|z_{1:t-1},u_{1:t})}&\text {(Bayes' rule)}
\\ &=\eta p(z_t|x_t,z_{1:t-1},u_{1:t})p(x_t|z_{1:t-1},u_{1:t})
\\ &=\eta p(z_t|x_t)p(x_t|z_{1:t-1},u_{1:t}) &\text { (Markov assumption)}
\\&=\eta p(z_t|x_t) \int p(x_t|x_{t-1},z_{1:t-1},u_{1:t})p(x_{t-1}|z_{1:t-1}, u_{1:t})dx_{t-1} &\text { (Law of total probablility)}
\\&=\eta p(z_t|x_t) \int p(x_t|x_{t-1},u_{t})p(x_{t-1}|z_{1:t-1}, u_{1:t})dx_{t-1} &\text { (Markov assumption)}
\\&=\eta p(z_t|x_t) \int p(x_t|x_{t-1},u_{t})p(x_{t-1}|z_{1:t-1}, u_{1:t-1})dx_{t-1} &\text { (Independence assumption)}
\end{align*}
$$
</div>

So that Bayes filter can be written as two step process:

1. Prediction step:
<div>    
    $$
    \begin{align}
    \overline {bel(x_t)} &= \int p(x_t|x_{t-1},u_{t})p(x_{t-1}|z_{1:t-1}, u_{1:t-1})dx_{t-1}\\
    &= \int p(x_t|x_{t-1},u_{t})bel(x_{t-1})dx_{t-1}
    \end{align}
    $$
</div>    

2. Correction step:
<div>
$$
\begin{align}
bel(x_t) = \eta p(z_t|x_t)\overline {bel(x_t)}
\end{align}
$$
</div>

{{< alert info >}}
conclusion:<br>
 - Bayes filter is a framework for state estimation
 - The dynamics of the robot and its environment are characterized in the form of two probabilistic laws:
     1. state transition distribution
     2. measurement distribution.
{{< /alert >}}

# Bayes rule
---

Starts from conditional probability:

- Discrete

<div>
$$
\begin{align}
p(x|y) &= \frac{p(x,y)}{p(y)} \\
&= \frac{p(y|x)p(x)}{p(y)} \\
&=\frac{p(y|x)p(x)}{\sum_{x'} p(y|x')p(x')}
\end{align}     
$$
</div>

- Continuous

<div>
$$
\begin{align}
p(x|y) &= \frac{p(x,y)}{p(y)} \\
&= \frac{p(y|x)p(x)}{p(y)} \\
&=\frac{p(y|x)p(x)}{\int p(y|x')p(x')dx'}
\end{align}     
$$
</div>

# Law of Total Probability
---

Discrete case:
<div>
$$
\begin{align}
P(A) &= \sum_n P(A \cap B_n)\\
&= \sum_n P(A|B_n)P(B_n)
\end{align}
$$
</div>
Discrete case:
<div>
$$
\begin{align}
P(A) &= \int_{-\infty}^\infty P(A| X=x)dF_X(x)\\
&= \int_{-\infty}^\infty P(A| X=x)f_X(x)dx
\end{align}
$$
</div>

# Motion Model and Observation Model
---
The term $p(x_t|x_{t-1},u_{t})$ is motion model
The term $p(z_t|x_{t})$ is observation or sensor model

# Assignment
---
Consider a robot that navigates in a **discrete 1D grid world** composed by 15 cells as the one illustrated below.

{{< image classes="fancybox fig-100" src="/images/bayes_world_map.png" thumbnail="/images/bayes_world_map.png"  title="World Map" >}}

The floor can be either black **(0)** or white **(1)**


In this world, the robot can move forward (F) or backward (B).

Given a command, it moves according to the following motion model:

- with probability **0.7**, it moves in the **correct direction** (i.e. F→F,B→B );
- with probability **0.2** or if the command cannot be exectuted (e.g. end of the world!), it **does not move**;
- with probability **0.1**, it moves in the **opposite direction** (i.e.F→B,B→F ).
- The initial position of the robot is known and is at cell  **7**

The sensor is able to recognize that:

- a tile is **white** with probability **0.7**
- a tile is **black** with probability **0.9**

```python
from ex2_1 import *
import numpy as np

belief = np.zeros(15)
x_start = 7
belief[x_start] = 1
belief_init = np.copy(belief)

plot_belief(belief)

observation = [0 , 1, 0, 0, 0, 0, 1, 0, 0, 0]
world = [0, 0, 1, 1, 0, 1, 0, 0, 1, 0, 0, 0, 0, 1, 0]
actions = ['F', 'F', 'F', 'F', 'B', 'B', 'F', 'F', 'B']

for i, action in enumerate(actions):
    belief = motion_model(action, belief)
    plot_belief(belief)

belief = belief_init
belief = recursive_bayes_filter(actions, observation,
                                belief, world)
plot_belief(belief)
```

<iframe width="560" height="315" src="https://www.youtube.com/embed/S_KLcy-Ee7g" title="Odometry Only" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://www.youtube.com/embed/eREzILR41xI" title="Localization with Bayes Filter" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
import numpy as np
import matplotlib.pyplot as plt

def plot_belief(belief):
    plt.figure()
    ax = plt.subplot(2,1,1)
    ax.matshow(belief.reshape(1, belief.shape[0]))
    ax.set_xticks(np.arange(0, belief.shape[0],1))
    ax.xaxis.set_ticks_position("bottom")
    ax.set_yticks([])
    ax.title.set_text("Grid")
    
    ax = plt.subplot(2, 1, 2)
    ax.bar(np.arange(0, belief.shape[0]), belief)
    ax.set_xticks(np.arange(0, belief.shape[0], 1))
    ax.set_ylim([0, 1.05])
    ax.title.set_text("Histogram")
    

def motion_model(action, belief):
    p_ff = p_bb = 0.7
    p_fb = p_bf = 0.1
    p_remain = 0.2
    ret = np.zeros(len(belief))

    if action == 'F':
        for i in range(len(belief)):
            if i == 0:
                ret[i] = 0.2 * belief[i] + 0.1 * belief[i+1]
            elif i == (len(belief) - 1):
                ret[i] = 0.2 * belief[i] + 0.7 * belief[i-1]
            else:
                ret[i] = 0.2 * belief[i] + 0.7 * belief[i-1]
                         + 0.1 * belief[i+1]
    else:
        for i in range(len(belief)):
            if i == 0:
                ret[i] = 0.2 * belief[i] + 0.7 * belief[i+1]
            elif i == (len(belief) - 1):
                ret[i] = 0.2 * belief[i] + 0.1 * belief[i-1]
            else:
                ret[i] = 0.2 * belief[i] + 0.7 * belief[i+1]
                         + 0.1 * belief[i-1]
    return ret
               
    
def sensor_model(observation, belief, world):
    white_correct = 0.7
    black_correct = 0.9

    ret = np.copy(belief)

    # white observed
    if observation == 1:
        for i, val in enumerate(world):
            if val == 0:
                ret[i] = (1 - white_correct) * belief[i]
            else:
                ret[i] =  white_correct * belief[i]
    # black observed
    else:
        for i, val in enumerate(world):
            if val == 0:
                ret[i] = black_correct * belief[i]
            else:
                ret[i] = (1 - black_correct) * belief[i]

    return ret / np.sum(ret)

    
def recursive_bayes_filter(actions, observations,
                           belief, world):
    bel = sensor_model(observations[0], belief, world)
    for i, action in enumerate(actions):
        bel_bar = motion_model(action, bel)
        bel = sensor_model(observations[i], bel_bar, world)
    return bel
```

# Video Lecture
---
<iframe width="560" height="315" src="https://www.youtube.com/embed/0lKHFJpaZvE" title="Recursive Bayes Filter" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# Reference
---
- [Probabilistic Robotics](https://www.amazon.com/Probabilistic-Robotics-INTELLIGENT-ROBOTICS-AUTONOMOUS/dp/0262201623) , Ch2
