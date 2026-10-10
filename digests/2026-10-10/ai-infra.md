# AI 基础设施日报 2026-10-10

> 生成时间: 2026-10-10 06:21 UTC | 覆盖项目: 6 个

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## 横向对比

# **Cross-Project AI Infrastructure Ecosystem Report – 2026-10-10**

---

### **1. Ecosystem Overview**  
The AI inference and serving ecosystem is entering a phase of high specialization and hardware-aware optimization, driven by next-generation GPUs (Blackwell SM120, RDNA4, Mi355X) and increasingly complex models (Flash attention, MoE, multimodal). Projects are diverging in focus: vLLM and SGLang lead in high-throughput, low-latency inference with speculative decoding and kernel fusion; llama.cpp remains the dominant local runtime for CPU/GPU hybrid deployments; Ollama consolidates developer experience via OpenAI parity; LiteLLM dominates as the multi-provider gateway with growing security and billing rigor; Unsloth advances agent-centric fine-tuning and studio UX. Stability remains a persistent challenge—especially around FP8, MoE, and GPU-specific backends—indicating maturity pressure as performance targets are pushed.

---

### **2. Activity Comparison**

| Project         | Issues Open (High/Critical) | PRs Landed (Last 24h) | Releases | Notes |
|----------------|------------------------------|------------------------|----------|-------|
| **vLLM**       | 14 (4 High, 2 Critical)      | ~12                    | None     | Heavy focus on Blackwell/ROCm stability & speculative decoding correctness |
| **SGLang**     | 12 (3 High, 1 Critical)       | ~20                    | None     | Aggressive kernel fusion + ComfyUI integration push |
| **llama.cpp**  | 17 (6 High, 2 Critical)       | ~10                    | 10+ (b11540+) | Rapid nightly updates, strong SYCL/MoE gains |
| **Ollama**     | 14 (4 High, 2 Critical)       | ~5                     | None     | Regression-heavy, with MLX/CUDA issues affecting new hardware |
| **LiteLLM**    | 10 (3 High, 2 Critical)       | ~4                     | v1.106.0-dev.3 | Security-focused release; budgeting/billing bugs persist |
| **Unsloth**    | 13 (4 High, 1 Critical)       | ~8                     | None     | AMD/ROCm fixes + TRL 1.15 compatibility in flight |

> 🔍 *Observation*: vLLM and SGLang show highest engineering velocity, while llama.cpp leads in release frequency. Ollama and LiteLLM face critical regressions despite lower activity.

---

### **3. Model Support Race**

| New Model / Architecture       | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|-------------------------------|------|--------|-----------|--------|---------|---------|
| **DeepSeek-V4.1-Flash**        | ✅ (PCP, FlashMLA) | ✅ (DSpark, FP8) | ✅ (Speculative path) | ❌ | ❌ | ❌ |
| **GLM-5.3-Flash**              | ✅ (packed KV, ring slots) | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Qwen3.8-Flash-Next**         | ✅ (FP8 on Ampere) | ✅ (MoE, TP8) | ✅ (Speculative decode) | ✅ (via llama.cpp b115xx) | ❌ | ❌ |
| **MiniCPM-V 4.7**              | ❌ | ❌ | ✅ (3D RoPE) | ❌ | ❌ | ❌ |
| **Kolibri 1**                  | ❌ | ❌ | ❌ | ✅ (MLX) | ❌ | ❌ |
| **Qwen3-VL / GGUF V3**         | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ (V2 supported) |
| **MiniMax-H3 / Wan 3.0 Video** | ❌ | ❌ | ❌ | ❌ | ✅ (`/v1/videos`) | ❌ |
| **Databricks Agent Platform**  | ❌ | ❌ | ❌ | ❌ | ✅ | ❌ |

> 🏆 **Winner**: **SGLang** leads in cutting-edge model support (DeepSeek-V4.1-Flash, MoE), followed closely by **llama.cpp** in raw model breadth and **LiteLLM** in provider diversity.

---

### **4. Performance Frontier**

Optimization efforts are concentrated across five key areas:

