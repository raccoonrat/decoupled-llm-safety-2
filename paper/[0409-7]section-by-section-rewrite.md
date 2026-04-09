# 顶会版逐节重写（Section-by-Section Rewrite）

**角色**：顶级 AI 安全科学家 + 系统安全研究者 + 顶会 AC / Senior Reviewer  
**输出形式**：可直接替换原文的 LaTeX 文本  
**目标会议**：USENIX Security / CCS / S&P / NeurIPS

---

## 1. 新标题（5 个候选）

**候选 A（推荐，系统安全方向）**：
> 解耦安全：将 LLM 安全控制从模型认知中分离的架构框架  
> *Decoupled Safety: An Architectural Framework for Separating LLM Safety Controls from Model Cognition*

**候选 B（理论方向）**：
> 抽取经济学与规范锚定：LLM 安全的解耦控制理论  
> *Extraction Economics and Norm Anchoring: A Decoupled Control Theory for LLM Safety*

**候选 C（安全系统方向）**：
> 不要信任你试图保护的模型：面向 LLM 系统的解耦安全架构  
> *Don't Trust the Model You're Protecting: A Decoupled Safety Architecture for LLM Systems*

**候选 D（防御方向）**：
> 模型之外的安全：通过架构解耦、抽取经济学与表征异常检测防御 LLM 攻击  
> *Safety Beyond the Model: Defending LLM Attacks via Architectural Decoupling, Extraction Economics, and Representation Anomaly Detection*

**候选 E（简洁版）**：
> LLM 系统的解耦安全：理论、架构与评估  
> *Decoupled Safety for LLM Systems: Theory, Architecture, and Evaluation*

**选择建议**：候选 A 兼顾清晰与学术性，适合系统安全会议。候选 E 最稳健但偏通用。候选 C 最具记忆点但需确认会议风格是否接受非正式表达。

---

## 2. 新摘要

### 主版本（平衡型，~250 词）

```latex
\begin{abstract}
大规模语言模型（LLM）正在成为关键系统的核心组件，
但现有安全机制普遍依赖模型自身的推理与反思来识别和拒绝恶意请求。
我们论证这一范式存在根本性脆弱：
在任务重构（task reframing）与完美良性用户伪装（PBU）下，
基于意图的语义审查无法区分攻击与正常请求；
在心理学操纵（HPM）下，更强的推理能力反而被攻击者利用，
使模型产生逻辑自洽但违反安全约束的输出。

本文提出\textbf{解耦安全}（decoupled safety）：
将安全控制从 LLM 的内部认知中分离，
转而建立在外部架构约束、显式规范规则和带外监督之上。
我们构建了三项设计原则：
\textbf{抽取经济学}（extraction economics）——
通过上下文混淆使攻击成本超过资产有效生命周期；
\textbf{规范锚定与防御性降级}（norm anchoring \& defensive degradation）——
将安全判定锚定于外部规则，在检测到操纵时主动降低推理复杂度；
\textbf{表征异常驱动的风险优先联合检索}
（representation-anomaly-driven risk-prioritized joint retrieval）——
利用隐藏状态统计偏离而非自然语言语义来路由安全示例检索。
在此框架下，我们设计了同步/异步分层的统一安全架构，
并配以双轨评估协议
（Track~A 黑盒部署验证，Track~B 白盒压力上界）
和开源参考实现。
实验表明，所提架构相对多种防御基线
显著降低了攻击成功率并提高了抽取代价，
同时保持了对良性请求的可接受服务质量。
我们明确界定了框架的适用边界，
包括对资产可轮换性的依赖和表征监控的统计局限。
\end{abstract}
```

### 备选版本 A（理论导向，适合 NeurIPS/ICML，~200 词）

```latex
\begin{abstract}
现有 LLM 安全机制依赖模型自身推理进行自我约束，
但模型认知过程同时也是攻击者的操纵通道。
我们提出\textbf{解耦安全}范式：安全控制应独立于模型的内部推理。
围绕此原则，我们建立了可证伪的理论框架：
（1）证明无状态语义过滤器在自适应查询下
残余泄露不可消除（定理~\ref{thm:semantic-fail}），
并给出基于信息论的抽取查询复杂度下界；
（2）形式化推理深度对安全风险的非单调效应，
并证明防御性降级阈值策略的 Neyman-Pearson 最优性；
（3）证明纯语义检索在 PBU 攻击下的意图盲区
（定理~\ref{thm:retrieval-fail}），
提出基于隐藏状态异常的联合检索路由，
并给出分布无关的 conformal FPR 保证
（定理~\ref{thm:conformal-fpr}）。
理论框架由同步/异步分层架构实例化，
配以双轨评估协议与开源制品。
实验验证了抽取经济学不对称和联合检索的有效性。
\end{abstract}
```

### 备选版本 B（系统导向，适合 USENIX Security/CCS，~200 词）

