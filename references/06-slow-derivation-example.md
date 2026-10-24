# 06 · 慢节奏推导示例

针对 `math_background ≤ 1`（学过但忘了）的用户，演示如何一步步推导，不跳步。

---

## 示例 1：用户数量对数据量的导数

### 背景（先给）

**问题**：平台收集数据量 $d$，用户数量 $n(d)$ 如何变化？我们要算 $\frac{dn}{dd}$。

**已知**：
- 消费者类型 $\theta \in [0, 1]$，分布为 $F(\theta)$（累积分布函数）
- 边际消费者 $\theta^*(d)$ 满足 $u(q(d, \theta^*)) = c(d)$（效用正好为 0）
- 用户数量 $n(d) = 1 - F(\theta^*(d))$（类型 > $\theta^*$ 的人会用平台）

**目标**：求 $\frac{dn}{dd}$

---

### 第 1 步：写出要求导的函数

$$n(d) = 1 - F(\theta^*(d))$$

**观察**：这是一个**复合函数**。
- 外层：$F(\cdot)$
- 内层：$\theta^*(d)$（$\theta^*$ 本身是 $d$ 的函数）

**工具**：链式法则（chain rule）

---

### 第 2 步：回顾链式法则

**如果** $y = f(g(x))$（$f$ 套 $g$），**那么**：

$$\frac{dy}{dx} = f'(g(x)) \cdot g'(x)$$

用语言说：
- 先对外层函数 $f$ 求导（把内层 $g(x)$ 当作一个整体）
- 再乘以内层函数 $g$ 对 $x$ 的导数

**例子**（复习）：
- $y = (x^2 + 1)^3$
- 外层：$f(u) = u^3$，$f'(u) = 3u^2$
- 内层：$g(x) = x^2 + 1$，$g'(x) = 2x$
- 链式法则：$\frac{dy}{dx} = 3(x^2+1)^2 \cdot 2x$

---

### 第 3 步：应用链式法则到 $n(d)$

$$n(d) = 1 - F(\theta^*(d))$$

拆成两部分：

**Part A**：常数 1 的导数 = 0

**Part B**：$F(\theta^*(d))$ 的导数

- 外层：$F(\cdot)$，导数是 $F'(\cdot) = f(\cdot)$（密度函数）
- 内层：$\theta^*(d)$，导数是 $\frac{d\theta^*}{dd}$（我们还不知道，待定）

链式法则：

$$\frac{d}{dd}[F(\theta^*(d))] = F'(\theta^*) \cdot \frac{d\theta^*}{dd} = f(\theta^*) \cdot \frac{d\theta^*}{dd}$$

**合并**：

$$\frac{dn}{dd} = 0 - f(\theta^*) \cdot \frac{d\theta^*}{dd} = -f(\theta^*) \cdot \frac{d\theta^*}{dd}$$

---

### 第 4 步：检验理解（小问题）

**问**：为什么有负号？

**提示**：$n(d) = 1 - F(\theta^*)$，如果 $\theta^*$ 增加，$F(\theta^*)$ 会增加还是减少？$n$ 呢？

**答案**：
- $\theta^*$ 增加 → 更多人被"卡"在临界线下 → 用户数 $n$ 减少
- 所以 $\frac{dn}{dd}$ 和 $\frac{d\theta^*}{dd}$ 符号相反 → 负号

---

### 第 5 步：现在的问题

我们得到了：

$$\frac{dn}{dd} = -f(\theta^*) \cdot \frac{d\theta^*}{dd}$$

**还缺什么**？$\frac{d\theta^*}{dd}$（临界消费者如何随数据量变化）

这需要**隐函数微分**（implicit differentiation）。

---

## 示例 2：隐函数微分求 $\frac{d\theta^*}{dd}$

### 第 1 步：回顾什么是隐函数

**显函数**（explicit function）：$y$ 可以直接表示为 $x$ 的函数
- 例子：$y = x^2 + 3x$，$y$ 显式地等于某个 $x$ 的表达式

**隐函数**（implicit function）：$y$ 和 $x$ 通过一个方程联系，但 $y$ 没有显式表达式
- 例子：$x^2 + y^2 = 1$（单位圆），$y$ 不能唯一地表示为 $x$ 的函数（每个 $x$ 对应两个 $y$）

**在我们的问题中**：
- $\theta^*$ 通过方程 $u(q(d, \theta^*)) = c(d)$ 与 $d$ 联系
- 我们不能解出 $\theta^* = $ 某个 $d$ 的显式公式（因为 $u$, $q$ 可能很复杂）
- 所以 $\theta^*(d)$ 是隐函数

---

### 第 2 步：隐函数微分的思路

**核心思想**：虽然不能解出显式公式，但可以**两边对 $d$ 求导**。

**回顾简单例子**：$x^2 + y^2 = 1$

两边对 $x$ 求导（把 $y$ 当作 $x$ 的函数）：

$$\frac{d}{dx}[x^2] + \frac{d}{dx}[y^2] = \frac{d}{dx}[1]$$

$$2x + 2y \cdot \frac{dy}{dx} = 0$$

（注意：$y^2$ 的导数用了链式法则）

解出：

$$\frac{dy}{dx} = -\frac{x}{y}$$

虽然我们没有 $y = f(x)$ 的显式表达式，但我们得到了导数！

---

### 第 3 步：应用到 $u(q(d, \theta^*)) = c(d)$

**目标**：求 $\frac{d\theta^*}{dd}$

**方程**：

$$u(q(d, \theta^*)) = c(d)$$

**两边对 $d$ 求导**：

