# 05 · Progress：进度追踪与复习

每次会话结束更新**两个文件**：
1. **进度文件**（`progress/<concept>.md`）：简要记录，追踪等级
2. **详细对话记录**（Obsidian 笔记）：完整还原苏格拉底对话 + 推导过程

---

## 1. 进度文件格式（简要版）

**位置**：`~/.claude/math-mentor/progress/<concept-slug>.md`

**目的**：快速查看"学到哪、下次从哪开始"

**模板**：

```markdown
# [概念名] 学习进度

concept: <slug>
started: YYYY-MM-DD
last_session: YYYY-MM-DD
current_level: L2
target_level: L4

## 学习历史（摘要）

### Session 1 (YYYY-MM-DD, 45min)
- 起点：L0（完全不知道）
- 讲解：SPD 矩阵定义，为什么欧氏平均有问题
- 达到：L1（知道是什么，能说出"用于 BCI 解码协方差矩阵"）
- 困惑：为什么矩阵的"距离"需要特殊定义？
- 下次：从流形的局部/全局结构开始
- 对话记录：见 Obsidian `SPD流形-Session1-详细对话.md`

### Session 2 (YYYY-MM-DD, 50min)
- 起点：L1
- 讲解：流形直觉（地球表面类比），测地线 vs 欧氏直线
- 练习：
  - [✓] 画出球面上两点的测地线
  - [✗] 计算 2×2 SPD 矩阵的 log 映射（公式记混了）
- 达到：L2（有直觉，能用地球类比解释）
- 困惑：log 映射的公式从哪来？和矩阵对数函数是一回事吗？
- 下次：推导 log 映射公式，手算一个 2×2 例子
- 对话记录：见 Obsidian `SPD流形-Session2-详细对话.md`

### Session 3 (YYYY-MM-DD, 1h)
- 模式：study-group 讨论"为什么要用 log 映射"
- Bob 的代码例子最有帮助
- 达到：L2→L3（能写出公式 log_A(B) = A^{1/2} log(A^{-1/2} B A^{-1/2}) A^{1/2}）
- 下次：手推一个数值例子验证公式
- 对话记录：见 Obsidian `SPD流形-Session3-学习小组讨论.md`

## 检查题记录

| 日期 | 题目 | 结果 | 等级判定 |
|---|---|---|---|
| 2026-10-24 | 说出 SPD 矩阵的两个等价定义 | ✓ | L1 确认 |
| 2026-10-25 | 用自己的话解释为什么不能用欧氏距离 | ✓ | L2 确认 |
| 2026-10-25 | 手算 diag(4,1) 到 diag(1,4) 的 log 映射 | ✗ | 重做 |
| 2026-10-26 | 重做：手算 log 映射 | ✓ | L3 确认 |

## 资源已用

- [x] Congedo 2017 综述 Section 2-3
- [x] pyRiemann 文档：TangentSpace 类
- [ ] Absil 书 Chapter 6（计划下周读）

## 相关概念

- 前置：正定矩阵 [√ L3]、特征值 [√ L3]、矩阵指数/对数 [L1]
- 后续：Fréchet mean、parallel transport、切空间分类

---

## 3. 写入规则（SKILL.md 硬规则 #7 更新）

每次会话结束，写入**两个文件**：

1. **进度文件**（`~/.claude/math-mentor/progress/<concept>.md`）：
   - 简要摘要（5-10 行）
   - 指向详细对话记录的链接

2. **详细对话记录**（如果 `notes_dir` 已设置）：
   - 完整还原苏格拉底对话（AI 提问 → 用户回答 → AI 引导）
   - 数学推导的每一步（不省略中间步骤）
   - 用户的插话、困惑、"啊哈时刻"
   - 检查题的答案（对/错/跳过）
   - 时间戳（大致的，如 00:00, 10:00, ...）

**命名规范**：
- `<concept-slug>-Session<N>-详细对话.md`
- 学习小组模式：`<concept>-Session<N>-学习小组讨论.md`
- 纯数学推导：`<concept>-Session<N>-数学推导.md`
