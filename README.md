# 声音与音频大模型 · Audio Foundation Models

> 同济大学研究者，专注**声音与音频大模型**：从通用音频自监督表示、神经音频编解码，
> 到音频语言模型与可控声音生成。偏好**可复现、离线优先**的开源实现 —— 核心逻辑尽量纯
> `numpy` / 纯 PyTorch，零重依赖、CPU 可跑通、确定性单测，`torch` 与真实权重作为可选后端接入。

![Focus](https://img.shields.io/badge/focus-audio%20foundation%20models-6f42c1)
![Representation](https://img.shields.io/badge/self--supervised-representation-blue)
![Codec](https://img.shields.io/badge/neural-codec%20%2B%20RVQ-1f6feb)
![Audio--LM](https://img.shields.io/badge/audio-language%20model-0a7ea4)
![Generation](https://img.shields.io/badge/controllable-generation-e36209)
![Reproducible](https://img.shields.io/badge/reproducible-offline--first-2ea44f)

---

## 研究主线

我把「声音大模型」这条链路拆成四个可独立复现、又能相互衔接的开源组件，覆盖
**表示 → 离散化 → 理解 → 生成**的完整闭环：

```
                  ┌────────────────────────────────────────────────────────────┐
   原始音频 ─────▶│  resona   自监督表示：语音 / 音乐 / 环境声的通用音频基座      │
                  └───────────────────────────┬────────────────────────────────┘
                                               ▼
                  ┌────────────────────────────────────────────────────────────┐
   连续音频 ─────▶│  soniq    神经编解码 + RVQ：把波形压成离散音频 token          │
                  └───────────────────────────┬────────────────────────────────┘
                          离散 token / 连续嵌入 ▼
        ┌──────────────────────────────┐   ┌──────────────────────────────────┐
        │  auris   音频语言模型（理解）│   │  vocca   可控声音语言模型（生成）│
        │  描述 / 问答 / 检索 / 分类   │   │  文本·提示 → 情感·风格·音色可控   │
        └──────────────────────────────┘   └──────────────────────────────────┘
```

## 精选项目

| 项目 | 定位 | 一句话简介 |
|------|------|-----------|
| **[resona](https://github.com/LX588387/resona)** | 自监督表示 | 在同一 Transformer 骨干上统一**掩码频谱建模 / 对比 / 自举（BYOL 风格）**三种自监督方法，自带纯 PyTorch log-mel 前端与线性探针 / kNN 评估协议，为语音·音乐·环境声学习通用表示。 |
| **[soniq](https://github.com/LX588387/soniq)** | 神经编解码 | 低比特率神经音频编解码器：SEANet 风格卷积编解码器 + **残差矢量量化（RVQ）**，把连续波形离散成整数 token；支持可变比特率（quantizer dropout）与 kbps 计算工具，服务音频语言模型。 |
| **[auris](https://github.com/LX588387/auris)** | 音频理解 | 结构清晰的**音频语言模型（Audio-LM）**框架，用「编码器 → 投影器 → LLM 后端」三段式接入音频，开箱即用地支持**理解 / 描述（captioning）/ 问答 / 音频-文本检索**；核心纯 numpy、零下载确定性单测。 |
| **[vocca](https://github.com/LX588387/vocca)** | 可控生成 | 文本 / 提示驱动的**可控声音生成**框架：情感 / 风格 / 音色可控，**分类器无关引导（CFG）** + KV cache + **流式推理**（分块与整段解码逐样本相等），把可控生成的机制做对、做透、可测。 |

> 说明：各项目定位于把机制「做对、可复现、可测」的基础实现。`vocca` 默认使用固定种子随机初始化权重、
> 产出结构化合成音，真实权重可通过 `.npz` 载入，编解码器可替换为 `soniq` 等神经声码器，接口保持不变。

## 技术栈与方法

![Python](https://img.shields.io/badge/Python-3.9%2B-3776AB?logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-optional%20backend-EE4C2C?logo=pytorch&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-core-013243?logo=numpy&logoColor=white)
![Ruff](https://img.shields.io/badge/lint-ruff-000000)
![mypy](https://img.shields.io/badge/typed-mypy-2A6DB2)
![CI](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?logo=githubactions&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-green)

- **表示学习**：掩码自编码（MAE 风格）、InfoNCE 对比学习、BYOL 自举、线性探针 / kNN 评估
- **音频离散化**：SEANet 卷积编解码、残差矢量量化（RVQ）、可变比特率、直通估计
- **音频语言模型**：编码器 / 投影器 / LLM 后端的可插拔协议，captioning / QA / retrieval 评测（BLEU / ROUGE-L / CIDEr、recall@k / mAP）
- **可控生成**：条件注入、CFG、采样、KV cache 增量解码、流式推理、可插拔编解码器
- **工程实践**：纯 numpy / 纯 PyTorch 核心、零重依赖、确定性单测、CI + ruff + mypy + pre-commit

## 关于

- 🎧 研究方向：声音与音频大模型 —— 表示 · 编解码 · 理解 · 生成
- 🧪 偏好：可复现、离线优先、CPU 可跑通、接口清晰可替换
- 🌱 正在做：打磨上述四个组件的接口一致性，让 `resona → soniq → auris / vocca` 能端到端串联

<sub>本主页为对应 GitHub 账号已有开源仓库的索引与概述。</sub>
