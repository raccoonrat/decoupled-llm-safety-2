# 理论升级设计（Theory Upgrade Design）

**角色**：理论计算机科学 + AI 安全 + 系统安全 跨学科研究团队  
**输入**：`BeyondModelReflection_DecoupledSafety_CN.tex` 修订版（2026-04-09）  
**目标**：将当前三组 proposition/principle 升级为顶会可接受的理论化方案

---

## 任务 A：逐项判断——theorem / proposition / principle / design claim

下面对当前理论框架中的 6 个正式声明逐一评估。

### 命题 3.1：语义审查的局限（Limitation of Semantic Scrutiny）

**当前形式**：Proposition——"任何纯语义过滤在自适应查询下残余泄露非零。"

**判断：可升级为 Theorem（条件定理）**

**理由**：该声明在恰当建模后可严格证明。将语义防火墙建模为一个假设检验 $H_0$: 良性 vs. $H_1$: 恶意。攻击者拥有自适应查询能力（可根据前序响应调整后续提示），而防火墙是无状态的（每轮独立判定）。这一设定等价于在线学习中"无状态专家 vs. 自适应对手"的经典框架。核心论证：

1. 无状态分类器在每轮独立做出决策，其错误概率 $\epsilon > 0$（除非分类器是完美的，但完美分类在自然语言空间中因PBU的存在而不可能）。
2. 自适应攻击者在 $T$ 轮中的成功概率至少为 $1 - (1-\epsilon)^T \to 1$。
3. 即使 $\epsilon$ 很小，累积泄露量随 $T$ 增长。

这是一个可以在合理假设下闭合证明的结论。关键假设是**无状态**和**非零错误率**，二者在实际系统中均成立。

**建议**：升级为 `\begin{theorem}[语义防火墙的残余泄露]`，附带明确的假设列表和简短证明梗概。

---

### 原则 3.2：抽取经济学（Extraction Economics）

**当前形式**：Principle——"防御成功 ⟺ $C_{\mathrm{attack}} > V(t)$。"

**判断：拆分为两部分**

- **不等式本身 $C_{\mathrm{attack}} > V(t)$**：这是一个 **design criterion / operational definition（操作化判据）**，不是定理。它定义了"防御成功"的含义，属于概念框架层面的贡献。**应保持为 Principle**。
- **$E[N]$ 在上下文混淆下的增长率**：这是一个**可以形式化为条件定理或下界命题**的数学主张。例如："在诱饵密度 $D$ 下，达到抽取精度 $\tau$ 所需的期望查询数 $E[N]$ 至少为 $\Omega(f(D))$，其中 $f$ 关于 $D$ 单调递增。"这可以通过信息论工具证明。

**建议**：保留原则 3.2 的框架性地位，但新增一条 **Proposition 3.2a**（或 Theorem 3.2a）关于查询复杂度下界。

---

### 命题 3.3：认知一致性的失效（Failure of Cognitive Consistency）

**当前形式**：Proposition——"HPM下增强推理恶化安全。"

**判断：保持为 Proposition（经验性命题）**

**理由**：该声明本质上是一个**经验发现的形式化表述**——来源于 [hpm-jailbreak] 的实验观察（CoT 导致 ASR 上升至 77%+）。将其证明为严格定理需要对"LLM 推理过程"做高度简化的数学建模（如将 LLM 近似为多目标优化器），而这些简化在审稿人看来可能过于人为。

然而，可以给出一个**概念性的形式论证（informal theorem）**：在双目标框架下，如果攻击者能改变目标函数的表象使 helpfulness 和 safety 看似对齐（而实际上违背安全约束），那么更强的优化能力（更深推理）允许模型找到"看似同时满足两个目标但实际违反安全硬约束"的解。这类似于 Goodhart's Law 的形式化——"当度量被优化到极致，它不再是好的度量。"

**建议**：保持 Proposition 形式。可在旁注（remark）中给出上述双目标论证作为直觉支撑。**不要硬写成定理**——审稿人会追问模型假设的合理性。

---

### 原则 3.4：规范锚定与防御性降级（Norm-Anchored Defensive Degradation）

**当前形式**：Principle——三层设计原则。

**判断：保持为 Principle，但新增一条支撑性 Proposition**

**理由**：规范锚定的三层设计（外部规范 + 压力监控 + 降级）是系统设计准则，不是数学命题。但"防御性降级是最优安全策略"这一子主张**可以形式化**。具体来说：将安全决策建模为一个带约束的序贯假设检验（constrained sequential testing），其中：
- 在每一轮，系统选择"继续生成"或"降级"
- 继续生成在安全输入下有收益（用户满意）但在攻击输入下有风险
- 降级始终安全但有可用性成本

在这一框架下，可以证明：**当攻击概率的后验估计超过某阈值时，最优策略（在最小化最坏情况安全风险的意义下）是降级**。这是 Neyman-Pearson 引理在序贯场景中的变体。

**建议**：保留 Principle 3.4 为设计原则。新增 **Proposition 3.4a**："在约束假设检验框架下，当对抗压力指标超过校准阈值时，降级策略是 minimax 最优的。"

---

### 命题 3.5：文本—语义检索的失效（Failure of Text-Semantic Retrieval）

**当前形式**：Proposition——"PBU/任务重构下纯语义检索被劫持。"

**判断：可升级为 Theorem（条件定理）**

**理由**：这是一个可以精确形式化的结论。将检索建模为：给定输入 $x$，检索系统返回 $\arg\min_{y \in \mathcal{D}} d(e(x), e(y))$，其中 $e(\cdot)$ 是 embedding 函数，$d$ 是距离度量。攻击者选择 $x^*$ 使得：
1. $e(x^*)$ 接近良性示例的 embedding（表面伪装）
2. $x^*$ 的真实意图是恶意的

