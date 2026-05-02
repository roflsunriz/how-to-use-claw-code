# Survey of GGUF-Based Local LLMs Suitable for Agentic Coding (Hugging Face)

## Executive Summary

As of April 2026, for GGUF-based local deployment targeting GPUs with 32GB or less VRAM, the most practical top candidates are Devstral-Small-2507, Qwen3-Coder-30B-A3B-Instruct (GGUF), and the Qwen2.5-Coder family (14B/7B). Devstral is explicitly designed for agentic coding and posts a SWE-bench Verified score of 53.6, making it a reliable choice among 24B-class models.

Qwen3-Coder-Next is very capable but its official GGUF Q4_K_M quantization alone is ~48.4GB, which is outside a 32GB constraint. Qwen3-Coder-30B-A3B-Instruct GGUF (Q4_K_M ~19.2GB) is a realistic modern candidate that balances long context and tooling friendliness.

For stability per parameter under 32GB, Qwen2.5-Coder remains strong. Official GGUF versions are effectively targeted at 32k context; if you need 128k you should prefer non-GGUF distributions.

On 16GB-class systems, Qwen2.5-Coder-14B/7B and DeepSeek-Coder-V2-Lite are practical choices; Devstral remains the top option when 24–32GB is available.

## Selection Approach and Sources

This survey prioritized official model cards from Mistral, Qwen, DeepSeek and other vendors, plus documentation for inference stacks (llama.cpp, vLLM, LM Studio) and community GGUF releases. Because agentic coding emphasizes tool usage and long-context behavior, we weighted SWE-bench, agent-specific benchmarks, and long-context capability more heavily than simple HumanEval scores.

Key conclusion: if you can run 24–32GB, Devstral-Small-2507 is the safest anchor. If you prioritize the latest long-context coder architectures, Qwen3-Coder-30B-A3B-Instruct GGUF is compelling for practical local setups.

## Comparison Table (Selected Models)

Speed indicators are relative approximations based on parameters, architecture, and available vendor/official hints. VRAM/context estimates assume single-GPU full-offload with flash attention and FP16 KV cache where applicable.

| Model | HF GGUF repo | Base Architecture | Estimated Params | GGUF quantization / recommended | License | Inference framework compatibility | Speed hint | VRAM & max context (estimates) | Notes |
|---|---|---|---:|---|---|---|---|---|---|
| Devstral-Small-2507 | mistralai/Devstral-Small-2507_gguf | Mistral Small derivative | 24B | Official Q4_K_M / Q5_K_M / Q8_0. Recommend Q5_K_M for 24–32GB | Apache-2.0 | GGUF: llama.cpp / LM Studio; non-GGUF: transformers / vLLM | ○–◎ | 32GB: Q4≈106k, Q5≈90k; 24GB: Q4≈54k | Agentic-coding focus; SWE Verified 53.6 |
| DeepSWE-Preview | agentica-org/DeepSWE-Preview-GGUF variants | Qwen3-32B base | ~33B | Community GGUF; recommend Q4_K_M for 32GB | MIT | GGUF: llama.cpp / LM Studio | △ | 32GB: Q4≈40k | Very high SWE scores but community GGUFs; sensitive to runtime config |
| Qwen3-Coder-30B-A3B-Instruct | unsloth/Qwen3-Coder-30B-A3B-Instruct-GGUF | Qwen3 MoE | 30.5B (3.3B active) | Q4_K_M recommended for 24–32GB; Q5_K_M for quality on 32GB | Apache-2.0 | Ollama / LM Studio / llama.cpp / transformers | ◎ | 32GB: Q4≈123k; 24GB: Q4≈36k | Practical 30B MoE option for 32GB; strong tool and long-context orientation |
| Qwen2.5-Coder-32B | Qwen/Qwen2.5-Coder-32B-Instruct-GGUF | Qwen2.5 Coder | 32.5B | Official q2_K–q8_0; GGUF practically assumes 32k context | Apache-2.0 | GGUF: llama.cpp | △ | 32GB: Q4=32k | Very strong official benchmarks but GGUF limits context to ~32k |
| DeepSeek-Coder-V2-Lite | community GGUFs | MLA (DeepSeek) | 16B total / 2.4B active | Community Q4_K_M options for 12–16GB | DeepSeek license | transformers/vLLM; GGUF via llama.cpp | ◎ (efficiency) | 16GB viable for long-context | MLA-based; KV behavior differs from standard GQA models |
| Qwen2.5-Coder-14B | Qwen/Qwen2.5-Coder-14B-Instruct-GGUF | Qwen2.5 Coder | 14.7B | Q4_K_M recommended for 16GB | Apache-2.0 | GGUF: llama.cpp | ○ | 16GB: Q4≈30k | Strong 16GB candidate |
| Qwen2.5-Coder-7B | Qwen/Qwen2.5-Coder-7B-Instruct-GGUF | Qwen2.5 Coder | 7.6B | Q4_K_M recommended for 8GB | Apache-2.0 | GGUF: llama.cpp / LM Studio | ◎ | 8–10GB workable | Best 8–10GB local agent candidate |

