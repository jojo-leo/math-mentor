# Riemannian Geometry on SPD Manifolds (对称正定矩阵流形上的黎曼几何)

## Meta
- slug: riemannian-spd-manifold
- aliases: ["SPD manifold", "Symmetric Positive Definite manifold", "协方差矩阵流形"]
- difficulty: 8/10 (博士水平，但 BCI 应用可以降到 6/10)
- typical_level_target: L4 (能用 pyRiemann 实现，不需要推广黎曼几何)

## Prerequisites
**必须**：
- 正定矩阵、特征值 → L3
- 矩阵的指数和对数函数 → L2
- 流形的直觉（局部欧氏空间）→ L1

**建议**（深入理解需要）：
- 微分几何基础（切空间、度量张量）
- 李群基础

## 一句话解释
SPD 矩阵（如协方差矩阵）的集合不是线性空间，是一个弯曲的流形；用黎曼几何定义它们之间的"距离"和"平均"，比欧氏方法更合理。

## 直觉
**核心想法**：
想象地球表面（球面）。两点间的距离不是"直线穿过地球"（欧氏距离），而是"沿球面走"（测地线距离）。SPD 矩阵也一样：欧氏平均 $(A+B)/2$ 会"扭曲"，黎曼平均保持几何结构。

**为什么需要它**：
欧氏运算的三个问题：
1. **膨胀效应**：$\det((A+B)/2)$ 通常大于 $\sqrt{\det(A) \det(B)}$（几何平均），协方差矩阵的"信息量"被夸大
2. **不是仿射不变**：对 $A, B$ 做线性变换 $PAP^T, PBP^T$ 后，欧氏距离会变，但物理意义不应该变（如 BCI 中换电极位置）
3. **不保持正定性的"度"**：虽然 $(A+B)/2$ 还是正定，但特征值分布会失真

**典型应用**：
- **BCI 解码**：EEG 协方差矩阵的跨被试迁移学习（zero-calibration）
- **计算机视觉**：协方差描述子（covariance descriptor）
- **医学成像**：扩散张量成像（DTI），大脑白质纤维束追踪
- **雷达信号处理**：协方差矩阵自适应滤波

## 数学定义
**SPD 流形**：
$\mathcal{P}_n = \{A \in \mathbb{R}^{n \times n} : A = A^T, \, v^T A v > 0 \,\forall v \neq 0\}$

维数：$\dim(\mathcal{P}_n) = n(n+1)/2$（对称矩阵的自由度）

**仿射不变黎曼度量**（Affine-Invariant Riemannian Metric, AIRM）：
在点 $A \in \mathcal{P}_n$ 处，切空间是对称矩阵空间 $\text{Sym}(n)$。两个切向量 $U, V \in T_A \mathcal{P}_n$ 的内积：

$\langle U, V \rangle_A = \text{tr}(A^{-1} U A^{-1} V)$

这个度量是**仿射不变**的（对任意可逆矩阵 $P$，$d(PAP^T, PBP^T) = d(A, B)$），即在 $GL(n)$ 的作用 $A \mapsto PAP^T$ 下不变。$\mathcal{P}_n$ 可看作齐性空间 $GL(n)/O(n)$。

**测地线距离**：
$d_R(A, B) = \|\log(A^{-1/2} B A^{-1/2})\|_F = \sqrt{\sum_{i=1}^n (\log \lambda_i)^2}$

其中 $\lambda_i$ 是 $A^{-1/2} B A^{-1/2}$ 的特征值，$\|\cdot\|_F$ 是 Frobenius 范数。

**对数映射**（Logarithmic map，从 $A$ 到 $B$）：
$\log_A(B) = A^{1/2} \log(A^{-1/2} B A^{-1/2}) A^{1/2}$

这是从 $A$ 点指向 $B$ 的切向量。

**指数映射**（Exponential map，从切空间回到流形）：
$\exp_A(U) = A^{1/2} \exp(A^{-1/2} U A^{-1/2}) A^{1/2}$

**Fréchet 均值**（流形上的"平均"）：
$\bar{A} = \arg\min_{X \in \mathcal{P}_n} \sum_{i=1}^N d_R(X, A_i)^2$

无解析解，用梯度下降求解。

**常见替代度量**：
- **Log-Euclidean metric**：$d_{LE}(A, B) = \|\log A - \log B\|_F$（更快，牺牲仿射不变性）
- **Stein divergence / Bhattacharyya distance**（信息几何）

## 实现
**Python (pyRiemann)**:
```python
from pyriemann.utils.mean import mean_riemann, mean_logeuclid
from pyriemann.utils.distance import distance_riemann
from pyriemann.tangentspace import TangentSpace
import numpy as np

# 两个 SPD 矩阵
A = np.array([[4, 1], [1, 2]])
B = np.array([[2, -0.5], [-0.5, 3]])

# 黎曼距离
d = distance_riemann(A, B)

# 黎曼均值
matrices = np.array([A, B])
mean_r = mean_riemann(matrices)

# Log-Euclidean 均值（更快）
mean_le = mean_logeuclid(matrices)

# 切空间投影（用于分类）
ts = TangentSpace(metric='riemann')
X_train = np.array([随机SPD矩阵...])  # shape (n_trials, n_channels, n_channels)
X_tangent = ts.fit_transform(X_train)  # → (n_trials, n_features)，可以喂给 Logistic Regression
```

