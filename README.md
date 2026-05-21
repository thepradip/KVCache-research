# The KV Cache Evolution: From Attention Heads to DeepSeek V4's Memory Savings

**Author:** [thepradip](https://github.com/thepradip) &nbsp;|&nbsp; 25 Chapters &nbsp;|&nbsp; 47 Citations &nbsp;|&nbsp; 41 Papers &nbsp;|&nbsp; Interactive Animations

[![Live Article](https://img.shields.io/badge/📖_Read_Live_Article-GitHub_Pages-blue?style=for-the-badge)](https://thepradip.github.io/KVCache-research/)
[![DeepSeek V4](https://img.shields.io/badge/🔥_DeepSeek_V4-90%25_Memory_Reduction-red?style=for-the-badge)](https://thepradip.github.io/KVCache-research/#sec18)
[![DFlash](https://img.shields.io/badge/⚡_DFlash-Speculative_Decoding-purple?style=for-the-badge)](https://thepradip.github.io/KVCache-research/#sec19)

---

## 🎬 Live Article

<p align="center">
  <a href="https://thepradip.github.io/KVCache-research/" target="_blank">
    <strong>▶ Open Interactive Article with Animations</strong>
  </a>
</p>

---

## 🧠 What is KV Cache?

Imagine you are having a conversation with a friend. Before every reply, they read **the full conversation from the beginning**. Every word. Then they answer. That is slow and wasteful.

**KV Cache is like giving your friend a notepad.** Instead of reading everything again, they keep notes as the conversation grows. Each new word can look at those notes instead of rebuilding the whole history.

In AI terms:
- **K (Key)** = the notepad index: *"what topics have we discussed?"*
- **V (Value)** = the actual notes: *"what were the details?"*
- **Q (Query)** = the current question: *"what do I need right now?"*

The problem is size. **The notepad gets huge.** For a 1-million-token conversation, storing K and V for every word in every layer of a 70B model can take **hundreds of gigabytes of GPU memory.** This article follows the methods researchers use to shrink that memory and serve long contexts faster.

---

## 📚 All 25 Chapters

| # | Chapter | The Big Idea | Memory Impact |
|---|---------|-------------|--------------|
| 1 | **Transformers & Attention** | How AI reads text using Q, K, V matrices | Baseline |
| 2 | **Keys, Values & Queries** | The math behind what gets cached | Baseline |
| 3 | **KV Cache Basics** | Cache the notepad instead of re-reading | ✅ Speed ↑ |
| 4 | **The Memory Bottleneck** | Why GPU memory, not compute, becomes the limit | Problem |
| 5 | **Multi-Head Attention (MHA)** | Many notepads in parallel for richer attention | ❌ Memory ↑↑ |
| 6 | **Multi-Query Attention (MQA)** | All heads share one notepad | ✅ Memory ↓ 8× |
| 7 | **Grouped-Query Attention (GQA)** | Groups of heads share notepads | ✅ Balance |
| 8 | **Multi-Head Latent Attention (MLA)** | Compress the notepad into a tiny summary | ✅ Memory ↓ 93% |
| 9 | **Live Comparison: MHA vs MQA vs GQA vs MLA** | Interactive side-by-side animation | Visual |
| 10 | **Cross-Layer Attention (CLA)** | Share notepads across layers too | ✅ Memory ↓ 50% |
| 11 | **Sliding Window & Local-Global Attention** | Mistral 7B, Gemma 2/3 window tricks | ✅ Bounded |
| 12 | **PagedAttention** | Store notepad pages non-contiguously (like OS paging) | ✅ No waste |
| 13 | **KV Cache Eviction** | H2O and Scissorhands keep the tokens that matter most | ✅ Bounded |
| 14 | **KV Cache Quantization** | KIVI (2-bit), KVQuant, TurboQuant, Bonsai 1-bit | ✅ Memory ↓ 4–8× |
| 15 | **FlashAttention** | Tiled SRAM computation without KV materialisation | ✅ Speed ↑↑ |
| 16 | **Model-by-Model Comparison** | LLaMA, Gemma, Qwen, DeepSeek, Kimi, and Phi checked side by side | Reference |
| 17 | **DFlash** ⚡ | Block diffusion speculative decoding for 2–4× speed | ✅ Speed ↑↑ |
| 18 | **DeepSeek V4** 🔥 | CSA/HCA hybrid with 90% KV reduction at 1M tokens | ✅ Memory ↓ 90% |
| 19 | **Kimi K2.5** | How agentic reasoning changes KV cache needs | Long-context |
| 20 | **Linear Attention & SSMs** | Mamba, RWKV, and RetNet as O(1) KV alternatives | ✅ Constant |
| 21 | **Prefix Caching & RadixAttention** | Reuse KV across requests with SGLang RadixAttention | ✅ Reuse |
| 22 | **Mixture of Experts & KV Cache** | How sparse MoE routing shrinks effective KV | ✅ Sparse |
| 23 | **KV Cache Offloading** | FlexGen and InfiniGen move KV to CPU or NVMe when needed | ✅ Overflow |
| 24 | **Disaggregated Prefill & Decode** | Splitwise, DistServe, Mooncake, Ring Attention | ✅ Serving |
| 25 | **2026 Summary & Open Questions** | What works now and what still needs proof | Survey |

---

## 🔥 DeepSeek V4: Million-Token Context in Practice (Chapter 18)

> *"At one million tokens of context, V4-Pro needs only 10% of the KV cache that V3 required."*

### Architecture

| Layer Type | Mechanism | KV Cost |
|-----------|-----------|---------|
| **CSA (Compressed Sparse Attention)** | Per-token low-rank KV + DeepSeek Sparse Attention | ~4× compressed |
| **HCA (Heavily Compressed Attention)** | Groups of tokens collapsed into a single KV entry | ~128× compressed |
| **mHC (Manifold-Constrained Hyper-Connections)** | Stabilises residual path across CSA/HCA alternation | N/A |

### Numbers at 1M-token context

| Metric | V3.2 (baseline) | V4-Pro | V4-Flash |
|--------|----------------|--------|----------|
| KV cache memory | 100% | **10%** | **7%** |
| Inference FLOPs | 100% | **27%** | **10%** |
| Active parameters | 37B | 49B | 13B |
| Context window | 128K | **1M** | **1M** |

---

## ⚡ DFlash: Speculative Decoding Meets Diffusion (Chapter 17)

Most language models write one token at a time. **DFlash writes a whole block at once** using a draft model, then verifies the block in parallel while reusing the existing KV cache. It does this without increasing KV cache size.

**Results:** 2–4× generation speedup with no KV cache size increase.

---

## 📊 KV Cache Size: Real Numbers

| Model | Architecture | KV Cache @ 128K tokens |
|-------|-------------|----------------------|
| LLaMA 3 70B | GQA | ~18 GB |
| DeepSeek V3 | MLA | ~2 GB |
| DeepSeek V4-Pro | CSA/HCA | ~1.8 GB |
| DeepSeek V4-Flash | CSA+ | ~1.2 GB |
| Bonsai 8B (1-bit KV) | GQA + Quant | ~0.4 GB |

---

## 🎥 Animations (MP4)

| Video | Contents |
|-------|--------------|
| `videos/qkv-attention-matmul.mp4` | Q, K, V matrix multiplication step by step |
| `videos/kv-cache-growth.mp4` | KV cache growing with each generated token |
| `videos/mha-mqa-gqa-mla-comparison.mp4` | Side-by-side MHA vs MQA vs GQA vs MLA |
| `videos/model-kv-cache-chart.mp4` | KV cache size across 12 recent models |
| `videos/kv-cache-quantization.mp4` | KIVI / KVQuant / TurboQuant / Bonsai comparison |
| `videos/optimization-radar-chart.mp4` | Radar chart of all optimisation techniques |
| `videos/deepseek-v4-hybrid-attention.mp4` | DeepSeek V4 CSA/HCA layer-by-layer animation |
| `videos/dflash-block-diffusion.mp4` | DFlash block diffusion speculative decoding |
| `videos/kv-cache-recap-canvas.mp4` | Full recap from MHA to MLA |
| `videos/recap-paced-canvas.mp4` | Paced recap for social sharing |
| `videos/recap-5x-slow-full.mp4` | Slow-motion full recap |

---

## 📖 Key Papers Cited (41 unique)

| Paper | Contribution |
|-------|-------------|
| Vaswani et al. (2017) | Transformer with Q, K, V attention |
| Shazeer (2019) | Multi-Query Attention (MQA) |
| Ainslie et al. (2023) | Grouped-Query Attention (GQA) |
| DeepSeek-AI (2024) | MLA with 93% KV reduction (DeepSeek-V2) |
| DeepSeek-AI (2024) | DeepSeek-V3 technical report |
| Kwon et al. (2023) | PagedAttention in vLLM |
| Dao et al. (2022) | FlashAttention |
| Liu et al. (2024) | KIVI 2-bit KV quantization |
| Hooper et al. (2024) | KVQuant per-channel quantization |
| Zhang et al. (2023) | H2O heavy-hitter KV eviction |
| Brandon et al. (2024) | Cross-Layer Attention (CLA) |
| Jiang et al. (2023) | Mistral 7B sliding window attention |
| Touvron et al. (2023) | LLaMA 2 |
| Meta AI (2024) | LLaMA 3 |
| Gemma Team (2024/2025) | Gemma 2 / Gemma 3 |
| vLLM (2026) | DeepSeek V4 CSA/HCA, 1M-token context |
| Anonymous (2026) | DFlash block diffusion speculative decoding |

Full reference list with arXiv links in the article.

---

## 🗂️ Repository Contents

| File | Description |
|------|-------------|
| [`index.html`](index.html) | Main 25-chapter interactive article with all animations embedded |
| [`videos/`](videos/) | 11 exported MP4 animations used in the article |
| [`README.md`](README.md) | This file |

---

## 🔗 Live Article

**Read the full interactive article:**
👉 [https://thepradip.github.io/KVCache-research/](https://thepradip.github.io/KVCache-research/)

---

## License

MIT license. Educational use. thepradip, 2026.