## VRAM-Tier Recommendations and Presets

These are conservative, practical presets prioritizing operational stability rather than pushing theoretical maxima.

| VRAM | First Choice | Second Choice | Recommended quantization | Recommended context |
|---|---|---|---|---|
| 32GB | Devstral-Small-2507 | Qwen3-Coder-30B-A3B | Devstral: Q5_K_M; Qwen3: Q5_K_M or Q4_K_M | Devstral 64k–96k; Qwen3 64k–96k |
| 24GB | Devstral-Small-2507 | Qwen3-Coder-30B-A3B | Devstral Q4_K_M; Qwen3 Q4_K_M | Devstral 32k–53k; Qwen3 32k–36k |
| 16GB | Qwen2.5-Coder-14B | DeepSeek-Coder-V2-Lite | Qwen2.5 Q4_K_M | Qwen ~24k–30k |
| 12GB | Qwen2.5-Coder-7B | DeepSeek-Coder-V2-Lite | Qwen Q8_0 or Q6_K | Qwen: 32k; DeepSeek: potentially longer but needs validation |
| 10GB | Qwen2.5-Coder-7B | — | Q6_K | 16k–32k |
| 8GB | Qwen2.5-Coder-7B | — | Q4_K_M | 16k–32k |

Recommended workflow: start with Qwen 7B for small VRAM or Qwen 14B for 16GB. For 24–32GB produce a Devstral anchor and evaluate Qwen3-Coder-30B as a higher long-context option.

## Memory and Calculation Basis

Estimates use single GPU, full offload, flash attention, KV cache in FP16, and ~1.5GB runtime reservation. Approximate formula: max_context ≈ floor((VRAM - reserved - model_weight) / KV_per_token). KV per token is approximated from layers × kv_heads × head_dim × 2(K,V) × 2 bytes (FP16).

Exceptions: DeepSeek MLA models use different KV-like structures, which makes direct KV/token formula comparisons less accurate; measure in your runtime.

## Compatibility and Operational Notes

For stable GGUF local serving, llama.cpp is the most mature path. Transformers supports GGUF but dequantizes to FP32 for many workflows, making it less suitable for low-VRAM serving. vLLM's GGUF support is experimental. LM Studio supports GGUF consumption and can provide a user-friendly local API.

GPU compatibility: NVIDIA remains the broadest choice. ROCm support exists for some stacks but Linux is the primary target. Windows local GGUF workflows are primarily via llama.cpp / LM Studio / Vulkan.

Practical advice: fix your agent prompt scaffold and tool contract first, then iterate with 2–3 models to compare performance and stability. Model behavior depends as much on prompt/tool scaffolding as on raw model selection.

## Open Questions and Sources

Uncertainties include runtime tok/s variances, MLA differences for DeepSeek models, and community GGUF quality variation. Primary references include model cards and official docs where available; community GGUFs require careful validation.

(End of file)
