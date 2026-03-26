# Chapter 06: Transformer 架构全景

> **预计学习时间：约 30 分钟**

## 学习目标

1. 理解 Transformer 的完整架构：从输入到输出的完整流水线
2. 掌握 Transformer Block 的四大组件（Multi-Head Attention、Add & Norm、FFN、Add & Norm）
3. 理解 Encoder 与 Decoder 的核心区别（双向 vs 单向注意力）
4. 了解三种 Transformer 变体（BERT、GPT、T5）的适用场景
5. 能追踪数据在 Transformer 中每一步的形状变化

## 核心知识点

1. **Transformer 整体结构** — 输入 → Embedding + 位置编码 → N 个 Transformer Block → 输出，像流水线一样逐层加工
2. **Transformer Block** — Attention + Add&Norm + FFN + Add&Norm，输入输出形状完全一致，可以无限堆叠
3. **Multi-Head Attention** — 将 d_model 拆分成多个头并行运算，每个头学习不同的注意力模式（语法、指代、修饰等）
4. **残差连接与 LayerNorm** — 残差连接防止信息丢失，LayerNorm 稳定数值范围，两者结合让深层网络可以训练
5. **FFN（前馈网络）** — 先扩展 4 倍再压缩回来，让每个词独立处理信息（Attention 负责词间交流，FFN 负责词内消化）
6. **Encoder vs Decoder** — 结构几乎一样，唯一区别是 Decoder 使用因果 mask 阻止看到未来的词
7. **三种变体** — Encoder-only（BERT，理解）、Decoder-only（GPT，生成）、Encoder-Decoder（T5，转换），现代大模型主要用 Decoder-only

## 学习路径

```
Chapter 05 (注意力机制)
    ↓
Chapter 06 (Transformer 架构全景) ← 你在这里
    ↓
Chapter 07 (位置编码)
    ↓
Chapter 08 (分词算法)
```

## 文件结构

```
Chapter-06/
├── chapter-06.ipynb          # 理论 + 实践合一的 Jupyter Notebook
├── README.md                 # 本文件
├── generate_diagrams.py      # 图表生成脚本
└── images/
    ├── transformer_architecture.png  # Transformer 整体架构图
    ├── transformer_block.png         # Transformer Block 内部结构
    ├── encoder_decoder_variants.png  # Encoder vs Decoder 对比
    ├── data_flow.png                 # 数据流过 Transformer 的每一步
    └── why_transformer.png           # Transformer 三大优势
```

## FAQ

**Q1: Transformer Block 的输入和输出形状为什么一样？**
因为 Attention 和 FFN 都设计为保持维度不变（d_model → d_model）。这让 Block 可以像乐高积木一样无限堆叠——前一个 Block 的输出直接作为下一个 Block 的输入。

**Q2: 残差连接为什么这么重要？**
没有残差连接，深层网络容易出现梯度消失问题——反向传播时梯度越来越小，底层参数无法更新。残差连接提供了"直通车"，让梯度可以直接流回浅层。

**Q3: FFN 为什么要先扩展 4 倍再压缩回来？**
更大的中间维度提供了更多的"计算空间"来处理信息。研究发现，4 倍是性能和效率的良好平衡点。这就像在草稿纸上展开计算，最后把结果写到答题卡上。

**Q4: 为什么现代大模型几乎都用 Decoder-only？**
三个原因：(1) 生成能力最通用——任何任务都可以转化为"给定输入，生成输出"；(2) 自回归训练目标简单高效；(3) 模型越大性能提升越明显（Scaling Law 效果最好）。

**Q5: BERT 和 GPT-2 参数量差不多，为什么 GPT 系列后来比 BERT 更成功？**
BERT 需要针对下游任务微调，而 GPT 走了"大力出奇迹"路线——通过不断增大模型和数据量，发现模型可以直接通过 prompt 完成各种任务（few-shot / zero-shot），无需微调。

## 下一章预告

在 **Chapter 07: 位置编码** 中，我们将：
- 理解为什么 Transformer 需要位置编码（注意力机制本身不区分词的先后顺序）
- 学习正弦位置编码的数学原理和直觉理解
- 对比可学习位置编码 vs 固定位置编码
- 了解 RoPE（旋转位置编码）——现代大模型的主流选择
