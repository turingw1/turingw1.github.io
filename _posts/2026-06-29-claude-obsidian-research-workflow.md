---
title: "How I read papers and grow ideas with Claude + Obsidian"
date: 2026-06-29
permalink: /posts/2026/06/claude-obsidian-research-workflow/
excerpt: "A practical, honest workflow for literature review: Claude compresses and interrogates papers, Obsidian remembers the structure and links, and the judgment stays with me."
tags:
  - research
  - reading
  - Claude
  - Obsidian
  - note-taking
---

<div class="lang-switch">
  <a href="#zh">中文</a>
  <a href="#en">English</a>
</div>

> 分享一套我自己在用的论文工作流。一句话讲清分工：**Claude 负责压缩和追问，Obsidian 负责记忆和连接，判断始终是我自己的。** 三者缺一不可，但顺序不能反——让 AI 替你读可以，替你相信不行。

## 中文版 {#zh}

### 一、为什么是"Claude + Obsidian"，而不是只用其中一个

我做的是生成模型 / 世界模型方向，arXiv 每天刷新一大批，读不完是常态。试过很多工具后，我把角色拆成了三块：

- **Claude**：把一篇 20 页的论文压成我能 5 分钟吃透的结构，并且能被我反复"追问"；
- **Obsidian**：把这些理解**长期存下来、连起来**——它是我的外部记忆；
- **我**：负责判断哪些可信、哪些是坑、哪些能组合出新东西。

只用 Claude，读完就忘、无法沉淀；只用 Obsidian，面对原始 PDF 还是慢。两者合起来才顺。

<figure class="ac-figure">
  <img src="/images/blog/claude-obsidian-pipeline.svg" alt="从论文源到 Claude 到 Obsidian 笔记再到 idea 综合的流水线">
  <figcaption>整体流水线：Claude 起草，Obsidian 记忆，你做决定。灰色部分交给 AI，青色部分留给自己。</figcaption>
</figure>

### 二、Claude 阶段：把"读"变成"追问"

我不会让 Claude 直接给我一段总结就完事——那样很容易被它的自信带偏。我的用法是**分层追问**：

1. **先 skim**：给它 PDF，让它输出一段 TL;DR + 三条核心贡献。
2. **抽取结构**：让它分别列出 *problem / method / key results*，尽量引用原文的句子和数字。
3. **跨论文对比**：把两三篇相关工作一起丢进去，让它做一张对比表（谁解决了什么、假设差在哪）。
4. **反向质疑**：这一步最关键——让它扮演审稿人挑毛病："消融够不够支撑主张？""换成 mismatched 条件会怎样？"

我常用的几个 prompt：

```text
# 结构化摘要
用 TL;DR / Problem / Method / Key results 四段总结这篇，
每段尽量引用原文数字，不确定的地方标出来。

# 对比
把这几篇放到一张表里：解决的问题、核心假设、和前作的区别、局限。

# 当审稿人
扮演一个挑剔的审稿人，列出 3 个这篇最站不住脚的地方，
以及各自可以用什么实验证伪。
```

> ⚠️ 一条铁律：**Claude 的输出永远要回原文核对再入库**。它负责帮我"读得快"，不负责替我"相信"。凡是要写进笔记的数字和结论，我都会翻回 PDF 对一眼。

### 三、落成一篇 literature note：模板决定复利

读完的理解必须落成一条结构固定的笔记，未来才能被检索和连接。我一篇论文一条 note，模板长这样：

<figure class="ac-figure">
  <img src="/images/blog/literature-note-anatomy.svg" alt="一条文献笔记的结构：元数据、Claude 起草的摘要、我自己的批判与链接">
  <figcaption>笔记解剖图：Claude 填灰色，我填青色。<strong>"My take"和链接才是真正属于你的部分。</strong></figcaption>
</figure>

```markdown
---
tags: [world-model, diffusion, video-gen]
year: 2026
status: read          # to-read / reading / read
rating: ★★★★
---

# Physics-guided Video Diffusion

**TL;DR** — 用光流条件化视频模型，让长时序里的接触更稳定。

## Problem · Method · Results
- 问题：长时序运动漂移、接触错误
- 方法：把光流作为显式条件注入
- 结果：长时序一致性 benchmark 上 +X

## My take ✎
消融偏薄——到底是"用了光流"还是"参数更多"？
可以用 mismatched flow 做对照实验验证。

## Links
[[flow-conditioning]] · [[error-accumulation-in-distillation]] · [[idea-03]]
```

关键是最后两块：**"My take"是你自己的批判**，链接 `[[ ]]` 把这篇和你已有的想法接上。前面的摘要 Claude 能帮你写九成，这两块只能自己来——而它们恰恰是日后长出 idea 的地方。

### 四、idea 分析：让链接和 AI 一起帮你找空白

当 vault 里攒了几十条 note，真正的价值开始显现。我的做法：

- 用 **MOC（Map of Content）** 给每个方向建一页综述，把相关 note 用 `[[ ]]` 串起来；
- 打开 Obsidian 的 **Graph view**，孤立的节点、稀疏的连接，往往就是文献里的空白；
- 再把某个方向下的几条 note 一起交给 Claude，让它找**矛盾点 / 空白 / 可组合点**，生成候选 idea——我只做筛选。

