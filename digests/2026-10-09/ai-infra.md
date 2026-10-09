# AI 基础设施日报 2026-10-09

> 生成时间: 2026-10-09 06:39 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# **跨项目AI基础设施生态报告 – 2026-10-09**

---

### **1. 生态概览**  
AI推理与服务生态正进入**硬件专业化与生产级成熟化**阶段，由GPU架构的快速演进（Blackwell SM120、MI355X）以及以智能体为中心的工作负载兴起所驱动。各项目在定位上日益分化：部分聚焦底层内核优化（vLLM、llama.cpp），部分强调安全沙箱执行（Unsloth），而LiteLLM和Ollama等网关则致力于统一跨提供商与后端的访问。关键稳定性问题——尤其是FP8/KV缓存、推测性解码及后端崩溃——如今已与性能提升同等重要，标志着从单纯追求速度转向规模化下的可靠性。

---

### **2. 活跃度对比**

| 项目       | 开放问题数 (↑/↓) | 合并PR数 (↑/↓) | 最新发布 | 状态 |
|------------|-------------------|----------------|----------|------|
| **vLLM**   | 47 (+3)           | 12 (+2)        | `v0.30.1rc1` | 活跃开发 (`v0.31.0`) |
| **SGLang** | 102 (+5)          | 9 (+1)         | 无       | 高度不稳定（严重回归） |
| **llama.cpp** | 118 (+4)        | 15 (+3)        | `b11515` | 频繁修复发布 |
| **Ollama** | 187 (+6)          | 12 (+2)        | `v0.40.2` | 发布后回归问题 |
| **LiteLLM** | 89 (+2)          | 7 (+1)         | `v1.106.0-dev.2` | 安全性更新为主 |
| **Unsloth** | 124 (+3)         | 11 (+2)        | `v0.1.905-beta` | 新增沙箱功能 |

> 🔍 *观察：* 尽管新发布数量较少，**vLLM与llama.cpp** 在技术推进速度上领先，持续进行多项内核级优化与硬件特异性修复。**Ollama与SGLang** 的问题数更高，源于发布后的不稳定性，反映出部署复杂度上升带来的成长阵痛。

---

### **3. 模型支持竞赛**

| 新模型 / 架构                | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|-------------------------------|------|--------|-----------|--------|---------|---------|
| **Qwen3.8-Flash-Next**       | ✅ (ROCm/AMD, SM120) | ✅ (DeepSeek V4.1 优先) | ⚠️ (性能下降) | ✅ (已请求) | ❌ | ❌ |
| **GLM-5.3-Flash**            | ✅ (SM120, ROCm) | ⚠️ (推测解码崩溃) | ⚠️ (OOM, 断言失败) | ✅ (已请求) | ❌ | ❌ |
| **Qwen-Image-2.1-GGUF (Q4_K_M)** | ❌ | ❌ | ✅ (ROCm 修复) | ✅ (已请求) | ❌ | ✅ (ROCm + 沙箱) |
| **d1-3B / d1-omni-600M**     | ❌ | ❌ | ❌ | ✅ (已请求) | ❌ | ❌ |
| **Gemma 4 模型**             | ❌ | ❌ | ❌ | ❌ | ✅ (已添加) | ❌ |
| **MoE LRU 缓存（多GPU）**     | ❌ | ❌ | ✅ (b11507) | ❌ | ❌ | ❌ |

> 🏆 **胜出者：** **llama.cpp** 在模型多样性与实验性支持方面领先（如多GPU MoE），而 **Unsloth** 则在视觉语言模型集成与沙箱就绪方面表现突出。**Ollama** 对社区模型请求响应最快，但在实际实现上滞后。

---

### **4. 性能前沿**

| 优化方向                     | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|------------------------------|------|--------|-----------|--------|---------|---------|
| **KV缓存与内存效率**         | ✅ (FP8, HiSparse, GDN) | ✅ (HiCache 分阶段写回) | ✅ (MMQ 外部缓冲修复, MoE LRU) | ⚠️ (MLX/Vulkan 崩溃) | ⚠️ (内存泄漏) | ✅ (显存估算) |
| **内核融合与启动开销**       | ✅ (AITER, GEMM, RoPE) | ✅ (BCG, 融合内核) | ✅ (基数top-k, NV_coopmat2) | ❌ | ❌ | ✅ (int64 RoPE) |
| **分布式 / 多GPU服务**       | ✅ (page_block_size=32) | ✅ (CP + BCG) | ✅ (跨GPU MoE) | ❌ | ✅ (Rust迁移) | ❌ |
| **量化 (FP8/MXFP4)**         | ✅ (完整支持) | ✅ (Int4, FP4) | ✅ (MXFP4 加速) | ⚠️ (MLX 异常) | ❌ | ❌ |
| **流式输出与结构化结果**     | ✅ (logprobs, `/derender`) | ⚠️ (解析器错位) | ❌ | ⚠️ (模式被忽略) | ✅ (重试逻辑) | ✅ (沙箱输出) |

> 🔥 **前沿领军者：**  
> - **vLLM**：在NVIDIA/AMD平台上的KV缓存与FlashInfer集成最为成熟。  
> - **llama.cpp**：内核优化处于前沿（top-k、MMQ、Vulkan）。  
> - **Unsloth**：凭借`int64` RoPE与智能卸载能力，实现业界领先的长上下文处理。  
> - **LiteLLM**：通过Rust迁移引领子毫秒级代理延迟的新一代趋势。

