# DDPM 概率去噪扩散模型

[参考链接](https://github.com/wangjia184/diffusion_model)

扩散模型：通过向图像中加入高斯噪声来模拟扩散现象，并通过逆向过程从(随机)噪声中生成图像

## 正态分布

Normal Distribution (高斯分布): 如果某随机变量受很多种因素的影响，但是在这些因素中没有任何一个能够起决定性作用，那么改随机变量的概率密度曲线形状一般接近钟形。

正态分布的概率密度函数(高斯函数):
$$
f(x) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x-\mu)^2}{2\sigma^2}}
$$

正态分布一般记为: $X \sim N(\mu, \sigma^2)$, 其中: $\mu = \frac{\sum_{i=1}^{N}x_i}{N}, \sigma^2 = \frac{\sum_{i=0}^N(x_i-\mu)^2}{N}$

标准正态分布: $N(0, 1)$

正态分布的性质:

- $cN(\mu, \sigma^2) = N(c\mu, (c\sigma)^2)$
- $N(\mu_1, \sigma_1^2) + N(\mu_2, \sigma_2^2) = N(\mu_1+\mu_2, (\sigma_1+\sigma_2)^2)$

## 贝叶斯公式

$$
P(A|B) = \frac{P(B|A)P(A)}{P(B)}
$$

其中，$P(A|B)$ 表示后验概率，$P(A)$ 表示先验概率，$P(B)$ 表示证据，$P(B|A)$ 表示似然(B 对 A 的证据强度)

整个公式表明: 在已知事件 B 发生的条件下，修正事件 A 发生的概率

## 前向加噪过程

### 原图像归一化

将原始图像的像素值归一化到 $[-1, 1]$ 区间，即对每一个像素值$x$:
$$
x = \frac{2\times x}{255}-1
$$

### 加一次噪声

随机生成一张噪声图像$\epsilon$，这个噪声图像和原图像具有相同尺寸并且所有的像素值服从标准正态分布，假设原图像$x_0$, 那么加一次噪声之后的图像$x_1$
$$
x_1 = \sqrt{\beta_1}\epsilon_1 + \sqrt{1-\beta_1}x_0
$$

其中 $\beta, (0 < \beta < 1)$ 是一个系数，它决定原图像和噪声图像的比例，并且前向加噪过程中每一次加噪声的 $\beta$ 是递增的，即 $0 < \beta_1 < \beta_2 < \dots < \beta_t < \dots < \beta_T < 1$, 其中 $T$ 是总加噪声的次数，$T$ 是一个超参数

令 $\alpha = 1 - \beta$, 则
$$
x_1 = \sqrt{1 - \alpha_1}\epsilon_1 + \sqrt{\alpha_1}x_0
$$

### 加 t 次噪声 --重参数化技巧

假设现在有第 $t-1$ 次加噪声的结果 $x_{t-1}$, 那么在 $x_{t-1}$ 的基础上再加一次噪声的结果是 $x_t$
$$
x_t = \sqrt{1 - \alpha_t}\epsilon_t + \sqrt{\alpha_t}x_{t-1}
$$

那么，是否可以由 $x_{t-2}$ 得到 $x_t$ 呢？答案是肯定的
$$
x_t = \sqrt{1 - \alpha_t}\epsilon_t + \sqrt{\alpha_t}x_{t-1} \\
    = \sqrt{1 - \alpha_t}\epsilon_t + \sqrt{\alpha_t}(\sqrt{1 - \alpha_{t-1}}\epsilon_{t-1} + \sqrt{\alpha_{t-1}}x_{t-2}) \\
    = \sqrt{\alpha_t - \alpha_t \alpha_{t-1}}\epsilon_{t-1} + \sqrt{1 - \alpha_t}\epsilon_t + \sqrt{\alpha_t \alpha_{t-1}}x_{t-2}
$$

重参数化技巧(由正态分布的性质推导得): $\sqrt{\alpha_t - \alpha_t \alpha_{t-1}}\epsilon_{t-1} + \sqrt{1 - \alpha_t}\epsilon_t = \sqrt{1-\alpha_t\alpha_{t-1}}\epsilon$ 注意这里的 $\epsilon$ 是一个新的噪声，既不是 $\epsilon_{t-1}$, 也不是 $\epsilon_t$

那么，原式转换成:
$$
x_t = \sqrt{1-\alpha_t\alpha_{t-1}}\epsilon + \sqrt{\alpha_t \alpha_{t-1}}x_{t-2}
$$

由数学归纳法可得: 只需要原图像确定，那么任意一个加噪步的结果 $x_t$ :
$$
x_t = \sqrt{1-\alpha_t\alpha_{t-1}\dots\alpha_1}\epsilon + \sqrt{\alpha_t\alpha_{t-1}\dots\alpha_1}x_0
$$

## 逆向去噪过程

逆向去噪过程: 假设现在有第 $t$ 次加噪声的结果 $x_{t}$, 如何使用 $x_t$ 估计 $x_{t-1}$ 的概率分布？

由贝叶斯公式得：
$$
P(x_{t-1}|x_t, x_0) = \frac{P(x_t|x_{t-1}, x_0) P(x_{t-1}|x_0)}{P(x_t|x_0)}
$$
其中 $x_0$ 这个条件可以省略

$$
P(x_t|x_{t-1}) \sim N(\sqrt{\alpha_t}x_{t-1}, 1-\alpha_t) \\
P(x_{t-1}|x_0) \sim N(\sqrt{\bar{\alpha_t}}x_0, 1 - \bar{\alpha_t}) \\
P(x_t|x_0) \sim N(\sqrt{\bar{\alpha_{t-1}}}x_0, 1 - \bar{\alpha_{t-1}})
$$
代入公式得:
$$
P(x_{t-1}|x_{t}) \sim N\left(  {\color{Purple} \frac{\sqrt{a_t}(1-\bar{a}_{t-1})}{1-\bar{a}_t}x_t
	+ \frac{\sqrt{\bar{a}_{t-1}}(1-a_t)}{1-\bar{a}_t}x_0},
    \left( {\color{Red} \frac{ \sqrt{1-a_t} \sqrt{1-\bar{a}_{t-1}} } {\sqrt{1-\bar{a}_{t}}}}  \right)^2 \right)
$$
由于 $x_{t} = \sqrt{\bar{a}_t}\times x_0+ \sqrt{1-\bar{a}_t}\times ϵ$, 那么: $x_0 = \frac{x_t - \sqrt{1-\bar{a}_t}\times ϵ}{\sqrt{\bar{a}_t}}$, 带入得:
$$
P(x_{t-1}|x_{t}) \sim N\left( {\color{Purple} \frac{\sqrt{a_t}(1-\bar{a}_{t-1})}{1-\bar{a}_t}x_t
    + \frac{\sqrt{\bar{a}_{t-1}}(1-a_t)}{1-\bar{a}_t}\times \frac{x_t - \sqrt{1-\bar{a}_t}\times ϵ}{\sqrt{\bar{a}_t}} } ,
    {\color{Red} \frac{ \beta_{t} (1-\bar{a}_{t-1}) } { 1-\bar{a}_{t}}} \right)
$$

## 神经网络

初始时刻 $x_T$ 近似标准正态分布，因此可以使用从标准正态分布中采样的图像作为初始输入

神经网络接收一张图像和一个时间步作为输入，预测噪声 $\epsilon$ ，通过 $\epsilon$ 计算前一个时间步的概率分布，然后从概率分布中采样得到前一个时间步的图像

重复上述过程知道得到原始图像