举个真实发生过的例子：我有一条 note 讲"如何验证模型是否真的用了物理条件"，另一条讲"少步蒸馏里的误差累积"。把它们放一起追问时，冒出一个交叉问题——*蒸馏到少步之后，模型对物理条件的利用会不会先崩？* 这就是一个可以直接设计对照实验的 idea。**Graph 负责让它们相邻，Claude 负责点破，我负责判断值不值得做。**

### 五、几条我踩过坑后总结的原则

- **别让 Claude 直接写进 vault**：先核对再手动落笔，笔记的可信度是复利，污染一次很难清。
- **笔记要短、链接要多**：一条 note 讲清一件事，靠 `[[ ]]` 而不是长文来承载关系。
- **可溯源**：结论旁边留一句出处（页码/图号），未来的你会感谢现在的你。
- **注意隐私**：未发表的核心想法要不要喂给云端服务，是个人权衡——涉及双盲在投的内容我会格外克制。

**总结**：Claude 让我读得快、问得深，Obsidian 让理解不流失、能连接，而 idea 来自"被链接放到一起、被追问点破、最后由我拍板"的那一刻。工具负责搬运和起草，判断这件事，别外包。

---

## English version {#en}

> A workflow I actually use. The division of labour in one line: **Claude compresses and interrogates, Obsidian remembers and connects, and the judgment stays mine.** Let the AI read for you — never let it believe for you.

### 1. Why both, not either

I work on generative / world models, where arXiv refreshes faster than anyone can read. I split the job into three roles: **Claude** turns a 20-page paper into a 5-minute structure I can interrogate; **Obsidian** stores and links that understanding as external memory; and **I** decide what's trustworthy, what's a trap, and what combines into something new. Claude alone and you forget it by tomorrow; Obsidian alone and the raw PDF is still slow. Together they click.

<figure class="ac-figure">
  <img src="/images/blog/claude-obsidian-pipeline.svg" alt="A pipeline from paper sources through Claude to Obsidian notes and idea synthesis">
  <figcaption>The pipeline: Claude drafts, Obsidian remembers, you decide. Grey is delegated; teal stays with you.</figcaption>
</figure>

### 2. The Claude stage: turn *reading* into *interrogating*

I never accept a single summary and move on — that's how you get led astray by a confident model. I ask in layers: (1) a **TL;DR + three contributions**; (2) a structured **problem / method / key results**, quoting the paper's own numbers; (3) a **comparison table** across two or three related papers; and (4) the crucial one — **play a harsh reviewer** and attack the weakest claims.

```text
Summarize this as TL;DR / Problem / Method / Key results,
quoting the paper's own numbers; flag anything you're unsure of.

Put these papers in one table: problem solved, core assumption,
difference from prior work, limitations.

Act as a critical reviewer: list the 3 weakest points and,
for each, an experiment that would falsify it.
```

> ⚠️ One hard rule: **everything from Claude gets checked against the source before it enters the vault.** It helps me read fast; it does not get to believe on my behalf.

### 3. One literature note: the template is where compounding happens

Every paper becomes one note with a fixed shape, so it stays searchable and linkable later.

<figure class="ac-figure">
  <img src="/images/blog/literature-note-anatomy.svg" alt="Anatomy of a literature note: metadata, Claude-drafted summary, your critique and links">
  <figcaption>Claude fills the grey; you fill the teal. <strong>Your "take" and your links are the parts that are actually yours.</strong></figcaption>
</figure>

```markdown
---
tags: [world-model, diffusion, video-gen]
year: 2026
status: read
rating: ★★★★
---

# Physics-guided Video Diffusion

**TL;DR** — flow-conditioning keeps contacts stable over long horizons.

## Problem · Method · Results
- drift & broken contacts → inject optical flow as explicit condition → +X on the consistency benchmark

## My take ✎
Ablation is thin — is it the flow, or just more params?
Test with mismatched flow.

## Links
[[flow-conditioning]] · [[error-accumulation-in-distillation]] · [[idea-03]]
```

The last two blocks matter most: **"My take" is your own critique**, and the `[[ ]]` links wire this paper into what you already think. Claude can draft the summary; only you can write those two — and they're exactly where future ideas grow.

### 4. Growing ideas: let links and the AI find the gaps

Once the vault holds a few dozen notes, the payoff starts. I keep a **Map of Content** per direction, watch the **graph view** for isolated nodes and thin connections (usually the gaps in the literature), and hand a cluster of notes to Claude to surface **contradictions / gaps / combinable pieces** — then I filter.

A real example: one note on *how to verify a model actually uses its physical conditions* sat next to another on *error accumulation in few-step distillation*. Interrogating them together produced a cross-question — *after distilling to few steps, does the model's use of physical conditions collapse first?* — which is directly an experiment I can design. **The graph makes them adjacent, Claude names the tension, I decide if it's worth doing.**

### 5. Principles I learned the hard way

**Never let Claude write straight into the vault** — verify, then type it yourself; note trust compounds and is hard to clean once polluted. **Keep notes short, links many.** **Stay traceable** — leave the page/figure a claim came from. And **mind privacy** — whether to feed unpublished core ideas to a cloud service is a personal call; for anything under double-blind review I'm especially careful.

**In short:** Claude lets me read fast and probe deep, Obsidian keeps understanding from leaking away, and ideas arrive at the moment two linked notes are placed together, a question is sharpened, and I make the call. Delegate the hauling and the drafting — never the judgment.