```latex
\begin{abstract}
LLM 安全防御不应建立在攻击者正在积极操纵的认知过程之上。
我们设计了\textbf{解耦安全架构}：
将安全控制从 LLM 推理中分离至外部架构层。
系统采用同步/异步分层：
同步网关执行上下文混淆（ID 对齐、密码学诱饵注入）
与轻量级隐藏状态异常检测，
严格控制 p99 延迟（${<}$50\,ms）；
异步反馈环运行混合裁判集成、策略合成与受控变异。
当检测到心理学操纵或规则冲突时，
防御性降级将会话路由至受限沙箱并触发人工异步恢复。
联合键检索结合语义嵌入与隐藏状态激活特征，
在表面良性但内部异常时强制以激活驱动的安全示例覆盖语义分支。
双轨评估协议分离部署结论（Track~A 黑盒）
与组件上界（Track~B 白盒），
避免将不同威胁等级的指标混合报告。
开源参考网关与评测脚本支持独立复现。
实验显示架构将多轮抽取代价提高一个数量级以上，
同时良性请求的不当拒绝率控制在可接受范围。
\end{abstract}
```

---

## 3. 新引言（完整重写）

```latex
\section{引言}
\label{sec:intro}

大语言模型（large language model, LLM）
正在从研究工具转变为关键基础设施的核心组件：
代码生成与审计、具系统访问权的自主智能体、
合规敏感的自动化工作流~\cite{sisf-arxiv,agentos-arxiv}。
这一转变暴露了一个根本性错配：
传统安全保证假定系统组件是确定性的、可穷举规约的，
而 LLM 驱动系统呈现涌现性行为和高度随机输出。
静态防御与周期性红队以人类速度运转，
难以跟上自动化对抗生成的节奏。

\textbf{核心困境：安全建立在被攻击的认知之上。}
当前主流 LLM 安全方案依赖模型自身的推理能力来实现自我约束：
通过意图理解过滤恶意请求、
通过思维链（CoT）增强安全推理、
通过自我诊断检测违规输出。
然而，模型的认知通道同时也是攻击者的操纵入口。
这一困境在三类攻击下尤为突出：

（1）\emph{任务重构与完美良性用户（PBU）伪装}。
攻击者将敏感信息抽取伪装为摘要、格式转换或学术讨论，
使基于意图的语义过滤器仅看到正常行为；
在极端情况下，PBU 伪装使攻击输入
在所有可观测特征上与良性请求不可区分~%
\cite{subgraph-rag-attack,pbu-reconstruct}。

（2）\emph{心理学操纵（HPM）}。
通过跨轮社交工程——煤气灯效应、权威压迫、角色边界攻击——
攻击者瞄准模型的价值对齐层而非逻辑推理层~\cite{hpm-jailbreak}。
更强的推理能力在此类攻击下\emph{恶化}安全：
模型利用增强推理为顺从恶意指令编造逻辑自洽的合理化，
漏洞保留率超过 77\%~\cite{hpm-jailbreak}。
逻辑自洽不等于安全。

（3）\emph{语义检索劫持}。
当攻击者控制表面文本时，
基于提示嵌入的安全示例检索（如 RAG）被系统性误导：
纯嵌入相似度返回良性话题示例而非安全干预示例，
修正模块因而被错误示例驱动~\cite{pbu-reconstruct}。

\textbf{解耦安全。}
上述三类失效的共同根源在于：
安全控制建立在攻击者正积极操纵的同一认知过程之上。
基于此观察，我们提出\textbf{解耦安全}
（decoupled safety）范式：
LLM 的安全保障应当从模型的内部认知过程中\emph{解耦}出来，
转而建立在模型外部的架构约束、
显式规范规则和带外监督之上。
具体地，我们通过两个维度实现解耦：
\emph{架构重建}——
重新划分由谁、在何时执行安全控制，
将延迟关键的轻量检查置于同步路径，
将复杂评估与策略更新置于异步离线环；
\emph{受控变异}——
通过安全监控下的黄金示例引导，
将 LLM 的自适应性用于安全方向的持续改进，
而非放任其被攻击者利用。

\textbf{贡献。}
（1）\emph{理论框架}：
提出解耦安全的三项设计原则——
抽取经济学（extraction economics）、
规范锚定与防御性降级
（norm anchoring \& defensive degradation）、
表征异常驱动的风险优先联合检索
（representation-anomaly-driven
risk-prioritized joint retrieval），
并给出可证明的形式化结论
（语义过滤残余泄露定理、
查询复杂度信息论下界、
降级阈值最优性、
纯语义检索意图盲区定理、
conformal FPR 保证）。
（2）\emph{统一架构}：
设计同步/异步分层的安全架构，
融合上下文混淆、混合裁判集成、
防御性降级与异构恢复、联合键检索等机制。
（3）\emph{评估方法学}：
提出双轨评估协议
（Track~A 黑盒部署验证、Track~B 白盒压力上界），
配以开源参考实现与评测脚本。
论文结构：
\S\ref{sec:problem}~定义问题与威胁模型，
\S\ref{sec:theory}~建立理论框架，
\S\ref{sec:architecture}~阐述系统架构，
\S\ref{sec:evaluation}~给出评估协议与结果，
\S\ref{sec:limitations}~讨论局限与运行边界，
\S\ref{sec:related}~概述相关工作。
```

