# Chapter 05: 注意力机制的直觉理解

> **预计学习时间：约 30 分钟**

## 学习目标

1. 理解注意力机制要解决的核心问题（为什么需要 Attention）
2. 掌握 Query、Key、Value 三个核心概念的直觉含义
3. 手动实现 Scaled Dot-Product Attention 的完整计算流程
4. 理解 Self-Attention 的工作原理并实现一个完整模块
5. 掌握因果掩码（Causal Mask）在自回归生成中的作用

## 核心知识点

1. **为什么需要 Attention** — RNN/LSTM 的瓶颈：串行处理 + 长距离遗忘，Attention 让每个词直接"看到"所有其他词
2. **Q/K/V 三元组** — Query（我在找什么）、Key（我的标签是什么）、Value（我的内容是什么），类比搜索引擎或数据库查询
3. **Scaled Dot-Product Attention** — 四步计算：Q@K^T → 缩放 → Softmax → 加权求和 V
4. **为什么要缩放** — 防止点积值过大导致 Softmax 梯度消失，除以 sqrt(d_k)
5. **Self-Attention** — Q/K/V 来自同一个序列，通过线性变换投影到不同空间
6. **因果掩码** — 用下三角矩阵阻止模型"偷看"未来的 token，是 GPT 等自回归模型的关键
7. **注意力权重可视化** — 通过热力图观察模型在关注哪些词

## 学习路径

```
Chapter 04 (词嵌入)
    ↓
Chapter 05 (注意力机制) ← 你在这里
    ↓
Chapter 06 (Transformer 架构全景)
    ↓
Chapter 07 (位置编码)
```

## 文件结构

```
Chapter-05/
├── chapter-05.ipynb          # 理论 + 实践合一的 Jupyter Notebook
├── README.md                 # 本文件
├── generate_diagrams.py      # 图表生成脚本
└── images/
    ├── attention_analogy.png        # 注意力机制 = 搜索引擎类比
    ├── attention_computation.png    # Scaled Dot-Product Attention 计算流程
    ├── self_vs_cross_attention.png  # Self-Attention vs Cross-Attention
    └── causal_mask.png              # 因果掩码可视化
```

## FAQ

**Q1: Attention 和传统的加权平均有什么区别？**
Attention 的权重是**动态计算**的，而不是固定的。每次输入不同的句子，权重分布都不同——模型根据内容自动决定"关注哪里"。

**Q2: 为什么 Q/K/V 要用不同的线性变换？**
如果 Q/K/V 完全相同，模型只能学到"自己和自己最像"。通过不同的投影矩阵，模型可以学到更丰富的关系模式，比如"动词应该关注它的主语"。

**Q3: 缩放因子 sqrt(d_k) 为什么重要？**
当维度 d_k 很大时，点积的值也会很大，导致 Softmax 输出趋近于 one-hot（梯度几乎为零）。除以 sqrt(d_k) 让值回到合理范围，保证训练稳定。

**Q4: 因果掩码只用于训练还是推理也需要？**
训练时一定需要（否则模型会作弊）。推理时，由于是逐 token 生成，天然就看不到未来，但为了与训练保持一致，通常仍会应用掩码。

**Q5: 这一章的 Self-Attention 和 Multi-Head Attention 是什么关系？**
Multi-Head Attention = 多个 Self-Attention 并行运行，每个"头"关注不同的模式。下一章（Transformer 架构全景）会详细讲解。

## 下一章预告

在 **Chapter 06: Transformer 架构全景** 中，我们将：
- 学习完整的 Transformer 编码器和解码器架构
- 理解 Multi-Head Attention 如何让模型同时关注多种模式
- 实现 Feed-Forward Network、Layer Normalization 等核心组件
- 把所有组件组装成一个完整的 Transformer Block
