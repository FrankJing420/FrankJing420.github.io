---
layout: post
title: "6Neighbour_Sampling_Strategy"
date: 2026-04-17
tags: [denoising, self-supervised, sampling, srdtrans, microscopy]
categories: research-notes
---

# 基于 SRDTrans 的创新思考：6 邻居采样策略

**核心采样模块、Patch 准备与训练流程完整解析**

---

## 1 `sampling_6neighbor.py` — 核心采样模块

这是整个系统的数学核心。

### 关键设计：对角线采样

```python
generate_mask_pair(img, split_axis)
```

输入的 patch 在 H 或 W 方向被预先扩大了 2 倍（由 `p2g_single_6neighbor.py` 的 `build_epoch_hw_samples` 完成）。`generate_mask_pair` 的作用是：

- `split_axis="h"`：H 是 $2\times\text{patch\_y}$，从中采样出 $\text{patch\_y}$ 大小的子图
- `split_axis="w"`：W 是 $2\times\text{patch\_x}$，从中采样出 $\text{patch\_x}$ 大小的子图

采样策略是**奇偶帧交替对角线**：

```
diag_map[n, t] = (base_diag[n] + t % 2) % 2
```

- 偶数帧用主对角线，奇数帧用反对角线
- **目的**：确保被采样的像素在时间维度上不与相邻帧的同位置像素重叠，从而让时间邻居 T±1 成为真正独立的监督信号

### 三种 role

```python
generate_subimages(img, mask, role="input"|"target"|"target_triplet")
```

| role | 输出 | 用途 |
|------|------|------|
| `"input"` | $[N, C, T, H, W]$ | 送入网络的输入 |
| `"target"` | $[N, C, T, H, W]$ | 6 邻居加权均值（时间邻居权重 ×2） |
| `"target_triplet"` | $[N, 3, C, T, H, W]$ + valid mask | T/H/W 三对邻居各随机选一个，分别作为独立监督目标 |

`target_triplet` 是训练实际使用的模式，三路损失分别对应时间、高度、宽度方向的邻居。

---

## 2 对角线采样详解

### 示例：原始 patch（H=8, W=4，split_axis="h"）

```
行0 [ a0 b0 c0 d0 ]  → 输出行 rr=0 的候选池（行0/行1）
行1 [ a1 b1 c1 d1 ]
行2 [ a2 b2 c2 d2 ]  → 输出行 rr=1 的候选池（行2/行3）
行3 [ a3 b3 c3 d3 ]
行4 [ a4 b4 c4 d4 ]  → 输出行 rr=2 的候选池（行4/行5）
行5 [ a5 b5 c5 d5 ]
行6 [ a6 b6 c6 d6 ]  → 输出行 rr=3 的候选池（行6/行7）
行7 [ a7 b7 c7 d7 ]
```

**diag=0 的采样结果（偶列取上行，奇列取下行）**：

```
rr=0: [ a0  b1  c0  d1 ]
rr=1: [ a2  b3  c2  d3 ]
rr=2: [ a4  b5  c4  d5 ]
rr=3: [ a6  b7  c6  d7 ]
```

规律：偶列(0,2)取偶行，奇列(1,3)取奇行 → **棋盘格 A**

**diag=1 的采样结果（与 diag=0 互补）**：

```
rr=0: [ a1  b0  c1  d0 ]
rr=1: [ a3  b2  c3  d2 ]
rr=2: [ a5  b4  c5  d4 ]
rr=3: [ a7  b6  c7  d6 ]
```

规律：偶列取奇行，奇列取偶行 → **棋盘格 B（与 A 完全互补）**

---

## 3 奇偶帧交替 + 监督目标独立性验证

**时间轴**：

```
帧t=0 (diag=0) → 采样棋盘格A：[ a0 b1 c0 d1 ]
                    ↑
帧t=1 (diag=1) → 采样棋盘格B：[ a1 b0 c1 d0 ]
                    ↑
帧t=2 (diag=0) → 采样棋盘格A：[ a0 b1 c0 d1 ]
...
```

以帧 $t=1$，输出位置 $(rr=0, cc=0) = a_1$ 为例，验证三个方向的监督目标：

- **T 方向邻居**：帧 $t=0$ 的 $(0,0) = a_0$，帧 $t=2$ 的 $(0,0) = a_0$ → $a_0$ 未被帧 $t=1$ 采样 ✓
- **H 方向邻居**：同帧 $t=1$，$(rr=0, cc=0)$ 的 $y+1$ = 原始行 2 列 0 = $a_2$；$a_2$ 在棋盘格 B 中未出现（B 在列 0 取奇行：$a_1, a_3, a_5, a_7$）✓
- **W 方向邻居**：同帧 $t=1$，$(rr=0, cc=0)$ 的 $x+1$ = 原始行 1 列 1 = $b_1$；$b_1$ 在棋盘格 B 中未出现（B 在列 1 取偶行：$b_0, b_2, b_4, b_6$）✓

**三路监督目标完全独立。**

---

## 4 最终 Loss 计算

网络输入（diag 采样子图）经过 SRDTrans 后，与三路监督目标对比：

```
[N, C, T, 4, 4] → SRDTrans → vs tgt_T [N,C,T,4,4]（时间邻居）
                              vs tgt_H [N,C,T,4,4]（H方向邻居）
                              vs tgt_W [N,C,T,4,4]（W方向邻居）

loss = (L_T + L_H + L_W) / 3
```

---

## 5 `p2g_single_6neighbor.py` — Patch 准备与损失计算工具

这个文件是训练循环的辅助工具层，提供三类功能：

### 5.1 Patch 扩展：`build_epoch_hw_samples`

每个 epoch 开始时，将原始坐标列表中的每个 patch 扩展为两个版本：
- `__h2`：H 方向扩大 2 倍（用于 `split_axis="h"` 的采样）
- `__w2`：W 方向扩大 2 倍（用于 `split_axis="w"` 的采样）

### 5.2 数据增强：`augment_single_6neighbor_tensors`

8 种空间变换（4 种旋转 × 2 种翻转），对 `input`、`target_triplet`、`valid_mask` 同步施加。

### 5.3 损失函数：`compute_single_den_loss` / `masked_l1_l2`

$$\text{loss} = 0.5 \times L_1(\text{pred}, \text{target}) + 0.5 \times L_2(\text{pred}, \text{target})$$

$$\text{total} = (\text{loss\_T} + \text{loss\_H} + \text{loss\_W}) / 3$$

---

## 6 训练/验证核心类与命令行入口

### `260408_train_and_val_collection_6n.py` — 训练/验证核心类

这是最大的文件，包含 `training_class` 和 `testing_class`。`training_class.run()` 的完整流程包含数据预处理、网络初始化、循环训练及验证。

### `260408_train_and_val_6n.py` — 命令行入口

最外层的启动脚本，负责解析参数、检查实验目录、动态导入类并执行训练流程。

---

## 7 数据流总结

```
tif 文件
  └─ train_preprocess: 切成 3D patch 坐标列表 (name_list, coordinate_list)
        └─ 每 epoch: build_epoch_hw_samples → H_2 + W_2 双倍 patch
              └─ generate_mask_pair → 对角线采样 mask
                    ├─ generate_subimages("input") → [N,C,T,H,W] → SRDTrans
                    └─ generate_subimages("target_triplet") → [N,3,C,T,H,W]
                          └─ masked L1+L2 loss (T/H/W 三路) → 反向传播
  └─ 每 epoch 末: test() → patch 推理 → stitch → PSNR/SSIM/SNR
```