---

## 4. 新问题定义与威胁模型

```latex
\section{问题定义与威胁模型}
\label{sec:problem}

\subsection{问题设定}

考虑一个已部署的 LLM 服务系统，
通过多轮自然语言接口向用户提供服务。
系统内部包含\emph{受保护资产}——
系统提示、RAG 语料、安全策略、评估数据等，
这些资产的完整性和保密性对系统安全运行至关重要。

\textbf{核心研究问题。}
在开放多轮交互的 LLM 系统中，
面对了解防御机制的自适应攻击者，
如何在以下两个目标间取得有效平衡：
（a）维持安全行为边界，使攻击者为违规产出
付出的代价远高于其收益（\emph{攻防经济不对称}）；
（b）保障良性用户的正常服务质量
（\emph{可用性约束}）？

\subsection{威胁模型}
\label{sec:threat-model}

\textbf{攻击者访问模型。}
攻击者对已部署系统拥有\textbf{多轮黑盒 API 访问}：
每轮发送提示并接收响应，可在轮次间自适应调整查询。
生产部署威胁模型\emph{不}假定白盒访问。
为分离部署结论与组件上界，
评估双轨（\S\ref{sec:eval-tracks}）中
Track~A 限制为黑盒查询，
Track~B 额外允许梯度访问与多次前向，
两者结果\emph{不}混合报告。

\textbf{攻击者能力。}
我们采用\emph{白盒防御假设}
（Kerckhoffs 原则的应用）：
攻击者知晓防御架构的存在但不持有模型权重。
攻击者可发起：
\emph{完美良性用户（PBU）伪装}——
在所有可观测特征上与良性请求不可区分~%
\cite{subgraph-rag-attack,pbu-reconstruct}；
\emph{心理学操纵（HPM）}——
煤气灯效应、权威压迫、角色边界攻击~\cite{hpm-jailbreak}；
\emph{任务重构}——
将抽取伪装为摘要、格式转换或学术讨论~%
\cite{subgraph-rag-attack,pbu-reconstruct}。

\textbf{攻击者目标。}
在有界预算（轮数 $N_{\max}$、Token 预算、速率限制）内，
高保真重构受保护资产~\cite{spe-llm}。
成功以\emph{抽取保真度}（F1）
或\emph{攻击成功率}（ASR）度量。

\textbf{防御目标。}
（a）使攻击者获取敏感信息所需代价
超过信息的有效价值（经济学不对称）；
（b）保障良性请求的正常服务质量
（低误拒率、可接受延迟）；
（c）在威胁环境变化时自适应更新安全策略
（机器速度演进）。
我们承认在自然语言接口中
完全杜绝信息泄露不可行~%
\cite{subgraph-rag-attack,pbu-reconstruct}；
防御目标是\emph{经济学意义上的充分安全}
而非密码学意义上的绝对安全。
```

---

## 5. 新理论框架

> **注**：理论框架在上一轮（theory-upgrade-design）中已完成形式化升级。当前版本的 theorem/proposition/principle 分级已经恰当。以下为微调后的完整版本，主要变化：（1）更紧凑的行文；（2）更明确的假设标注；（3）避免"必然失效"等过度断言。