| Focus Area               | Leading Projects                         | Key Advances |
|--------------------------|------------------------------------------|------------|
| **KV Cache Efficiency**   | vLLM, SGLang, Unsloth                    | HiSparse fusions (vLLM #60103), FP8 cache reuse (SGLang #43498), row-wise layout fixes (Unsloth #13141) |
| **Kernel Fusion & Launch Reduction** | SGLang, vLLM, llama.cpp | Up to 50% fewer launches (SGLang #42497), fused slot invalidation (vLLM #60103) |
| **Quantization (FP8/MXFP)** | vLLM, SGLang, llama.cpp | `fp8` support on Ampere (vLLM #60602), MXFP4 acceleration (llama.cpp), DSpark FP8 reads (SGLang #43498) |
| **Distributed Serving & Scalability** | vLLM, SGLang, LiteLLM | Prefill context parallelism (vLLM), pipeline refactoring (SGLang), cross-provider caching (LiteLLM) |
| **Hardware-Specific Optimization** | All, but especially vLLM & SGLang | ROCm gfx950 tuning (vLLM), RDNA4 kernel disable (vLLM #57861), SYCL MoE scheduling (llama.cpp) |

> 💡 **Trend**: The frontier is no longer just "faster inference"—it's **hardware-aware, model-aware, and context-aware** optimization at the kernel level.

---

### **5. Layer Positioning**

| Project         | Primary Layer             | Differentiators |
|----------------|----------------------------|----------------|
| **vLLM**       | Inference Engine           | Best-in-class CUDA graph support, FlashMLA, speculative decoding, scalable batch processing |
| **SGLang**     | Inference Engine + Gateway | Full pipeline control, rich plugin system (ComfyUI), DSpark speculative decode |
| **llama.cpp**  | Local Runtime / Edge Engine | Universal backend support (SYCL, Vulkan, OpenCL), CPU/GPU hybrid execution, lightweight |
| **Ollama**     | Developer Gateway / CLI    | Unified API, model manager, MLX/CUDA convergence, tool calling alignment |
| **LiteLLM**    | Multi-Provider Gateway     | OpenAI-compatible abstraction, spend tracking, virtual keys, cost injection, agent routing |
| **Unsloth**    | Fine-Tuning Framework      | Fast LoRA training, GRPO/PPO support, Studio UI, GGUF integration |

> ⚙️ **Positioning Insight**: vLLM/SGLang are becoming *inference engines*; LiteLLM/Ollama are *application gateways*; llama.cpp is the *universal runtime*; Unsloth is the *agent trainer*.

---

### **6. Trend Signals**

Based on today’s activity, three major trends are emerging:

1. **Hardware-Aware Optimization is Now Mandatory**  
   - Blackwell (SM120), RDNA4, and Mi355X are not just “new GPUs”—they require dedicated kernel patches (e.g., vLLM #56892, SGLang #33412).  
   - **Developer Action**: Never assume backward compatibility—validate on target hardware early.

2. **Speculative Decoding is Maturing into Production-Ready**  
   - DSpark, FlashMLA, and FP8 optimizations are converging in vLLM and SGLang.  
   - But **correctness still lags**—divergence on quantized models (llama.cpp #25618), determinism failures (SGLang #43055).  
   - **Developer Action**: Use speculative decoding only after validating output consistency—avoid in safety-critical systems.

3. **Security and Billing Integrity Are Non-Negotiable in Gateways**  
   - LiteLLM now uses signed Docker images (cosign); Ollama’s auto-updates risk storage exhaustion.  
   - Budget enforcement bypasses (LiteLLM #26672) and silent cost leakage remain open—critical for enterprise use.  
   - **Developer Action**: Pin versions, audit image signatures, and explicitly enable `include_usage` in streaming APIs.

> 🎯 **Final Recommendation for Application Developers**:  
> Choose your stack based on **deployment layer**:  
> - **High-throughput inference** → vLLM or SGLang  
> - **Local edge/offline** → llama.cpp  
> - **Multi-model agentic apps** → Ollama + LiteLLM  
> - **Fast LoRA fine-tuning** → Unsloth  
> Always test on **target hardware**, avoid `v0.40.x` (Ollama), and validate **cost, correctness, and stability** before production.

---  
*Report compiled from GitHub activity (2026-10-10). Data sources: vLLM, SGLang, llama.cpp, Ollama, LiteLLM, Unsloth.*

---

## 各项目详细报告

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

### **vLLM Digest — 2026-10-10**

#### **1. 今日亮点**  
vLLM 项目持续聚焦在 Blackwell（SM120）和 ROCm（gfx950/Mi355X）硬件上新一代模型的稳定性与性能，针对 DeepSeek-V4.1-Flash 与 GLM-5.3-Flash 在多个后端的关键修复已推进。推测在推测解码正确性（PR #60252）、Ampere GPU 上 Qwen 混合模型的 FP8 支持（PR #60602），以及 RDNA4 上的 ROCm 特定内核优化方面取得重大进展，以消除性能下降问题。

#### **2. 发布与破坏性变更**  
*过去 24 小时内无新发布。*  
然而，正在进行的工作表明，`v0.30.1rc1` 和夜间构建版本可能包含以下破坏性变更：  
- **KV 缓存 dtype 回退逻辑**：当 `kv_cache_dtype="fp8"` 且 JIT 缺失时，现在将自动选择 FlashInfer 后端而不再回退——导致崩溃（#60262）。修复正在等待中。  
- **批处理不变行为**：尽管已有支持，但完整一致性仍在积极优化中（#27433）。

> 🔗 [Issue #60262](https://github.com/vllm-project/vllm/issues/60262) | [RFC #59665](https://github.com/vllm-project/vllm/issues/59665)

#### **3. 新模型与硬件支持**  
- ✅ **DeepSeek-V4.1-Flash**：通过 Model Runner V2 与 FlashMLA 稀疏注意力，在 CUDA 上新增预填充上下文并行（PCP）支持（PR #59857）。  
- ✅ **GLM-5.3-Flash**：现支持通用打包 KV 布局与推测环形槽位（PR #57169），提升内存效率与兼容性。  
- 📌 **AMD ROCm (gfx950 / Mi355X)**：对 `Qwen3.8-2.4T-A95B` 与 `Qwen3.8-Flash-Next` 的优化工作持续推进（问题 #57149, #59575）；包括在 RDNA4 上禁用 RowWise FP8 内核（PR #57861）。  
- 📌 **NVIDIA SM120 (Blackwell)**：针对 DeepSeek-V4.1-Flash 在解码吞吐量下降的问题（`--enforce-eager` 与 CUDA 图捕获）正优先修复（问题 #56892）。

> 🔗 [PR #59857](https://github.com/vllm-project/vllm/pull/59857) | [PR #57169](https://github.com/vllm-project/vllm/pull/57169) | [PR #57861](https://github.com/vllm-project/vllm/pull/57861)

#### **4. 性能与优化**  
- **Ampere (sm_80/sm_86) 上的 FP8**：PR #60602 通过将不支持的 `fp8e4nv` 指针访问替换为位转换解码，实现对 `Qwen3.8-Flash-Next` 的 `--kv-cache-dtype fp8` 支持——解决崩溃问题的同时保持精度。  
- **HiSparse 优化**：PR #60103 通过将槽位失效合并为一次 GPU 启动（从 ~61 → 1），降低多层模型（如 DeepSeek-V3.2）每层解码开销，显著减少延迟。  
- **ROCm MLA LSE 布局修复**：PR #60822 将 FlashAttention 的 log-sum-exp 张量布局（`[num_heads, num_query_tokens]`）标准化为符合 vLLM 预期的格式，修复分块预填充失败问题（问题 #60822）。  
- **结构化输出效率**：PR #44619 限制 JSON 模式有限状态机中的空白字符，防止令牌生成失控（修复 #38696）。

> 🔗 [PR #60602](https://github.com/vllm-project/vllm/pull/60602) | [PR #60103](https://github.com/vllm-project/vllm/pull/60103) | [PR #60822](https://github.com/vllm-project/vllm/pull/60822)

#### **5. 稳定性与回归问题**  
| 严重程度 | 问题 | 描述 | 修复状态 |
|--------|------|-------------|------------|
| ⚠️ 高 | [#56892](https://github.com/vllm-project/vllm/issues/56892) | DeepSeek-V4.1-Flash 在 SM120（RTX PRO 6000 Blackwell）上使用 `--enforce-eager` 时解码吞吐量极低；CUDA 图不可用 | ✅ *相关 PR 正在进行中* |
| ⚠️ 高 | [#56868](https://github.com/vllm-project/vllm/issues/56868) | GLM-5.3-Flash 在累积推理解码后出现长期解码退化（量化 W4A16） | 🔴 *严重问题；暂无修复* |
| ⚠️ 高 | [#54317](https://github.com/vllm-project/vllm/issues/54317) | 多个内核在 4xB200 上反复出现非法内存访问（KDA 线性注意力、MHC TileLang、TRT-LLM 融合 MoE） | 🔴 *未解决；影响生产环境* |
| ⚠️ 中 | [#60262](https://github.com/vllm-project/vllm/issues/60262) | `kv_cache_dtype="fp8"` 因缺失 FlashInfer JIT 回退导致崩溃 | ✅ *修复 PR 已提交 (#60262)* |
| ⚠️ 低 | [#59770](https://github.com/vllm-project/vllm/issues/59770) | Nemotron-3.5-Lightning NVFP4 在 DGX Spark（GB10/SM121）上解码速度比 v0.29.0 低约 16% | 🔴 *确认为回归问题，暂无修复* |

> 🔗 [Issue #56892](https://github.com/vllm-project/vllm/issues/56892) | [Issue #54317](https://github.com/vllm-project/vllm/issues/54317)

#### **6. 对应用开发者的意义**  
- **在旧版 GPU（sm_80/sm_86）上谨慎使用 `kv_cache_dtype=fp8`**：请确保使用包含 PR #60602 的构建版本，或暂勿使用直至稳定。  
- **若部署 DeepSeek-V4.1-Flash，请避免在 Blackwell GPU 上使用 `--enforce-eager`**：否则将遭遇严重性能损失。建议启用支持 SM120 的 CUDA 图。  
- **对于代理类工作负载**：由于缺乏上下文感知的 KV 缓存保留机制（问题 #37003），并发负载下前缀缓存将失效——建议采用手动状态管理方案。  
- **预计 v0.30.0+ 版本将显著提升稳定性**，随着 LoRA 生命周期修复（#48297）、结构化输出正确性（#54437）及批处理不变行为（#27433）等补丁逐步落地。  
- **若在 AMD ROCm 上部署**，请确认模型使用 `gfx950` 优化内核，并通过配置或打补丁方式在 RDNA4 上禁用 RowWise FP8。

> 🔗 [Issue #37003](https://github.com/vllm-project/vllm/issues/37003) | [PR #60602](https://github.com/vllm-project/vllm/pull/60602)

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

**SGLang 消息简报 – 2026-10-10**

---

### **1. 今日亮点**  
SGLang 项目持续推进 DeepSeek-V4.1 与 DSpark 预测解码的深度优化，多个 PR 针对 TP8 和 AMD GPU 上的 CUDA graph 稳定性进行改进。关键正确性修复已合并至 `SGLANG_RAGGED_VERIFY_MODE=compact` 与 `--enable-deterministic-inference`，同时新工作加速了 ComfyUI 集成中的多模态扩散支持。生态系统保持高度活跃，今日超过 20 个 PR 落地，聚焦于内核融合、内存管理及后端特定优化。

---

### **2. 发布与破坏性变更**  
*无*。过去 24 小时内未发布新版本或破坏性变更。

---

### **3. 新模型与硬件支持**  
- ✅ **AMD MI355X 支持**：PR #43499 修复了在 AMD GPU 上运行 DeepSeek-V4.1-Flash 且 TP > 1 时出现的四个 MegaMoE 崩溃问题，使大型 MoE 模型的推理更加稳定。
- ✅ **摩尔线程（MUSA）GPU 支持**：功能请求 #16565 仍开放但积极讨论中；PR #43398 改进了集成 MUSA 设备的主机内存处理。
- ✅ **ComfyUI 集成增强**：多个 PR (#43490, #43493, #43495) 统一修复了 SGLDiffusion 插件中 FluxAdapter 与动态 LoRA 处理的正确性问题，提升了扩散模型服务的可靠性。

> 🔗 [PR #43499](https://github.com/sgl-project/sglang/pull/43499) | [PR #43398](https://github.com/sgl-project/sglang/pull/43398) | [PR #43490](https://github.com/sgl-project/sglang/pull/43490)

---

### **4. 性能与优化**  
- 🚀 **DeepSeek-V4.1 预填充优化**：PR #43498 在长前缀预填充阶段启用 FP8 缓存直接读取，减少冗余反量化开销。结合 #40421（复用压缩 KV），目标实现显著吞吐提升。
- 🔥 **AMD 上的内核融合**：PRs #42497（按 token-group 的 FP4 查询量化）和 #42498（MXFP8 量化至小 MoE 排序）在大注意力头场景下将内核启动次数降低高达 ~50%，提升解码效率。
- ⚙️ **CUDA Graph 重用与流水线重构**：一系列 PR (#43460–#43453) 重构流水线阶段绑定逻辑，消除冗余元数据构建，改善 JIT 编译性能——对高吞吐推理至关重要。
- 📈 **内存效率优化**：PR #43495 在 ComfyUI 模式下增加早期验证，防止无声特征丢失，提升资源利用率并减少无效计算。

> 🔗 [PR #43498](https://github.com/sgl-project/sglang/pull/43498) | [PR #42497](https://github.com/sgl-project/sglang/pull/42497) | [PR #43460–43453](https://github.com/sgl-project/sglang/pulls?q=is%3Aopen+label%3ARefactor+sort%3Aupdated-desc)

---

### **5. 稳定性与回归问题**  
- ⚠️ **关键确定性缺陷**：问题 #43055 报告 `--enable-deterministic-inference` 在 `gpt-oss-20b` 上失效，尽管已启用却产生非确定性输出。*尚未修复。*
- ⚠️ **SM120 上解码图崩溃**：问题 #33412 确认在 `compact ragged-verify` 路径中因 `fused_norm_rope_v2` c4 压缩器存储中的 IMA 导致崩溃（如 H100）。*通过 #31023 / #33356 的跨 TP 计划一致性修复。*
- ⚠️ **僵尸请求泄漏**：问题 #36333 显示断开连接的客户端会留下僵尸请求，解码至 `max_tokens`，导致日志中充斥 `"state was deleted in TokenizerManager"`。*由回滚 #34160 引发的回归。*
- ⚠️ **中断时的 VMM 内存泄漏**：问题 #43402 指出当多模态请求在执行前被中止时，CUDA VMM 传输切片未被释放，导致负载下内存耗尽。

> 🔗 [Issue #43055](https://github.com/sgl-project/sglang/issues/43055) | [Issue #33412](https://github.com/sgl-project/sglang/issues/33412) | [Issue #36333](https://github.com/sgl-project/sglang/issues/36333) | [Issue #43402](https://github.com/sgl-project/sglang/issues/43402)

---

### **6. 对应用开发者的影响**  
- **谨慎使用 `--enable-deterministic-inference`**：在 #43055 修复前，请手动验证确定性行为——预计在 `gpt-oss-20b` 上会出现不一致表现。
- **充分利用 DSpark + FP8 优化**：部署 DeepSeek-V4.1 时，请确保使用最新代码，包含 #43498 和 #40421 等优化，避免不必要的反量化开销。
- **监控 ComfyUI 集成稳定性**：若使用 SGLDiffusion 配合 `FluxAdapter`，请通过 #43490 和 #43493 应用最新修复，避免静默失败（如 ControlNet 丢失）。
- **避免在 SM120 上使用 TP8 + compact verify**：在确认稳定前，若运行 MoE 模型，请避开在 H100 类硬件上使用 `SGLANG_RAGGED_VERIFY_MODE=compact`。
- **优雅处理客户端断连**：实现超时与会话清理逻辑——僵尸请求可能导致内存膨胀和日志泛滥。

> 💡 实用提示：在 TP4+ 环境下，使用 `--speculative-algorithm DSPARK --speculative-dspark-block-size 5` 配合 `--cuda-graph-max-bs-decode 64` 可获得最优预测解码吞吐。

---  
*本简报基于 GitHub 活动汇总（2026-10-10）。来源：[sgl-project/sglang](https://github.com/sgl-project/sglang)*

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

**llama.cpp 消息简报 – 2026-10-10**

---

### **1. 今日亮点**  
最新更新聚焦于推测解码的关键性能与稳定性改进，SYCL 上 MoE 推理加速，以及 GPU 后端优化。值得注意的是，**SYCL 现已将 MXFP4 MoE 模型的推理速度提升高达 2 倍**，而 CUDA 与 OpenCL 则针对 Flash Attention、内核编译和内存管理进行了针对性修复。

---

### **2. 发布版本与破坏性变更**  
- **新发布版本**：`b11540`、`b11539`、`b11538`、`b11537`、`b11535`、`b11534`、`b11533`、`b11532`、`b11531`、`b11530`  
  - [GitHub 发布 b11540](https://github.com/ggml-org/llama.cpp/releases/tag/b11540)  
  - 所有版本均包含底层修复与功能补丁；未报告任何破坏性 API 变更。

---

### **3. 新模型与硬件支持**  
- ✅ **模型支持**：  
  - 通过 PR #29416（支持 3D RoPE）新增对 **MiniCPM-V 4.7** 的支持。  
  - 在推测解码路径中增强与 **Qwen3.8-Flash-Next** 和 **Gemma4-Assistant** 的兼容性（PRs #30257、#29809）。  
- ✅ **硬件/后端支持**：  
  - **SYCL**：新增完整的设备端 MoE 调度支持（PR #30156），显著提升 GPU 利用率。  
  - **OpenCL**：优化 `dk=512`（Gemma-4）与 `dk=64`（GPT-OSS-20B）的 Flash Attention 支持；新增 Adreno 二进制内核用于 MoE Q4_K/Q6_K（PR #30187、#30266）。  
  - **Vulkan**：现已支持多 GPU 共享主机内存访问（PR #29741）；CI 已升级至 NVIDIA r615 驱动（Issue #28659）。

---

### **4. 性能与优化**  
- **SYCL MoE 加速**：  
  - **gpt-oss-20b**：解码速度从 **55.84 → 106.64 tok/s（提升 1.91×）**  
  - **gpt-oss-120b（双 GPU）**：解码速度翻倍（**33.27 → 67.14 tok/s**）  
  - 提示词处理性能亦获提升（**594.5 → 600.1 tok/s**）  
  - *来源*：[PR #29809](https://github.com/ggml-org/llama.cpp/pull/29809)  
- **CUDA**：  
  - 减少 `SSM_SCAN` 路径中的冗余 GPU-CPU 数据拷贝（PR #29807）。  
  - 修复 MSVC 环境下的舍入误差问题（PR #30229）。  
- **CPU**：  
  - s390x 平台上的 FP32→FP16 转换实现向量化，使令牌生成性能提升约 **6.3%**（PR #30157）。  
- **内核优化**：  
  - Vulkan：针对 NVIDIA 设备调优 MMVQ 行几何结构（MUL_MAT_ID 使用 4 行）——显著提升小批量性能（PR #29274）。

---

### **5. 稳定性与回归问题**  
| 严重程度 | 问题描述 | 状态 | 修复 PR？ | 链接 |
|--------|------|--------|--------|------|
| 🔴 高 | 在贪婪采样下，量化目标（`Q4_K_M`）的推测解码出现结果分歧 | 开放 | ❌ | [#25618](https://github.com/ggml-org/llama.cpp/issues/25618) |
| 🔴 高 | `llama-server` 在使用 `Qwen3.8-Flash-Next` + MTP 草稿模型时崩溃 | 开放 | ❌ | [#29811](https://github.com/ggml-org/llama.cpp/issues/29811) |
| 🟡 中 | AMD Vulkan 的 `DeviceLostError` 对 `ubatch_size` 与上下文长度敏感 | 已关闭（过期） | ✅ | [#20515](https://github.com/ggml-org/llama.cpp/issues/20515) |
| 🟡 中 | 长对话过程中发生 `bad allocation` 崩溃 | 开放 | ❌ | [#30091](https://github.com/ggml-org/llama.cpp/issues/30091) |
| 🟡 中 | PR #29622 后出现性能下降（SYCL，Qwen3.8-Flash-Next） | 开放 | ❌ | [#30033](https://github.com/ggml-org/llama.cpp/issues/30033) |
| 🟡 中 | A6X GPU 内核编译失败（着色器编译器崩溃） | 开放 | ✅ | [#30176](https://github.com/ggml-org/llama.cpp/issues/30176) |

> **注意**：多个回归问题与 **推测解码**、**MoE 模型** 以及 **特定 GPU 后端**（AMD/Vulkan、NVIDIA/CUDA、Apple/Metal）相关。

---

### **6. 对应用开发者的启示**  
- **在量化模型上使用推测解码需谨慎** —— 在 #25618 修复前，`Q4_K_M` 等格式可能出现输出不一致。  
- **优先选用 SYCL 处理 MXFP4 MoE 工作负载** —— 实际可获得高达 **2 倍的解码速度提升**。  
- **通过 `/v1/completions` 启用 `logit_gate`**（PR #30265），实现无需完整自回归的快速工具选择 —— 适用于智能体系统。  
- **若在 Vulkan/AMD 上遇到崩溃，请避免使用大 `ubatch_size` 或长上下文**（参见 #20515、#30091）。  
- **建议升级至 `b11540+`** 以获取最新的 MoE、Flash Attention 与内核稳定性修复。  
- **注意监控 `--split-mode tensor` 的使用情况** —— 已知其与 DFlash 推测存在兼容问题（issue #27833）。  

> 💡 **实用提示**：在多 GPU 系统上进行高吞吐量推理时，请确保使用最新版 Vulkan 构建并启用多设备共享内存（PR #29741），以避免性能瓶颈。

---  
*数据来源：[ggml-org/llama.cpp GitHub](https://github.com/ggml-org/llama.cpp)*

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

**Ollama Digest – 2026-10-10**

---

### **1. 今日亮点**  
Ollama 生态系统持续演进，关键改进集中在 OpenAI 兼容 API 的对齐性以及模型运行时的稳定性，特别是在工具调用和推理处理方面。针对 MLX 与 CUDA 后端的严重回归问题——尤其是高端硬件如 RTX 5070 Ti 和 Apple M4 上的问题——正在积极修复中，多个 PR 正在解决核心推理正确性与内存管理问题。

---

### **2. 发布与破坏性变更**  
*过去 24 小时内未发布新版本。*  
然而，`v0.40.x` 版本中的持续变更引入了破坏性行为：  
- **自动模型升级**现已默认启用（参见 [Issue #18909](https://github.com/ollama/ollama/issues/18909)），可能在资源受限系统上引发意外的磁盘占用。目前虽有禁用需求，但尚未提供相应选项。  
- **本地模型兼容性迁移步骤**因高吞吐场景下的性能开销（如嵌入任务）而被临时跳过（[PR #18908](https://github.com/ollama/ollama/pull/18908)）。

---

### **3. 新模型与硬件支持**  
- **MLX Runner**：通过 [PR #18780](https://github.com/ollama/ollama/pull/18780) 新增对 **Kolibri 1** 的支持，扩展了 Apple Silicon GPU 加速选项。  
- **模型架构**：`llama.cpp` 已更新至 **b11521**，原生支持 **决策模型**（如 `basal-1.0`）和 **EmbeddingGemma 2**（[PR #18917](https://github.com/ollama/ollama/pull/18917)）。  
- **新模型请求**：社区对 **d1-3B** 与 **d1-omni-600M**（具备图像支持的小型决策模型）的需求持续增长——目前因不支持编码格式（`lfm2-d1`）而受阻（[Issue #18890](https://github.com/ollama/ollama/issues/18890)）。

---

### **4. 性能与优化**  
- **流式 API 对齐**：PR [#18914](https://github.com/ollama/ollama/pull/18914) 与 [#18913](https://github.com/ollama/ollama/pull/18913) 将 `/v1/completions` 流式输出格式及工具调用解析与 OpenAI 规范对齐，减少客户端偏差。  
- **内存效率优化**：PR [#18915](https://github.com/ollama/ollama/pull/18915) 改进用户体验，移除了模型显示名称中的 `:latest`，提升 `ollama list` 与 `/api/tags` 命令的清晰度。  
- **嵌入功能增强**：`EmbeddingGemma2Model` 架构现可通过每项字典形式支持多模态输入（文本 + 媒体），提升混合嵌入的灵活性（[PR #18820](https://github.com/ollama/ollama/pull/18820)）。

---

### **5. 稳定性与回归问题**  
⚠️ **今日报告的关键问题**：  
1. **MLX 在 Qwen3.6:35b-mlx 上崩溃**（[Issue #18856](https://github.com/ollama/ollama/issues/18856)）：`v0.40.x` 中可复现的恐慌，`v0.35.0` 下正常。疑似与近期 `llama.cpp` 更新有关。*修复待定*。  
2. **RTX 5070 Ti 上 CUDA 初始化失败**（[Issue #17380](https://github.com/ollama/ollama/issues/17380)）：间歇性出现 `shared object initialization failed`，导致无声回退至 CPU。与 `CUDA_Host → CPU` 缓冲区退化相关。*高严重性，影响使用新显卡的 Windows 用户*。  
3. **Gemma4:12b 因缺失 `ctx_other` 而失败**（[Issue #18898](https://github.com/ollama/ollama/issues/18898)）：尽管 `gemma4:latest` 可正常工作，仍报错。可能是模板或配置不匹配所致。  
4. **Qwen3.5:4b 仅返回 `thinking` 内容**（[Issue #18916](https://github.com/ollama/ollama/issues/18916)）：完整响应中无 `content` 或 `tool_calls`，仅含 `message.thinking`。在长上下文场景下观察到静默失败。  

> ✅ **修复进行中**：多个 PR 正在解决根本原因（例如，[#18911](https://github.com/ollama/ollama/pull/18911) 中的推理标签解析，[#18906](https://github.com/ollama/ollama/pull/18906) 中的 UTF-8 保留）。

---

### **6. 对应用开发者的影响**  
- **若使用 MLX 或高端 CUDA**，请避免 `v0.40.x` —— 预期存在回归；建议暂定为 `0.35.0` 直至修复上线。  
- **严格验证工具调用与推理输出** —— 当前已接受 `reasoning_content` 与 `custom_tool_call` 字段，但旧版本会静默丢弃它们（[PR #18911](https://github.com/ollama/ollama/pull/18911), [#18913](https://github.com/ollama/ollama/pull/18913)）。  
- **设计上下文感知的模型选择逻辑** —— 新决策模型（如 `basal-1.0`, `d1-*`）需正确提示格式与 `num_predict` 控制，因 `/v1/chat/completions` 中 `max_tokens` 已被忽略（[Issue #18575](https://github.com/ollama/ollama/issues/18575)）。  
- **监控更新期间的存储使用** —— 自动模型升级可能在无预警情况下耗尽 SSD 空间（[Issue #18909](https://github.com/ollama/ollama/issues/18909)）。  

> 🛠️ **建议**：生产环境使用 `OLLAMA_AUTO_UPDATE=false`，直至稳定升级控制功能发布。

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

### **LiteLLM Digest — 2026-10-10**

#### **1. 今日亮点**  
LiteLLM v1.106.0-dev.3 引入了增强的安全性，通过 cosign 对 Docker 镜像进行签名，强化了生产部署中的信任机制。关键稳定性改进正在进行中，已启动专项冲刺，重点修复预算控制、支出追踪和流式成本注入中的高严重性漏洞——这些是多租户 LLM 网关的核心关切。

#### **2. 发布与破坏性变更**  
- **v1.106.0-dev.3**：聚焦安全的开发版发布，使用 [cosign](https://github.com/BerriAI/litellm/commit/0112e53046018d726492c814b3644b7d376029d0) 实现镜像签名验证。所有官方 Docker 镜像现已经过密码学签名，防止供应链篡改。  
  > 🔐 验证命令：`cosign verify --certificate-oidc-issuer=https://token.actions.githubusercontent.com berriai/litellm:v1.106.0-dev.3`

#### **3. 新模型与硬件支持**  
- ✅ **MiniMax-H3 / H3-Max 视频生成**：通过 `/v1/videos` 接口新增支持（PR #43705）。  
- ✅ **DASHSCOPE Wan 3.0, Wan 2.7, HappyHorse**：视频模型现原生支持 `/v1/videos`（PR #43706）。  
- ✅ **CoralBricks**：新增原生 OpenAI 兼容提供者（PR #44700）。  
- ✅ **Databricks Agent Platform**：完整代理网关集成已启用（PR #45681），包含 OAuth token 处理和 AI Function 路由（PR #45722）。

#### **4. 性能与优化**  
- **跨提供者缓存历史估算** 优化（PR #44948）：现在使用模型限定前缀和价格感知的使用追踪，避免在层级切换时出现基线拆分问题。  
- **流式成本注入现在尊重 `include_usage` 标志**（PR #38504）：禁用时可防止无声的成本泄漏，并保持快速路径性能。  
- **支出日志一致性修复**：流式与非流式请求现在共享同一 `SpendLogs` 记录（PR #45730），解决 SEV1 回归中 $0 或缺失支出行的问题。

#### **5. 稳定性与回归问题**  
今日报告的最高优先级稳定性问题（按严重性排序）：

| 问题 | 严重性 | 状态 | 修复 PR |
|------|----------|--------|--------|
| [#30484](https://github.com/BerriAI/litellm/issues/30484) | 高 | 打开 | 进行中 |
| [#26672](https://github.com/BerriAI/litellm/issues/26672) | 严重 | 打开 | 尚无修复 |
| [#36926](https://github.com/BerriAI/litellm/issues/36926) | 高 | 打开 | 尚无修复 |
| [#45422](https://github.com/BerriAI/litellm/issues/45422) | 中等 | 打开 | 尚无修复 |
| [#45379](https://github.com/BerriAI/litellm/issues/45379) | 中等 | 打开 | 尚无修复 |

> ⚠️ **严重**：`v1.82.3` 版本中预算强制绕过问题（#26672）以及虚拟密钥中过期支出报告问题（#27735, #36926）仍未解决。这些问题影响生产系统的计费完整性。  
> 🛑 **回归**：`end_user` 字段在共享虚拟密钥下错误地绑定到首次请求的 `user`（PR #31441，v1.87.0 版本引入的回归）。

#### **6. 对应用开发者的影响**  
- **安全性**：部署至生产环境前，务必使用 `cosign` 验证镜像签名——此操作现为强制要求。  
- **计费完整性**：若预算控制至关重要，请避免使用 `v1.82.3` 及更早版本；密切监控问题 #26672。  
- **多租户系统**：建议使用新的按月令牌预算功能（问题 #44555），实现团队间公平使用，无需依赖美元限额。  
- **流式应用**：如需准确的成本追踪，请显式设置 `include_usage: true`——否则成本可能被静默丢弃（PR #45730）。  
- **新提供者**：充分利用 CoralBricks、MiniMax、DASHSCOPE 以及 Databricks 代理的原生支持，简化集成并减少代理配置开销。

> 🔗 查看变更详情：[GitHub 仓库](https://github.com/BerriAI/litellm) | [发布记录](https://github.com/BerriAI/litellm/releases) | [问题列表](https://github.com/BerriAI/litellm/issues)

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# **Unsloth Digest – 2026-10-10**

---

### **1. 今日亮点**  
Unsloth 生态系统持续扩展对多模态和代理驱动工作流的支持，修复了 AMD ROCm 兼容性及 Studio 中的 GPU 内存管理关键问题。重点 PR 解决了长期存在的 FP8 LoRA 核函数问题、RTX 6000 上的非法内存访问，以及在 Windows AMD 系统上的模型加载行为——尤其影响 Qwen3-VL 和 GGUF 推理。

---

### **2. 发布与破坏性变更**  
*过去 24 小时内未检测到新发布版本。*  
然而，正在进行的 PR 表明即将推出 TRL 集成的破坏性变更：  
- 正在实现对 TRL 1.14–1.15 的支持（PR #13203），包括更新 ORPO/CPO 行数上限和掩码 GRPO 采样指标。  
- `AsyncGRPOTrainer` 现可通过打补丁方式支持 Unsloth 模型（PR #13205）。  
> 🔗 [PR #13203](https://github.com/unslothai/unsloth/pull/13203) | [PR #13205](https://github.com/unslothai/unsloth/pull/13205)

---

### **3. 新模型与硬件支持**  
- ✅ **Qwen-Image-2.1-Turbo-GGUF** 已添加至 Studio 模型选择器，并支持完整的 8 步采样调度（PR #13159）。  
- ✅ 通过 `install.sh` 检测逻辑新增对 **Intel Arc / Data Center GPU (XPU)** 的支持（PR #13193）。  
- ✅ Studio 安装程序强制要求 Linux AMD 主机使用 **AMD ROCm 6.4+ PyTorch**，以防止 bitsandbytes 崩溃（PR #10277）。  
- ✅ 已提出对 **Qwen3-TTS** 微调支持的需求（Issue #3951）；尚未实现但正在考虑中。  

> 🔗 [PR #13159](https://github.com/unslothai/unsloth/pull/13159) | [PR #13193](https://github.com/unslothai/unsloth/pull/13193) | [Issue #3951](https://github.com/unslothai/unsloth/issues/3951)

---

### **4. 性能与优化**  
- **FP8 LoRA 核函数修复** 通过修复元素偏移中的 int32 溢出问题，解决了长批次下的非法内存访问（PR #13141）。  
- **Studio UI 改进**：  
  - 可选支持 `unsloth.ini` 文件用于针对 GGUF 的精细化调优（PR #13201）。  
  - 改进 Windows ROCm 环境下 OpenBLAS 的 CPU 线程控制（PR #13048）。  
- **模型上下文估算**：VRAM 适配的上下文阈值现在在 `/v1/models` API 中明确命名（PR #12599）。  
- **工具调用去重开关** 已引入 Studio 设置中（PR #11685）。  

> 🔗 [PR #13141](https://github.com/unslothai/unsloth/pull/13141) | [PR #13201](https://github.com/unslothai/unsloth/pull/13201)

---

### **5. 稳定性与回归问题**  
**报告的关键问题：**  
1. **PRO RTX 6000 (96GB)** 上出现非法内存访问 —— 尽管已更新，TRITON CUDA 错误仍持续存在（Issue #3921）。  
   → 已在 PR #13141 修复（待合并）。  
2. **AMD Strix Halo (Windows)**：模型权重被加载至系统内存而非显存；GPU 计算活跃但无显存使用（Issue #7449）。  
   → 通过强制启用 ROCm 6.4+ 部分解决（PR #10277）。  
3. **Qwen3.8-27B V3 GGUF** 在 R9700（Vulkan）上预填充后崩溃 —— 回滚至 V2 可修复（Issue #9792）。  
   → 可能由 GGUF 格式回归或内核不匹配导致。  
4. **在 H100（80GB）上进行 GRPO 训练时即使小批次也发生 OOM**（Issue #3603）。  
   → 暗示 KV 缓存效率低下或存在隐藏内存泄漏。  

> 🔗 [Issue #3921](https://github.com/unslothai/unsloth/issues/3921) | [Issue #7449](https://github.com/unslothai/unsloth/issues/7449) | [Issue #9792](https://github.com/unslothai/unsloth/issues/9792) | [Issue #3603](https://github.com/unslothai/unsloth/issues/3603)

---

### **6. 对应用开发者的意义**  
- **对于使用 GRPO/PPO 的代理开发者**：预计不久将实现更紧密的 TRL 1.15 兼容性（PR #13205）。请在确认补丁已应用后再使用 `async_grpo.AsyncGRPOTrainer`。  
- **对于多 GPU/多设备部署**：请关注 AMD ROCm 与 Intel XPU 支持情况——近期 PR 显著提升了非 NVIDIA 硬件的稳定性。  
- **对于生产环境推理**：在 HF/Unsloth 验证构建前，请避免在 AMD GPU 上使用 `Qwen3-VL` GGUF V3 版本。建议优先使用 V2 或 BF16 流水线。  
- **对于内存受限环境**：如 #3603、#4504 所示，显存估算可能不准确——始终使用 `nvidia-smi` 验证并监控 KV 缓存使用情况。  
- **对于 Studio 用户**：启用 `unsloth.ini` 以实现细粒度 llama.cpp 调优；若需保证代理一致性，可禁用工具去重功能。

> 💡 **可操作提示**：若在 AMD 平台运行，请通过 `UNSLOTH_TORCH_INDEX_FAMILY=rocm64` 确保使用 ROCm 6.4+。在 Windows 系统上，请留意 RDNA4 空闲驱逐警告（PR #13208）。

---  
*本摘要由 GitHub 数据生成：[unslothai/unsloth](https://github.com/unslothai/unsloth)*

</details>

---
*本日报由 [agents-radar](https://github.com/Qyii22/agents-radar) 自动生成。*