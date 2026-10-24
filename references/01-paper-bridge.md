# 01 · Paper-to-Math Bridge：从论文到概念依赖树

用户贴了一段看不懂的论文/算法，你要做三件事：**识别概念 → 画依赖树 → 给最小学习路径。**

---

## 1. 识别概念（5 分钟完成）

用 grep/关键词扫描：
- 数学实体：出现的分布名（Weibull / Poisson / ...）、变换名（Fourier / Laplace / log / ...）、矩阵性质（symmetric positive definite / Toeplitz / ...）、优化目标（convex / L1-norm / ...）
- 方法名：Cox proportional hazards / Riemannian geometry / kernel density estimation / ...
- 软件包名：如果论文提到 `pyRiemann.tangentspace.TangentSpace` 或 Stata 的 `stcox`，这是可查文档的锚点。

列出 5–10 个核心概念（按重要性），对每个标注：
- 是否用户已知（查档案 `research.methods` 和 `math_background`）
- 前置概念是什么（如"正定矩阵" → "特征值" → "线代基础"）

---

## 2. 画依赖树（只画不会的部分）

从用户已知点开始往上长，而不是从基础往下教。

**例子**：论文说"We compute the Fréchet mean on the SPD manifold..."

用户档案显示：
- 线代 2（能看懂推导），计算过特征值
- 没学过流形
- 没学过黎曼几何

依赖树（精简）：

```
[Target] Fréchet mean on SPD manifold
  ├─ [L3] SPD 矩阵的黎曼度量
  │   ├─ [L2] 流形的度量张量（只需 SPD 的例子，不讲一般定义）
  │   └─ [L1] SPD 矩阵是什么、为什么不能用欧氏距离
  │       └─ [√ 已会] 正定矩阵定义、特征值
  └─ [L3] 流形上的均值（Fréchet mean 定义）
      └─ [L2] 测地线距离

√ = 用户已知；L1-L5 = 学完后应达到的等级
```

**规则**：
- 不画用户已经会的节点。
- 前置概念链只推到用户已知点为止（不要一路推回高中）。
- 标出哪些是"可以暂时不深究"的（如测地线的曲率张量表达式，BCI 解码用不到）。

---

## 3. 给最小学习路径（周为单位）

按 80/20 原则：20% 的数学能解决 80% 的看论文需求。

**输出模板**：

```markdown
📊 检测到 X 个核心概念，其中 Y 个需要学习。

🎯 最小学习路径（Z 周，每周 N 小时）

Week 1-2：[概念名]
  目标：L0 → L2
  资源：
    - 教材/博客：[链接或书名+章节]
    - 视频：[如 3Blue1Brown 的某集]
    - 代码：[某个 GitHub repo 的某文件]
  练习：
    - [L1 检查] 说出它是干什么用的
    - [L2 检查] 用自己的话+一个例子解释

Week 3-4：[概念名]
  前置：Week 1-2 达到 L2
  目标：L2 → L4
  ...

Week 5-6：合成（回到原论文）
  - 用 pyRiemann / Stata / R 跑通论文算法
  - 解释每一步在做什么

✅ 里程碑检查点：
  □ Week 2 结束能手算 SPD 矩阵的 log 映射（2×2 情况）
  □ Week 4 结束能解释为什么 BCI 要用黎曼度量
  □ Week 6 结束能复现论文的 Figure 3
```

时间估算原则：
- 一个全新 L3 概念：2-3 周（用户基础好可压缩到 1 周）
- L4（能实现）：加 1-2 周
- 如果用户每周只有 3-5 小时，时间翻倍

---

## 4. 给完路径后问

"现在想：
1. 从 Week 1 开始学（进入 **learn** 模式）
2. 先看学习小组怎么讨论这个概念（进入 **group** 模式）
3. 调整路径（哪些想跳过/深入）
4. 换一个更简单的切入点"

记得把路径写到 `~/.claude/math-mentor/plans/<concept-slug>.md`，标题是论文来源或用户的问题。