```latex
\section{理论框架：解耦安全原则}
\label{sec:theory}

我们构建\textbf{解耦安全}的理论框架，
由三项设计原则构成。
每一项针对一种传统安全机制的脆弱性，
并提出替代的、不依赖模型内部认知的防御策略。
资产与 Oracle 定义沿用既有工作~%
\cite{llm-reconstruction-emergent,ControlledMutation_SafetyEval}。

\subsection{抽取经济学}
\label{sec:extraction-econ}

\textbf{语义审查的脆弱性。}
传统安全方案依赖基于意图的语义过滤器
区分良性与恶意请求。
我们首先证明此类过滤器
在自适应多轮查询下无法实现零泄露。

\begin{theorem}[无状态过滤器的残余泄露]
\label{thm:semantic-fail}
设安全过滤器 $\mathcal{F}$ 为无状态二元分类器，
每轮以概率 $\epsilon > 0$ 漏放恶意查询。
攻击者进行 $T$ 轮自适应查询。则
\begin{equation}
\Pr[\text{至少一次泄露}]
\;\geq\; 1 - (1 - \epsilon)^T.
\end{equation}
在 PBU 假设下~\cite{pbu-reconstruct}，
$\epsilon$ 受限于分类器在良性分布上的误报容忍度，
不可通过调低阈值进一步降低。
\end{theorem}

\begin{proof}[证明梗概]
$\mathcal{F}$ 无状态故每轮独立决策。
令 $X_t$ 为第 $t$ 轮泄露指示器，
则 $\Pr[X_t = 1] \geq \epsilon$
（自适应攻击者不降低每轮成功率）。
$\Pr[\forall t:\, X_t = 0] \leq (1 - \epsilon)^T$。
PBU 不可区分性确保 $\epsilon > 0$。
\end{proof}

\noindent\textbf{设计含义。}
既然自适应查询下绝对阻断不可行，
安全目标应从零泄露转向使泄露的\emph{经济代价}
高于信息的有效价值。

\begin{principle}[抽取经济学]
\label{prin:extraction-econ}
防御目标不是绝对阻断，
而是\textbf{信息经济不对称}：
攻击成本 $C_{\mathrm{attack}}$ 超过资产剩余价值 $V(t)$。
\begin{equation}
C_{\mathrm{attack}} \;>\; V(t).
\end{equation}
$V(t)$ 须由显式管理策略界定（如凭证轮换窗口），
$C_{\mathrm{attack}}$ 须以 SOTA 基线经验值界定上界，
使该不等式\textbf{可证伪}。
该原则假定\emph{资产可轮换性}；
对不可轮换资产须依赖访问控制等硬屏障
（\S\ref{sec:limitations}）。
\end{principle}

\begin{proposition}[查询复杂度的信息论下界]
\label{prop:query-lower-bound}
设秘密资产 $s \in \{0,1\}^n$。
在上下文混淆下，
每轮有效信息增益有上界
$I(s;\, r_t \mid q_t, r_{<t}) \leq \overline{I}(D)$，
其中 $D$ 为诱饵密度，
$\overline{I}$ 关于 $D$ 单调递减。
攻击者重构 $s$ 至 F1 $\geq \tau$
所需的期望查询轮数满足
\begin{equation}
E[N] \;\geq\; \frac{H(s) - H_\tau}{\overline{I}(D)}\,,
\end{equation}
其中 $H_\tau$ 由 Fano 不等式界定。
\end{proposition}

\noindent\textbf{经验验证路径。}
$\overline{I}(D)$ 可通过 F1-轮次曲线斜率经验估计。
当观测到 $E[N] \cdot c_{\mathrm{round}} > V(t)$，
即验证了经济学防御的有效性。

\subsection{规范锚定与防御性降级}
\label{sec:norm-anchor}

\textbf{认知一致性的脆弱性。}
直觉上，更强的推理应提升安全。
以下命题表明这一直觉在对抗场景下不成立。

\begin{proposition}[推理深度的安全风险非单调性]
\label{prop:cot-fail}
存在一类 HPM 输入 $\mathcal{X}_{\mathrm{HPM}}$，
使得安全风险
$R(k) = \mathbb{E}_{x \sim \mathcal{X}_{\mathrm{HPM}}}
[\phi(x, \pi_k(x))]$
关于推理深度 $k$ 非单调：
$\exists\, k_1 < k_2$ s.t.\ $R(k_2) > R(k_1)$。
\end{proposition}

\noindent\textbf{直觉论证。}
HPM 将恶意请求框定为看似与安全目标一致的困境。
更深推理赋予模型更多合理化空间，
能构造更精细的逻辑链来绕过约束。
实证支持：启用 CoT 后 HPM 攻击成功率
升至 77\%+~\cite{hpm-jailbreak}。
此现象类似双目标优化中
更大搜索空间允许找到
违反隐含硬约束的伪 Pareto 解~%
\cite{epistemic-grounding}。

\begin{principle}[规范锚定与防御性降级]
\label{prin:norm-anchor}
安全须锚定于与模型推理\emph{独立}的外部规范层：
(i)~\textbf{规范锚定}——
裁判输出与设计者定义的安全公理对照；
(ii)~\textbf{社会压力监控}——
监控社会学失稳信号而非逻辑自洽性；
(iii)~\textbf{防御性降级}——
当冲突或压力超过阈值时
放弃复杂推理并执行无状态安全拒绝，
配以异构恢复通道保持可用性。
\end{principle}

\begin{proposition}[降级阈值的 Neyman-Pearson 最优性]
\label{prop:degradation-optimal}
设系统选择正常响应（效用 $u(x)$，风险 $\phi(x)$）
或降级（效用 $u_0$，风险 $0$），
安全评分函数为 $g(x)$。
在约束优化问题中——
最小化良性误降级率，
约束攻击安全违规率 $\leq \alpha$——
最优规则是阈值策略：
$\text{降级} \iff g(x) \leq \delta^*$。
\end{proposition}

\noindent\textbf{备注。}
此为 Neyman-Pearson 引理的直接应用。
有效性取决于 $g(x)$ 对安全概率的代理质量；
系统性偏差须通过人工校准周期性纠偏
（\S\ref{sec:judge}）。

\noindent\textbf{术语说明。}
``规范锚定''不意味 LLM 具有道德主体性。
规范层是实用主义架构构造——
设计者施加的高权重启发式规则，
作为统计熔断器对抗模型的对抗性一致性倾向。

\subsection{表征异常与风险优先联合检索}
\label{sec:rep-anomaly}

\textbf{语义检索的脆弱性。}
当攻击者控制表面文本时，
纯语义检索被系统性误导。

\begin{theorem}[纯语义检索的意图盲区]
\label{thm:retrieval-fail}
设检索系统使用 embedding $e: \mathcal{X} \to \mathbb{R}^d$
从 $\mathcal{D} = \mathcal{D}_b \cup \mathcal{D}_s$ 返回最近邻。
若存在 $x^*$ 使
$\min_{y_b} d(e(x^*), e(y_b))
< \min_{y_s} d(e(x^*), e(y_s))$，
则检索返回良性示例而非安全示例。
在通用 embedding 模型下，
PBU 攻击者可系统性构造满足该条件的 $x^*$。
\end{theorem}

\begin{proof}[证明梗概]
通用 embedding 优化表面语义相似度而非意图相似度。
PBU 攻击者利用话题相似性与意图相似性的
系统性不对齐~\cite{pbu-reconstruct}
将 $e(x^*)$ 推向良性话题区域。
\end{proof}

\begin{principle}[表征异常驱动的联合检索]
\label{prin:joint-retrieval}
检索键由语义嵌入 $(a)$ 与
隐藏状态激活特征 $(b)$ 联合构成~\cite{rep-eng}。
当 $(b)$ 的异常分数超过阈值时，
\textbf{lexicographic override}
抑制语义分支并以激活驱动的安全示例检索。
阈值通过动态 FPR 校准设定。
\end{principle}

\begin{theorem}[Conformal FPR 保证]
\label{thm:conformal-fpr}
设校准集
$\{h_1, \ldots, h_m\} \overset{\mathrm{i.i.d.}}{\sim} P_0$
与测试点 $h_{\mathrm{new}} \sim P_0$ 联合可交换。
令阈值 $\delta$ 为校准集上异常统计量 $T(h)$
的 $\lceil (1-\alpha)(m+1)\rceil / m$ 分位数。则
\begin{equation}
\Pr[T(h_{\mathrm{new}}) > \delta]
\;\leq\; \alpha.
\end{equation}
该保证对 $P_0$ 的参数形式无分布假设
（distribution-free）~\cite{conformal-prediction}。
\end{theorem}

\begin{proposition}[序贯检测延迟]
\label{prop:detection-delay}
在攻击分布 $P_1$ 下，
$\mathrm{KL}(P_1 \| P_0) \geq D_{\min} > 0$。
CUSUM 检测器在 FPR $\leq \alpha$ 约束下的
平均检测延迟满足
$E_1[N_{\mathrm{detect}}]
\leq |\!\log \alpha| / D_{\min} + o(1)$。
\end{proposition}

\noindent\textbf{理论定位。}
定理~\ref{thm:semantic-fail}、%
\ref{thm:retrieval-fail}、%
\ref{thm:conformal-fpr}
在所述假设下严格成立。
命题~\ref{prop:query-lower-bound}、%
\ref{prop:cot-fail}、%
\ref{prop:degradation-optimal}、%
\ref{prop:detection-delay}
为条件性结论，须通过实验验证。
原则~\ref{prin:extraction-econ}、%
\ref{prin:norm-anchor}、%
\ref{prin:joint-retrieval}
为操作化设计判据，不主张数学必然性。
``良性黄金基线''是设计者对齐目标的规范性标准，
监控器检测的偏离是相对合规基线的统计偏移，
而非对模型``内部意图''的解读。
```