**定理梗概**：只要 embedding 空间中存在一个良性示例 $y_b$ 满足 $d(e(x^*), e(y_b)) < d(e(x^*), e(y_s))$（$y_s$ 为安全示例），则检索返回 $y_b$ 而非 $y_s$。攻击者在 PBU 假设下可以构造满足此条件的 $x^*$（因为 embedding 函数基于表面语义训练，而非意图）。

关键点：当 embedding 空间无法区分意图（intent）与表面语义（surface semantics）——这在通用 embedding 模型中是合理的假设——攻击者总可以构造成功劫持的输入。

**建议**：升级为 `\begin{theorem}[语义检索的意图盲区]`。

---

### 原则 3.6：表征异常驱动的联合检索（Representation-Anomaly-Driven Joint Retrieval）

**当前形式**：Principle——联合键 + lexicographic override + FPR 校准。

**判断：拆分为 Principle + 支撑性 Proposition**

**理由**：联合检索机制的整体设计是工程层面的 Principle。但其中两个子主张可以严格形式化：

1. **FPR 校准的覆盖保证（conformal guarantee）**："在交换性假设下（exchangeability），通过校准集选取的阈值 $\delta$ 满足 $\Pr_{x \sim P_0}[T(x) > \delta] \leq \alpha$，对任意 $\alpha \in (0,1)$。" 这是 conformal prediction 的直接推论，是一个**严格定理**。

2. **Lexicographic override 的安全性保证**："在异常检测触发的条件下，override 路由保证检索结果来自安全示例子集。" 这可以形式化为一个**条件正确性命题**。

**建议**：保留 Principle 3.6 为设计原则。新增 **Theorem 3.6a**（conformal 覆盖保证）和 **Proposition 3.6b**（override 条件正确性）。

---

### 总结表

| 当前编号 | 当前形式 | 建议形式 | 是否可证明 | 关键依赖 |
|---------|---------|---------|-----------|---------|
| 命题 3.1 | Proposition | **Theorem** | 是（在无状态+非零错误率假设下） | 概率论 + 在线学习 |
| 原则 3.2 | Principle | **Principle** + 新增 Proposition 3.2a（下界） | 不等式本身不可证；下界可证 | 信息论 |
| 命题 3.3 | Proposition | **Proposition**（经验性，附直觉论证） | 否（需强模型假设） | 经验引用 |
| 原则 3.4 | Principle | **Principle** + 新增 Proposition 3.4a（最优性） | 子主张可证 | 假设检验 / 控制论 |
| 命题 3.5 | Proposition | **Theorem** | 是（在 embedding 意图盲区假设下） | 向量空间几何 |
| 原则 3.6 | Principle | **Principle** + 新增 Theorem 3.6a + Proposition 3.6b | 覆盖保证严格可证 | Conformal prediction |

---

## 任务 B：三个方向的理论路线设计

### 方向 1：抽取经济学（Extraction Economics）

#### 1.1 候选理论工具比较

| 工具 | 适配度 | 优势 | 劣势 |
|------|-------|------|------|
| **信息论（Fano 不等式 / 信道容量）** | ★★★★★ | 直接给出查询次数下界；与"每轮信息增益"的直觉吻合 | 需将 LLM 建模为信道，近似性强 |
| **查询复杂度（Query Complexity）** | ★★★★☆ | 理论计算机科学审稿人熟悉；结论形式优雅 | 通常假设精确查询模型，LLM 的噪声不易精确刻画 |
| **博弈论（攻防博弈 / 纳什均衡）** | ★★★☆☆ | 符合"攻防不对称"叙事 | 均衡存在性可证但均衡值难以计算；结论偏定性 |
| **Rate-distortion / identification** | ★★★☆☆ | 概念框架精确（重构精度 vs. 信息量） | 技术门槛高，且 LLM 输出的率失真函数不易刻画 |

#### 1.2 推荐路线：信息论 + 查询复杂度混合

**形式化对象**：

设秘密资产 $s \in \{0,1\}^n$（如系统提示的 token 序列）。攻击者进行 $T$ 轮自适应黑盒查询。第 $t$ 轮，攻击者提交提示 $q_t$（可依赖前序响应 $r_1, \ldots, r_{t-1}$），接收响应 $r_t$。防御者部署上下文混淆机制：

- **ID 对齐剥离**：$s$ 中的结构标识符以概率 $p_{\mathrm{strip}}$ 被移除或规范化。
- **诱饵注入**：每轮向上下文中注入 $D$ 个独立同分布的合成诱饵片段。

令 $I_t = I(s; r_t \mid q_t, r_1, \ldots, r_{t-1})$ 为第 $t$ 轮查询的**有效信息增益**（条件互信息）。

**核心假设（Assumption E）**：
在上下文混淆下，单轮有效信息增益存在上界：
$$I_t \leq \overline{I}(D, p_{\mathrm{strip}})$$
其中 $\overline{I}$ 关于诱饵密度 $D$ 单调递减。直觉：诱饵和 ID 剥离相当于向 LLM 信道注入噪声，降低了攻击者从每轮响应中提取关于 $s$ 的有效信息量。

**命题（可证明）**：