---

### **5. 层级定位**

| 项目       | 主要层级                     | 核心差异化 |
|------------|------------------------------|------------|
| **vLLM**   | **服务引擎** (NVIDIA/AMD)    | 生产级、高吞吐推理，集成FlashInfer |
| **SGLang** | **服务引擎 + 智能体框架**     | 专注推测性解码、确定性推理与多模态流水线 |
| **llama.cpp** | **本地运行时 / 嵌入式推理** | 跨平台、轻量，适合边缘与本地部署 |
| **Ollama** | **网关 / 开发者体验**         | 统一CLI、模型仓库与用户友好的API —— 但新后端下不稳定 |
| **LiteLLM** | **API网关 / 抽象层**          | 供应商无关路由、安全加固，正向基于Rust的代理演进 |
| **Unsloth** | **智能体工作室 / 沙箱平台**   | 全生命周期管理：训练 → 测试 → 服务，具备隔离与文件解析能力 |

> 💡 **战略洞察：** 该技术栈正在碎片化：**vLLM/SGLang** 主导高性能服务；**llama.cpp** 承担本地推理；**Ollama/LiteLLM** 抽象访问入口；**Unsloth** 构建端到端智能体环境。

---

### **6. 趋势信号**

1. **硬件专业化已成为主流**  
   AMD ROCm（MI355X）与NVIDIA SM120（Blackwell）已非小众选择——vLLM与llama.cpp均投入专项调优，包括AITER、GDN及page_block_size=32等优化。未来模型将更趋向按架构定制。

2. **安全与稳定性不再是可选项**  
   多个项目（Ollama、LiteLLM、SGLang）遭遇关键崩溃或回归问题。**LiteLLM的签名验证（Cosign）** 与 **Unsloth的沙箱机制** 反映出基础设施正向“设计即信任”转变。

3. **智能体工作流要求隔离与可靠性**  
   Unsloth的沙箱机制与LiteLLM的结构化输出改进表明，**智能体系统需要安全、可审计的执行环境**，而不仅是更快的推理。

4. **Rust迁移 = 下一代代理性能飞跃**  
   LiteLLM向Rust迁移旨在实现亚毫秒级开销——对高吞吐大模型网关而言是颠覆性变革。此趋势预计将在其他网关层蔓延。

5. **模型格式多样化倒逼工具链进化**  
   GGUF、FP8、MXFP4与云原生格式共存，开发者必须选择能处理格式迁移与校验的工具（如Ollama的自动升级、Unsloth的显存估算器）。

> ✅ **对应用开发者建议：**  
> - 优先选用 **vLLM** 实现现代GPU上的可扩展、稳定推理。  
> - 使用 **Unsloth** 构建安全、长上下文智能体系统。  
> - 仅在确认Rust迁移进展后，再采用 **LiteLLM**。  
> - 暂避 **Ollama v0.40.2+** 在MLX与Vulkan后端的使用，直至修复落地。  
> - 持续关注 **SGLang的确定性推理状态** —— 尽管前景可观，仍脆弱不堪。

---  
*报告生成时间：2026-10-09 | 数据来源：GitHub活动、项目摘要、发布日志*

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

**vLLM Digest – 2026-10-09**

---

### **1. 今日亮点**  
vLLM 项目持续加速对 **ROCm/AMD GPU 性能优化** 的投入，多个 PR 针对 AITER 启用的后端以及 gfx950/gfx942 上的 GDN 预填充优化。关键稳定性修复解决了 FP8 KV 缓存 OOM 问题、FlashInfer 回退失败，以及在长解码场景下 GLM-5.3-Flash 的高危崩溃问题。这些更新对 Blackwell (SM120) 和 MI355X 硬件上的生产部署至关重要。

---

### **2. 发布与破坏性变更**  
*过去 24 小时内无报告。*  
未发布新版本或破坏性 API/配置变更。当前稳定版本仍为 `v0.30.1rc1`，`v0.31.0` 处于积极开发中。

---