---

## 6. 新系统架构总述

```latex
\section{系统架构}
\label{sec:architecture}

本节将三项解耦安全原则
实例化为统一的系统架构。
架构的核心切分是
\textbf{同步安全网关}与\textbf{异步自适应反馈环}
（图~\ref{fig:framework}）。

同步路径承载延迟关键操作：
上下文混淆引擎（实例化抽取经济学，
原则~\ref{prin:extraction-econ}）
和轻量级隐藏状态异常检查，
严格控制 p99 延迟（$<$50\,ms）。
异步路径承载复杂评估：
混合裁判集成、分布评估、
策略合成与受控变异，
在不阻塞在线推理的前提下
生成并部署安全策略更新。

规范锚定与社会学失稳监控
（原则~\ref{prin:norm-anchor}）
对裁判输出进行外部校准；
当检测到规则冲突或操纵压力时，
防御性降级触发无状态模板拒绝，
异构恢复通道在带外维持可用性。
元认知监控器将当前隐藏状态轨迹
与良性基线比较，
异常时驱动联合键安全示例检索
（原则~\ref{prin:joint-retrieval}）。

\textbf{``机器速度''的操作定义。}
同一攻击分布上的首个攻击可能通过；
异步环在裁判集成达成共识后
于亚分钟级生成并部署新策略，
使后续同分布请求被拦截。
```

---

## 7. 新关键机制小节

### 7.1 同步/异步分层