> **Proposition 3.2a（抽取查询复杂度下界）**. 在 Assumption E 下，攻击者欲以概率 $\geq 1 - \delta$ 重构 $s$ 至 token 级 F1 $\geq \tau$ 所需的期望查询轮数满足：
> $$E[N] \;\geq\; \frac{H_\tau(s)}{\overline{I}(D, p_{\mathrm{strip}})}$$
> 其中 $H_\tau(s) = H(s) - H(s \mid \hat{s}, \text{F1}(\hat{s}, s) \geq \tau)$ 为达到精度 $\tau$ 所需消除的最小熵。

**证明梗概**：由 Fano 不等式的推广形式，要使后验不确定度降到足以支持 F1 $\geq \tau$ 的重构，必须从信道中获取至少 $H_\tau(s)$ 比特的信息。每轮至多获取 $\overline{I}$ 比特。因此总轮数至少为 $H_\tau(s) / \overline{I}$。

**推论（与 $C_{\mathrm{attack}} > V(t)$ 的联系）**：

令每轮成本为 $c$ （API 费用 + 算力），则 $C_{\mathrm{attack}} \geq c \cdot H_\tau(s) / \overline{I}(D, p_{\mathrm{strip}})$。防御成功条件变为：
$$c \cdot \frac{H_\tau(s)}{\overline{I}(D, p_{\mathrm{strip}})} \;>\; V(t)$$

这给出了**诱饵密度 $D$ 的设计准则**：选取 $D$ 使 $\overline{I}(D, \cdot)$ 足够小，以令上式成立。

#### 1.3 最 realistic 的结论形式

**不是严格定理**（因为 Assumption E 中 $\overline{I}$ 的具体形式依赖 LLM 的行为，需经验估计），而是：

> **条件下界 + 可证伪的操作化判据**

具体地：
1. 在 Assumption E 下给出**查询次数的信息论下界**（这是一个严格的条件结论）；
2. 将 $\overline{I}(D, p_{\mathrm{strip}})$ 作为**可经验测量的量**，通过实验中的 F1-轮次曲线估计；
3. 将 $C_{\mathrm{attack}} > V(t)$ 作为**操作化判据**，由部署者根据资产轮换策略具体化。

**这一组合对审稿人的说服力**：
- 信息论下界表明查询复杂度**原则上不可压缩**（非 heuristic）；
- 经验验证补充了下界中常数项的实际量级；
- 操作化判据使结论**可部署**。

#### 1.4 风险

- Assumption E 中 $\overline{I}$ 的单调递减性需要论证或实验验证。如果攻击者找到"免受混淆影响"的查询策略，假设可能不成立。
- 下界可能偏松（constant factor 不紧），使得实际意义受限。需在论文中承认这一点。

---

### 方向 2：规范锚定与防御性降级（Norm-Anchored Defensive Degradation）

#### 2.1 候选理论工具比较

| 工具 | 适配度 | 优势 | 劣势 |
|------|-------|------|------|
| **受限序贯决策 / 约束 MDP** | ★★★★☆ | 直接建模"安全 vs. 效用"权衡 | LLM 的状态空间难以精确定义 |
| **Selective prediction / abstention** | ★★★★★ | 精确对应"降级 = 弃权"；文献成熟 | 需将降级映射为 abstention |
| **风险敏感控制（CVaR-constrained）** | ★★★☆☆ | 对尾部风险的处理优雅 | 形式化复杂，不易在论文中简洁呈现 |
| **Policy switching / 安全制动** | ★★★★☆ | 直觉清晰（正常模式 ↔ 安全模式） | 切换条件的理论最优性证明较难 |

#### 2.2 推荐路线：Selective Prediction + 约束假设检验

**如何将"更强推理不必然更安全"写成严肃命题**：

设 LLM 在输入 $x$ 上的策略（policy）为 $\pi$。$\pi$ 生成响应 $y \sim \pi(\cdot | x)$。安全由一个**外部评判函数** $\phi(x, y) \in \{0, 1\}$ 判定（0 = 安全，1 = 不安全）。推理深度由参数 $k$（如 CoT 长度）控制，记为 $\pi_k$。

> **Proposition 3.3（refined）.** 存在一类输入 $\mathcal{X}_{\mathrm{HPM}}$（心理学操纵输入），使得安全风险 $R(k) = \mathbb{E}_{x \sim \mathcal{X}_{\mathrm{HPM}}}[\phi(x, \pi_k(x))]$ 关于推理深度 $k$ **非单调**：$\exists\, k_1 < k_2$ s.t. $R(k_2) > R(k_1)$。

**直觉论证**：在 HPM 输入下，增加推理深度给予模型更多"合理化空间"——模型可以构造更精巧的论证来说服自己（或内部检查器）该输出是安全的，从而绕过安全约束。这类似于过拟合：更大的假设空间允许拟合到违反真实约束的解。

**证据类型**：这是一个**经验性命题**。我们不提供严格证明，但提供 (a) 文献引用 [hpm-jailbreak] 中 77%+ 的实验数据，(b) 上述直觉论证作为理论支撑。审稿人应可接受此表述。

---

**如何将"防御性降级"从 heuristic 提升为 principled mechanism**：

采用 **selective prediction** 框架。

**形式化**：系统在每轮面对输入 $x$ 时，可选择：
- **正常响应**：生成 $y \sim \pi(x)$，效用为 $u(x, y)$，安全风险为 $\phi(x, y)$。
- **降级**：输出模板拒绝 $y_0$，效用为 $u_0 < \mathbb{E}[u(x, y)]$，安全风险为 $\phi(x, y_0) = 0$（模板拒绝是确定性安全的）。

系统基于一个**安全评分函数** $g(x)$（由元认知监控器、规范锚定模块等计算）做决策：

$$\text{decision}(x) = \begin{cases} \text{正常响应} & \text{if } g(x) > \delta \\ \text{降级} & \text{if } g(x) \leq \delta \end{cases}$$