**MATLAB**:
```matlab
% Manifold Toolbox (manopt) 或手写
A = [4, 1; 1, 2];
B = [2, -0.5; -0.5, 3];

% 测地线距离
M_inv = sqrtm(A) \ B / sqrtm(A);  % A^{-1/2} B A^{-1/2}
d_R = norm(logm(M_inv), 'fro');

% Fréchet 均值（迭代）
matrices = cat(3, A, B);
mean_R = riemannian_mean(matrices);  % 需自己实现或用第三方库
```

**诊断检查**：
- 验证矩阵是否正定：`np.linalg.eigvals(A) > 0`
- 检查仿射不变性：`distance_riemann(P@A@P.T, P@B@P.T) ≈ distance_riemann(A, B)`

## 常见误解与陷阱
1. **误解**："欧氏平均会得到非正定矩阵"
   - **纠正**：错。凸组合 $(1-\lambda)A + \lambda B$ 仍是正定的（$\lambda \in [0,1]$）。问题是"膨胀"：$\det((A+B)/2) > \sqrt{\det(A)\det(B)}$，失真了。
   - **验证**（手算）：$A=\text{diag}(10,0.1), B=\text{diag}(0.1,10)$。$(A+B)/2 = \text{diag}(5.05, 5.05)$，$\det = 25.5025$；而 $\sqrt{\det(A)\det(B)} = \sqrt{1 \times 1} = 1$。

2. **误解**："黎曼均值一定比欧氏均值好"
   - **纠正**：如果 outliers 存在，两者都不 robust。需要用 **geometric median**（把平方距离和换成距离和）。

3. **误解**："流形维数是 $n^2$"
   - **纠正**：SPD 矩阵是对称的，只有 $n(n+1)/2$ 个自由度。

4. **误解**："log-Euclidean 和 affine-invariant 一样"
   - **纠正**：log-Euclidean 是近似，速度快（$O(n^3)$ 一次 log），但不满足仿射不变性。affine-invariant 满足但慢（两次 $\sqrt{\cdot}$）。

5. **陷阱**：数值稳定性
   - 小特征值接近 0 时，$A^{-1/2}$ 和 $\log$ 都不稳定。加正则化：$A + \epsilon I$。

## 检查题
**L1**：SPD 矩阵流形是什么？为什么协方差矩阵在这个流形上？  
*答*：所有对称正定矩阵的集合。协方差矩阵 $\Sigma = E[(X-\mu)(X-\mu)^T]$ 必定是对称正定的。

**L2**：用地球表面类比解释为什么不能用欧氏距离。  
*答*：地球是弯的，两点间的真实距离是"沿球面走"（测地线），不是"打隧道"（欧氏）。SPD 流形也是弯的（负曲率），欧氏距离会失真。

**L3**：手算 $A=\begin{pmatrix}4&0\\0&1\end{pmatrix}$ 和 $B=\begin{pmatrix}1&0\\0&4\end{pmatrix}$ 的黎曼距离。  
*答案提示*：  
1. 算 $A^{-1/2} B A^{-1/2} = \begin{pmatrix}1/2&0\\0&2\end{pmatrix} \begin{pmatrix}1&0\\0&4\end{pmatrix} \begin{pmatrix}1/2&0\\0&2\end{pmatrix} = \begin{pmatrix}1/4&0\\0&16\end{pmatrix}$  
2. 特征值 $\lambda_1 = 1/4, \lambda_2 = 16$  
3. $d_R = \sqrt{(\log 1/4)^2 + (\log 16)^2} = \sqrt{2} \cdot |\log 4| = \sqrt{2} \log 4 \approx 1.96$

**L4**：用 pyRiemann 跑运动想象 BCI 数据集  
*任务*：下载 BNCI Horizon 2020 的 Dataset 2a（9 个被试，运动想象），用 `TangentSpace + LogisticRegression` 做分类，对比欧氏方法（直接拉平协方差矩阵）。  
*期望输出*：准确率表（黎曼应比欧氏高 5-15%），混淆矩阵。

## 学习资源
**入门**：
- Congedo et al. (2017), "Riemannian geometry for EEG-based brain-computer interfaces; a primer and a review", *Brain-Computer Interfaces*（最佳综述）
- pyRiemann 文档 + Tutorials

**中级**：
- Barachant et al. (2013), "Classification of covariance matrices using a Riemannian-based kernel for BCI applications", *Neurocomputing*
- Pennec et al. (2006), "A Riemannian framework for tensor computing", *IJCV*

**进阶**：
- Absil et al. (2008), *Optimization Algorithms on Matrix Manifolds*, Chapter 6（流形优化圣经）
- Bhatia (2007), *Positive Definite Matrices*（数学理论深入）

## PBL 项目种子
1. **BCI**：实现跨被试 zero-calibration EEG 解码器（用 pyRiemann + BNCI 数据）
2. **计算机视觉**：用协方差描述子做纹理分类（如 VisTex 数据集）
3. **医学成像**：DTI 纤维束追踪中的张量插值（用 log-Euclidean 代替线性插值）
4. **信号处理**：雷达杂波协方差矩阵的 robust 估计（geometric median on SPD manifold）

## 相关概念
**更基础**：正定矩阵、矩阵 log/exp、流形的切空间  
**同级**：Grassmann 流形、Stiefel 流形（正交矩阵流形）  
**更高级**：信息几何（Fisher 度量）、平行移动（parallel transport）、测地线回归