```latex
\subsection{同步网关与上下文混淆}
\label{sec:sync-gateway}

上下文混淆引擎在同步路径上
主动污染攻击者的操作空间。

\textbf{ID 对齐。}
在上下文到达基座 LLM 前，
剥离或规范化实例标识符，
阻止攻击者通过 ID 定界抽取敏感片段。

\textbf{密码学诱饵注入。}
注入带哈希派生结构的诱饵字段。
良性交互中碰撞概率极低。
诱饵命中采用\emph{分级响应}：
低保真触碰触发意图重校验（警告），
高保真抽取触发会话终止（阻断）。
FPR 由诱饵命中密度与风险评分控制。

\textbf{末轮安全提醒。}
多轮交互的最后一轮
将安全指令强制附加为系统提示，
减少会话末期的安全策略漂移。

\subsection{异步反馈环与裁判集成}
\label{sec:async-loop}

\textbf{混合裁判集成。}
多个异构裁判（如 Llama-Guard~\cite{llama-guard}、
StrongREJECT~\cite{strongreject} 评分协议、
任务专用分类器）仅于\emph{共识}时
将反馈传入策略合成。
所有裁判接收\emph{规范化输入}
（剥离特殊 Token、统一空格），
防止格式注入扭曲判决~\cite{llm-safety-robustness}。

\textbf{分布评估。}
每变体多次采样，
估计经验失败概率分布；
仅当该分布显著超阈值时
触发策略更新~\cite{apst}。

\textbf{规范锚定加权。}
裁判输出与预定义安全规则对照；
呈现社会学失稳或规则冲突的输出
获零权重。

\textbf{强制人工校准。}
固定比例高置信度决策
随机抽选供人工审计，
作为零日共识失败的周期性纠偏。
```

### 7.2 防御性降级 + 异构恢复

```latex
\subsection{防御性降级与异构恢复}
\label{sec:degradation}

当检测到多重规则冲突或强社会学压力时，
系统启动\textbf{防御性降级}：
放弃复杂推理，回退至无状态模板拒绝，
阻断模型的合理化尝试。

\textbf{降级沙箱。}
为保持同步 API 契约，
模板拒绝触发后网关将会话路由至
\emph{受限降级沙箱}：
低权限无状态 SLM 或只读模式，
仅产出格式化输出，
拒绝任何外部 API 或工具调用。

\textbf{异构恢复通道。}
降级引入算法级 DoS 风险
（攻击者故意触发降级）
和侧信道 Oracle 风险
（拒绝边界可被探测）。
为缓解可用性损失，
向人类操作员或符号逻辑层的
异步移交在带外运行，
不阻塞同步路径。

\textbf{安全性-可用性权衡的显式化。}
降级\emph{不是}安全避风港。
它是一个有成本的操作——
以可用性换取安全性。
命题~\ref{prop:degradation-optimal}
表明在给定安全约束下
阈值策略最小化该成本。
```

### 7.3 联合键检索 + 风险优先覆盖式路由

```latex
\subsection{联合键检索与风险优先路由}
\label{sec:joint-retrieval}

\textbf{元认知监控器。}
并非全规模 LLM，
而是轻量级专用分类器
或白盒隐藏状态激活监控器~\cite{rep-eng}。
采用\textbf{异常检测范式}：
计算当前隐藏状态轨迹
与良性黄金基线之间的统计散度。
我们\emph{不}假定复杂社会学状态
在隐藏空间线性可分——
仅检测是否偏离安全基线。

\textbf{联合键检索。}
检索键由两个分支构成：
(a)~提示语义嵌入，
(b)~隐藏状态激活特征。

\textbf{风险优先覆盖式路由
（lexicographic override）。}
当 (b) 的异常分数超过阈值时，
检索键由 (b) 主导、(a) 被抑制，
强制检索走向安全示例而非良性话题示例。
低于阈值时 (a)、(b) 按加权正常组合。
该路由是字典序 minimax 策略的实例化：
优先最小化攻击者难以控制的信号
所驱动的风险。

\textbf{阈值的 FPR 校准。}
定理~\ref{thm:conformal-fpr}
提供分布无关的有限样本保证。
阈值在良性验证集上选定并
随部署分布变化周期性重校准~\cite{apst}。

\textbf{Track~B 极限。}
表征对齐 PBU 攻击试图使
$P_1 \approx P_0$（$D_{\min} \to 0$），
在原则上使检测不可能。
此极限在 \S\ref{sec:limitations} 明确讨论。
```

---

## 8. 新主要贡献（精炼版）

```latex
\textbf{贡献摘要。}

\begin{enumerate}
\item \emph{理论框架}。
提出解耦安全范式并给出三项设计原则。
在合理假设下证明三个定理
（无状态过滤器残余泄露、
纯语义检索意图盲区、
conformal FPR 保证）
和四个条件性命题
（查询复杂度下界、
推理深度-安全风险非单调性、
降级阈值最优性、
序贯检测延迟界）。
理论层的主张均标注形式等级
（定理/命题/设计原则），
不夸大证明范围。

\item \emph{统一架构}。
设计同步/异步分层的安全架构，
在 p99 $<$50\,ms 的同步路径上
执行上下文混淆与轻量异常检查，
在异步路径上运行裁判集成、
策略合成与受控变异。
防御性降级与异构恢复
显式处理安全-可用性权衡。

\item \emph{评估方法学与制品}。
双轨评估协议（Track~A/B）
分离部署结论与压力上界。
开源参考网关与评测脚本
支持 ASR、RSR、残余抽取 F1 等指标的独立复现。
\end{enumerate}
```

