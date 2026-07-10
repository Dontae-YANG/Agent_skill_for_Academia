---
name: exam-worksheet-generator
description: Generate university exam-style practice worksheets as polished LaTeX-typeset PDFs, following a fixed page/structure convention (course header, lettered Parts, numbered Questions with marks, one Part per page). Use this skill whenever the user asks to create a practice worksheet, practice exam, mock exam, problem set, revision sheet, or "练习卷 / 模拟卷 / 出题 / 练习题 PDF" for any university course or module — even if they only mention a topic and don't explicitly say "worksheet". Also use it when the user uploads a past worksheet and asks for one "in the same format/style".
---

# 考试风格练习卷生成器（Exam-Style Worksheet Generator）

生成大学课程的考试风格练习卷 PDF。所有练习卷共享同一套版式与结构规范（见下），
学科内容则完全由用户输入的参数决定，因此可跨课程、跨专业复用。

## 第 1 步：收集参数

从用户请求中提取以下参数。**加粗项为必需**；缺失时先向用户确认（一次问完，不要分多轮）。
其余参数缺失时直接使用默认值，不必追问。

| 参数 | 说明 | 默认值 |
|---|---|---|
| **课程代码与名称** | 如 `ENGR20005 — Numerical Methods` | 必需，需询问 |
| **模块主题** | 如 `Module 3: Interpolation & Quadratic Spline` | 必需，需询问 |
| **知识点列表** | 本卷要覆盖的具体考点 | 必需；若用户只给了模块名，可根据该模块的典型大纲拟一份考点清单并请用户确认 |
| 部分（Part）数量 | 每个 Part 对应一个知识板块 | 3 个（A、B、C） |
| 每部分题数 | | 1–2 题 |
| 分值范围 | 每题标注 marks | 2–5 marks，总分 15–20 |
| 语言 | 卷面语言 | 英文（题干），与用户上传的样卷一致 |
| 副标题 | | `Practice Worksheet — Exam Style` |
| 是否附答案 | 单独生成一份 solutions PDF | 否，除非用户要求 |

如果用户上传了一份已有的练习卷或考卷作为样例，优先模仿该样例的版式细节（页眉措辞、
编号方式、分值风格），本规范中与之冲突的条目以样例为准。

## 第 2 步：命题规范

> **优先级说明：** 本节内容只是默认样例，不是硬性要求。生成时优先遵循当前对话、
> project instructions 或用户上传样卷中的要求；只有在这些来源没有给出相应规定时，
> 才回退到下面的默认规范。例如用户或 project 要求纯选择题、不同的分值体系、
> 或每页多个 Part，都应直接照做，无需逐条套用本节。

### 结构规范（所有学科通用）

- 试卷分为若干 Part（A、B、C…），每个 Part 有描述性小标题，
  格式：`Part A — <知识板块名>`。
- 题目编号为 `Question A1`、`Question A2`、`Question B1`…，每题在标题行标注分值，
  如 `Question A1 (3 marks)`。
- **每个 Part 独占一页**，页面剩余空白即为作答区。
- Part 之间尽量体现递进关系：前一部分推导/建立的结果，在后一部分的题目中被使用
  （例如 Part A 推导的公式用于 Part B 的建模题）。

### 题型配比（按学科灵活套用）

每卷应混合以下三类**通用题型原型**，典型配比为每卷至少各含一道。
每类原型在不同学科中的表现形式不同，命题时根据课程性质选择对应形态，
下表示例仅供参考，遇到其他学科时按同样逻辑类推：

| 题型原型 | 数学 / 数值 / 物理类 | 算法 / CS 理论类 | 编程 / 系统类 |
|---|---|---|---|
| **1. 理论推导 / 证明 / 分析** —— 从基本原理出发推导、证明或分析性质 | 用 Taylor 级数推导差分格式并给出主导误差项 | 求解递归式、证明算法正确性（归纳法/循环不变量）、推导并证明时间复杂度的紧界 | 分析某段代码的复杂度或并发行为，说明理由 |
| **2. 构造 / 设计但不完全求解** —— 建立模型、方程或方案，明确指令不必解到底（如 "DO NOT solve the system"、"pseudocode only"） | 离散化 BVP 列出三对角方程组但不求解 | 设计算法并写出伪代码、给出 DP 的状态定义与转移方程但不算出全表 | 写出函数签名与关键数据结构设计，不要求完整实现 |
| **3. 具体实例上的手工执行** —— 给出小规模具体输入，手算 / 逐步追踪，展示中间状态 | 用梯形法则、Δ = 1/3 手算积分，与精确值比较并给出绝对误差 | 在给定数组上追踪 quicksort 的每次 partition、画出 Dijkstra 的距离表逐步更新、填 DP 表 | 手动追踪给定输入下的程序输出或内存/指针状态 |

此外：

- 至少一题末尾附简短评述要求，如 "Comment briefly on why …"。
- 实例参数要选得**适合手工完成**，并在命题前自己完整做一遍验证答案：
  - 数值类：步长取 1/2、1/3、1/4 之类，函数取初等函数，精确值应有解析表达（如 ln 2），
    注明精度要求（如 "Give function values to 4 decimal places"）；
  - 算法类：数组 6–8 个元素、图 5–6 个结点、DP 表不超过约 6×6，
    要求写出关键中间状态（如每轮 partition 后的数组、每步更新后的距离表）而非只给最终答案；
  - 避免出成手工无法完成、篇幅失控或标准答案有误的题。

## 第 3 步：排版与 PDF 生成

### 版式规范

- LaTeX 排版（article 类，A4）。数学公式一律用规范 LaTeX 数学环境；
  被引用的公式需编号，编号带 Part 前缀，如 `(A.1)`。
- 每页页眉：左侧为模块名（如 `Module 3: Interpolation & Spline`），
  右侧为课程代码与课程名（用 `fancyhdr`）。
- 首页顶部依次为：模块主标题、副标题、知识点范围一行、
  `Closed book. Show all working inside the boxes provided.`。
- 每页底部居中页码。

### 生成流程

1. 先在工作目录写出完整 `.tex` 源文件。
2. 检查环境中是否有 LaTeX 编译器（`pdflatex` / `tectonic` / `xelatex`）：
   - 有 → 直接编译，检查编译日志无错误后交付 PDF。
   - 没有且无法安装 → 改用当前环境提供的 PDF 生成方式（如环境内的 pdf skill），
     用等效版式重现上述规范（页眉、编号公式、每 Part 一页）。
3. 编译产物只交付最终 PDF（如用户要求，另附 `.tex` 源文件方便日后修改）。
4. 若用户要求答案，另外生成独立的 `<同名>_Solutions.pdf`，
   逐题给出完整推导与数值过程，不与题卷混排。

## 输出检查清单

交付前逐项自查（若某项已被对话/project instructions 覆盖，则按覆盖后的要求检查）：

- [ ] 页眉、副标题、closed-book 声明、页码齐全
- [ ] 每个 Part 独占一页；题号与分值格式正确
- [ ] 三类题型原型（理论分析、构造设计、实例手工执行）各至少一道，形态与学科匹配
- [ ] 所有手算/追踪题已亲自完整做过一遍，标准答案与中间状态正确
- [ ] 公式编号带 Part 前缀且被正文引用
- [ ] 知识点覆盖与用户要求一致，Part 之间有递进关系
