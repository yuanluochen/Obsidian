# Kalman Filter 卡尔曼滤波器讲解

> 参考资料 
> [MATLAB中国 卡尔曼滤波器讲解](https://www.bilibili.com/video/BV1V5411V72J/?spm_id_from=333.337.search-card.all.click&vd_source=fb54463fa9160b349a7111ad9b7afbf8)
> [双倍蛋 卡尔曼滤波器](https://www.bilibili.com/video/BV12P411V7pc/?spm_id_from=333.337.search-card.all.click&vd_source=fb54463fa9160b349a7111ad9b7afbf8)					

---

## 卡尔曼滤波器核心方程

Prediction 预测

$$
\hat{x}_{k}^{-}=A \hat{x}_{k-1}+B u_{k}   
$$
$$
P_{k}^{-}=A P_{k-1} A^{T}+Q
$$
Update 更新

$$
K_{k}=\frac{P_{k}^{-} C^{T}}{C P_{k}^{-} C^{T}+R}
$$
$$
\hat{x}_{k}=\hat{x}_{k}^{-}+K_{k}\left(y_{k}-C \hat{x}_{k}^{-}\right)
$$
$$
P_{k}=\left(I-K_{k} C\right) P_{k}^{-}
$$

$$
\hat{x}
\hat{y}
$$