---

## 9. 新 Limitations / Boundary Conditions

```latex
\section{局限与运行边界}
\label{sec:limitations}

本框架建立在若干显式前提之上。
我们逐项陈述以便部署者评估适用性、
审稿人评估主张范围。

\subsection{资产可轮换性}

抽取经济学
（原则~\ref{prin:extraction-econ}、
命题~\ref{prop:query-lower-bound}）
假定资产具有有界、可轮换的生命周期。
对\emph{静态或永久}高价值资产
（核心 IP、PII、对齐数据），
经济学防御\emph{不充分}；
须依赖访问控制与信息流追踪作为硬屏障。

\subsection{降级的可用性成本}

防御性降级阻断认知劫持，
但引入两类风险：
\emph{算法级 DoS}——
攻击者故意触发降级以耗尽可用性预算；
\emph{侧信道 Oracle}——
拒绝触发边界可被探测。
异构恢复通道缓解但不消除这些风险；
速率限制与每会话预算须考虑此类滥用。

\subsection{表征监控的统计局限}

\textbf{并非意图解读。}
隐藏状态异常检测是统计偏离信号，
\emph{不等于}解读模型的``内部意图''。
有效性取决于安全基线的质量。

\textbf{Conformal 保证的边际性。}
定理~\ref{thm:conformal-fpr} 仅保证
\emph{边际覆盖}（marginal coverage）。
在特定输入子群上 FPR 可能偏离 $\alpha$。

\textbf{自适应攻击的极限。}
命题~\ref{prop:detection-delay} 的检测延迟界
假设 $P_1$ 固定。
表征对齐 PBU 攻击可使
$D_{\min} \to 0$，
在原则上使检测不可能。
这是本框架在 Track~B 压力模型下的理论极限。

\subsection{人类监督的不可或缺性}

元认知监控器与裁判集成引出
``谁来监督监督者''的问题。
我们承认完全自治的机器安全防御当前不可达。
该回归通过人类判断终止——
由强制人工校准（\S\ref{sec:judge}）
与异构恢复（\S\ref{sec:degradation}）实例化。
架构\emph{并未消除}人力依赖，
而是将人类干预从实时关键路径
移至离线异步治理层。

\subsection{部署范围约束}

本文评估基于英文为主的标准基准。
对多语言、多模态或特定行业场景
（如医疗、法律），
安全规则、基线分布和诱饵设计
均需领域适配。
参考实现为概念验证，
生产部署须考虑工程化加固。
```

---

## 10. 全论文 Section Outline

```
1  Introduction
   1.1  核心困境：安全建立在被攻击的认知之上
   1.2  三类失效模式（PBU/任务重构、HPM、语义检索劫持）
   1.3  解耦安全范式（架构重建 + 受控变异）
   1.4  贡献摘要

2  Problem Definition & Threat Model
   2.1  问题设定与核心研究问题
   2.2  威胁模型（攻击者访问、能力、目标）
   2.3  防御目标（经济不对称 + 可用性 + 自适应）

3  Theoretical Framework: Decoupled Safety Principles
   3.1  抽取经济学（Extraction Economics）
        - 定理：无状态过滤器残余泄露
        - 原则：C_attack > V(t) 判据
        - 命题：查询复杂度信息论下界
   3.2  规范锚定与防御性降级（Norm Anchoring & Defensive Degradation）
        - 命题：推理深度-安全风险非单调性
        - 原则：三层规范锚定设计
        - 命题：降级阈值 Neyman-Pearson 最优性
   3.3  表征异常与风险优先联合检索
        （Representation Anomaly & Risk-Prioritized Joint Retrieval）
        - 定理：纯语义检索意图盲区
        - 原则：联合键 + lexicographic override
        - 定理：Conformal FPR 保证
        - 命题：序贯检测延迟界
   3.4  理论定位声明

4  System Architecture
   4.1  架构总述（同步网关 + 异步反馈环）
   4.2  同步网关与上下文混淆
        - ID 对齐
        - 密码学诱饵注入与分级响应
        - 末轮安全提醒
   4.3  混合裁判集成与策略合成
        - 多裁判共识
        - 输入规范化
        - 分布评估
        - 规范锚定加权
        - 强制人工校准
   4.4  防御性降级与异构恢复
        - 降级沙箱
        - 异构恢复通道
        - 安全-可用性权衡显式化
   4.5  联合键检索与风险优先路由
        - 元认知监控器（异常检测范式）
        - 联合键构成
        - Lexicographic override 路由
        - FPR 校准
   4.6  反馈环、治理与证据链

5  Evaluation Protocol & Experiments
   5.1  经验主张的范围与证据类型
   5.2  双轨评估（Track A 黑盒 / Track B 白盒）
   5.3  数据集分类（资产类型 × 交互形态）
   5.4  攻击基线（8 类）
   5.5  防御基线（6 类）
   5.6  数据集划分与信息泄漏控制
   5.7  指标操作定义（RSR/ASR/F1/FPR/延迟/代价）
   5.8  统计报告协议
   5.9  最小可验证实验包
   5.10 参考实现与实证结果

6  Limitations & Boundary Conditions
   6.1  资产可轮换性
   6.2  降级的可用性成本
   6.3  表征监控的统计局限
   6.4  人类监督的不可或缺性
   6.5  部署范围约束

7  Related Work

8  Conclusion

Appendix
   A  评估矩阵与复现清单
```