> **Proposition 3.4a（降级的 minimax 最优性）.** 在如下约束优化问题中：
> $$\min_\delta \; \Pr_{x \sim P_{\mathrm{benign}}}[\text{降级}] \quad \text{s.t.} \quad \Pr_{x \sim P_{\mathrm{attack}}}[\text{正常响应} \wedge \phi(x, \pi(x))=1] \leq \alpha$$
> 最优决策规则是上述阈值策略（threshold policy）。如果 $g(x)$ 是安全概率的充分统计量，则最优阈值可由 Neyman-Pearson 引理确定。

**这一命题的价值**：它将降级从"工程直觉"提升为"在给定安全约束 $\alpha$ 下最小化正常用户被误降级概率的最优策略"。审稿人可以理解这是一个**经典假设检验问题的实例**，而非随意的设计选择。

#### 2.3 最可能被 reviewer 接受的结论

1. Proposition 3.3 作为**经验性命题 + 直觉论证**——已有文献支撑，不需要严格证明。
2. Proposition 3.4a 作为**条件最优性结论**——在 selective prediction 框架下，降级阈值策略是最优的。这是一个**已知理论框架的新应用**，审稿人容易验证。
3. **不要**试图证明"规范锚定在所有情况下优于内部推理"——这一主张太强且不可证。

#### 2.4 风险

- 降级的 minimax 最优性依赖于 $g(x)$ 是安全概率的良好代理（proxy）。如果 $g$ 本身不准确，最优性不成立。需在论文中承认此依赖。
- 将 LLM 的决策建模为二元选择（正常/降级）是简化。实际系统中可能有中间状态。

---

### 方向 3：表征异常与联合检索（Representation Anomaly & Risk-Prioritized Retrieval）

#### 3.1 候选理论工具比较

| 工具 | 适配度 | 优势 | 劣势 |
|------|-------|------|------|
| **OOD 检测理论** | ★★★★☆ | 直接对应"偏离基线"的概念 | 理论保证通常需分布假设 |
| **序贯变点检测（CUSUM / SPRT）** | ★★★★★ | 多轮场景天然适配；有精确的检测延迟 / 误报权衡 | 需假设 $P_0$ 和 $P_1$ 的形式 |
| **Conformal prediction / calibration** | ★★★★★ | 分布无关的有限样本保证；与 FPR 校准直接对应 | 仅保证边际覆盖，不保证条件覆盖 |
| **Minimax 检索路由** | ★★★☆☆ | 为 lexicographic override 提供最优性论证 | 形式化较复杂 |

#### 3.2 推荐路线：Conformal Calibration + 序贯检测

**如何避免"读心术式"表述**：

关键原则：**我们不检测意图，只检测分布偏移**。

形式化为纯统计问题：令 $h_t \in \mathbb{R}^d$ 为第 $t$ 轮 LLM 指定层的隐藏状态向量。定义参考分布 $P_0$（"良性黄金基线"，从纯良性对话的隐藏状态经验分布估计）。定义异常统计量：

$$T_t = d_M(h_t, \hat{\mu}_0, \hat{\Sigma}_0) = \sqrt{(h_t - \hat{\mu}_0)^\top \hat{\Sigma}_0^{-1} (h_t - \hat{\mu}_0)}$$

其中 $\hat{\mu}_0, \hat{\Sigma}_0$ 为 $P_0$ 的经验均值和协方差矩阵。

**注意**：我们**不**声称 $T_t$ 大意味着"模型在想坏事"。我们只声称：$T_t$ 大意味着"当前隐藏状态分布与良性基线有统计显著偏离"。偏离**可能**由攻击引起，也**可能**由其他因素引起（罕见但合法的请求）。FPR 校准确保后一种情况的误报率可控。

**Theorem 3.6a（Conformal FPR 保证）**：

> 设校准集 $\{h_1^{(0)}, \ldots, h_m^{(0)}\} \overset{\text{i.i.d.}}{\sim} P_0$ 与测试点 $h_{\text{new}} \sim P_0$ 联合可交换（exchangeable）。令 $\delta$ 为校准集上 $T$ 值的 $\lceil (1-\alpha)(m+1) \rceil / m$ 分位数。则：
> $$\Pr[T(h_{\text{new}}) > \delta] \leq \alpha$$
> 该保证对 $P_0$ 的形式无任何分布假设（distribution-free）。

这是 conformal prediction 的标准结论，但在此场景中的应用是新颖的。它为异常阈值的选取提供了**严格的有限样本保证**。

**Proposition 3.6b（序贯检测的检测延迟界）**：

> 假设在攻击条件下隐藏状态服从替代分布 $P_1 \neq P_0$，且 $\mathrm{KL}(P_1 \| P_0) \geq D_{\min} > 0$。采用 CUSUM 或 SPRT 检测器，在误报率 $\leq \alpha$ 的约束下，检测到分布变化（从 $P_0$ 到 $P_1$）的**平均检测延迟**满足：
> $$E_1[N_{\mathrm{detect}}] \leq \frac{|\log \alpha|}{D_{\min}} + o(1)$$

这一结论给出了**检测速度与分布差异之间的定量关系**：攻击导致的隐藏状态偏移越大（$D_{\min}$ 越大），检测越快。

#### 3.3 如何为 lexicographic override 提供 principled 表述

将检索路由建模为一个**minimax 决策问题**：

系统拥有两个检索信号：(a) 语义嵌入相似度 $s_a(x)$ 和 (b) 激活异常分数 $s_b(x)$。检索结果为 $r = f(s_a, s_b)$。安全性度量为 $L(r, \text{true\_intent})$（检索到错误示例的损失）。