$$\frac{d}{dd}[u(q(d, \theta^*))] = \frac{d}{dd}[c(d)]$$

---

### 第 4 步：左边的导数（分步）

$$\frac{d}{dd}[u(q(d, \theta^*))]$$

**这是多层复合函数**：
- $u$ 是 $q$ 的函数
- $q$ 是 $d$ 和 $\theta^*$ 的函数
- $\theta^*$ 又是 $d$ 的函数

**用链式法则**：

$$\frac{d}{dd}[u(q)] = u'(q) \cdot \frac{dq}{dd}$$

（外层：$u$ 对 $q$ 的导数；内层：$q$ 对 $d$ 的导数）

**现在算 $\frac{dq}{dd}$**：

$q = q(d, \theta^*(d))$ 是两个变量的函数，但都依赖 $d$。

**用全导数公式**（total derivative）：

$$\frac{dq}{dd} = \frac{\partial q}{\partial d} + \frac{\partial q}{\partial \theta} \cdot \frac{d\theta^*}{dd}$$

解释：
- $\frac{\partial q}{\partial d}$：$d$ **直接**影响 $q$（固定 $\theta^*$）
- $\frac{\partial q}{\partial \theta} \cdot \frac{d\theta^*}{dd}$：$d$ **间接**影响 $q$（通过改变 $\theta^*$）

**代回**：

$$\frac{d}{dd}[u(q)] = u'(q) \left[ \frac{\partial q}{\partial d} + \frac{\partial q}{\partial \theta} \cdot \frac{d\theta^*}{dd} \right]$$

---

### 第 5 步：右边的导数（简单）

$$\frac{d}{dd}[c(d)] = c'(d)$$

---

### 第 6 步：合并，解出 $\frac{d\theta^*}{dd}$

**方程**：

$$u'(q) \left[ \frac{\partial q}{\partial d} + \frac{\partial q}{\partial \theta} \cdot \frac{d\theta^*}{dd} \right] = c'(d)$$

**展开**：

$$u'(q) \frac{\partial q}{\partial d} + u'(q) \frac{\partial q}{\partial \theta} \cdot \frac{d\theta^*}{dd} = c'(d)$$

**移项**（把含 $\frac{d\theta^*}{dd}$ 的项移到一边）：

$$u'(q) \frac{\partial q}{\partial \theta} \cdot \frac{d\theta^*}{dd} = c'(d) - u'(q) \frac{\partial q}{\partial d}$$

**解出**：

$$\frac{d\theta^*}{dd} = \frac{c'(d) - u'(q) \frac{\partial q}{\partial d}}{u'(q) \frac{\partial q}{\partial \theta}}$$

**完成！**

---

### 第 7 步：理解这个公式

**分子**：$c'(d) - u'(q) \frac{\partial q}{\partial d}$

- $c'(d)$：数据增加 → 隐私成本增加 → 推高 $\theta^*$（更多人不愿用平台）
- $u'(q) \frac{\partial q}{\partial d}$：数据增加 → 匹配质量提高 → 效用提高 → 拉低 $\theta^*$（更多人愿用平台）

两者相减 = **净效应**

**分母**：$u'(q) \frac{\partial q}{\partial \theta}$

- 如果类型 $\theta$ 提高，匹配质量 $q$ 如何变化
- 通常 $\frac{\partial q}{\partial \theta} > 0$（高类型更容易被匹配）

**符号**：
- 如果隐私成本增加得快（$c'$ 大），分子为正 → $\frac{d\theta^*}{dd} > 0$ → $\theta^*$ 随 $d$ 上升 → 用户数减少
- 如果匹配改进得快（$u' \frac{\partial q}{\partial d}$ 大），分子为负 → $\frac{d\theta^*}{dd} < 0$ → $\theta^*$ 随 $d$ 下降 → 用户数增加

---

### 第 8 步：数值验证（确保理解）

**假设**（简化）：
- $u(q) = 2\sqrt{q}$ → $u'(q) = \frac{1}{\sqrt{q}}$
- $q(d, \theta) = \theta \cdot \frac{d}{1+d}$ → $\frac{\partial q}{\partial d} = \frac{\theta}{(1+d)^2}$，$\frac{\partial q}{\partial \theta} = \frac{d}{1+d}$
- $c(d) = d^2$ → $c'(d) = 2d$

**在 $d = 0.5$，$\theta^* = 0.6$ 处**：

计算 $q(0.5, 0.6) = 0.6 \times \frac{0.5}{1.5} = 0.2$

$$\frac{d\theta^*}{dd} = \frac{2 \times 0.5 - \frac{1}{\sqrt{0.2}} \times \frac{0.6}{1.5^2}}{\frac{1}{\sqrt{0.2}} \times \frac{0.5}{1.5}}$$

$$= \frac{1 - 2.236 \times 0.267}{2.236 \times 0.333} = \frac{1 - 0.597}{0.745} = \frac{0.403}{0.745} \approx 0.54$$

**含义**：$d$ 增加 1 单位，$\theta^*$ 增加约 0.54（临界线上移，用户数减少）

---

## 总结：慢节奏推导的六要素

1. **先说"为什么"**（动机）
2. **回顾工具**（如链式法则、全导数）
3. **每一步只做一件事**（不合并）
4. **符号先解释**（$\frac{\partial}{\partial}$ vs $\frac{d}{d}$）
5. **每步出小问题**（检验理解）
6. **数值验证**（用具体数字算一遍）

对比"跳步版"，慢节奏版篇幅长 3-5 倍，换来的是基础薄弱的用户能跟上每一步。