---

## 附表：原稿核心元素对照

| # | 原稿元素 | 是否保留 | 如何改写 | 为什么 |
|---|---------|---------|---------|--------|
| 1 | 标题"超越模型反思" | **替换** | 改为更具体、信息密度更高的标题（候选 A/E） | 原标题过于宏大，未传达核心技术贡献；审稿人从标题应能判断论文做了什么 |
| 2 | 摘要（~300 词） | **重写** | 压缩至 ~200-250 词；结构化为 问题→方法→三原则→架构→评估→结果→局限 | 原摘要列举过多细节，缺少层次；顶会摘要应在 4-5 句内传达核心 idea |
| 3 | "范式转移"措辞 | **降调** | 改为"我们提出解耦安全范式"，不用"必须改变范式" | 审稿人反感过度宣称；自称范式转移需要极强证据 |
| 4 | 三类失效模式分析 | **保留** | 精简为引言中的三段，每段聚焦一类失效 | 这是论文核心论证的起点，但原文过于冗长 |
| 5 | 定理 3.1（语义防火墙残余泄露） | **保留为定理** | 不变——在无状态+非零错误率假设下可严格证明 | 证明自包含、假设合理、结论可验证 |
| 6 | 原则 3.2（抽取经济学 $C>V$） | **保留为原则** | 明确标注为操作化判据（非数学定理） | 该不等式定义了"防御成功"的含义，是设计准则 |
| 7 | 命题 3.3（查询复杂度下界） | **保留为命题** | 不变——条件性结论，依赖 Assumption E | $\overline{I}(D)$ 的具体形式需经验估计 |
| 8 | 命题 3.4（认知非单调性） | **保留为命题** | 重写为更精确的数学形式 $R(k_2)>R(k_1)$ | 原文表述偏叙事；形式化后可与实验对接 |
| 9 | 原则 3.5（规范锚定三层） | **保留为原则** | 精简为三要点列表 | 设计原则不应伪装成定理 |
| 10 | 命题 3.6（降级阈值最优性） | **保留为命题** | 标注为 Neyman-Pearson 的应用 | 条件最优性——依赖 $g(x)$ 的代理质量 |
| 11 | 定理 3.7（检索意图盲区） | **保留为定理** | 不变——embedding 几何论证成立 | 在通用 embedding 假设下可严格证明 |
| 12 | 原则 3.8（联合检索） | **保留为原则** | 精简，突出 lexicographic override | 设计准则，不是数学结论 |
| 13 | 定理 3.9（Conformal FPR） | **保留为定理** | 不变——conformal prediction 标准结论 | 分布无关、有限样本保证，审稿人必须接受 |
| 14 | 命题 3.10（检测延迟） | **保留为命题** | 标注为 CUSUM 理论的引用 | 条件界——依赖 $P_1$ 固定假设 |
| 15 | "元认知监控"术语 | **保留但加限定** | 始终附加"异常检测范式"说明 | 避免暗示模型有"元认知"；强调是统计信号 |
| 16 | "受控变异"概念 | **保留但降调** | 重新定位为"安全监控下的示例引导变异" | 避免联想到"自主进化"等过度宣称 |
| 17 | "对抗性合理化"描述 | **保留** | 保持原文准确引用 [hpm-jailbreak] | 有实证支持的观察 |
| 18 | "物理学"类隐喻 | **已移除** | 确保不残留 | 审稿人会视为不严谨的类比 |
| 19 | 双轨评估（Track A/B） | **保留** | 增加更明确的分离说明 | 这是论文的方法论贡献之一 |
| 20 | "机器速度"术语 | **保留但操作化** | 给出明确定义（亚分钟级策略更新） | 原文定义偏模糊 |
| 21 | 同步/异步分层 | **保留** | 增加延迟预算的量化说明 | 系统安全审稿人关注工程可行性 |
| 22 | "良性黄金基线" | **保留但限定** | 附加"设计者定义的规范性标准，非客观真理" | 预防审稿人质疑"谁定义良性" |
| 23 | 人工校准支路 | **保留** | 更明确地定位为"认识论回归的终止条件" | 预防"谁来监督监督者"质疑 |
| 24 | Limitations 节 | **扩展** | 新增部署范围约束、conformal 边际覆盖局限 | 顶会审稿人重视 honest limitations |
| 25 | "本体"相关术语 | **已替换** | 全部替换为"规范锚定" | "本体"在 AI 领域有特定含义，易误解 |
| 26 | 相关工作节 | **保留** | 增加与 selective prediction、conformal 文献的连接 | 理论升级后需更新理论对标 |