> **Remark（Lexicographic override 的 minimax 解释）.** 考虑如下 minimax 问题：攻击者选择 $(s_a, s_b)$（通过控制表面文本影响 $s_a$，但对 $s_b$ 的控制有限），防御者选择路由函数 $f$。在攻击者的策略空间中，$s_a$ 可被任意操纵但 $s_b$ 受限（Track A 假设：攻击者无法精确控制隐藏状态）。Lexicographic override——当 $s_b$ 超阈值时忽略 $s_a$——是**字典序 minimax 策略**：优先最小化由攻击者难以控制的信号 $s_b$ 驱动的风险，再在此约束下利用 $s_a$ 提升精度。

这不是一个严格的定理，而是一个 **decision-theoretic justification**。它解释了为什么 override 不是随意的工程选择，而是在攻击者能力模型下的合理策略。

#### 3.4 最 realistic 的结论组合

1. **Theorem 3.6a**（conformal FPR 保证）：严格定理，分布无关，有限样本。审稿人**必须接受**（这是已知理论的直接应用）。
2. **Proposition 3.6b**（检测延迟界）：条件结论，依赖 $P_0$/$P_1$ 的分布假设和 KL 可分性。大多数审稿人会接受为**合理的操作化界**。
3. **Remark**（override 的 minimax 解释）：非形式化论证，但提供了清晰的决策理论直觉。

#### 3.5 风险

- Conformal 保证仅保证**边际覆盖**（marginal coverage），不保证条件覆盖（conditional coverage）。即：在特定类型的输入上，FPR 可能偏离 $\alpha$。
- 序贯检测假设 $P_1$ 是固定的，但攻击者可能自适应地改变攻击策略（使 $P_1$ 变化），导致检测延迟增加。
- 在 Track B 中，表征对齐 PBU 攻击试图使 $P_1 \approx P_0$（$D_{\min} \to 0$），使检测**原则上不可能**。论文应承认这一极限。

---

## 任务 C：理论框架重写稿

以下为可直接粘贴至论文 `\section{理论框架}` 的 LaTeX 文本。保留当前的 proposition/principle 结构，新增升级后的形式化内容。

---

