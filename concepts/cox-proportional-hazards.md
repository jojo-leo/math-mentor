# Cox Proportional Hazards Model (Cox 比例风险模型)

## Meta
- slug: cox-proportional-hazards
- aliases: ["Cox 回归", "Cox regression", "proportional hazards model"]
- difficulty: 5/10 (硕士水平，社科研究者常用)
- typical_level_target: L4

## Prerequisites
**必须**：
- 生存分析基础概念（生存函数、风险函数、审查 censoring）→ L2
- 最大似然估计 → L2
- OLS/Logit 回归 → L3

**建议**：
- Kaplan-Meier 估计

## 一句话解释
Cox 模型用于分析"某事件发生的时间"如何受协变量影响，核心是假设不同个体的风险比（hazard ratio）不随时间变化。

## 直觉
**核心想法**：
比如研究"失业者多久能再就业"。传统回归只能分析"是否再就业"（0/1），Cox 模型能分析"多快再就业"，同时处理还没再就业的人（censored）。

**为什么需要它**：
- OLS：因变量是连续时间，但忽略了 censoring（研究结束时还没发生事件的人）
- Logit：只能分析是否发生，不能分析何时发生
- Parametric survival models（Weibull/指数）：需要假设 hazard 的具体分布，Cox 是半参数，不需要

**典型应用**：
- 社会学：失业再就业时长、婚姻持续时间、社会运动参与的采纳时间
- 医学：患者生存时间、疾病复发
- 经济学：企业破产风险、技术创新采纳
- 政治学：政权更迭、冲突持续时间

## 数学定义
**风险函数**：  
$h(t|X) = h_0(t) \exp(X\beta)$

其中：
- $h(t|X)$：在协变量 $X$ 条件下 $t$ 时刻的瞬时风险（hazard）
- $h_0(t)$：**基线风险**（baseline hazard），所有 $X=0$ 时的 hazard，是时间的任意函数（不需要指定形式）
- $\beta$：回归系数向量

**风险比**（hazard ratio, HR）：  
$\frac{h(t|X_1)}{h(t|X_2)} = \exp((X_1 - X_2)\beta)$

关键：HR 不依赖 $t$，这就是"比例风险"（proportional hazards）假设。

**Partial Likelihood**：  
由于 $h_0(t)$ 未知，用 partial likelihood 估计 $\beta$（Cox 1972 的核心贡献）：

$L(\beta) = \prod_{i:事件发生} \frac{\exp(X_i \beta)}{\sum_{j \in R_i} \exp(X_j \beta)}$

其中 $R_i$ 是 $t_i$ 时刻的风险集（risk set，还没发生事件的个体）。

**Ties（同时发生）处理**：
- Breslow 近似（默认）：把同时发生的事件当作瞬间序列
- Efron 方法：更准确但计算稍慢
- Exact method：精确但很慢

## 实现
**Stata**:
```stata
* 定义生存数据
stset time_var, failure(event_var) [id(id_var)]

* 基础 Cox 回归
stcox x1 x2 x3, robust

* 检验比例风险假设
estat phtest, detail
stphplot, by(x1)

* 分层 Cox（某变量不满足 PH 假设）
stcox x2 x3, strata(x1)
```

**R**:
```r
library(survival)
fit <- coxph(Surv(time, event) ~ x1 + x2 + x3, data = df)
summary(fit)

# 检验 PH 假设
cox.zph(fit)
```

**Python**:
```python
from lifelines import CoxPHFitter
cph = CoxPHFitter()
cph.fit(df, duration_col='time', event_col='event')
cph.print_summary()

# PH 检验
from lifelines.statistics import proportional_hazard_test
proportional_hazard_test(cph, df, time_transform='rank')
```

**诊断检查**：
1. **Schoenfeld 残差**：检验 PH 假设（残差应与时间无关）
2. **Cox-Snell 残差**：检验模型拟合
3. **Influence diagnostics**：找异常值

