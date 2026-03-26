# Chapter 08: 阶段总结——构建迷你 Transformer

> **预计学习时间：约 60 分钟**

## 学习目标

1. 回顾前 7 章学到的所有组件（Embedding、注意力、位置编码、FFN、残差连接、LayerNorm）
2. 理解如何将这些组件组装成一个完整的 Transformer 模型
3. 掌握字符级语言建模的训练流程（数据准备 → 前向传播 → 损失计算 → 反向传播 → 参数更新）
4. 理解自回归文本生成的工作原理和 Temperature 参数的作用
5. 通过可视化（损失曲线、困惑度、注意力热力图）直观观察模型的学习过程

## 核心知识点

1. **完整架构** — Transformer = Token Embedding + 位置编码 + N x TransformerBlock + LayerNorm + Output Head，本章模型约 1M 参数
2. **因果遮罩（Causal Mask）** — 下三角矩阵，确保每个位置只能看到自己和前面的 token，这是 GPT 等自回归模型的核心机制
3. **字符级语言建模** — 最简单的语言建模：给定前面的字符，预测下一个字符。GPT 的 token 级预测是同样的原理
4. **训练循环** — 采样批次 → 前向传播得到 logits → 计算交叉熵损失 → 反向传播计算梯度 → Adam 优化器更新参数
5. **自回归生成** — 从 prompt 开始，每次用模型预测下一个 token，将其拼接到序列末尾，重复直到达到目标长度
6. **Temperature 控制** — 低温（0.3）更保守确定，高温（1.5）更随机多样，中温（0.8）通常效果最好
7. **困惑度（Perplexity）** — e^loss，表示模型认为下一个 token 有多少种同等可能的选择，越低越好

## 学习路径

```
Chapter 03 (张量与神经网络)
    ↓
Chapter 04 (词嵌入)
    ↓
Chapter 05 (注意力机制)
    ↓
Chapter 06 (Transformer 架构全景)
    ↓
Chapter 07 (位置编码)
    ↓
Chapter 08 (阶段总结：构建迷你 Transformer) ← 你在这里
    ↓
Stage 2: 深入理解 Transformer (第 9-20 章)
```

## 文件结构

```
Chapter-08/
├── chapter-08.ipynb                    # 理论 + 实践合一的 Jupyter Notebook
├── README.md                           # 本文件
└── images/
    ├── mini_transformer_architecture.png  # 迷你 Transformer 完整架构图
    ├── training_task.png                  # 字符级语言建模任务示意
    ├── training_pipeline.png              # 训练循环流程图
    └── stage1_summary.png                 # Stage 1 学习路径总结
```

## FAQ

**Q1: 为什么用字符级而不是 token 级（像 GPT 那样）？**
字符级的词表只有 65 个字符（vs GPT-2 的 50257 个 token），模型可以做得很小（~1M 参数），在 CPU 上几分钟就能训练出效果。原理是完全一样的——都是"预测下一个"，只是粒度不同。

**Q2: 我们的迷你 Transformer 和 GPT-3 有什么本质区别？**
核心架构完全相同！区别只在规模：我们用 4 层、128 维、1M 参数，GPT-3 用 96 层、12288 维、175B 参数。这就是 Scaling Law 的基础——同样的架构，放大规模就能获得更强的能力。

**Q3: Temperature 是怎么工作的？**
生成时，模型输出每个字符的 logits（原始分数）。Temperature 作为除数：`logits / temperature`。低温让高分字符的概率更突出（更确定），高温让概率分布更均匀（更随机）。Temperature=1.0 就是原始分布。

**Q4: 为什么需要因果遮罩？**
因为我们要训练模型"预测下一个 token"，如果它能看到后面的 token，就等于提前知道答案。因果遮罩（下三角矩阵）确保每个位置只能注意到自己和前面的位置，这样模型只能通过已有信息来预测。

**Q5: 训练 500 步够吗？**
对于我们 3KB 的训练文本和 1M 参数的小模型来说足够了——模型已经能学到基本的词汇和句式模式。但这个模型学到的只是简单的统计规律，离真正的"理解"还很远。真正的大模型需要在数百 GB 数据上训练数月。

## 下一章预告

在 **Stage 2: 深入理解 Transformer**（第 9-20 章）中，我们将：
- 深入剖析多头注意力每个头在"看"什么（第9章）
- 理解 FFN 的记忆存储功能（第10章）
- 掌握 LayerNorm 和残差连接的训练稳定性作用（第11-12章）
- 实现完整的 Encoder/Decoder Block（第13-16章）
- 学习 Warmup、梯度裁剪、混合精度等训练技巧（第17-20章）