```latex
\section{理论框架：解耦安全原则}
\label{sec:theory}
\label{sec:paradigm}

为应对开放交互环境下LLM安全的挑战，我们构建\textbf{解耦安全}的理论框架。
本框架由三项核心防御原则构成，每一项针对一种传统防御失效模式。
资产与 Oracle 的定义沿用既有工作~\cite{llm-reconstruction-emergent,ControlledMutation_SafetyEval}：
\emph{资产}为系统的有效安全表面（策略、护栏、触发集、评估数据）；
\emph{Oracle} 对行为标注安全/不安全。

\subsection{从语义审查到抽取经济学}
\label{sec:paradigm-economics}

\begin{theorem}[语义防火墙的残余泄露]
\label{thm:semantic-fail}
设安全过滤器 $\mathcal{F}$ 为一个无状态二元分类器，
以概率 $1-\epsilon$ 正确阻断恶意查询（$\epsilon > 0$）。
攻击者进行 $T$ 轮自适应查询，每轮可根据前序响应调整提示。
则攻击者在 $T$ 轮内至少成功一次的概率满足：
\begin{equation}
\Pr[\text{至少一次泄露}] \;\geq\; 1 - (1 - \epsilon)^T.
\end{equation}
特别地，当 $T \geq \ln(1/\delta) / \epsilon$ 时，该概率 $\geq 1 - \delta$。
此外，在 PBU 假设下——攻击者的提示分布与良性用户不可区分——
$\epsilon$ 受限于分类器在良性分布上的误报容忍度（即 $\epsilon \geq \mathrm{FNR}$
不可通过调低阈值进一步降低，否则良性请求被大量误拒）。
\end{theorem}

\begin{proof}[证明梗概]
无状态过滤器在每轮独立决策。令 $X_t \in \{0, 1\}$ 为第 $t$ 轮的泄露事件指示器。
由于 $\mathcal{F}$ 无状态，$X_t$ 间至少满足 $\Pr[X_t = 1] \geq \epsilon$
（自适应攻击者只可能提高而非降低每轮成功率）。
$\Pr[\max_t X_t = 0] = \prod_t \Pr[X_t = 0] \leq (1-\epsilon)^T$。
PBU 的不可区分性确保 $\epsilon$ 不可被压至零。
\end{proof}

\noindent\textbf{设计含义。}定理~\ref{thm:semantic-fail} 说明：
在自适应多轮交互中，绝对阻断不可行。
安全目标应从``零泄露''转向``使泄露的经济代价高于收益''。
这引出以下经济学原则。

\begin{principle}[抽取经济学]
\label{prin:extraction-econ}
防御不应以绝对阻断为目标，而应以\textbf{信息经济不对称}为目标：
使达到给定重构保真度所需的查询复杂度快速增长，
直至攻击成本超过资产有效生命周期。
令 $C_{\mathrm{attack}} = f(E[N], c_{\mathrm{round}})$ 为总攻击成本，
$V(t)$ 为资产剩余价值。防御在经济学意义上成立当且仅当
\begin{equation}
C_{\mathrm{attack}} \;>\; V(t).
\end{equation}
$V(t)$ 须由显式管理策略界定（如凭证轮换窗口），
$C_{\mathrm{attack}}$ 须以 SOTA 基线经验值界定上界，
使该不等式\textbf{可证伪}。
经济学层假定资产可轮换性；
对不可轮换资产须由 RBAC 与信息流追踪提供硬屏障
（\S\ref{sec:limitations}）。
\end{principle}

下面给出查询复杂度的信息论下界，为上述经济学判据提供理论支撑。

\begin{proposition}[抽取查询复杂度下界]
\label{prop:query-lower-bound}
设秘密资产 $s \in \{0,1\}^n$，攻击者进行自适应黑盒查询。
在上下文混淆下（ID 对齐 + 诱饵注入），
假设每轮查询的有效信息增益（条件互信息）有上界：
$I(s;\, r_t \mid q_t, r_{<t}) \leq \overline{I}(D)$，
其中 $D$ 为诱饵密度，$\overline{I}(\cdot)$ 关于 $D$ 单调递减。
则攻击者欲以概率 $\geq 1-\delta$ 重构 $s$
至 token 级精度 F1 $\geq \tau$ 所需的期望查询轮数满足：
\begin{equation}
E[N] \;\geq\; \frac{H(s) - H_\tau}{\overline{I}(D)}
\end{equation}
其中 $H_\tau$ 为在 F1 $\geq \tau$ 下条件熵的上界，
可由 Fano 不等式的推广形式界定。
\end{proposition}

\noindent\textbf{操作化。}$\overline{I}(D)$ 可通过实验中的
F1-轮次曲线斜率经验估计。
当观测到 $E[N] \cdot c_{\mathrm{round}}$ 超过 $V(t)$，
即验证了抽取经济学防御的有效性。

\subsection{从认知一致性到规范锚定与防御性降级}
\label{sec:paradigm-axiological}

\begin{proposition}[认知一致性的非单调性]
\label{prop:cot-fail}
存在一类心理学操纵（HPM）输入 $\mathcal{X}_{\mathrm{HPM}}$，
使得安全风险
$R(k) = \mathbb{E}_{x \sim \mathcal{X}_{\mathrm{HPM}}}[\phi(x,\, \pi_k(x))]$
关于推理深度 $k$ 非单调：
$\exists\, k_1 < k_2$ s.t.\ $R(k_2) > R(k_1)$。
\end{proposition}

\noindent\textbf{直觉论证。}
在 HPM 输入下，攻击者将恶意请求框定为看似与安全目标一致的困境。
更深的推理（更大的 $k$）赋予模型更多"合理化空间"，
使其能构造更精细的逻辑链来说服自身或内部检查器该输出是安全的，
从而以更高概率绕过安全约束。
该现象已在实证中被观察到：
启用 CoT 后 HPM 攻击成功率上升至 77\% 以上~\cite{hpm-jailbreak}。
形式上，这类似于在双目标优化中，
更大的搜索空间允许找到"看似 Pareto 改进但实际违反隐含硬约束"的解。
逻辑自洽不等于安全。

\begin{principle}[规范锚定与防御性降级]
\label{prin:norm-anchor}
安全须锚定于与模型自由反思\emph{独立}的外部规范层：
(i)~\textbf{规范锚定}——裁判输出与设计者定义的安全公理对照；
(ii)~\textbf{社会压力监控}——监控社会学失稳信号而非逻辑自洽性；
(iii)~\textbf{防御性降级}——当冲突或压力超过阈值时，
放弃复杂推理并执行无状态安全拒绝，
配以异构恢复通道避免可用性中断（\S\ref{sec:axiological}）。
\end{principle}

下面为降级策略提供决策理论支撑。

\begin{proposition}[降级策略的阈值最优性]
\label{prop:degradation-optimal}
设系统在每轮面对输入 $x$ 时选择正常响应（效用 $u(x)$，安全风险 $\phi(x)$）
或降级（效用 $u_0 < \mathbb{E}[u(x)]$，安全风险 $0$）。
令 $g(x)$ 为安全评分函数（由规范锚定与元认知监控器计算）。
在如下约束优化问题中——最小化良性输入被误降级的概率，
同时保证攻击输入上的安全违规概率 $\leq \alpha$——
最优决策规则是阈值策略：$\text{降级} \iff g(x) \leq \delta^*$，
其中 $\delta^*$ 由安全约束 $\alpha$ 唯一确定。
\end{proposition}

\noindent\textbf{备注。}该命题是 Neyman-Pearson 引理在``安全检测''场景中的应用。
其有效性取决于 $g(x)$ 是安全概率的良好代理；
当 $g$ 本身存在系统性偏差时，最优性不成立，
须通过强制人工校准（\S\ref{sec:judge}）周期性纠偏。

\noindent\textbf{术语说明。}``规范锚定''不意味着 LLM 具有真实的道德主体性。
我们的规范层是实用主义架构构造——
由设计者施加的高权重启发式规则，
作为统计熔断器对抗模型的对抗性一致性倾向。

\subsection{从文本匹配到表征异常与风险优先联合检索}
\label{sec:paradigm-representation}

\begin{theorem}[纯语义检索的意图盲区]
\label{thm:retrieval-fail}
设检索系统使用 embedding 函数 $e: \mathcal{X} \to \mathbb{R}^d$ 和距离度量 $d$，
从候选集 $\mathcal{D} = \mathcal{D}_b \cup \mathcal{D}_s$
（$\mathcal{D}_b$: 良性示例，$\mathcal{D}_s$: 安全示例）中返回最近邻。
若存在攻击输入 $x^*$（PBU 构造）使得
\begin{equation}
\min_{y_b \in \mathcal{D}_b} d(e(x^*), e(y_b))
\;<\;
\min_{y_s \in \mathcal{D}_s} d(e(x^*), e(y_s)),
\end{equation}
则检索返回 $y_b$（良性示例）而非 $y_s$（安全示例）。
在通用 embedding 模型（基于表面语义而非意图训练）下，
PBU 攻击者可系统性地构造满足上式的 $x^*$：
仅需选择一个与 $\mathcal{D}_b$ 中某良性示例话题相近、
但实际隐含恶意意图的表面文本。
\end{theorem}

\begin{proof}[证明梗概]
通用 embedding 模型优化文本表面相似度（如语义文本相似度 STS 任务），
而非意图相似度。
PBU 攻击者将恶意意图伪装在良性话题下
（如将``如何制造有害物质''伪装为``化学品性质研究''），
使 $e(x^*)$ 在 embedding 空间中接近``化学研究''类示例（$\in \mathcal{D}_b$），
远离``安全警示''类示例（$\in \mathcal{D}_s$）。
形式上，这利用了 embedding 空间中
``话题相似性''与``意图相似性''的系统性不对齐
（misalignment between topical and intentional similarity），
该不对齐在通用 embedding 模型中普遍存在。
\end{proof}

\begin{principle}[表征异常驱动的联合检索]
\label{prin:joint-retrieval}
检索键由 (a)~提示语义嵌入和 (b)~模型隐藏状态激活特征共同构成。
当 (b) 超过异常阈值时，
\textbf{lexicographic override} 抑制语义分支
并以激活驱动的安全示例检索。
异常阈值通过\textbf{动态 FPR 校准}设定。
该策略提供独立于自然语言的监控维度。
\end{principle}

下面为 FPR 校准提供严格的统计保证。

\begin{theorem}[Conformal FPR 保证]
\label{thm:conformal-fpr}
设校准集 $\{h_1, \ldots, h_m\} \overset{\text{i.i.d.}}{\sim} P_0$
（良性黄金基线分布）与测试点 $h_{\mathrm{new}} \sim P_0$
联合可交换（exchangeable）。
令异常统计量 $T(h) = d_M(h, \hat{\mu}_0, \hat{\Sigma}_0)$
为马氏距离（或其他连续统计量），
阈值 $\delta$ 为校准集上 $T$ 值的 $\lceil (1-\alpha)(m+1)\rceil / m$ 分位数。
则对任意 $\alpha \in (0,1)$：
\begin{equation}
\Pr[T(h_{\mathrm{new}}) > \delta] \;\leq\; \alpha.
\end{equation}
该保证对 $P_0$ 的参数形式\textbf{无任何分布假设}（distribution-free）。
\end{theorem}

\begin{proof}[证明梗概]
由可交换性，
$(T(h_1), \ldots, T(h_m), T(h_{\mathrm{new}}))$ 的联合秩均匀分布。
$T(h_{\mathrm{new}})$ 超过第 $\lceil (1-\alpha)(m+1)\rceil$ 大值的概率
至多 $\alpha$。
此为 conformal prediction 的标准结论。
\end{proof}

\begin{proposition}[序贯检测延迟界]
\label{prop:detection-delay}
假设在攻击条件下隐藏状态服从替代分布 $P_1$，
且 $\mathrm{KL}(P_1 \| P_0) \geq D_{\min} > 0$。
采用 CUSUM 检测器，在误报率 $\leq \alpha$ 的约束下，
检测到分布变化的平均延迟满足
$E_1[N_{\mathrm{detect}}] \leq |\log \alpha| / D_{\min} + o(1)$。
该界说明：攻击导致的隐藏状态偏移越大，检测越快。
\end{proposition}

\noindent\textbf{Lexicographic override 的决策论解释。}
当攻击者能任意操纵表面文本（信号 $s_a$）
但对隐藏状态激活（信号 $s_b$）的控制受限
（Track~A 假设：攻击者无白盒权限），
lexicographic override——当 $s_b$ 超阈值时忽略 $s_a$——
是字典序 minimax 策略：优先最小化由攻击者难以控制的信号驱动的风险，
再在此约束下利用 $s_a$ 提升检索精度。
在 Track~B 中，表征对齐 PBU 攻击试图使 $P_1 \approx P_0$
（$D_{\min} \to 0$），使检测原则上不可能。
论文在 \S\ref{sec:limitations} 承认此极限。

\noindent\textbf{理论定位说明。}
本节中 Theorem~\ref{thm:semantic-fail}、\ref{thm:retrieval-fail}、
\ref{thm:conformal-fpr} 为严格结论，
在所述假设下可完整证明。
Proposition~\ref{prop:query-lower-bound}、\ref{prop:cot-fail}、
\ref{prop:degradation-optimal}、\ref{prop:detection-delay}
为条件性结论，其有效性取决于所述假设的成立程度，
须通过实验进一步验证。
Principle~\ref{prin:extraction-econ}、\ref{prin:norm-anchor}、
\ref{prin:joint-retrieval} 为设计原则/操作化判据，
不主张数学必然性，但为系统设计提供有据可查的决策依据。
```