### **3. 新模型与硬件支持**  
- **AMD ROCm 支持**：通过 AITER 与 GDN 后端，扩展了 `Qwen3.8-2.4T-A95B` 与 `Qwen3.8-Flash-Next` 在 **MI355X (gfx950)** 上的调优与性能追踪。[Issue #57149](https://github.com/vllm-project/vllm/issues/57149)，[Issue #59575](https://github.com/vllm-project/vllm/issues/59575)  
- **NVIDIA SM120 (Blackwell)**：正在推进为 DeepSeek-V4.1-Flash 启用 `page_block_size=32`，并解决缺失的 FlashInfer 内核问题。[Issue #59203](https://github.com/vllm-project/vllm/issues/59203)  
- **Intel GPU**：为 Arc Pro B60 添加了调优后的 Mamba SSU 配置。[PR #56765](https://github.com/vllm-project/vllm/pull/56765)  
- **量化支持**：全面支持 AMD 与 NVIDIA 平台上的 `FP8` 与 `MXFP4` 量化模型。

---

### **4. 性能与优化**  
- **ROCm AITER 优化**：  
  - 在 gfx942/gfx950 上默认启用 AITER FlyDSL GDN 预填充。[PR #60645](https://github.com/vllm-project/vllm/pull/60645)  
  - 跳过稀疏 MQA logit 清理以避免冗余填充。[PR #51314](https://github.com/vllm-project/vllm/pull/51314)  
  - 在 ROCm 上使用调优后的 AITER GEMM 用于 MoE 路由门。[PR #50535](https://github.com/vllm-project/vllm/pull/50535)  
- **内核级改进**：  
  - GDN 后端内部预填充检查点改善对齐并减少调度器停顿。[PR #60659](https://github.com/vllm-project/vllm/pull/60659)  
  - 通过延迟 `pixel_values` 预处理至 `/generate` 降低多模态负载。[RFC #46722](https://github.com/vllm-project/vllm/issues/46722)  
- **内存效率**：HiSparse 现可在不修改核心块表的前提下管理 GPU 居住性。[PR #60647](https://github.com/vllm-project/vllm/pull/60647)

---

### **5. 稳定性与回归问题**  
| 严重程度 | 问题 | 描述 | 修复状态 |
|---------|------|-------------|------------|
| 🔴 高 | [#56868](https://github.com/vllm-project/vllm/issues/56868) | GLM-5.3-Flash 在累积推理解码后出现长解码性能退化 | 进行中 |
| 🔴 高 | [#60174](https://github.com/vllm-project/vllm/issues/60174) | DFlash2/DSpark + 前缀缓存导致 Qwen3.8-27B NVFP4 输出损坏 | v0.31 中已复现；修复待定 |
| 🟡 中 | [#60262](https://github.com/vllm-project/vllm/issues/60262) | `kv_cache_dtype="fp8"` 在 FlashInfer JIT 不可用时崩溃 | 已在 PR #60785 修复 |
| 🟡 中 | [#60350](https://github.com/vllm-project/vllm/issues/60350) | FP8 KV 缓存启动时因 CUDA graph 内存排除导致 OOM | PR #60350 仍在开放 |
| 🟡 中 | [#60473](https://github.com/vllm-project/vllm/issues/60473) | MoRIIO 解码在通知端口被占用时挂起 | 进行中 |

---

### **6. 对应用开发者的影响**  
- **对于 AMD/ROCm 用户**：请优先使用 `v0.31.0+rocm723` 构建版本以在 MI355X 上获得最佳性能。预计使用 AITER 与 GDN 后端的 `Qwen3.8-*` 模型将实现更快的推理速度。  
- **对于 Blackwell (SM120) 用户**：使用 `DeepSeek-V4.1` 与 `GLM-5.3-Flash` 时需谨慎——在内核支持确认前，请避免设置 `page_block_size=32`。密切监控回归报告。  
- **对于多节点/分布式系统**：使用 `--kv-cache-dtype fp8` 时需小心——确保 FlashInfer JIT 可用，否则可能触发回退。如需临时解决方案，可启用 `VLLM_USE_FLASHINFER_SAMPLER=0`。  
- **对于代理开发者**：通过 `/derender` 和解析引擎迁移（如 Olmo3），流式 logprobs 与结构化输出现已获得更好支持。关注解析路径中 `logprobs` 流式传输的持续开发进展。  
- **对于安全敏感应用**：OpenCV 视频解码现已在子进程中隔离，降低因畸形输入导致服务器崩溃的风险。[PR #60612](https://github.com/vllm-project/vllm/pull/60612)

> ✅ **建议**：为获取 ROCm 与 Blackwell 的修复，更新至最新 nightly 版本（`v0.31.0.dev`）。测试 `kv_cache_dtype=fp8` 时请确保完整 FlashInfer 工具链就绪，以避免崩溃。

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

**SGLang 消息简报 – 2026-10-09**

---

### **1. 今日重点**  
SGLang 生态系统持续成熟，当前工作聚焦 DeepSeek V4.1 优化、多模态及 NPU/XPU 支持，以及推测解码和确定性推理的关键稳定性修复。值得注意的是，多个 PR 解决了长期存在的缓存管理（HiCache）、内核调度（Mamba）和 GPU 内存安全问题，凸显出对生产级可靠性的日益重视。

---

### **2. 发布与破坏性变更**  
*过去 24 小时内无新发布。*  
然而，已标记若干配置敏感行为：  
- `--enable-deterministic-inference` **无法保证** `gpt-oss-20b` 的确定性（问题 #43055）。  
- 在 `sglang.Engine` 中使用 `dtype="float32"` 会触发 `KeyError: torch.float32`（问题 #43162）。  
→ 开发者应在修复合并前避免使用这些配置。

---

### **3. 新模型与硬件支持**  
- **DeepSeek V4.1**：通过预填充 CP + BCG 支持（PR #40816, #40323）持续优化，并融合 RoPE/fp4 内核（PR #42911）。  
- **Intel XPU**：扩展强化学习功能支持（PR #39891），引入用于 PD 分离的暂存缓冲区 KV 传输（PR #40861），修复 int4 密集层测试问题（PR #43306）。  
- **Ascend NPU**：修复通用注意力下的预填充上下文并行问题（PR #41564）；A5 压缩器路由现支持显式 SWA 映射（PR #40816）。  
- **AMD ROCm**：新增 MORI EP V2 支持（PR #40666），gfx950 解码分数瓦片大小调整为 128 个 token 以保持一致性（PR #42772）。

---

### **4. 性能与优化**  
- **DeepSeek V4.1 预填充**：现已支持交错式 CP + 可中断 CUDA 图（PR #38603），提升在 TP=2 配置下的可扩展性。  
- **内核融合**：AMD ROCm 现可融合预填充索引-Q RoPE 伪量化与 fp4 量化内核（PR #42911），降低内核启动开销。  
- **内存效率**：为页统一 KV 缓存添加 HiCache 阶段写回功能（PR #39606）；L2/L3 命中归属在重试过程中保持不变（PR #39297）。  
- **解码速度**：Qwen4Exp 在 DGX Spark 上显示 QSA/PLE/GDN 内核时间占主导（问题 #36796），促使针对 SM121 后端的调优需求。

---

### **5. 稳定性与回归问题**  
| 严重程度 | 问题 | 描述 | PR/临时方案 |
|---------|-------|-------------|---------------|
| 严重 | #42752 | PR 监护工作流中存在不稳定的 CI 测试与基础设施故障 | 跟踪问题；尚未修复 |
| 高 | #43061 | `--enable-deterministic-inference` + `repetition_penalty` 导致 granite-4.0-h 的调度器崩溃（TorchDynamoError） | 尚未修复 |
| 高 | #43204 | `AssertionError: Can not alloc mamba cache` 在所有 Mamba 状态被锁定时导致引擎崩溃 | 严重崩溃；尚未修复 |
| 高 | #40843 | GLM-5.3-Flash 推测解码产生退化输出（无限 '!' 循环） | 可复现；正在调查中 |
| 中 | #43146 / #43145 / #43157 | 推理解析逻辑与 Minimax/M3 严格语法规则不一致 | 多个相关问题报告 |
| 中 | #43287 | EAGLE+DP 注意力空闲验证因缺少 `kv_indptr` 而崩溃 | 在无任务负载的 DP rank 上触发 |

> ⚠️ **注意**：多个回归问题影响代理工具调用（如空 SSE 数据块 #29441，Glm47MoeDetector 输出无效 JSON #43273），影响 SDK 集成。

---

### **6. 对应用开发者的启示**  
- 若使用 `repetition_penalty` 或 `granite-4.0-h`，请避免启用 `--enable-deterministic-inference`，因其可能导致调度器崩溃（问题 #43061）。  
- **验证模型特定配置** —— 如 `rope_theta` 正在被错误生成（#43158），且 `json-model-override-args` 会丢失 `rope_scaling` 字段（#41227）。  
- 在推测解码下使用 GLM-5.3-Flash 时，应预期复杂工具链可能出现不稳定（#40843, #36669）。  
- **谨慎使用最新 CI 构建** —— 不稳定测试（#42752）和基础设施故障可能导致合并阻塞。  
- **多后端部署场景中**，务必端到端测试设备特定逻辑（如 XPU/NPU），尤其关注缓存与张量序列化部分。

👉 *请通过 [SGLang GitHub Issues](https://github.com/sgl-project/sglang/issues) 保持更新，并加入 [Slack](https://slack.sglang.ai) 实时协作。*

---  
*简报生成时间：2026-10-09 | 来源：github.com/sgl-project/sglang*

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

**llama.cpp 消息简报 – 2026-10-09**

---

### **1. 今日重点**  
最新更新聚焦于关键的 CUDA 与 SYCL 稳定性修复，包括针对大行数的 top-k 性能优化以及 MMQ 越界读取问题的修复。MoE（专家混合）支持持续取得进展，涵盖多 GPU 缓存共享及新的 MoE LRU 缓存提案；Vulkan 与 HIP 后端也获得了针对性的正确性与性能改进。

---

### **2. 发布与破坏性变更**  
- **新版本发布**：`b11515`（SYCL）、`b11514`（Musa）、`b11513`（CUDA）、`b11512`（DFlash 输出头共享修复）、`b11511`（CUDA MMQ 越界修复）、`b11510`（>65535 行的循环 PAD 内核）、`b11509`（CCCL 版本保护修复）、`b11507`（跨 GPU 的 MoE 缓存）、`b11505`（Vulkan TOP_K 支持 +inf/NaN）、`b11503`（cpp-httplib 升级）。  
- **迁移提示**：`--moe-cache-mib` 标志现在支持按每个 GPU 使用逗号分隔的值（#30205），可实现对 MoE 专家缓存分配的细粒度控制。  
- **API 变更**：`yaRN` 现通过 `#30206` 支持 `truncate=false`，允许非整数插值边界（目前仅限 CPU）。

> 🔗 [GitHub 发布页](https://github.com/ggml-org/llama.cpp/releases)

---

### **3. 新模型与硬件支持**  
- **后端新增**：  
  - **XDNA 后端请求**已开启：请求集成 XDNA 硬件支持（#21725）。  
  - **Metal 多 GPU 支持**：请求在 Intel Mac 上通过 Metal 使用 eGPU + dGPU（#28565）。  
- **硬件特定优化**：  
  - CUDA：将 `sm_86` 添加至 MMVQ 截断表，适用于 A10（Q4_0 跨越点从 7 提升至 +9.1% 性能）（#28090）。  
  - Vulkan：基于 NV_coopmat2 的分块 GATED_DELTA_NET 内核已合并（#30207），显著提升 RTX 5060 Ti 的可扩展性。  
- **模型支持**：  
  - Qwen3.8-Flash-Next（UD-IQ4_XS, IQ3_XXS）因性能下降及推测解码崩溃问题正被重点关注（#29949, #30033）。  

> 🔗 [问题 #21725](https://github.com/ggml-org/llama.cpp/issues/21725) | [PR #30207](https://github.com/ggml-org/llama.cpp/pull/30207)

---

### **4. 性能与优化**  
- **CUDA**：  
  - 基于基数的 `top-k` 替换 CUB 的逐行内核 —— 在 qwen4exp 34,816 token 场景下减少 160 万次内核启动（#28713）。  
  - MMA 内核中对 Q8/Q4 实现即时反量化原型，有望降低内存压力（#30202）。  
- **SYCL**：  
  - 通过算术解码与权重重排，MXFP4 MoE 推理加速达 **1.9×–2.0×**（#29809）。  
- **Vulkan**：  
  - 通过专用化常量设置量化块大小，恢复完整的 K 循环展开 —— 在 RTX 5060 Ti 上使提示处理延迟降低约 **16–19%**（#30164）。  
- **通用优化**：  
  - 分块 GDN 内核已合并至 CUDA/HIP（#29353），在大型模型上预填充吞吐量提升约 10%。

> 🔗 [PR #28713](https://github.com/ggml-org/llama.cpp/pull/28713) | [PR #30164](https://github.com/ggml-org/llama.cpp/pull/30164) | [PR #29809](https://github.com/ggml-org/llama.cpp/pull/29809)

---

### **5. 稳定性与回归问题**  
| 问题 | 严重性 | 状态 | 备注 |
|------|----------|--------|-------|
| [#25593](https://github.com/ggml-org/llama.cpp/issues/25593) | 严重 | 开放 | SM_60（P100）上静默使用 FP32 而非 FP16，导致质量损失。修复已在分支中存在。 |
| [#29949](https://github.com/ggml-org/llama.cpp/issues/29949) | 高 | 开放 | MoE 专家缓存溢出；需 GPU 居住式 LRU 缓存。 |
| [#29811](https://github.com/ggml-org/llama.cpp/issues/29811) | 高 | 开放 | 使用 MTP 运行 Qwen3.8 Flash 时触发断言失败。 |
| [#27638](https://github.com/ggml-org/llama.cpp/issues/27638) | 高 | 开放 | Flash Attention 回退至 SCALAR 路径导致 O(N²) PP 性能退化，并引发 Intel Arc 设备丢失。 |
| [#30000](https://github.com/ggml-org/llama.cpp/issues/30000) | 中等 | 开放 | RTX 5060 Ti 上 Vulkan 提示处理速度比 #25773 之后慢 16–19%。已在 PR #30164 修复。 |
| [#26447](https://github.com/ggml-org/llama.cpp/issues/26447) | 中等 | 开放 | Vega 8 iGPU 上运行约 50K 个上下文后出现 `vk::Queue::submit: ErrorDeviceLost`。 |

> 🔗 [问题 #25593](https://github.com/ggml-org/llama.cpp/issues/25593) | [PR #30164](https://github.com/ggml-org/llama.cpp/pull/30164)

---

### **6. 对应用开发者的启示**  
- **优先保障 CUDA 与 SYCL 稳定性**：若使用 SM_60（P100）或更早显卡，请警惕静默的 FP32 回退对模型质量的影响（#25593）。如可能，应显式使用 FP16。  
- **充分利用 MoE 扩展能力**：借助 `b11507`，MoE 模型现已支持跨多 GPU —— 适用于大规模代理工作流。建议关注即将推出的 GPU 居住式 LRU 缓存提案（#29949）以实现高效专家管理。  
- **为新硬件优化配置**：使用 `--moe-cache-mib` 按 GPU 调优（#30205），并在 RTX 5060 Ti 上启用 `NV_coopmat2` 内核以加快提示处理。  
- **避免推测解码陷阱**：在 #29811 与 #30033 解决前，谨慎使用 MTP + Qwen3.8-Flash-Next。  
- **监控 Vulkan 行为**：在 Intel Arc 或旧 RDNA 显卡上，长时间上下文处理期间可能出现内存不足或设备丢失，除非已打补丁。  

> 📌 **可操作建议**：升级至 `b11515+` 以获得更好的 top-k 与 MoE 稳定性。在当前版本中测试带有 MTP 的 Qwen3.8-Flash-Next 工作负载。

---  
*本简报由 2026-10-09 的 GitHub 活动生成。如需实时更新，请关注 [llama.cpp on GitHub](https://github.com/ggml-org/llama.cpp)。*

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

**Ollama Digest – 2026-10-09**

---

### **1. 今日亮点**  
最新发布的 **v0.40.2** 版本引入了对旧版 Ollama 下载模型的自动后台升级功能，提升了在 `llama.cpp` 上运行时的兼容性和性能。然而，该更新也引发了一系列稳定性问题——尤其集中在 **Apple Silicon（M系列）上的 MLX 后端崩溃** 以及 **AMD Radeon 780M 显卡的 Vulkan 回退问题**，影响了 macOS 与 Linux 用户。与此同时，用户对 **Intel OpenVINO 集成** 的需求日益增长，以及对新小型决策模型如 *d1-3B* 的支持呼声，反映出硬件多样性持续扩展和应用场景不断演进。

---

### **2. 发布与破坏性变更**  
- **v0.40.2**：后台自动模型升级确保与 `llama.cpp` 更佳兼容性。原始模型将作为备份保留，以便安全回滚。  
  🔗 [GitHub Release v0.40.2](https://github.com/ollama/ollama/releases/tag/v0.40.2)  

> ⚠️ **注意**：从 v0.35.x 升级的用户可能遇到回归问题——尤其在 MLX 与 Vulkan 后端上。若出现不稳定情况，建议回滚版本。

---

### **3. 新模型与硬件支持**  
- **Intel OpenVINO**：高优先级功能请求 (#2169) 呼吁在 Intel CPU/GPU 上原生支持 OpenVINO，以提升多模态模型（如 LLaVA）的运行效率。  
  🔗 [Issue #2169 – 功能请求：Intel 上的 OpenVINO](https://github.com/ollama/ollama/issues/2169)  
- **新增模型请求**：  
  - *d1-3B* 与 *d1-omni-600M*（支持图像的小型决策模型）：通过 #18890 提出  
    🔗 [Issue #18890 – d1-3B 与 d1-omni-600M 请求](https://github.com/ollama/ollama/issues/18890)  
  - *Clef Flash*, *Index-Translate*, *Qwen 3.8 Flash Next*, *Mimo v2.6*, *Hy4*, *Stepfun*, *Laguna*, *Reflection AI*：请求开放云端可用性 (#18850)  
    🔗 [Issue #18850 – 云端模型请求](https://github.com/ollama/ollama/issues/18850)  
- **后端进展**：  
  - MLX 支持扩展，通过 PR #18780 新增 Kolibri 1  
    🔗 [PR #18780 – mlx: 添加 Kolibri 1 支持](https://github.com/ollama/ollama/pull/18780)  
  - 通过 PR #18882 正在推进旧版 GGUF 迁移到现代格式的初步工作  
    🔗 [PR #18882 – 移除 llama.cpp 补丁并迁移旧版 GGUF](https://github.com/ollama/ollama/pull/18882)

---

### **4. 性能与优化**  
- **MLX 后端优化**：  
  - PR #18805：在推测回滚后压缩恢复的循环状态，减少内存开销。  
    🔗 [PR #18805 – mlx: 压缩恢复的循环状态](https://github.com/ollama/ollama/pull/18805)  
  - PR #18886：在清理过程中保留生成异常，避免掩盖根本错误。  
    🔗 [PR #18886 – mlx: 在清理过程中保留生成异常](https://github.com/ollama/ollama/pull/18886)  
- **API 与流式传输改进**：  
  - PR #18891：在 Responses API 中压缩时保留推理上下文。  
    🔗 [PR #18891 – openai: 保留保留的推理上下文](https://github.com/ollama/ollama/pull/18891)  
  - PR #18881：在原始生成响应中包含 EOS token（修复缺失序列结束信号的问题）。  
    🔗 [PR #18881 – llm: 在原始生成响应中包含 eos token](https://github.com/ollama/ollama/pull/18881)  
- **CI 稳定性提升**：PR #18883 为下载步骤添加重试逻辑，提高 CI 可靠性。  
  🔗 [PR #18883 – ci: 通过重试加固下载步骤](https://github.com/ollama/ollama/pull/18883)

---

### **5. 稳定性与回归问题**  
| 严重程度 | 问题 | 影响 | 状态 |
|---------|------|--------|--------|
| 🔴 严重 | **M系列 Mac 上的 MLX 崩溃（v0.40.0+）**<br>```mlx runner failed: panic: mlx: Maximum threads per threadgroup is 896 but requested 1024``` | `gemma4:e2b-mlx`、`qwen3.6:35b-mlx` 运行时崩溃 | 📌 开放 ([#18846](https://github.com/ollama/ollama/issues/18846), [#18885](https://github.com/ollama/ollama/issues/18885)) |
| 🔴 严重 | **AMD Radeon 780M Vulkan 回退（≥0.32.10）**<br>```radv/amdgpu: Not enough memory for command submission vk::Queue::submit: ErrorDeviceLost``` | 较大模型无法在 Vulkan 上运行 | 📌 开放 ([#17748](https://github.com/ollama/ollama/issues/17748)) |
| 🔴 严重 | **云端模型忽略 JSON Schema**<br>`qwen3-coder:480b-cloud` 返回非结构化 JSON 尽管有 schema 定义 | 打破结构化输出工作流 | 📌 开放 ([#12362](https://github.com/ollama/ollama/issues/12362)) |
| 🟡 高 | **Linux 上无法拉取 `embeddinggemma-2:740m`**<br>```Error: this model requires MLX support, but the MLX runtime is not available``` | 错误误导；Intel 系统未启用 MLX | 📌 开放 ([#18825](https://github.com/ollama/ollama/issues/18825)) |
| 🟡 高 | **Gemma4: think=false → 由于未闭合思考块导致空回复** | 工具使用场景中静默失败 | 📌 开放 ([#18861](https://github.com/ollama/ollama/issues/18861)) |
| 🟡 中 | **不支持图像生成模型**<br>尝试运行 `x/z-image-turbo` 时提示“当前不支持” | 阻碍多模态应用开发 | 📌 已关闭 ([#18863](https://github.com/ollama/ollama/issues/18863)) |

> ✅ **部分问题已有修复 PR**：  
> - PR #18882（旧版 GGUF 迁移）可缓解潜在数据损坏风险。  
> - PR #18886 提升了 MLX 失败时的错误可见性。

---

### **6. 对应用开发者的影响**  
- **避免在 Apple Silicon 上使用 v0.40.0–0.40.1**：若使用基于 MLX 的模型（如 `gemma4`、`qwen3`），**请回滚至 v0.35.1**，直至解决 MLX 线程组大小限制问题。  
- **预期在 AMD GPU 上使用大模型时存在不稳定性**：仅在确认可用时使用 `--device vulkan`；建议考虑 ROCm 或 CPU 降级方案。  
- **不要依赖云端模型的 schema 强制校验**：对于 `qwen3-coder:480b-cloud` 等模型，需在事后验证输出——当前 schema 解析被忽略。  
- **规划 MLX 与 Vulkan 的弃用路径**：项目正在重构旧后端，未来版本可能出现破坏性变更。  
- **谨慎使用 `/v1/responses`**：流式行为可能导致输出重排或复用 `output_index`——在代理流水线中需验证消息边界。  
- **准备应对模型迁移**：v0.40.2 的自动升级机制要求你的应用能处理更新后的模型格式与清单文件（如 `manifests-v2`）。  

> 💡 **实用建议**：关注 PR #18882 与 #18886，它们将带来与 MLX 崩溃容错及模型一致性相关的后续修复。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

### **1. 今日亮点**  
LiteLLM 持续快速演进，带来关键的安全与稳定性改进，包括解决一个高危供应链攻击问题（#24518），该问题虽已在 2026 年 3 月被遏制，但仍关乎社区信任的核心议题。项目正通过正在进行的 **Rust 迁移计划**（#31263）加速实现性能飞跃，目标是将代理开销降至 1 毫秒以下。与此同时，新提交的 PR 聚焦于优化 Anthropic、Bedrock 等提供商的认证、日志记录及流式传输可靠性。

---

### **2. 发布与破坏性变更**  
最新版本（`v1.106.0-dev.2`、`v1.105.0-rc.3`、`v1.104.2`、`v1.102.4`、`v1.101.6`）未引入任何破坏性变更。所有 Docker 镜像现已使用 **cosign** 进行加密签名，验证密钥已固定至 [commit `0112e53`](https://github.com/BerriAI/litellm/commit/0112e53046018d726492c814b3644b7d376029d0)。此举确保了镜像完整性，并降低未来供应链风险。

> 🔐 **验证镜像签名**：[cosign 文档](https://docs.sigstore.dev/cosign/overview/) | [GitHub commit](https://github.com/BerriAI/litellm/commit/0112e53046018d726492c814b3644b7d376029d0)

---

### **3. 新模型与硬件支持**  
- **Gemma 4 模型**（gemma-4-31b-it、gemma-4-26b-a4b-it）通过 Issue #26973 加入模型注册表，现已支持在 `model_prices_and_context_window.json` 中使用。  
- **Ace Data Cloud** 集成范围扩大，全面支持聊天与响应 API，包含定价信息及已验证的徽标展示（PR #44817）。  
- **Laya、Bespoke 及评估模式**现已可在“添加模型” UI 中使用（PR #45260），支持部署 TypeSafe Jev 等专用模型。

> 📌 [添加 Gemma 4 模型](https://github.com/BerriAI/litellm/issues/26973) | [Ace Data Cloud 提供商](https://github.com/BerriAI/litellm/pull/44817) | [Laya/Bespoke 支持](https://github.com/BerriAI/litellm/pull/45260)

---

### **4. 性能与优化**  
**Rust 迁移**（Issue #31263）现已进入积极开发阶段，如 PR [#45519](https://github.com/BerriAI/litellm/pull/45519) 所示，重点减少跨路由主机的冗余公共调用解析。早期基准测试表明，核心代理操作的开销已低于 1 毫秒，目标是相比当前基于 Python 的延迟实现 10 倍以上的提升。

> ⚙️ [Rust 迁移博客](https://docs.litellm.ai/blog/litellm-rust-launch) | [Rust PR #45519](https://github.com/BerriAI/litellm/pull/45519)

---

### **5. 稳定性与回归问题**  
今日报告的关键稳定性问题包括：

- **内存泄漏** 导致长时间使用后 Pod 崩溃（问题 #12685、#27954）：多位用户报告内存占用持续攀升，数日内无止境增长。尚未有修复的 PR，但已持续追踪。
- **流式传输失败** 由于缺失或丢弃数据块：
  - Mistral 流式传输会丢失 `reference` 数据块（问题 #45378）。
  - Google GenAI 在首个数据块前连接中断时无法重试（问题 #45457）。
- **令牌计数不准确**：Bedrock 的 `CountTokens` 对 Claude Opus 5/Sonnet 5 返回低估值（问题 #37102）。
- **缺少错误上下文**：模型访问拒绝日志中缺乏关键身份信息（PR #45526 提出修复方案）。

> 🔍 [内存泄漏 #12685](https://github.com/BerriAI/litellm/issues/12685) | [Google GenAI 重试缺陷 #45457](https://github.com/BerriAI/litellm/issues/45457) | [Bedrock 令牌计数 #37102](https://github.com/BerriAI/litellm/issues/37102)

---

### **6. 对应用开发者的影响**  
- **安全优先部署**：对所有 LiteLLM 镜像使用 `cosign verify` 确保未被篡改——尤其在发生供应链攻击事件（#24518）后至关重要。  
- **谨慎升级**：尽管无破坏性变更，但若依赖 GitHub BYOK token 跟踪功能，请避免从 `v1.103.1` 升级至 `v1.104.2`（问题 #45422）。  
- **可靠流式处理**：注意已知流式问题（如 Google GenAI、Mistral reference chunk）；建议准备降级策略或手动重试机制。  
- **为 Rust 做准备**：若构建低延迟推理网关，应关注 Rust 迁移进展——预计 2027 年初将显著提升吞吐量并大幅降低内存占用。  

> ✅ **可操作建议**：对照 [最新模型列表](https://github.com/BerriAI/litellm/blob/main/model_prices_and_context_window.json) 检查配置，并更新 Gemma 4 的 `model_name` 别名。

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# **Unsloth Digest – 2026-10-09**

---

### **1. 今日亮点**  
Unsloth 在 Windows、Mac 和 Linux 上全面推出**沙箱支持**，实现文本与视觉大模型作为 Jev 风格决策模型的安全、隔离执行——将决策准确率从约 30% 提升至 80%。这使得整个生命周期管理成为可能：在可信环境中完成训练、测试、导出和部署决策模型。与此同时，团队持续优化大型模型（如 Qwen-Image-2.1）在 ROCm 平台上的 GPU 内存处理，并解决训练与推理流程中的关键 OOM 问题。

---

### **2. 发布与破坏性变更**  
- **v0.1.905-beta**：通过 Unsloth Studio 引入**跨平台沙箱**（Windows、macOS、Linux）。  
  🔗 [发布说明](https://unsloth.ai/docs/new/studio/sandboxing-in-unsloth)  
  📌 *注意：新决策模型部署默认启用沙箱。现有用户可能需要更新工作流配置以使用隔离功能。*

---

### **3. 新模型与硬件支持**  
- **Qwen-Image-2.1-GGUF (Q4_K_M)** 现已提升对 **ROCm（AMD GPU）** 的兼容性，修复了 VAE tile OOM 问题及注意力降级逻辑。  
  🔗 [PR #13116](https://github.com/unslothai/unsloth/pull/13116)  
- **Office、OpenDocument、电子书、邮件及 RTF 文件解析**功能现已加入 Studio 的“文件聊天”功能中。  
  🔗 [PR #13081](https://github.com/unslothai/unsloth/pull/13081)，[PR #13078](https://github.com/unslothai/unsloth/pull/13078)  
- **代码代理（Claude Code、OpenAI Codex 等）** 现可通过 API 密钥经由 MCP 与 Unsloth Studio 交互。  
  🔗 [PR #13118](https://github.com/unslothai/unsloth/pull/13118)

---

### **4. 性能与优化**  
- **RoPE 与归一化核函数** 已更新为使用 `int64` 行偏移，可正确处理超过 2³¹ 元素（约 21 亿标记）的序列。  
  🔗 [PR #13121](https://github.com/unslothai/unsloth/pull/13121)  
  ✅ *测试通过，剩余约 5.5 GiB GPU 内存*  
- **显存预算估算** 现已根据实际 llama.cpp GPU 使用情况调整，考虑了驻留主机的嵌入层（例如输入层未置于 GPU）。  
  🔗 [PR #9931](https://github.com/unslothai/unsloth/pull/9931)  
- **卸载规划器改进**：现会权衡溢出成本与 llama.cpp 内部适配器，实现更智能的权重分布。  
  🔗 [PR #9872](https://github.com/unslothai/unsloth/pull/9872) *(行为受 `UNSLOTH_SMART_OFFLOAD` 控制)*

---

### **5. 稳定性与回归问题**  
- **在 M5 Max（48GB RAM）上运行 `unsloth/Qwen-Image-2.1-GGUF`（Q4_K_M）时出现严重 OOM**：  
  🔗 [Issue #11792](https://github.com/unslothai/unsloth/issues/11792)  
  ⚠️ *已报告但尚未提交修复 PR —— 可能源于 VAE 解码路径中的内存高估。*  
- **在 WSL 上进行 GRPO 训练时发生 CUDA 显存不足**，尽管显卡仍有 24GB 未使用：  
  🔗 [Issue #1744](https://github.com/unslothai/unsloth/issues/1744)  
  🛠️ *修复 PR 正在审核中：[PR #13116](https://github.com/unslothai/unsloth/pull/13116)*  
- **本地 HF 缓存配置错误时沙箱会静默失败**：  
  🔗 [Issue #2506](https://github.com/unslothai/unsloth/issues/2506)  
  🛠️ *临时解决方案：始终使用 `HF_ENDPOINT`；修复将在 v0.1.905+ 中发布。*

---

### **6. 对应用开发者的意义**  
- **构建安全可审计的 AI 代理**：利用沙箱隔离决策模型，防止意外副作用或数据泄露。适用于生产级 LLM 网关与代理系统。  
- **在 AMD 平台上可靠部署视觉语言模型**：随着 ROCm 修复落地，现在可在 H200/M5 Max 上稳定运行 Qwen-Image-2.1，不再频繁出现 OOM。  
- **优化长上下文推理**：`int64` RoPE 核函数确保超过 20 亿标记序列的正确性——对法律、医疗或代码分析类应用至关重要。  
- **避免本地模型加载中的静默失败**：若使用自定义仓库，请确保一致地使用 `HF_ENDPOINT`；通过扫描文件夹监控部分下载情况。  
- **准备启用智能卸载**：开启 `UNSLOTH_SMART_OFFLOAD` 可在多 GPU 环境中通过更贴合 llama.cpp 内部内存规划来提升吞吐量。

> 💡 *实用提示：在部署至生产前，务必在沙箱模式下验证模型加载。当传入标签时，需仔细监控 `UNSLOTH_RETURN_LOGITS=1` 的行为——近期 PR 已确保对数几率缩放的一致性。*

</details>

---
*本日报由 [agents-radar](https://github.com/Qyii22/agents-radar) 自动生成。*