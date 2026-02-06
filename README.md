# Competitive Isolation PSM-DID: Unbiased Platform-Level Causal Estimation for Search Systems

[English](#english) | [中文](#chinese)

<a name="chinese"></a>

## 📖 简介（中文）

本项目为ACM SIGKDD 2026投稿论文的实现及数据集。论文已发布在arXiv上：[https://arxiv.org/abs/2511.01329](https://arxiv.org/abs/2511.01329)

论文提出了**Competitive Isolation PSM-DID框架**，用于在搜索型双边市场中进行无偏的**平台级因果推断**。

传统的PSM-DID方法在双边市场中面临两大挑战：
1. **跨单位干扰**：商品间的竞争效应导致SUTVA假设违反，造成流量争夺（baseline: 5.7%）
2. **选择偏差**：缺乏系统的方法应对商品间的竞争干扰

本框架通过**三大创新**解决这些问题：
- 🔗 **互斥性图分割**：使用min-cut算法隔离竞争商品
- 🎯 **同质项目挖掘**：分层CTCVR匹配确保平行趋势
- ⚖️ **双边沉底机制**：维持市场完整性同时隔离实验组

**关键成果**：在600K日订单规模上实现80%方差降低，流量争夺从2.0%降至0.1%

---

<a name="english"></a>

## 📖 Introduction (English)

This repository contains the implementation and dataset for a paper submitted to ACM SIGKDD 2026. The paper is now available on arXiv: [https://arxiv.org/abs/2511.01329](https://arxiv.org/abs/2511.01329)

We present the **Competitive Isolation PSM-DID Framework** for unbiased platform-level causal inference in search-based two-sided marketplaces.

Traditional PSM-DID approaches face two fundamental challenges in two-sided marketplaces:
1. **Cross-Unit Interference**: Competition effects between items violate the SUTVA assumption, causing cannibalization (baseline: 5.7%)
2. **Selection Bias**: Lack of systematic methods to handle inter-item competition

Our framework addresses these challenges through **three core innovations**:
- 🔗 **Mutual Exclusivity Graph Partitioning**: Min-cut algorithm to isolate competing items
- 🎯 **Homogeneous Item Mining**: Stratified CTCVR matching to ensure parallel trends
- ⚖️ **Two-Sided Sinking Mechanism**: Preserve market completeness while isolating treatment groups

**Key Results**: 80% variance reduction at 600K daily orders, cannibalization reduced from 2.0% to 0.1%

---

## 🎯 核心创新点 / Core Innovations

### 1️⃣ Competitive Isolation PSM-DID框架
支持平台级因果推断（GMV、订单量），而非传统的商品级指标
- ✅ 处理商品间竞争干扰
- ✅ 保持市场完整性（相比竞争隔离方案，流量损失从15-30%降至0%）
- ✅ 理论保证无偏估计

### 2️⃣ 理论等价性证明
在互斥性和平行趋势假设下，框架等价于完美A/B测试
- 📊 Min-cut分割：流量争夺 2.0% → 0.1%
- 📉 订单量差距：30天 3.44%±2.05% → 1.36%±0.51%
- 🎯 方差降低：80% vs 基准方法

### 3️⃣ 开源数据集与工具
首个用于市场干扰分析的公开数据集，支持1.2百万商品规模
- 📁 完整的数据预处理管道
- 🧮 参考实现算法
- 📈 离线/在线评估脚本

---

## 🔧 技术方法 / Technical Methods

### 方法框架 / Framework Overview

```
Phase 1: 互斥性分割 (Mutual Exclusion Partition)
├─ 输入：商品竞争图 G = (V, E, w)
├─ 算法：Kernighan-Lin min-cut
└─ 输出：平衡子图 G_A, G_B (互斥性: 0.1%)

Phase 2: 同质项目挖掘 (Homogeneous Item Mining)
├─ 分层策略：类目、曝光、交易、价格 (4维)
├─ 匹配方式：CTCVR相似度排序
└─ 优化目标：最小化pre-treatment差距

Phase 3: 实验分组 (Experiment Grouping)
├─ 处理组：A (G_A中的目标商品)
├─ 对照组：B (G_B中的目标商品)
├─ 中性集：C_A, C_B (匹配的同质商品)
└─ 机制：双边沉底 (Two-sided sinking)

Phase 4: DID估计 (DID Estimation)
├─ 前期差分：D_0 = Y(A+C|_B) - Y(B+C|_A) at T_0
├─ 后期差分：D_1 = Y(A'+C|_B) - Y(B+C|_A') at T_1
└─ 因果效应：τ̂ = D_1 - D_0
```

### 理论保证 / Theoretical Guarantees

**定理**：在以下条件下，CI-PSM-DID估计量 ≡ 完美A/B测试效应
1. 互斥性：Γ_B(A) = Γ_A(B) = 0（无处理-对照竞争）
2. 平行趋势：pre-treatment处理组和对照组趋势一致

---

## 📊 关键性能指标 / Key Performance Metrics

### 离线评估 / Offline Evaluation

#### 图分割性能
| 指标 | 值 |
|------|-----|
| 图规模 | 20,027个叶子类别 / 67M条边 |
| 平衡性 | ±0.1% 节点数差异 |
| Min-cut容量 | 0.02 (仅2%边权被切割) |
| 互斥性达成 | 0.1% 流量争夺 |

#### 匹配性能 (Stratified CTCVR Matching)
| 规模 | 7日订单量差距 | 30日订单量差距 |
|------|---------------|----------------|
| 100K | 1.09% ± 0.81% | 2.18% ± 1.51% |
| 150K | 0.44% ± 0.33% | 1.81% ± 0.83% |
| 300K | 0.43% ± 0.34% | 1.65% ± 0.64% |
| **600K** | **0.34% ± 0.25%** | **1.36% ± 0.51%** |

**vs 传统方法**：降低60% (30日) 和 80% (方差)

### 在线评估 / Online Evaluation

| 指标 | 结果 |
|------|------|
| 流量争夺（互斥性） | 0.1% ± 0.05% (p=0.12) |
| 订单量提升 | 0.06% ± 0.15% (30天) |
| GMV提升 | 0.01% ± 0.23% (30天) |
| 可扩展性 | 支持1.2M商品, 600K日订单 |


---

## 📖 论文与文档 / Paper & Documentation

### 论文发表信息 / Paper Publication

- **arXiv预发表**: [https://arxiv.org/abs/2511.01329](https://arxiv.org/abs/2511.01329)

### 引用方式 / Citation

#### BibTeX
```bibtex
@article{song2025unbiased,
  title={Unbiased Platform-Level Causal Estimation for Search Systems: A Competitive Isolation PSM-DID Framework},
  author={Song, Ying and Wang, Yijing and Yang, Hui and Jin, Weihan and Xiong, Jun and Zhou, Congyi and Zhu, Jialin and Gao, Xiang and Chen, Rong and Deng, HuaGuang and others},
  journal={arXiv preprint arXiv:2511.01329},
  year={2025}
}
```
---

## ❓ 常见问题 / FAQ

**Q1: 框架适用于哪些场景？**

A: 适用于任何存在商品间竞争的双边市场（电商搜索、O2O平台、内容推荐等）。只需构建竞争图即可。

**Q2: 如何处理新商品或快速变化的市场？**

A: 支持增量更新。竞争图可定期重计算（日/周），分层匹配基于实时数据。

**Q3: 与A/B测试相比的优势？**

A: 
- 无需伤害某些用户体验（如同一商品不同价格）
- 可测量平台级效应，而非单个商品
- 更快速部署（无需等待实验期）

**Q4: 方差为何能降低80%？**

A: 通过互斥性分割消除流量争夺（减少干扰噪声）+ 精细的CTCVR匹配（提高对照组质量）的组合效应。

**Q5: 是否支持多个并行实验？**

A: 支持。只需确保不同实验的目标商品在图分割后不重叠即可。

---

## 👥 贡献者 / Contributors

### 完整作者列表
Ying Song, Yijing Wang, Hui Yang, Weihan Jin, Jun Xiong, Congyi Zhou, Jialin Zhu, Xiang Gao, Rong Chen, HuaGuang Deng, Ying Dai, Fei Xiao, Haihong Tang, Bo Zheng, KaiFu Zhang

---

## 📝 许可证 / License

- **代码**: Apache License 2.0
- **数据集**: Creative Commons Attribution 4.0 International (CC BY 4.0)

---

## 📧 联系方式 / Contact

- **论文相关**: kaifu.zkf@alibaba-inc.com
- **GitHub Issues**: [提交问题](https://github.com/SY575/CI_PSM_DID/issues)
- **Pull Requests**: 欢迎贡献！

---

⭐ **如果项目对您有帮助，请Star本仓库！**

GitHub: https://github.com/SY575/CI_PSM_DID
arXiv: https://arxiv.org/abs/2511.01329