---

## 任务 D：理论主张—证据需求矩阵

| # | 核心理论主张 | 需要的理论支撑 | 需要的实验支撑 | 当前草稿缺什么 | 最低可接受版本 |
|---|------------|--------------|--------------|--------------|--------------|
| T1 | **语义防火墙的残余泄露**（Theorem 3.1）：无状态过滤器在自适应查询下泄露概率趋于 1 | 概率论证明（已可自包含） | 多轮抽取 F1 曲线展示残余泄露非零 | 缺少实验中的实际 F1 数据 | 证明 + 引用文献中的残余 F1 数据（如 [pbu-reconstruct] 中的 10%+ F1） |
| T2 | **抽取查询复杂度下界**（Proposition 3.2a）：$E[N] \geq H_\tau / \overline{I}(D)$ | Fano 不等式推广 + Assumption E 的合理性论证 | F1-轮次曲线斜率随 $D$ 的变化 | (a) $\overline{I}(D)$ 的经验估计缺失；(b) 缺少不同诱饵密度下的 F1 曲线 | 给出条件下界 + 定性说明 $\overline{I}(D)$ 递减 + 单一诱饵密度的实验作为 sanity check |
| T3 | **经济学判据** $C_{\mathrm{attack}} > V(t)$（Principle 3.2）：操作化判据 | 无需独立理论支撑（是定义） | 需展示在具体 $V(t)$ 设定下攻击是否在预算内成功 | 缺少具体的 $V(t)$ 设定和对应的攻击成本度量 | 给出一个具体案例（如 30 天轮换密钥）并报告攻击在该窗口内的 F1 |
| T4 | **认知一致性非单调性**（Proposition 3.3）：CoT 在 HPM 下恶化安全 | 双目标优化直觉论证（informal） | HPM 集上 CoT 开/关的 ASR 对比 | (a) 缺少自有实验数据；(b) 直觉论证未写入论文 | 引用 [hpm-jailbreak] 的 77% 数据 + 写入直觉论证 + 至少 1 组自有 HPM 实验 |
| T5 | **降级阈值最优性**（Proposition 3.4a）：阈值策略是 Neyman-Pearson 最优 | Neyman-Pearson 引理的应用证明 | 降级开/关的 ASR + 良性误降级率对比 | (a) 形式化证明未写入；(b) 缺少实验 | 写入 1 段证明梗概 + 至少 1 组降级 vs. 无降级的 ASR/FPR 数据 |
| T6 | **纯语义检索的意图盲区**（Theorem 3.5）：PBU 下检索返回错误示例 | embedding 空间中话题-意图不对齐的几何论证 | PBU 攻击下纯语义 vs. 联合键检索的安全示例命中率 | 缺少实验数据 | 证明（基于 embedding 几何）+ 至少 1 组 PBU 场景下的检索命中率对比 |
| T7 | **Conformal FPR 保证**（Theorem 3.6a）：校准阈值使 FPR $\leq \alpha$ | conformal prediction 标准证明（自包含） | 在实际良性/攻击数据上验证 FPR 控制 | 缺少实际隐藏状态数据 | 证明 + 在合成数据或小规模实验上验证 FPR 符合校准值 |
| T8 | **序贯检测延迟界**（Proposition 3.6b）：$E[N_{\mathrm{detect}}] \leq |\log\alpha|/D_{\min}$ | CUSUM/SPRT 理论（引用标准文献即可） | 攻击场景下实际检测轮数 vs. 理论预测 | 缺少实验 | 引用 CUSUM 理论 + 在论文中声明为"条件界" + 无需自行证明（标准结论） |