## 常见误解与陷阱
1. **误解**："HR=1.5 表示发生事件的概率高 50%"
   - **纠正**：HR 比的是**瞬时风险率**（hazard），不是累积概率。HR=1.5 表示在任一时刻，处理组的 hazard 是对照组的 1.5 倍（如基线 0.01/年 → 0.015/年）。但"到第 10 年时发生事件的概率"不是简单乘 1.5，要通过生存函数 $S(t)=\exp(-\int_0^t h(u)du)$ 换算。

2. **误解**："Censoring 是缺失数据，应该删掉"
   - **纠正**：Censoring 提供信息（"至少存活到 $t$"）。删掉会导致严重偏误。

3. **误解**："时间尺度无所谓"
   - **纠正**：时间原点（time origin）和时间尺度影响结果。如研究"婚姻持续时间"，时间原点是结婚日期；如研究"年龄对离婚的影响"，时间尺度可能是年龄而非婚龄。

4. **误解**："所有变量的 PH 假设都必须满足"
   - **纠正**：只有关心的变量需要满足。不满足的变量可以分层（stratify）或用时变系数。

5. **陷阱**：Left truncation（左截断）
   - 如果样本是"2010 年还活着的人"，那些 2010 年前死亡的被排除了，需要用 `stset entry_time exit_time, ...` 处理。

## 检查题
**L1**：Cox 模型是用来分析什么的？和 Logit 回归有什么区别？  
*答*：分析"事件发生的时间"如何受协变量影响。Logit 只分析是否发生，Cox 分析何时发生且能处理 censoring。

**L2**：用自己的话解释"比例风险假设"，给一个违反它的例子。  
*答*：两个个体的 hazard 比值不随时间变化。例子：吸烟对肺癌风险的影响，在年轻时很小（HR≈1），老年后很大（HR>>1），违反 PH。

**L3**：手推 Partial Likelihood（简化情况）  
*题目*：3 个人，事件时间 $t_1=1, t_2=2, t_3=3$（无 ties），$X=(x_1, x_2, x_3)$，写出 $L(\beta)$。  
*答案提示*：$t_1$ 时刻风险集是 $\{1,2,3\}$，分子是 $\exp(x_1\beta)$，分母是 $\sum_{j=1}^3 \exp(x_j\beta)$...

**L4**：用 CGSS 或 CFPS 跑一个 Cox 回归  
*任务*：构造一个 time-to-event 变量（如"首次失业的年龄"），分析教育、性别对失业风险的影响。检验 PH 假设，画 K-M 曲线。  
*期望输出*：系数表、Schoenfeld 残差图、至少一个分组的 survival curve。

## 学习资源
**入门**：
- StatQuest 视频："Survival Analysis"系列（YouTube，有中文字幕）
- IDRE UCLA："Introduction to Survival Analysis in Stata"（网页教程）

**中级**：
- Cleves et al., *An Introduction to Survival Analysis Using Stata* (2010), Chapter 6
- Therneau & Grambsch, *Modeling Survival Data* (2000), Chapter 3-4

**进阶**：
- Cox (1972), "Regression models and life-tables", *JRSS B*（原论文，partial likelihood 的提出）
- Klein & Moeschberger, *Survival Analysis* (2003)（理论深入）

## PBL 项目种子
1. **社会学**：政治消费行为的采纳时间分析（用 CGSS 回顾性问题构造）
2. **劳动经济学**：农民工首次失业再就业时长的城乡差异（用 CFPS 就业状态变化）
3. **健康**：老年人认知衰退到失能的时间（用 CHARLS 或 CLHLS）
4. **政治学**：选民从冷漠到积极参与的时长（用 ANES 或 CGSS）

## 相关概念
**更基础**：K-M 估计器、log-rank test  
**同级**：Parametric survival models (Weibull, 指数, log-normal)、竞争风险模型 (competing risks)  
**更高级**：时变系数 Cox、frailty models（随机效应生存模型）、多状态模型
