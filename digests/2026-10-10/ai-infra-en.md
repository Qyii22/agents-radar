# AI Infrastructure Digest 2026-10-10

> Generated: 2026-10-10 06:21 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

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

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

---

### **vLLM Digest — 2026-10-10**

#### **1. Today's Highlights**  
The vLLM project continues to prioritize stability and performance for next-gen models on Blackwell (SM120) and ROCm (gfx950/Mi355X) hardware, with critical fixes for DeepSeek-V4.1-Flash and GLM-5.3-Flash across multiple backends. Major progress is underway in speculative decoding correctness (PR #60252), FP8 support for Qwen hybrid models on Ampere GPUs (PR #60602), and ROCm-specific kernel optimizations to eliminate performance regressions on RDNA4.

#### **2. Releases & Breaking Changes**  
*No new releases in the past 24 hours.*  
However, ongoing work suggests that `v0.30.1rc1` and nightly builds may include breaking changes related to:  
- **KV cache dtype fallback logic**: `kv_cache_dtype="fp8"` now auto-selects FlashInfer backend without fallback if JIT is missing — leading to crashes (#60262). A fix is pending.  
- **Batch invariant behavior**: While support exists, full consistency remains under active refinement (#27433).

> 🔗 [Issue #60262](https://github.com/vllm-project/vllm/issues/60262) | [RFC #59665](https://github.com/vllm-project/vllm/issues/59665)

#### **3. New Model & Hardware Support**  
- ✅ **DeepSeek-V4.1-Flash**: Added Prefill Context Parallelism (PCP) support on CUDA via Model Runner V2 with FlashMLA sparse attention (PR #59857).  
- ✅ **GLM-5.3-Flash**: Now supports generic packed KV layout and speculative ring slots (PR #57169), improving memory efficiency and compatibility.  
- 📌 **AMD ROCm (gfx950 / Mi355X)**: Ongoing optimization efforts for `Qwen3.8-2.4T-A95B` and `Qwen3.8-Flash-Next` (issues #57149, #59575); includes disabling RowWise FP8 kernels on RDNA4 (PR #57861).  
- 📌 **NVIDIA SM120 (Blackwell)**: Critical fixes targeting decode throughput degradation in DeepSeek-V4.1-Flash (`--enforce-eager` and CUDA graph capture) are being prioritized (issue #56892).

> 🔗 [PR #59857](https://github.com/vllm-project/vllm/pull/59857) | [PR #57169](https://github.com/vllm-project/vllm/pull/57169) | [PR #57861](https://github.com/vllm-project/vllm/pull/57861)

#### **4. Performance & Optimization**  
- **FP8 on Ampere (sm_80/sm_86)**: PR #60602 enables `--kv-cache-dtype fp8` for Qwen3.8-Flash-Next by replacing unsupported `fp8e4nv` pointer access with bit-cast decoding — resolving crashes while preserving precision.  
- **HiSparse Optimization**: PR #60103 reduces per-layer decode overhead by fusing slot invalidation into one GPU launch (from ~61 → 1), cutting latency in multi-layer models like DeepSeek-V3.2.  
- **ROCm MLA LSE Layout Fix**: PR #60822 normalizes FlashAttention’s log-sum-exp tensor layout (`[num_heads, num_query_tokens]`) to match vLLM expectations, fixing chunked-prefill failures (issue #60822).  
- **Structured Output Efficiency**: PR #44619 limits whitespace in JSON schema FSM to prevent runaway token generation (fixing #38696).

> 🔗 [PR #60602](https://github.com/vllm-project/vllm/pull/60602) | [PR #60103](https://github.com/vllm-project/vllm/pull/60103) | [PR #60822](https://github.com/vllm-project/vllm/pull/60822)

#### **5. Stability & Regressions**  
| Severity | Issue | Description | Fix Status |
|--------|------|-------------|------------|
| ⚠️ High | [#56892](https://github.com/vllm-project/vllm/issues/56892) | DeepSeek-V4.1-Flash shows extremely low decode throughput on SM120 (RTX PRO 6000 Blackwell) with `--enforce-eager`; CUDA graphs unusable | ✅ *PRs in flight* |
| ⚠️ High | [#56868](https://github.com/vllm-project/vllm/issues/56868) | GLM-5.3-Flash long-decode degeneration after accumulated reasoning decode (quantized W4A16) | 🔴 *Critical; no fix yet* |
| ⚠️ High | [#54317](https://github.com/vllm-project/vllm/issues/54317) | Recurring CUDA illegal memory access on 4xB200 across multiple kernels (KDA linear-attention, MHC TileLang, TRT-LLM fused MoE) | 🔴 *Unresolved; affects production* |
| ⚠️ Medium | [#60262](https://github.com/vllm-project/vllm/issues/60262) | `kv_cache_dtype="fp8"` crashes due to missing FlashInfer JIT fallback | ✅ *Fix PR open (#60262)* |
| ⚠️ Low | [#59770](https://github.com/vllm-project/vllm/issues/59770) | Nemotron-3.5-Lightning NVFP4 decode ~16% slower on DGX Spark (GB10/SM121) since v0.29.0 | 🔴 *Regression confirmed, no fix yet* |

> 🔗 [Issue #56892](https://github.com/vllm-project/vllm/issues/56892) | [Issue #54317](https://github.com/vllm-project/vllm/issues/54317)

#### **6. What This Means for Application Developers**  
- **Use caution with `kv_cache_dtype=fp8` on older GPUs (sm_80/sm_86)**: Ensure you're using a build with PR #60602 or avoid it until stable.  
- **Avoid `--enforce-eager` on Blackwell GPUs** if serving DeepSeek-V4.1-Flash — expect severe performance penalties. Prefer CUDA graphs with proper SM120 support.  
- **For agentic workloads**: The lack of context-aware KV-cache retention (issue #37003) means prefix caching will fail under concurrent load — consider manual state management.  
- **Expect higher stability from v0.30.0+** as bug fixes for LoRA lifecycle (#48297), structured output correctness (#54437), and batch invariant behavior (#27433) progress.  
- **If deploying on AMD ROCm**, verify your model uses `gfx950`-optimized kernels and disable RowWise FP8 on RDNA4 (via config or patch).

> 🔗 [Issue #37003](https://github.com/vllm-project/vllm/issues/37003) | [PR #60602](https://github.com/vllm-project/vllm/pull/60602)

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

**SGLang Digest – 2026-10-10**

---

### **1. Today’s Highlights**  
The SGLang project continues its aggressive optimization push for DeepSeek-V4.1 and DSpark speculative decoding, with multiple PRs targeting CUDA graph stability on TP8 and AMD GPUs. Critical correctness fixes were merged for `SGLANG_RAGGED_VERIFY_MODE=compact` and `--enable-deterministic-inference`, while new work accelerates multi-modal diffusion support in ComfyUI integration. The ecosystem remains highly active, with over 20 PRs landed today focused on kernel fusion, memory management, and backend-specific optimizations.

---

### **2. Releases & Breaking Changes**  
*None.* No new releases or breaking changes were published in the last 24 hours.

---

### **3. New Model & Hardware Support**  
- ✅ **AMD MI355X Support**: PR #43499 fixes four MegaMoE crashes at TP > 1 for DeepSeek-V4.1-Flash on AMD GPUs, enabling stable inference with large MoE models.
- ✅ **Moore Threads (MUSA) GPU Support**: Feature request #16565 remains open but actively discussed; PR #43398 improves host memory handling for integrated MUSA devices.
- ✅ **ComfyUI Integration Enhancements**: Multiple PRs (#43490, #43493, #43495) consolidate correctness fixes for FluxAdapter and dynamic LoRA handling in SGLDiffusion plugin, improving reliability of diffusion model serving.

> 🔗 [PR #43499](https://github.com/sgl-project/sglang/pull/43499) | [PR #43398](https://github.com/sgl-project/sglang/pull/43398) | [PR #43490](https://github.com/sgl-project/sglang/pull/43490)

---

### **4. Performance & Optimization**  
- 🚀 **DeepSeek-V4.1 Prefill Optimization**: PR #43498 enables direct FP8 cache reads during long-prefix prefill steps, reducing redundant dequantization overhead. Combined with #40421 (reuse compressed KV), this targets significant throughput gains.
- 🔥 **Kernel Fusion on AMD**: PRs #42497 (FP4 query quantization per token-group) and #42498 (MXFP8 quant into small MoE sort) reduce kernel launch count by up to ~50% on large attention heads, improving decode efficiency.
- ⚙️ **CUDA Graph Reuse & Pipeline Refactoring**: Stack of PRs (#43460–#43453) refactor pipeline stage binding logic to eliminate redundant metadata builds and improve JIT compilation performance—critical for high-throughput inference.
- 📈 **Memory Efficiency**: PR #43495 adds early validation in ComfyUI mode to prevent silent feature loss, improving resource utilization and reducing wasted compute.

> 🔗 [PR #43498](https://github.com/sgl-project/sglang/pull/43498) | [PR #42497](https://github.com/sgl-project/sglang/pull/42497) | [PR #43460–43453](https://github.com/sgl-project/sglang/pulls?q=is%3Aopen+label%3ARefactor+sort%3Aupdated-desc)

---

### **5. Stability & Regressions**  
- ⚠️ **Critical Determinism Bug**: Issue #43055 reports that `--enable-deterministic-inference` fails for `gpt-oss-20b`, producing non-deterministic outputs despite being enabled. *No fix yet.*
- ⚠️ **Decode Graph Crashes on SM120**: Issue #33412 confirms a crash in `compact ragged-verify` path due to IMA in `fused_norm_rope_v2` c4 compressor store on SM120 (e.g., H100). *Fixed in #31023 / #33356 via cross-TP planning consistency.*
- ⚠️ **Zombie Request Leak**: Issue #36333 shows disconnected clients leave zombie requests that decode to `max_tokens`, flooding logs with `"state was deleted in TokenizerManager"`. *Regression from revert of #34160.*
- ⚠️ **VMM Memory Leak on Abort**: Issue #43402 reveals CUDA VMM transport slices are not freed when a multimodal request is aborted before execution, leading to memory exhaustion under load.

> 🔗 [Issue #43055](https://github.com/sgl-project/sglang/issues/43055) | [Issue #33412](https://github.com/sgl-project/sglang/issues/33412) | [Issue #36333](https://github.com/sgl-project/sglang/issues/36333) | [Issue #43402](https://github.com/sgl-project/sglang/issues/43402)

---

### **6. What This Means for Application Developers**  
- **Use `--enable-deterministic-inference` with caution**: Verify determinism manually until #43055 is resolved—expect inconsistent behavior on `gpt-oss-20b`.
- **Leverage DSpark + FP8 optimizations**: For DeepSeek-V4.1 deployments, ensure you’re using latest code with PRs like #43498 and #40421 to avoid unnecessary dequantization overhead.
- **Monitor ComfyUI integration stability**: If using SGLDiffusion with `FluxAdapter`, apply recent fixes via #43490 and #43493 to avoid silent failures (e.g., dropped ControlNet).
- **Avoid TP8 + compact verify on SM120**: Until confirmed stable, steer clear of `SGLANG_RAGGED_VERIFY_MODE=compact` on H100-class hardware if running MoE models.
- **Handle client disconnections gracefully**: Implement timeouts and session cleanup logic—zombie requests can cause memory bloat and log spam.

> 💡 Pro Tip: Use `--speculative-algorithm DSPARK --speculative-dspark-block-size 5` with `--cuda-graph-max-bs-decode 64` for optimal speculative decode throughput on TP4+ setups.

---  
*Digest compiled from GitHub activity (2026-10-10). Source: [sgl-project/sglang](https://github.com/sgl-project/sglang)*

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

**llama.cpp Digest – 2026-10-10**

---

### **1. Today's Highlights**  
The latest updates focus on critical performance and stability improvements for speculative decoding, MoE inference acceleration on SYCL, and GPU backend optimizations. Notably, **SYCL now accelerates MXFP4 MoE models by up to 2×**, while CUDA and OpenCL see targeted fixes for flash attention, kernel compilation, and memory management.

---

### **2. Releases & Breaking Changes**  
- **New releases**: `b11540`, `b11539`, `b11538`, `b11537`, `b11535`, `b11534`, `b11533`, `b11532`, `b11531`, `b11530`  
  - [GitHub Release b11540](https://github.com/ggml-org/llama.cpp/releases/tag/b11540)  
  - All include low-level fixes and feature patches; no breaking API changes reported.

---

### **3. New Model & Hardware Support**  
- ✅ **Model Support**:  
  - Added support for **MiniCPM-V 4.7** via PR #29416 (3D RoPE handling).  
  - Enhanced compatibility with **Qwen3.8-Flash-Next** and **Gemma4-Assistant** in speculative decoding paths (PRs #30257, #29809).  
- ✅ **Hardware/Backend**:  
  - **SYCL**: Full device-side MoE scheduling added (PR #30156), enabling better GPU utilization.  
  - **OpenCL**: Improved Flash Attention support for `dk=512` (Gemma-4) and `dk=64` (GPT-OSS-20B); new Adreno bin kernels for MoE Q4_K/Q6_K (PR #30187, #30266).  
  - **Vulkan**: Now supports multiple GPUs accessing shared host memory (PR #29741); updated CI to use NVIDIA r615 driver (Issue #28659).

---

### **4. Performance & Optimization**  
- **SYCL MoE Acceleration**:  
  - **gpt-oss-20b**: Decode speed increased from **55.84 → 106.64 tok/s (1.91×)**  
  - **gpt-oss-120b (dual GPU)**: Decode speed doubled (**33.27 → 67.14 tok/s**)  
  - Prompt processing also improved (**594.5 → 600.1 tok/s**)  
  - *Source*: [PR #29809](https://github.com/ggml-org/llama.cpp/pull/29809)  
- **CUDA**:  
  - Reduced redundant GPU-CPU copies in `SSM_SCAN` path (PR #29807).  
  - Fixed round-off issues under MSVC (PR #30229).  
- **CPU**:  
  - FP32→FP16 conversion vectorized on s390x, yielding **~6.3% improvement in token generation** (PR #30157).  
- **Kernel Optimization**:  
  - Vulkan: MMVQ row geometry tuned for NVIDIA devices (4 rows for MUL_MAT_ID) — improves small-batch performance (PR #29274).  

---

### **5. Stability & Regressions**  
| Severity | Issue | Status | Fix PR? | Link |
|--------|------|--------|--------|------|
| 🔴 High | Speculative decoding divergence on quantized targets (`Q4_K_M`) under greedy sampling | Open | ❌ | [#25618](https://github.com/ggml-org/llama.cpp/issues/25618) |
| 🔴 High | `llama-server` crash with `Qwen3.8-Flash-Next` + MTP draft model | Open | ❌ | [#29811](https://github.com/ggml-org/llama.cpp/issues/29811) |
| 🟡 Medium | AMD Vulkan `DeviceLostError` sensitive to `ubatch_size` and context length | Closed (stale) | ✅ | [#20515](https://github.com/ggml-org/llama.cpp/issues/20515) |
| 🟡 Medium | `bad allocation` crash during long conversations | Open | ❌ | [#30091](https://github.com/ggml-org/llama.cpp/issues/30091) |
| 🟡 Medium | Performance regression after PR #29622 (SYCL, Qwen3.8-Flash-Next) | Open | ❌ | [#30033](https://github.com/ggml-org/llama.cpp/issues/30033) |
| 🟡 Medium | A6X GPU kernel compilation failure (shader compiler crash) | Open | ✅ | [#30176](https://github.com/ggml-org/llama.cpp/issues/30176) |

> **Note**: Several regressions are tied to **speculative decoding**, **MoE models**, and **GPU-specific backends** (AMD/Vulkan, NVIDIA/CUDA, Apple/Metal).

---

### **6. What This Means for Application Developers**  
- **Use caution with speculative decoding on quantized models** — expect potential output divergence on `Q4_K_M` and similar formats until #25618 is resolved.  
- **Leverage SYCL for MXFP4 MoE workloads** — gains of **up to 2× decode speed** are real and available today.  
- **Enable `logit_gate` via `/v1/completions`** (PR #30265) for fast tool selection without full autoregression — ideal for agent systems.  
- **Avoid large `ubatch_size` or long contexts on Vulkan/AMD** if experiencing crashes (see #20515, #30091).  
- **Update to `b11540+`** for the latest MoE, flash attention, and kernel stability fixes.  
- **Monitor `--split-mode tensor` usage** — known to fail with DFlash speculation (issue #27833).  

> 💡 **Pro Tip**: For high-throughput inference on multi-GPU systems, ensure you're using the latest Vulkan build with multi-device memory sharing (PR #29741) to avoid bottlenecks.

---  
*Data source: [ggml-org/llama.cpp GitHub](https://github.com/ggml-org/llama.cpp)*

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

**Ollama Digest – 2026-10-10**

---

### **1. Today's Highlights**  
The Ollama ecosystem continues to evolve with key improvements in OpenAI-compatible API parity and model runtime stability, particularly around tool calling and reasoning handling. Critical regressions affecting MLX and CUDA backends—especially on high-end hardware like the RTX 5070 Ti and Apple M4—are actively being addressed, with several PRs targeting core inference correctness and memory management.

---

### **2. Releases & Breaking Changes**  
*No new releases were published in the last 24 hours.*  
However, ongoing changes in `v0.40.x` have introduced breaking behavior:  
- **Automatic model upgrades** are now enabled by default (see [Issue #18909](https://github.com/ollama/ollama/issues/18909)), which may trigger unexpected disk usage on constrained systems. A disable option is requested but not yet available.  
- The **local model compatibility migration step** has been temporarily skipped ([PR #18908](https://github.com/ollama/ollama/pull/18908)) due to performance overhead in high-throughput scenarios (e.g., embedding workloads).

---

### **3. New Model & Hardware Support**  
- **MLX Runner**: Added support for **Kolibri 1** via [PR #18780](https://github.com/ollama/ollama/pull/18780), expanding Apple Silicon GPU acceleration options.  
- **Model Architecture**: Updated `llama.cpp` to **b11521**, enabling native support for **decision models** (e.g., `basal-1.0`) and **EmbeddingGemma 2** ([PR #18917](https://github.com/ollama/ollama/pull/18917)).  
- **New Model Requests**: Community demand grows for **d1-3B** and **d1-omni-600M** (small decision models with image support) — currently blocked by unsupported encoding (`lfm2-d1`) ([Issue #18890](https://github.com/ollama/ollama/issues/18890)).  

---

### **4. Performance & Optimization**  
- **Streaming API Alignment**: PRs [#18914](https://github.com/ollama/ollama/pull/18914) and [#18913](https://github.com/ollama/ollama/pull/18913) align `/v1/completions` streaming format and tool call parsing with OpenAI’s spec, reducing client-side drift.  
- **Memory Efficiency**: PR [#18915](https://github.com/ollama/ollama/pull/18915) improves UX by omitting `:latest` from model display names, aiding clarity in `ollama list` and `/api/tags`.  
- **Embeddings**: The `EmbeddingGemma2Model` architecture now supports multimodal inputs (text + media) via per-item dicts, enhancing flexibility for hybrid embeddings ([PR #18820](https://github.com/ollama/ollama/pull/18820)).

---

### **5. Stability & Regressions**  
⚠️ **Critical Issues Reported Today:**  
1. **MLX Crash on Qwen3.6:35b-mlx** ([Issue #18856](https://github.com/ollama/ollama/issues/18856)): Reproducible panic in `v0.40.x`, working in `v0.35.0`. Regression likely tied to recent llama.cpp update. *Fix pending*.  
2. **CUDA Initialization Failure on RTX 5070 Ti** ([Issue #17380](https://github.com/ollama/ollama/issues/17380)): Intermittent `shared object initialization failed`, causing silent fallback to CPU. Correlated with `CUDA_Host → CPU` buffer degradation. *High severity, affects Windows users with new GPUs*.  
3. **Gemma4:12b Fails Due to Missing `ctx_other`** ([Issue #18898](https://github.com/ollama/ollama/issues/18898)): Error occurs despite `gemma4:latest` working. Likely a template or config misalignment.  
4. **Qwen3.5:4b Returns Only `thinking` Content** ([Issue #18916](https://github.com/ollama/ollama/issues/18916)): Complete response contains no `content` or `tool_calls`, only `message.thinking`. Silent failure observed under long-context use.  

> ✅ **Fixes in Progress**: Multiple PRs address root causes (e.g., reasoning tag parsing in [#18911](https://github.com/ollama/ollama/pull/18911), UTF-8 preservation in [#18906](https://github.com/ollama/ollama/pull/18906)).

---

### **6. What This Means for Application Developers**  
- **Avoid `v0.40.x` if using MLX or high-end CUDA** — expect regressions; consider pinning to `0.35.0` until fixes land.  
- **Validate tool calls and reasoning output rigorously** — both `reasoning_content` and `custom_tool_call` fields are now accepted, but prior versions silently dropped them ([PR #18911](https://github.com/ollama/ollama/pull/18911), [#18913](https://github.com/ollama/ollama/pull/18913)).  
- **Design for context-aware model selection** — new decision models (e.g., `basal-1.0`, `d1-*`) require proper prompt formatting and `num_predict` control, as `max_tokens` is ignored in `/v1/chat/completions` ([Issue #18575](https://github.com/ollama/ollama/issues/18575)).  
- **Monitor storage during updates** — automatic model upgrades can exhaust SSD space without warning ([Issue #18909](https://github.com/ollama/ollama/issues/18909)).  

> 🛠️ **Recommendation**: Use `OLLAMA_AUTO_UPDATE=false` in production environments until stable upgrade controls are released.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

---

### **LiteLLM Digest — 2026-10-10**

#### **1. Today's Highlights**  
LiteLLM v1.106.0-dev.3 introduces enhanced security with signed Docker images via cosign, reinforcing trust in production deployments. Critical stability improvements are underway, with a dedicated sprint focused on fixing high-severity bugs in budget enforcement, spend tracking, and streaming cost injection—key concerns for multi-tenant LLM gateways.

#### **2. Releases & Breaking Changes**  
- **v1.106.0-dev.3**: Security-focused dev release with verified image signing using [cosign](https://github.com/BerriAI/litellm/commit/0112e53046018d726492c814b3644b7d376029d0). All official Docker images are now cryptographically signed to prevent supply chain tampering.  
  > 🔐 Verify: `cosign verify --certificate-oidc-issuer=https://token.actions.githubusercontent.com berriai/litellm:v1.106.0-dev.3`

#### **3. New Model & Hardware Support**  
- ✅ **MiniMax-H3 / H3-Max video generation**: Added support via `/v1/videos` (PR #43705).  
- ✅ **DASHSCOPE Wan 3.0, Wan 2.7, HappyHorse**: Video models now natively supported under `/v1/videos` (PR #43706).  
- ✅ **CoralBricks**: Native OpenAI-compatible provider added (PR #44700).  
- ✅ **Databricks Agent Platform**: Full agent gateway integration enabled (PR #45681), including OAuth token handling and AI Function routing (PR #45722).

#### **4. Performance & Optimization**  
- **Cross-provider cache history estimation** improved (PR #44948): Now uses model-bounded prefixes and price-aware usage tracking to avoid baseline split issues during tier switches.  
- **Streaming cost injection now respects `include_usage` flag** (PR #38504): Prevents silent cost leakage and preserves fast-path performance when disabled.  
- **Spend logging consistency fix**: Streaming and non-streaming requests now bill the same `SpendLogs` row (PR #45730), resolving $0 or missing spend rows in SEV1 regressions.

#### **5. Stability & Regressions**  
Top stability issues reported today (ranked by severity):

| Issue | Severity | Status | Fix PR |
|------|----------|--------|--------|
| [#30484](https://github.com/BerriAI/litellm/issues/30484) | High | Open | In progress |
| [#26672](https://github.com/BerriAI/litellm/issues/26672) | Critical | Open | No fix yet |
| [#36926](https://github.com/BerriAI/litellm/issues/36926) | High | Open | No fix yet |
| [#45422](https://github.com/BerriAI/litellm/issues/45422) | Medium | Open | No fix yet |
| [#45379](https://github.com/BerriAI/litellm/issues/45379) | Medium | Open | No fix yet |

> ⚠️ **Critical**: Budget enforcement bypasses in `v1.82.3` (`#26672`) and stale spend reporting in virtual keys (`#27735`, `#36926`) remain unresolved. These affect billing integrity in production systems.  
> 🛑 **Regression**: `end_user` field is incorrectly pinned to first request’s `user` across shared virtual keys (PR #31441, regression in v1.87.0).

#### **6. What This Means for Application Developers**  
- **Security**: Always validate image signatures using `cosign` before deploying in production—this is now mandatory.  
- **Billing Integrity**: Avoid `v1.82.3` and earlier versions if budget enforcement is critical; monitor issue #26672 closely.  
- **Multi-Tenant Systems**: Use new monthly token-based budgets (issue #44555) to enforce fair usage across teams without relying on dollar caps.  
- **Streaming Apps**: Ensure `include_usage: true` is explicitly set if you need accurate cost tracking—otherwise, costs may be silently dropped (PR #45730).  
- **New Providers**: Leverage native support for CoralBricks, MiniMax, DASHSCOPE, and Databricks agents to simplify integrations and reduce proxy configuration overhead.

> 🔗 Explore changes: [GitHub Repo](https://github.com/BerriAI/litellm) | [Releases](https://github.com/BerriAI/litellm/releases) | [Issues](https://github.com/BerriAI/litellm/issues)

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# **Unsloth Digest – 2026-10-10**

---

### **1. Today's Highlights**  
The Unsloth ecosystem continues to expand its support for multimodal and agent-driven workflows, with critical fixes for AMD ROCm compatibility and GPU memory management in Studio. Key PRs address long-standing issues around FP8 LoRA kernels, illegal memory access on RTX 6000, and model loading behavior on Windows AMD systems—especially affecting Qwen3-VL and GGUF inference.

---

### **2. Releases & Breaking Changes**  
*No new releases detected in the last 24 hours.*  
However, ongoing work in PRs suggests upcoming breaking changes in TRL integration:  
- **Support for TRL 1.14–1.15** is being implemented (PR #13203), including updates to ORPO/CPO row caps and masked GRPO sampling metrics.  
- `AsyncGRPOTrainer` now supports Unsloth models via patching (PR #13205).  
> 🔗 [PR #13203](https://github.com/unslothai/unsloth/pull/13203) | [PR #13205](https://github.com/unslothai/unsloth/pull/13205)

---

### **3. New Model & Hardware Support**  
- ✅ **Qwen-Image-2.1-Turbo-GGUF** added to Studio’s model picker with full 8-step sampling schedule support (PR #13159).  
- ✅ **Intel Arc / Data Center GPU (XPU)** support added via `install.sh` detection logic (PR #13193).  
- ✅ **AMD ROCm 6.4+ PyTorch** enforced in Studio installer for Linux AMD hosts to prevent bitsandbytes crashes (PR #10277).  
- ✅ **Qwen3-TTS** fine-tuning support requested (Issue #3951); not yet implemented but under consideration.  

> 🔗 [PR #13159](https://github.com/unslothai/unsloth/pull/13159) | [PR #13193](https://github.com/unslothai/unsloth/pull/13193) | [Issue #3951](https://github.com/unslothai/unsloth/issues/3951)

---

### **4. Performance & Optimization**  
- **FP8 LoRA kernel fix** resolves illegal memory access on long batches by fixing int32 overflow in element offsets (PR #13141).  
- **Studio UI improvements**:  
  - Optional `unsloth.ini` file support for per-GGUF tuning (PR #13201).  
  - Improved CPU thread control for OpenBLAS on Windows ROCm (PR #13048).  
- **Model context estimation**: VRAM-fit context threshold now named explicitly in `/v1/models` API (PR #12599).  
- **Tool-call deduplication toggle** introduced in Studio settings (PR #11685).  

> 🔗 [PR #13141](https://github.com/unslothai/unsloth/pull/13141) | [PR #13201](https://github.com/unslothai/unsloth/pull/13201)

---

### **5. Stability & Regressions**  
**Critical Issues Reported:**  
1. **Illegal memory access on PRO RTX 6000 (96GB)** — TRITON CUDA error persists despite updates (Issue #3921).  
   → Fixed in PR #13141 (pending merge).  
2. **AMD Strix Halo (Windows)**: Model weights loaded into system RAM instead of VRAM; GPU compute active but no VRAM usage (Issue #7449).  
   → Partially addressed via ROCm 6.4+ enforcement (PR #10277).  
3. **Qwen3.8-27B V3 GGUF crashes after prefill on R9700 (Vulkan)** — rolling back to V2 fixes it (Issue #9792).  
   → Likely due to GGUF format regression or kernel mismatch.  
4. **OOM during GRPO training on H100 (80GB)** even with small batch sizes (Issue #3603).  
   → Suggests KV cache inefficiency or hidden memory leaks.  

> 🔗 [Issue #3921](https://github.com/unslothai/unsloth/issues/3921) | [Issue #7449](https://github.com/unslothai/unsloth/issues/7449) | [Issue #9792](https://github.com/unslothai/unsloth/issues/9792) | [Issue #3603](https://github.com/unslothai/unsloth/issues/3603)

---

### **6. What This Means for Application Developers**  
- **For agent developers using GRPO/PPO**: Expect tighter TRL 1.15 compatibility soon (PR #13205). Use `async_grpo.AsyncGRPOTrainer` only after confirming patching is applied.  
- **For multi-GPU/multi-device deployments**: Monitor AMD ROCm and Intel XPU support — recent PRs improve stability on non-NVIDIA hardware.  
- **For production inference**: Avoid `Qwen3-VL` GGUF V3 on AMD GPUs until HF/Unsloth validates the build. Prefer V2 or BF16 pipelines.  
- **For memory-constrained environments**: The OOM issues (e.g., #3603, #4504) indicate that VRAM estimates may be inaccurate — always validate with `nvidia-smi` and monitor KV cache usage.  
- **For Studio users**: Enable `unsloth.ini` for granular llama.cpp tuning, and disable tool deduplication if needed for agent consistency.

> 💡 **Actionable Tip**: If running on AMD, ensure ROCm 6.4+ is used via `UNSLOTH_TORCH_INDEX_FAMILY=rocm64`. On Windows, check for RDNA4 idle-eviction warnings (PR #13208).

---  
*Digest generated from GitHub data: [unslothai/unsloth](https://github.com/unslothai/unsloth)*

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/Qyii22/agents-radar).*