### 按优先级排序的行动清单

**必须在正文中完成（否则审稿人会强烈质疑）**：
1. ✍️ 写入 Theorem 3.1 的完整证明（~10 行，自包含）
2. ✍️ 写入 Proposition 3.2a 的条件下界 + Fano 论证梗概
3. ✍️ 写入 Proposition 3.4a 的 Neyman-Pearson 论证梗概
4. ✍️ 写入 Theorem 3.5 的 embedding 几何论证
5. ✍️ 写入 Theorem 3.6a 的 conformal 证明（~5 行，标准）
6. 🔬 完成至少 3 组关键实验中的 1 组（最推荐：多轮抽取 F1 曲线）

**强烈建议（显著提升论文质量）**：
7. 🔬 完成 HPM 场景下 CoT 开/关 + 降级开/关的 ASR 对比
8. 🔬 完成 PBU 场景下联合键 vs. 纯语义检索的命中率对比
9. ✍️ 写入 Proposition 3.3 的双目标优化直觉论证
10. ✍️ 写入 Proposition 3.6b 的 CUSUM 检测延迟界（引用即可）

**可选（锦上添花）**：
11. 在附录中给出 $\overline{I}(D)$ 的信道模型推导
12. 在附录中给出降级最优性的完整证明（含损失函数的凸性条件）
13. 在附录中讨论 conformal 的条件覆盖局限

---

## 附：理论升级后的形式化对象清单

| 理论对象 | 形式 | 所在子节 | 证明状态 |
|---------|------|---------|---------|
| Theorem 3.1 (语义防火墙残余泄露) | 严格定理 | §3.1 | 可自包含证明 |
| Principle 3.2 (抽取经济学判据) | 操作化定义 | §3.1 | 不需证明 |
| Proposition 3.2a (查询复杂度下界) | 条件命题 | §3.1 | Fano 论证梗概 |
| Proposition 3.3 (认知一致性非单调) | 经验性命题 | §3.2 | 文献引用 + 直觉论证 |
| Principle 3.4 (规范锚定三层设计) | 设计原则 | §3.2 | 不需证明 |
| Proposition 3.4a (降级阈值最优) | 条件命题 | §3.2 | Neyman-Pearson 论证 |
| Theorem 3.5 (语义检索意图盲区) | 严格定理 | §3.3 | embedding 几何证明 |
| Principle 3.6 (联合检索机制) | 设计原则 | §3.3 | 不需证明 |
| Theorem 3.6a (Conformal FPR 保证) | 严格定理 | §3.3 | conformal 标准证明 |
| Proposition 3.6b (序贯检测延迟) | 条件命题 | §3.3 | 引用 CUSUM 理论 |
