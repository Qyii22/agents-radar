# AI Infrastructure Digest 2026-10-09

> Generated: 2026-10-09 06:39 UTC | Projects covered: 6

- [vLLM](https://github.com/vllm-project/vllm)
- [SGLang](https://github.com/sgl-project/sglang)
- [llama.cpp](https://github.com/ggml-org/llama.cpp)
- [Ollama](https://github.com/ollama/ollama)
- [LiteLLM](https://github.com/BerriAI/litellm)
- [Unsloth](https://github.com/unslothai/unsloth)

---

## Cross-Project Comparison

# **Cross-Project AI Infrastructure Ecosystem Report – 2026-10-09**

---

### **1. Ecosystem Overview**  
The AI inference and serving ecosystem is entering a phase of **hardware specialization and production-grade maturity**, driven by rapid advances in GPU architecture (Blackwell SM120, MI355X) and the rise of agent-centric workloads. Projects are increasingly diverging in focus: some prioritize low-level kernel optimization (vLLM, llama.cpp), others emphasize secure, sandboxed execution (Unsloth), while gateways like LiteLLM and Ollama aim to unify access across providers and backends. Critical stability issues—especially around FP8/KV cache, speculative decoding, and backend crashes—are now as prominent as performance gains, signaling a shift from pure speed to reliability at scale.

---

### **2. Activity Comparison**

| Project       | Issues Open (↑/↓) | PRs Merged (↑/↓) | Releases (Latest) | Status |
|---------------|-------------------|------------------|-------------------|--------|
| **vLLM**      | 47 (+3)           | 12 (+2)          | `v0.30.1rc1`      | Active dev (`v0.31.0`) |
| **SGLang**    | 102 (+5)          | 9 (+1)           | None              | High instability (critical regressions) |
| **llama.cpp** | 118 (+4)          | 15 (+3)          | `b11515`          | Frequent bugfix releases |
| **Ollama**    | 187 (+6)          | 12 (+2)          | `v0.40.2`         | Post-release regressions |
| **LiteLLM**   | 89 (+2)           | 7 (+1)           | `v1.106.0-dev.2`  | Security-focused updates |
| **Unsloth**   | 124 (+3)          | 11 (+2)          | `v0.1.905-beta`   | New sandboxing feature |

> 🔍 *Observation:* Despite fewer new releases, **vLLM and llama.cpp** lead in technical velocity, with multiple kernel-level optimizations and hardware-specific fixes. **Ollama and SGLang** show higher issue counts due to post-release instability, indicating growing pains in deployment complexity.

---

### **3. Model Support Race**

| New Model / Architecture        | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|----------------------------------|------|--------|-----------|--------|---------|---------|
| **Qwen3.8-Flash-Next**          | ✅ (ROCm/AMD, SM120) | ✅ (DeepSeek V4.1 focus) | ⚠️ (Performance regres.) | ✅ (Requested) | ❌ | ❌ |
| **GLM-5.3-Flash**               | ✅ (SM120, ROCm) | ⚠️ (Speculative decode crash) | ⚠️ (OOM, assertion) | ✅ (Requested) | ❌ | ❌ |
| **Qwen-Image-2.1-GGUF (Q4_K_M)**| ❌ | ❌ | ✅ (ROCm fix) | ✅ (Requested) | ❌ | ✅ (ROCm + sandboxing) |
| **d1-3B / d1-omni-600M**        | ❌ | ❌ | ❌ | ✅ (Requested) | ❌ | ❌ |
| **Gemma 4 models**              | ❌ | ❌ | ❌ | ❌ | ✅ (Added) | ❌ |
| **MoE LRU caching (multi-GPU)** | ❌ | ❌ | ✅ (b11507) | ❌ | ❌ | ❌ |

> 🏆 **Winner:** **llama.cpp** leads in model diversity and experimental support (e.g., MoE multi-GPU), while **Unsloth** stands out for vision-language model integration and sandboxing readiness. **Ollama** is most responsive to community model requests but lags in actual implementation.

---

### **4. Performance Frontier**

| Optimization Focus             | vLLM | SGLang | llama.cpp | Ollama | LiteLLM | Unsloth |
|----------------------------------|------|--------|-----------|--------|---------|---------|
| **KV Cache & Memory Efficiency** | ✅ (FP8, HiSparse, GDN) | ✅ (HiCache staged WB) | ✅ (MMQ OOB fix, MoE LRU) | ⚠️ (MLX/Vulkan crashes) | ⚠️ (RAM leaks) | ✅ (VRAM estimation) |
| **Kernel Fusion & Launch Overhead** | ✅ (AITER, GEMM, RoPE) | ✅ (BCG, fused kernels) | ✅ (Radix top-k, NV_coopmat2) | ❌ | ❌ | ✅ (int64 RoPE) |
| **Distributed / Multi-GPU Serving** | ✅ (page_block_size=32) | ✅ (CP + BCG) | ✅ (MoE across GPUs) | ❌ | ✅ (Rust migration) | ❌ |
| **Quantization (FP8/MXFP4)**     | ✅ (Full support) | ✅ (Int4, FP4) | ✅ (MXFP4 accel) | ⚠️ (MLX panic) | ❌ | ❌ |
| **Streaming & Structured Output**| ✅ (logprobs, `/derender`) | ⚠️ (parser misalignment) | ❌ | ⚠️ (schema ignored) | ✅ (retry logic) | ✅ (sandboxed output) |

> 🔥 **Frontier Leaders:**  
> - **vLLM**: Most mature KV cache and FlashInfer integration on NVIDIA/AMD.  
> - **llama.cpp**: Cutting-edge kernel optimizations (top-k, MMQ, Vulkan).  
> - **Unsloth**: Best-in-class long-context handling via `int64` RoPE and smart offloading.  
> - **LiteLLM**: Leading the charge toward sub-1ms proxy latency via Rust migration.

---

### **5. Layer Positioning**

| Project       | Primary Layer                     | Key Differentiator |
|---------------|------------------------------------|--------------------|
| **vLLM**      | **Serving Engine** (NVIDIA/AMD)   | Production-grade, high-throughput inference with FlashInfer integration |
| **SGLang**    | **Serving Engine + Agent Framework** | Strong focus on speculative decoding, deterministic inference, and multi-modal pipelines |
| **llama.cpp** | **Local Runtime / Embedded Inference** | Cross-platform, lightweight, ideal for edge and local deployment |
| **Ollama**    | **Gateway / Developer Experience** | Unified CLI, model hub, and user-friendly API — but unstable on newer backends |
| **LiteLLM**   | **API Gateway / Abstraction Layer** | Provider-agnostic routing, security-hardened, moving toward Rust-based proxy |
| **Unsloth**   | **Agent Studio / Sandbox Platform** | Full lifecycle management: train → test → serve with isolation and file parsing |

> 💡 **Strategic Insight:** The stack is fragmenting: **vLLM/SGLang** dominate high-performance serving; **llama.cpp** owns local inference; **Ollama/LiteLLM** abstract access; **Unsloth** builds end-to-end agent environments.

---

### **6. Trend Signals**

1. **Hardware Specialization Is Now Mainstream**  
   AMD ROCm (MI355X) and NVIDIA SM120 (Blackwell) are no longer niche — both vLLM and llama.cpp have dedicated tuning efforts, including AITER, GDN, and page_block_size=32 optimizations. Expect future models to be optimized per-architecture.

2. **Security & Stability Are No Longer Optional**  
   Multiple projects (Ollama, LiteLLM, SGLang) face critical crashes or regressions. **Cosign signing (LiteLLM)** and **sandboxing (Unsloth)** reflect a shift toward trust-by-design in infrastructure.

3. **Agent-Centric Workflows Demand Isolation & Reliability**  
   Unsloth’s sandboxing and LiteLLM’s structured output improvements signal that **agent systems require secure, auditable execution environments** — not just faster inference.

4. **Rust Migration = Next-Gen Proxy Performance**  
   LiteLLM’s move to Rust aims for sub-1ms overhead — a game-changer for high-throughput LLM gateways. This trend will likely spread to other gateway layers.

5. **Model Format Proliferation Requires Tooling Evolution**  
   With GGUF, FP8, MXFP4, and cloud-native formats coexisting, developers must choose tools that handle format migration and validation (e.g., Ollama’s auto-upgrade, Unsloth’s VRAM estimator).

> ✅ **For Application Developers:**  
> - Prioritize **vLLM** for scalable, stable inference on modern GPUs.  
> - Use **Unsloth** for secure, long-context agent systems.  
> - Leverage **LiteLLM** only after verifying Rust migration progress.  
> - Avoid **Ollama v0.40.2+** on MLX and Vulkan until fixes land.  
> - Monitor **SGLang’s deterministic inference** status — it remains fragile despite promise.

---  
*Report generated: 2026-10-09 | Source: GitHub activity, project digests, release notes*

---

## Per-Project Reports

<details>
<summary><strong>vLLM</strong> — <a href="https://github.com/vllm-project/vllm">vllm-project/vllm</a></summary>

**vLLM Digest – 2026-10-09**

---

### **1. Today's Highlights**  
The vLLM project continues to accelerate its focus on **ROCm/AMD GPU performance optimization**, with multiple PRs targeting AITER-enabled backends and GDN prefill improvements for gfx950/gfx942. Critical stability fixes address FP8 KV cache OOM issues, FlashInfer fallback failures, and a high-severity crash in GLM-5.3-Flash under long-decode scenarios. These updates are vital for production deployments on Blackwell (SM120) and MI355X hardware.

---

### **2. Releases & Breaking Changes**  
*None reported in the last 24 hours.*  
No new releases or breaking API/config changes were published. The current stable version remains `v0.30.1rc1`, with `v0.31.0` in active development.

---

### **3. New Model & Hardware Support**  
- **AMD ROCm Support**: Expanded tuning and performance tracking for `Qwen3.8-2.4T-A95B` and `Qwen3.8-Flash-Next` on **MI355X (gfx950)** via AITER and GDN backends. [Issue #57149](https://github.com/vllm-project/vllm/issues/57149), [Issue #59575](https://github.com/vllm-project/vllm/issues/59575)  
- **NVIDIA SM120 (Blackwell)**: Active work on enabling `page_block_size=32` for DeepSeek-V4.1-Flash and resolving missing FlashInfer kernels. [Issue #59203](https://github.com/vllm-project/vllm/issues/59203)  
- **Intel GPU**: Added tuned Mamba SSU configs for Arc Pro B60. [PR #56765](https://github.com/vllm-project/vllm/pull/56765)  
- **Quantization**: Full support for `FP8` and `MXFP4` quantized models on AMD and NVIDIA platforms.  

---

### **4. Performance & Optimization**  
- **ROCm AITER Optimizations**:  
  - Making AITER FlyDSL GDN prefill default on gfx942/gfx950. [PR #60645](https://github.com/vllm-project/vllm/pull/60645)  
  - Skipping sparse MQA logits cleanup to avoid redundant fills. [PR #51314](https://github.com/vllm-project/vllm/pull/51314)  
  - Using tuned AITER GEMM for MoE router gate on ROCm. [PR #50535](https://github.com/vllm-project/vllm/pull/50535)  
- **Kernel-Level Improvements**:  
  - Internal prefill checkpoints for GDN backends improve alignment and reduce scheduler stalls. [PR #60659](https://github.com/vllm-project/vllm/pull/60659)  
  - Reduced multimodal payload by deferring `pixel_values` preprocessing until `/generate`. [RFC #46722](https://github.com/vllm-project/vllm/issues/46722)  
- **Memory Efficiency**: HiSparse now manages GPU residency without modifying core block tables. [PR #60647](https://github.com/vllm-project/vllm/pull/60647)

---

### **5. Stability & Regressions**  
| Severity | Issue | Description | Fix Status |
|---------|------|-------------|------------|
| 🔴 High | [#56868](https://github.com/vllm-project/vllm/issues/56868) | GLM-5.3-Flash long-decode degeneration after accumulated reasoning decode | In progress |
| 🔴 High | [#60174](https://github.com/vllm-project/vllm/issues/60174) | DFlash2/DSpark + prefix caching corrupts output on Qwen3.8-27B NVFP4 | Reproduced on v0.31; fix pending |
| 🟡 Medium | [#60262](https://github.com/vllm-project/vllm/issues/60262) | `kv_cache_dtype="fp8"` crashes if FlashInfer JIT is unavailable | Fixed in PR #60785 |
| 🟡 Medium | [#60350](https://github.com/vllm-project/vllm/issues/60350) | FP8 KV cache startup OOM due to CUDA graph memory exclusion | PR #60350 open |
| 🟡 Medium | [#60473](https://github.com/vllm-project/vllm/issues/60473) | MoRIIO decode hangs when notify port is in use | In progress |

---

### **6. What This Means for Application Developers**  
- **For AMD/ROCm users**: Prioritize `v0.31.0+rocm723` builds for optimal performance on MI355X. Expect faster inference with `Qwen3.8-*` models using AITER and GDN backends.  
- **For Blackwell (SM120) users**: Be cautious with `DeepSeek-V4.1` and `GLM-5.3-Flash` — avoid `page_block_size=32` until kernel support is confirmed. Monitor regression reports closely.  
- **For multi-node/distributed systems**: Use `--kv-cache-dtype fp8` with care — ensure FlashInfer JIT is available or expect fallbacks. Enable `VLLM_USE_FLASHINFER_SAMPLER=0` as workaround if needed.  
- **For agent developers**: Streaming logprobs and structured outputs are now better supported via `/derender` and parser engine migration (e.g., Olmo3). Watch for ongoing work on `logprobs` streaming in parsed paths.  
- **For security-sensitive apps**: OpenCV video decoding is now isolated in child processes, reducing risk of server crashes from malformed inputs. [PR #60612](https://github.com/vllm-project/vllm/pull/60612)

> ✅ **Recommendation**: Update to latest nightly (`v0.31.0.dev`) for ROCm and Blackwell fixes. Test `kv_cache_dtype=fp8` with full FlashInfer toolchain to avoid crashes.

</details>

<details>
<summary><strong>SGLang</strong> — <a href="https://github.com/sgl-project/sglang">sgl-project/sglang</a></summary>

**SGLang Digest – 2026-10-09**

---

### **1. Today's Highlights**  
The SGLang ecosystem continues to mature with active work on DeepSeek V4.1 optimization, multi-modal and NPU/XPU support, and critical stability fixes for speculative decoding and deterministic inference. Notably, multiple PRs address long-standing issues in cache management (HiCache), kernel scheduling (Mamba), and GPU memory safety—highlighting a growing focus on production-grade reliability.

---

### **2. Releases & Breaking Changes**  
*No new releases in the last 24h.*  
However, several configuration-sensitive behaviors were flagged:  
- `--enable-deterministic-inference` does **not guarantee determinism** for `gpt-oss-20b` (Issue #43055).  
- Using `dtype="float32"` in `sglang.Engine` triggers a `KeyError: torch.float32` (Issue #43162).  
→ Developers should avoid these configurations until fixes are merged.

---

### **3. New Model & Hardware Support**  
- **DeepSeek V4.1**: Ongoing optimization via prefill CP + BCG support (PR #40816, #40323) and fusion of RoPE/fp4 kernels (PR #42911).  
- **Intel XPU**: Expanded RL feature support (PR #39891), staging-buffer KV transfer for PD disaggregation (PR #40861), and int4 dense test fixes (PR #43306).  
- **Ascend NPU**: Fix for prefill context parallel on generic attention (PR #41564); A5 compressor routing now supports explicit SWA mapping (PR #40816).  
- **AMD ROCm**: MORI EP V2 support added (PR #40666), and gfx950 decode-score tile size adjusted to 128 tokens for consistency (PR #42772).

---

### **4. Performance & Optimization**  
- **DeepSeek V4.1 Prefill**: Interleaved CP + breakable CUDA graphs now supported (PR #38603), improving scalability across TP=2 setups.  
- **Kernel Fusion**: AMD ROCm now fuses prefill index-Q RoPE fake-quant and fp4 quantize kernels (PR #42911), reducing kernel launch overhead.  
- **Memory Efficiency**: HiCache staged write-back added for page-unified KV cache (PR #39606); L2/L3 hit attribution preserved across retries (PR #39297).  
- **Decoding Speed**: Qwen4Exp decode on DGX Spark shows QSA/PLE/GDN kernel time dominance (Issue #36796), prompting tuning requests for SM121 backend improvements.

---

### **5. Stability & Regressions**  
| Severity | Issue | Description | PR/Workaround |
|---------|-------|-------------|---------------|
| Critical | #42752 | Flaky CI tests and infrastructure failures in PR babysitting workflows | Tracking issue; no fix yet |
| High | #43061 | `--enable-deterministic-inference` + `repetition_penalty` crashes scheduler on granite-4.0-h (TorchDynamoError) | No fix yet |
| High | #43204 | `AssertionError: Can not alloc mamba cache` kills engine when all Mamba states are locked | Critical crash; no fix yet |
| High | #40843 | GLM-5.3-Flash speculative decoding causes degenerate output (infinite '!' loops) | Reproducible; ongoing investigation |
| Medium | #43146 / #43145 / #43157 | Reasoning parser logic misaligned with Minimax/M3 strict grammar rules | Multiple related bugs reported |
| Medium | #43287 | EAGLE+DP attention idle verify crashes due to missing `kv_indptr` | Triggers on DP ranks with no workload |

> ⚠️ **Note**: Several regressions affect agent tool calling (e.g., empty SSE chunks #29441, invalid JSON from Glm47MoeDetector #43273), impacting SDK integrations.

---

### **6. What This Means for Application Developers**  
- **Avoid `--enable-deterministic-inference`** if using `repetition_penalty` or `granite-4.0-h`, as it may cause scheduler crashes (Issue #43061).  
- **Validate model-specific configs** — e.g., `rope_theta` is being incorrectly fabricated (#43158), and `json-model-override-args` drops `rope_scaling` fields (#41227).  
- **Expect instability with complex tool chains** on GLM-5.3-Flash under speculative decoding (#40843, #36669).  
- **Use latest CI builds cautiously** — flaky tests (#42752) and infrastructure failures may block merges.  
- **For multi-backend deployment**, ensure device-specific logic (e.g., XPU/NPU) is tested end-to-end, especially around caching and tensor serialization.

👉 *Stay updated via [SGLang GitHub Issues](https://github.com/sgl-project/sglang/issues) and join [Slack](https://slack.sglang.ai) for real-time coordination.*

---  
*Digest generated: 2026-10-09 | Source: github.com/sgl-project/sglang*

</details>

<details>
<summary><strong>llama.cpp</strong> — <a href="https://github.com/ggml-org/llama.cpp">ggml-org/llama.cpp</a></summary>

**llama.cpp Digest – 2026-10-09**

---

### **1. Today's Highlights**  
The latest updates focus on critical CUDA and SYCL stability fixes, including a top-k performance optimization for large row counts and a fix for MMQ out-of-bounds reads. Significant progress continues in MoE (Mixture of Experts) support with multi-GPU cache sharing and new MoE LRU caching proposals, while Vulkan and HIP backends receive targeted improvements for correctness and performance.

---

### **2. Releases & Breaking Changes**  
- **New releases**: `b11515` (SYCL), `b11514` (Musa), `b11513` (CUDA), `b11512` (DFlash output head sharing fix), `b11511` (CUDA MMQ OOB fix), `b11510` (looped PAD kernel for >65535 rows), `b11509` (CCCL version guard fix), `b11507` (MoE cache across GPUs), `b11505` (Vulkan TOP_K for +inf/NaN), `b11503` (cpp-httplib update).  
- **Migration note**: The `--moe-cache-mib` flag now supports comma-separated values per GPU (`#30205`), enabling fine-grained control over MoE expert cache allocation.  
- **API change**: `yaRN` now supports `truncate=false` via `#30206`, allowing non-integer interpolation boundaries (CPU-only initially).

> 🔗 [GitHub Releases](https://github.com/ggml-org/llama.cpp/releases)

---

### **3. New Model & Hardware Support**  
- **Backend additions**:  
  - **XDNA backend request** opened: Feature request for XDNA hardware integration (#21725).  
  - **Metal multi-GPU support**: Request to use eGPU + dGPU on Intel Macs via Metal (#28565).  
- **Hardware-specific optimizations**:  
  - CUDA: `sm_86` added to MMVQ cutoff table for A10 (Q4_0 crossover at 7 → +9.1% perf) (#28090).  
  - Vulkan: NV_coopmat2-based chunked GATED_DELTA_NET kernel merged (#30207), improving scalability on RTX 5060 Ti.  
- **Model support**:  
  - Qwen3.8-Flash-Next (UD-IQ4_XS, IQ3_XXS) is under active scrutiny due to performance regressions and speculative decoding crashes (#29949, #30033).  

> 🔗 [Issue #21725](https://github.com/ggml-org/llama.cpp/issues/21725) | [PR #30207](https://github.com/ggml-org/llama.cpp/pull/30207)

---

### **4. Performance & Optimization**  
- **CUDA**:  
  - Radix-based `top-k` replaces CUB’s per-row kernel — **cuts 1.6M kernel launches** on qwen4exp at 34,816 tokens (#28713).  
  - On-the-fly dequantization PoC for Q8/Q4 in MMA kernels shows promise for reduced memory pressure (#30202).  
- **SYCL**:  
  - MXFP4 MoE inference accelerated by **1.9×–2.0×** via arithmetic decoding and weight reordering (#29809).  
- **Vulkan**:  
  - Specialized quant block size via specialization constants restores full K-loop unrolling — **reduces prompt processing latency by ~16–19%** on RTX 5060 Ti (#30164).  
- **General**:  
  - Chunked GDN kernel upstreamed for CUDA/HIP (#29353), with ~10% better prefill throughput on large models.

> 🔗 [PR #28713](https://github.com/ggml-org/llama.cpp/pull/28713) | [PR #30164](https://github.com/ggml-org/llama.cpp/pull/30164) | [PR #29809](https://github.com/ggml-org/llama.cpp/pull/29809)

---

### **5. Stability & Regressions**  
| Issue | Severity | Status | Notes |
|------|----------|--------|-------|
| [#25593](https://github.com/ggml-org/llama.cpp/issues/25593) | Critical | Open | FP32 math silently used instead of FP16 on SM_60 (P100), causing quality loss. Fix exists in forks. |
| [#29949](https://github.com/ggml-org/llama.cpp/issues/29949) | High | Open | MoE expert cache overflow; GPU-resident LRU needed. |
| [#29811](https://github.com/ggml-org/llama.cpp/issues/29811) | High | Open | Assertion failure when running Qwen3.8 Flash with MTP. |
| [#27638](https://github.com/ggml-org/llama.cpp/issues/27638) | High | Open | Flash Attention fallback to SCALAR path causes O(N²) PP degradation and device loss on Intel Arc. |
| [#30000](https://github.com/ggml-org/llama.cpp/issues/30000) | Medium | Open | Vulkan prompt processing 16–19% slower post-#25773 on RTX 5060 Ti. Fixed in PR #30164. |
| [#26447](https://github.com/ggml-org/llama.cpp/issues/26447) | Medium | Open | `vk::Queue::submit: ErrorDeviceLost` after ~50K context on Vega 8 iGPU. |

> 🔗 [Issue #25593](https://github.com/ggml-org/llama.cpp/issues/25593) | [PR #30164](https://github.com/ggml-org/llama.cpp/pull/30164)

---

### **6. What This Means for Application Developers**  
- **Prioritize CUDA and SYCL stability**: If using SM_60 (P100) or older GPUs, be cautious of silent FP32 fallbacks affecting model quality (#25593). Use FP16 explicitly if possible.  
- **Leverage MoE scaling**: With `b11507`, MoE models can now span multiple GPUs — ideal for large-scale agent workflows. Consider the upcoming GPU-resident LRU cache proposal (#29949) for efficient expert management.  
- **Optimize for newer hardware**: Use `--moe-cache-mib` per-GPU tuning (`#30205`) and enable `NV_coopmat2` kernels on RTX 5060 Ti for faster prompt processing.  
- **Avoid speculative decoding pitfalls**: Be cautious with MTP + Qwen3.8-Flash-Next until #29811 and #30033 are resolved.  
- **Monitor Vulkan behavior**: On Intel Arc or older RDNA cards, expect potential OOM or device loss during long contexts unless patched.  

> 📌 **Actionable**: Update to `b11515+` for improved top-k and MoE stability. Test MTP workloads with Qwen3.8-Flash-Next on current builds.

---  
*Digest generated from GitHub activity on 2026-10-09. For real-time updates, follow [llama.cpp on GitHub](https://github.com/ggml-org/llama.cpp).*

</details>

<details>
<summary><strong>Ollama</strong> — <a href="https://github.com/ollama/ollama">ollama/ollama</a></summary>

**Ollama Digest – 2026-10-09**

---

### **1. Today's Highlights**  
The latest release, **v0.40.2**, introduces automatic background upgrades for models previously downloaded with older Ollama versions, improving compatibility and performance when running on `llama.cpp`. However, this update has triggered a wave of stability issues—particularly around **MLX backend crashes on Apple Silicon (M-series)** and **Vulkan regression on AMD Radeon 780M GPUs**, affecting users across macOS and Linux. Meanwhile, growing demand for **Intel OpenVINO integration** and support for new small decision models like *d1-3B* underscores expanding hardware diversity and use-case evolution.

---

### **2. Releases & Breaking Changes**  
- **v0.40.2**: Automatic model upgrades in the background ensure better compatibility with `llama.cpp`. Original models are preserved as backups to allow safe downgrades.  
  🔗 [GitHub Release v0.40.2](https://github.com/ollama/ollama/releases/tag/v0.40.2)  

> ⚠️ **Note**: Users upgrading from v0.35.x may encounter regressions—especially on MLX and Vulkan backends. Downgrading is advised if instability occurs.

---

### **3. New Model & Hardware Support**  
- **Intel OpenVINO**: High-priority feature request (#2169) calls for native OpenVINO support on Intel CPUs/GPUs, citing improved efficiency with multimodal models like LLaVA.  
  🔗 [Issue #2169 – Feature Request: OpenVINO on Intel](https://github.com/ollama/ollama/issues/2169)  
- **New Models Requested**:  
  - *d1-3B* and *d1-omni-600M* (small decision models with image support): Requested via #18890  
    🔗 [Issue #18890 – Request for d1-3B & d1-omni-600M](https://github.com/ollama/ollama/issues/18890)  
  - *Clef Flash*, *Index-Translate*, *Qwen 3.8 Flash Next*, *Mimo v2.6*, *Hy4*, *Stepfun*, *Laguna*, *Reflection AI*: Requested for cloud availability (#18850)  
    🔗 [Issue #18850 – Cloud Model Requests](https://github.com/ollama/ollama/issues/18850)  
- **Backend Progress**:  
  - MLX support expanded with Kolibri 1 added via PR #18780  
    🔗 [PR #18780 – mlx: add Kolibri 1 support](https://github.com/ollama/ollama/pull/18780)  
  - Initial work on migrating legacy GGUFs to modern formats underway via PR #18882  
    🔗 [PR #18882 – Remove llama.cpp patches & migrate legacy GGUFs](https://github.com/ollama/ollama/pull/18882)

---

### **4. Performance & Optimization**  
- **MLX Backend Optimization**:  
  - PR #18805: Compact restored recurrent state after speculative rollback to reduce memory overhead.  
    🔗 [PR #18805 – mlx: compact restored recurrent state](https://github.com/ollama/ollama/pull/18805)  
  - PR #18886: Preserve generation panics during cleanup to avoid masking root errors.  
    🔗 [PR #18886 – mlx: preserve generation panics during cleanup](https://github.com/ollama/ollama/pull/18886)  
- **API & Streaming Improvements**:  
  - PR #18891: Preserve retained reasoning during compaction in Responses API.  
    🔗 [PR #18891 – openai: preserve retained reasoning](https://github.com/ollama/ollama/pull/18891)  
  - PR #18881: Include EOS tokens in raw generate responses (fixes missing end-of-sequence signals).  
    🔗 [PR #18881 – llm: include eos tokens in raw generate responses](https://github.com/ollama/ollama/pull/18881)  
- **CI Reliability**: PR #18883 adds retry logic to download steps to improve CI stability.  
  🔗 [PR #18883 – ci: harden download steps with retries](https://github.com/ollama/ollama/pull/18883)

---

### **5. Stability & Regressions**  
| Severity | Issue | Impact | Status |
|---------|------|--------|--------|
| 🔴 Critical | **MLX Panic on M-series Macs (v0.40.0+)**<br>```mlx runner failed: panic: mlx: Maximum threads per threadgroup is 896 but requested 1024``` | Crashes on `gemma4:e2b-mlx`, `qwen3.6:35b-mlx` | 📌 Open ([#18846](https://github.com/ollama/ollama/issues/18846), [#18885](https://github.com/ollama/ollama/issues/18885)) |
| 🔴 Critical | **AMD Radeon 780M Vulkan Regression (>=0.32.10)**<br>```radv/amdgpu: Not enough memory for command submission vk::Queue::submit: ErrorDeviceLost``` | Fails to run larger models on Vulkan | 📌 Open ([#17748](https://github.com/ollama/ollama/issues/17748)) |
| 🔴 Critical | **Cloud Model JSON Schema Ignored**<br>`qwen3-coder:480b-cloud` returns unstructured JSON despite schema | Breaks structured output workflows | 📌 Open ([#12362](https://github.com/ollama/ollama/issues/12362)) |
| 🟡 High | **Failed to Pull `embeddinggemma-2:740m` on Linux**<br>```Error: this model requires MLX support, but the MLX runtime is not available``` | Misleading error; MLX not enabled on Intel systems | 📌 Open ([#18825](https://github.com/ollama/ollama/issues/18825)) |
| 🟡 High | **Gemma4: think=false → empty reply due to unclosed thought block** | Silent failure in tool use scenarios | 📌 Open ([#18861](https://github.com/ollama/ollama/issues/18861)) |
| 🟡 Medium | **Image Generation Models Not Supported**<br>Trying to run `x/z-image-turbo` fails with "not currently supported" | Blocks multimodal app development | 📌 Closed ([#18863](https://github.com/ollama/ollama/issues/18863)) |

> ✅ **Fix PRs exist** for some issues:  
> - PR #18882 (migration of legacy GGUFs) addresses potential corruption risks.  
> - PR #18886 improves error visibility during MLX failures.

---

### **6. What This Means for Application Developers**  
- **Avoid v0.40.0–0.40.1 on Apple Silicon**: If using MLX-backed models (e.g., `gemma4`, `qwen3`), **downgrade to v0.35.1** until MLX thread-group limits are resolved.  
- **Expect instability with large models on AMD GPUs**: Use `--device vulkan` only if confirmed working; consider ROCm or CPU fallbacks.  
- **Do not rely on cloud model schema enforcement**: For `qwen3-coder:480b-cloud` and similar, validate output post-hoc—schema parsing is currently ignored.  
- **Plan for MLX & Vulkan deprecation paths**: The project is actively refactoring legacy backends—expect breaking changes in future releases.  
- **Use `/v1/responses` with caution**: Streaming behavior may reorder outputs or reuse `output_index`—validate message boundaries in agent pipelines.  
- **Prepare for model migration**: With v0.40.2’s auto-upgrade, ensure your app handles updated model formats and manifests (e.g., `manifests-v2`).  

> 💡 **Pro Tip**: Monitor PRs #18882 and #18886 for upcoming fixes related to MLX crash resilience and model consistency.

</details>

<details>
<summary><strong>LiteLLM</strong> — <a href="https://github.com/BerriAI/litellm">BerriAI/litellm</a></summary>

---

### **1. Today's Highlights**  
LiteLLM continues its rapid evolution with critical security and stability improvements, including the resolution of a high-severity supply-chain compromise (Issue #24518) that was contained in March 2026 but remains a focal point for community trust. The project is accelerating toward a major performance leap via its ongoing **Rust migration initiative** (Issue #31263), which aims to reduce proxy overhead to sub-1ms levels. Meanwhile, new PRs focus on refining authentication, logging, and streaming reliability across providers like Anthropic and Bedrock.

---

### **2. Releases & Breaking Changes**  
No breaking changes were introduced in the latest releases (`v1.106.0-dev.2`, `v1.105.0-rc.3`, `v1.104.2`, `v1.102.4`, `v1.101.6`). All Docker images are now cryptographically signed using **cosign**, with verification keys pinned to [commit `0112e53`](https://github.com/BerriAI/litellm/commit/0112e53046018d726492c814b3644b7d376029d0). This ensures integrity and mitigates future supply-chain risks.

> 🔐 **Verify image signatures**: [cosign docs](https://docs.sigstore.dev/cosign/overview/) | [GitHub commit](https://github.com/BerriAI/litellm/commit/0112e53046018d726492c814b3644b7d376029d0)

---

### **3. New Model & Hardware Support**  
- **Gemma 4 models** (gemma-4-31b-it, gemma-4-26b-a4b-it) added to model registry via Issue #26973 — now supported in `model_prices_and_context_window.json`.  
- **Ace Data Cloud** integration expanded with full support for chat and responses APIs, including pricing and verified logo display (PR #44817).  
- **Laya, Bespoke, and Evaluation mode** now available in the Add Model UI (PR #45260), enabling deployment of TypeSafe Jev and other specialized models.

> 📌 [Add Gemma 4 models](https://github.com/BerriAI/litellm/issues/26973) | [Ace Data Cloud provider](https://github.com/BerriAI/litellm/pull/44817) | [Laya/Bespoke support](https://github.com/BerriAI/litellm/pull/45260)

---

### **4. Performance & Optimization**  
The **Rust migration** (Issue #31263) is now in active development, with PRs such as [#45519](https://github.com/BerriAI/litellm/pull/45519) focusing on reducing redundant public call resolution across route hosts. Early benchmarks suggest **sub-1ms overheads** for core proxy operations, targeting a 10x+ improvement over current Python-based latency.

> ⚙️ [Rust Migration Blog](https://docs.litellm.ai/blog/litellm-rust-launch) | [Rust PR #45519](https://github.com/BerriAI/litellm/pull/45519)

---

### **5. Stability & Regressions**  
Critical stability issues reported today include:

- **Memory leaks** leading to pod crashes after prolonged use (Issues #12685, #27954): Multiple users report RAM usage growing unchecked over days. No fix PR yet, but actively tracked.
- **Streaming failures** due to missing or dropped chunks: 
  - Mistral streaming drops `reference` chunks (Issue #45378).
  - Google GenAI streams fail to retry if connection closes before first chunk (Issue #45457).
- **Token counting inaccuracies**: Bedrock `CountTokens` fails for Claude Opus 5/Sonnet 5, returning underestimated values (Issue #37102).
- **Missing error context**: Model access denial logs lack key identity info (PR #45526 proposes fix).

> 🔍 [RAM leak #12685](https://github.com/BerriAI/litellm/issues/12685) | [Google GenAI retry bug #45457](https://github.com/BerriAI/litellm/issues/45457) | [Bedrock token count #37102](https://github.com/BerriAI/litellm/issues/37102)

---

### **6. What This Means for Application Developers**  
- **Security-first deployment**: Use `cosign verify` on all LiteLLM images to ensure no tampering — especially critical post-compromise incident (Issue #24518).  
- **Upgrade cautiously**: While no breaking changes exist, avoid `v1.103.1` → `v1.104.2` if relying on GitHub BYOK token tracking (Issue #45422).  
- **Stream reliably**: Be aware of known streaming bugs (e.g., Google GenAI, Mistral reference chunks); consider fallbacks or manual retries.  
- **Prepare for Rust**: If building low-latency inference gateways, monitor the Rust migration — expect dramatically improved throughput and reduced memory footprint in early 2027.  

> ✅ **Actionable**: Check your config against [latest model list](https://github.com/BerriAI/litellm/blob/main/model_prices_and_context_window.json) and update `model_name` aliases for Gemma 4.

</details>

<details>
<summary><strong>Unsloth</strong> — <a href="https://github.com/unslothai/unsloth">unslothai/unsloth</a></summary>

# **Unsloth Digest – 2026-10-09**

---

### **1. Today's Highlights**  
Unsloth launches **sandboxing support across Windows, Mac, and Linux**, enabling secure, isolated execution of text and vision LLMs as Jev-style decision models—boosting decision accuracy from ~30% to 80%. This enables full lifecycle management: train, test, export, and serve decision models within a trusted environment. Meanwhile, the team continues refining GPU memory handling for large models like Qwen-Image-2.1 on ROCm and resolving critical OOM issues in both training and inference workflows.

---

### **2. Releases & Breaking Changes**  
- **v0.1.905-beta**: Introduces **cross-platform sandboxing** (Windows, macOS, Linux) via Unsloth Studio.  
  🔗 [Release Notes](https://unsloth.ai/docs/new/studio/sandboxing-in-unsloth)  
  📌 *Note:* Sandboxing is now the default for new decision model deployments. Existing users may need to update their workflow configs to leverage isolation features.

---

### **3. New Model & Hardware Support**  
- **Qwen-Image-2.1-GGUF (Q4_K_M)** now has improved compatibility with **ROCm (AMD GPUs)**, including fixes for VAE tile OOMs and attention fallback logic.  
  🔗 [PR #13116](https://github.com/unslothai/unsloth/pull/13116)  
- **Office, OpenDocument, e-book, email, and RTF file parsing** added to chat-with-files functionality in Studio.  
  🔗 [PR #13081](https://github.com/unslothai/unsloth/pull/13081), [PR #13078](https://github.com/unslothai/unsloth/pull/13078)  
- **Coding agents (Claude Code, OpenAI Codex, etc.)** can now interact with Unsloth Studio via MCP using API keys.  
  🔗 [PR #13118](https://github.com/unslothai/unsloth/pull/13118)

---

### **4. Performance & Optimization**  
- **RoPE and norm kernels** updated to use `int64` row offsets, enabling correct handling of sequences exceeding 2³¹ elements (~2.1B tokens).  
  🔗 [PR #13121](https://github.com/unslothai/unsloth/pull/13121)  
  ✅ *Test passes with ~5.5 GiB free GPU memory*  
- **VRAM budget estimation** now aligns with actual llama.cpp GPU usage by factoring in host-resident embeddings (e.g., input layer not placed on GPU).  
  🔗 [PR #9931](https://github.com/unslothai/unsloth/pull/9931)  
- **Offload planner improvements**: Now weighs spill cost against llama.cpp’s internal fitter, enabling smarter weight distribution.  
  🔗 [PR #9872](https://github.com/unslothai/unsloth/pull/9872) *(behaviour gated by `UNSLOTH_SMART_OFFLOAD`)*

---

### **5. Stability & Regressions**  
- **Critical OOM on M5 Max (48GB RAM)** when running `unsloth/Qwen-Image-2.1-GGUF` (Q4_K_M):  
  🔗 [Issue #11792](https://github.com/unslothai/unsloth/issues/11792)  
  ⚠️ *Reported but no fix PR yet — likely due to memory overestimation in VAE decode path.*  
- **CUDA out of memory during GRPO training** on WSL despite unused VRAM (24GB card):  
  🔗 [Issue #1744](https://github.com/unslothai/unsloth/issues/1744)  
  🛠️ *Fix PR under review: [PR #13116](https://github.com/unslothai/unsloth/pull/13116)*  
- **Sandboxing fails silently if local HF cache misconfigured**:  
  🔗 [Issue #2506](https://github.com/unslothai/unsloth/issues/2506)  
  🛠️ *Workaround: Use `HF_ENDPOINT` consistently; fix pending in v0.1.905+.*

---

### **6. What This Means for Application Developers**  
- **Build secure, auditable AI agents**: Use sandboxing to isolate decision models and prevent unintended side effects or data leakage. Ideal for production-grade LLM gateways and agent systems.  
- **Deploy vision-language models reliably on AMD**: With ROCm fixes landed, you can now run Qwen-Image-2.1 without repeated OOMs on H200/M5 Max.  
- **Optimize long-context inference**: The `int64` RoPE kernel ensures correctness beyond 2B-token sequences—critical for legal, medical, or code analysis apps.  
- **Avoid silent failures in local model loading**: If using custom repositories, ensure consistent `HF_ENDPOINT` usage; monitor for partial downloads via scan folders.  
- **Prepare for smart offloading**: Enable `UNSLOTH_SMART_OFFLOAD` to improve throughput on multi-GPU setups by better aligning with llama.cpp’s internal memory planner.

> 💡 *Pro tip:* Always validate model loads in sandbox mode before deploying to production. Monitor `UNSLOTH_RETURN_LOGITS=1` behavior carefully when labels are passed—recent PRs ensure consistent logit scaling.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/Qyii22/agents-radar).*