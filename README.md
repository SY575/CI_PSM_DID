# Competitive Isolation PSM-DID: Unbiased Platform-Level Causal Estimation for Search Systems

[English](#english) | [中文](#chinese)

<a name="chinese"></a>

## 📖 简介（中文）

本项目为ACM SIGKDD 2026发表的论文实现及数据集，提出了**Competitive Isolation PSM-DID框架**，用于在搜索型双边市场中进行无偏的**平台级因果推断**。

传统的PSM-DID方法在双边市场中面临两大挑战：
1. **跨单位干扰**：商品间的竞争效应导致SUTVA假设违反，造成流量争夺（baseline: 5.7%）
2. **选择偏差**：缺乏系统的方法应对商品间的竞争干扰

本框架通过**三大创新**解决这些问题：
- 🔗 **互斥性图分割**：使用min-cut算法隔离竞争商品
- 🎯 **同质项目挖掘**：分层CTCVR匹配确保平行趋势
- ⚖️ **双边下沉机制**：维持市场完整性同时隔离实验组

**关键成果**：在600K日订单规模上实现80%方差降低，流量争夺从2.0%降至0.1%

---

<a name="english"></a>

## 📖 Introduction (English)

This repository contains the implementation and dataset for the paper accepted by ACM SIGKDD 2026, presenting the **Competitive Isolation PSM-DID Framework** for unbiased platform-level causal inference in search-based two-sided marketplaces.

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
└─ 机制：双边下沉 (Two-sided sinking)

Phase 4: DID估计 (DID Estimation)
├─ 前期差分：D_0 = Y(A+C|_B) - Y(B+C|_A) at T_0
├─ 后期差分：D_1 = Y(A'+C|_B) - Y(B+C|_A') at T_1
└─ 因果效应：τ̂ = D_1 - D_0
```

### 理论保证 / Theoretical Guarantees

**定理**：在以下条件下，CI-PSM-DID估计量 ≡ 完美A/B测试效应
1. 互斥性：Γ_B(A) = Γ_A(B) = 0（无处理-对照竞争）
2. 平行趋势：pre-treatment处理组和对照组趋势一致

**关键公式**：
$$\hat{\tau} = [Y(A'+C)|_{\overline{B},T_1} - Y(B+C)|_{\overline{A'},T_1}] - [Y(A+C)|_{\overline{B},T_0} - Y(B+C)|_{\overline{A},T_0}]$$

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

### 计算性能 / Computational Performance

| 步骤 | 时间 | 资源 |
|------|------|------|
| Min-cut分割 | ~10分钟 | 单机8核, 16GB RAM |
| 分层匹配 | <1分钟 | 线性复杂度 |
| DID估计 | <1分钟 | 简单聚合 |
| **总耗时** | **~11分钟** | **可快速迭代** |

---

## 💻 使用说明 / Usage Guide

### 环境配置 / Installation

```bash
# 克隆仓库
git clone https://github.com/SY575/CI_PSM_DID.git
cd CI_PSM_DID

# 创建虚拟环境
conda create -n ci_psm_did python=3.9
conda activate ci_psm_did

# 安装依赖
pip install -r requirements.txt
```

**依赖包**：
- pandas, numpy：数据处理
- scikit-learn：机器学习基础
- scipy：图算法
- matplotlib, seaborn：可视化

### 快速开始 / Quick Start

#### 1. 准备数据
```python
import pandas as pd
from ci_psm_did import Pipeline

# 加载数据（商品-请求日志）
# 格式：item_id, user_id, timestamp, metric (orders/GMV)
df = pd.read_csv('data/marketplace_requests.csv')

# 构建竞争图
from ci_psm_did.graph import build_competition_graph
graph = build_competition_graph(df, min_co_occurrence=10)
```

#### 2. 执行框架
```python
# 初始化管道
pipeline = Pipeline(
    target_items=['item_1', 'item_2'],  # 要测试的商品
    pre_period=('2024-01-01', '2024-01-30'),
    post_period=('2024-02-01', '2024-02-28'),
    metric='order_volume'  # or 'gmv'
)

# Phase 1: 互斥性分割
partitioned_graph = pipeline.mutual_exclusion_partition(graph)

# Phase 2: 同质项目挖掘
homogeneous_items = pipeline.homogeneous_item_mining(partitioned_graph)

# Phase 3-4: DID估计
causal_effect = pipeline.did_estimation()
print(f"平台级因果效应: {causal_effect:.4f}")
```

#### 3. 评估与可视化
```python
# 获取详细结果
results = pipeline.get_results()
print(results)

# 绘制结果
from ci_psm_did.visualization import plot_framework
plot_framework(results, save_path='results/framework.png')
```

### 完整示例 / Full Example

详见 `examples/` 目录：
- `example_offline_evaluation.py`：离线评估完整流程
- `example_online_evaluation.py`：在线实验设置
- `example_visualization.py`：结果可视化

```bash
python examples/example_offline_evaluation.py --data data/marketplace.csv
```

---

## 📦 数据集信息 / Dataset Information

### 数据集说明

**CI_PSM_DID Dataset** 是首个用于市场干扰分析的公开数据集

#### 数据规模
- 商品数量：1.2 百万
- 请求记录：1.2 亿条
- 时间跨度：180天
- 类别数：20,027个

#### 数据结构
```
dataset/
├── marketplace_requests.csv          # 主数据集
│   ├── item_id (int): 商品ID
│   ├── user_id (int): 用户ID  
│   ├── timestamp (datetime): 请求时间
│   ├── impression (int): 曝光次数
│   ├── click (int): 点击次数
│   ├── transaction (int): 交易数
│   ├── gmv (float): 交易额
│   └── category (str): 商品类目
│
├── item_features.csv                 # 商品特征
│   ├── item_id (int)
│   ├── price (float)
│   ├── category_l1/l2/l3 (str)
│   ├── seller_id (int)
│   └── historical_orders (int)
│
├── competition_graph.json            # 竞争图结构
│   ├── nodes: 商品列表
│   └── edges: 竞争关系 (weight为共现次数)
│
└── ground_truth.csv                  # 离线评估真值
    ├── item_id (int)
    ├── causal_effect (float)
    ├── 7day_order_gap (float)
    └── 30day_order_gap (float)
```

#### 下载与使用

```bash
# 从GitHub下载（推荐）
wget https://github.com/SY575/CI_PSM_DID/releases/download/v1.0/ci_psm_did_dataset.tar.gz
tar -xzf ci_psm_did_dataset.tar.gz

# 或通过脚本下载
python scripts/download_dataset.py --version 1.0 --output_dir data/
```

#### 许可证
本数据集采用Creative Commons Attribution 4.0 International (CC BY 4.0)许可证。使用时请引用原论文。

---

## 📚 相关工作对比 / Related Work Comparison

| 方法 | 商品级干扰 | 平台级估计 | 运营开销 | 适用场景 |
|------|----------|----------|---------|---------|
| **集群随机化** | ❌ | ⚠️ | 低 | 自然分割市场 |
| **时间交换设计** | ✅ | ⚠️ | 中 | 短期效应 |
| **空间-时间分割** | ✅ | ❌ | 中 | 区域营销 |
| **竞争隔离(sinking)** | ✅ | ❌ | 高 | 广告拍卖 |
| **传统PSM-DID** | ❌ | ✅ | 低 | 无干扰场景 |
| **⭐ CI-PSM-DID** | ✅ | ✅ | **低** | **通用** |

---

## 🧪 复现实验 / Reproduction

### 离线评估复现

```bash
# 运行完整离线评估（~2小时）
python experiments/offline_evaluation.py \
  --data_dir data/ \
  --output_dir results/offline \
  --sample_sizes 100K 150K 300K 600K \
  --num_repeats 30

# 预期结果：与论文Table 1匹配 (RMSE < 0.1%)
```

### 在线评估复现

```bash
# 模拟在线实验环境
python experiments/online_evaluation.py \
  --marketplace_config config/marketplace.yaml \
  --experiment_duration 30 \
  --daily_orders 600000 \
  --output_dir results/online

# 检查互斥性达成情况
python experiments/check_mutual_exclusivity.py \
  --results_dir results/online \
  --threshold 0.001  # 0.1%
```

---

## 📖 引用方式 / Citation

如果本研究对您的工作有帮助，请使用以下格式引用：

### BibTeX
```bibtex
@inproceedings{Song2026CI_PSM_DID,
  author = {Song, Ying and Wang, Yijing and Yang, Hui and others},
  title = {Unbiased Platform-Level Causal Estimation for Search Systems: 
           A Competitive Isolation PSM-DID Framework},
  booktitle = {Proceedings of the 32nd ACM SIGKDD Conference on 
              Knowledge Discovery and Data Mining},
  pages = {XX--XX},
  year = {2026},
  organization = {ACM},
  address = {Jeju, Korea}
}
```

### 其他格式

**Chicago Style:**
Song, Ying, et al. "Unbiased Platform-Level Causal Estimation for Search Systems: A Competitive Isolation PSM-DID Framework." In Proceedings of the 32nd ACM SIGKDD Conference on Knowledge Discovery and Data Mining, pp. XX-XX. ACM, 2026.

**Harvard Style:**
Song, Y., Wang, Y., Yang, H., et al., 2026. Unbiased Platform-Level Causal Estimation for Search Systems: A Competitive Isolation PSM-DID Framework. In *32nd ACM SIGKDD Conference*. pp.XX-XX.

---

## 📄 论文与文档 / Papers & Documentation

- 📑 **论文** (ACM SIGKDD 2026): [Full Text](paper/)
- 📖 **技术文档**: [详细方法说明](docs/technical_details.md)
- 🔬 **实验说明**: [离线/在线评估指南](docs/experiments.md)
- 💡 **最佳实践**: [工业应用指南](docs/best_practices.md)

---

## 💬 常见问题 / FAQ

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

## 🔗 相关资源 / Related Resources

- [双边市场因果推断综述](docs/related_surveys.md)
- [Graph Partitioning算法详解](docs/graph_algorithms.md)
- [CTCVR匹配策略对比](docs/matching_strategies.md)
- [实战案例：Alibaba搜索平台应用](docs/case_study_alibaba.md)

---

## 👥 贡献者 / Contributors

### 核心开发团队
- **Ying Song** (宋莹) - Alibaba Group, 首席研究员
- **Yijing Wang** (王贻晶) - Alibaba Group, 资深工程师
- **KaiFu Zhang** (张开复) - Alibaba Group, 负责人

### 完整作者列表
Ying Song, Yijing Wang, Hui Yang, Weihan Jin, Jun Xiong, Congyi Zhou, Jialin Zhu, Xiang Gao, Rong Chen, HuaGuang Deng, Ying Dai, Fei Xiao, Haihong Tang, Bo Zheng, KaiFu Zhang

### 致谢
感谢Alibaba Group搜索团队的支持与反馈。

---

## 📝 许可证 / License

本项目采用 **Apache License 2.0** 许可证。详见 [LICENSE](LICENSE) 文件。

数据集采用 **Creative Commons Attribution 4.0 International (CC BY 4.0)** 许可证。

---

## 📧 联系方式 / Contact

- **对论文有疑问**: 提交Issue或联系 kaifu.zkf@alibaba-inc.com
- **代码bug反馈**: [GitHub Issues](https://github.com/SY575/CI_PSM_DID/issues)
- **合作洽谈**: 欢迎提交Pull Request或联系项目主页

---

## 🌟 致谢与鸣谢

本研究获得了以下机构/人员的支持：
- ✨ Alibaba Group搜索技术委员会
- 🔬 浙江大学数据驱动团队
- 📊 ACM SIGKDD审稿专家组

**更新历史** / Update History

| 版本 | 日期 | 主要更新 |
|------|------|---------|
| v1.0 | 2026-02 | 初始版本，包含完整代码与数据集 |
| v1.1 (计划) | 2026-Q2 | 支持多处理更新的框架扩展 |
| v2.0 (计划) | 2026-Q4 | 端到端自动化管道 |

---

**⭐ 如果该项目对您有帮助，请Star本仓库！**

```
GitHub: https://github.com/SY575/CI_PSM_DID
Paper: https://arxiv.org/abs/XXXX.XXXXX (KDD 2026)
Dataset: https://github.com/SY575/CI_PSM_DID/releases
```
