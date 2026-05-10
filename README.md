# The Evolution of KV Cache: From Transformers to TurboQuant

A comprehensive research survey covering the history and optimization of KV Cache in Large Language Models.

**Author:** Pradip Tivhale

## What's Inside

- 19 chapters covering MHA, MQA, GQA, MLA, CLA, PagedAttention, KIVI, KVQuant, TurboQuant, Bonsai 1-bit, DeepSeek V4, DFlash, and more
- 32 academic paper citations (arXiv / peer-reviewed only)
- Interactive animated visualizations (Q×K matrix multiplication, KV cache growth, quantization, MHA vs MQA vs GQA vs MLA comparison, DeepSeek V4 CSA/HCA hybrid attention)
- Exact KV cache calculations from official model config.json files
- Models covered: LLaMA 2/3/4, Mistral, Gemma 1/2/3/4, Qwen 2/3/3.5, DeepSeek-V2/V3/V4, Kimi K2/K2.5, Phi-4, Bonsai 8B

## Latest Additions

- **DeepSeek V4** — Million-token efficiency breakthrough: 90–93% KV cache reduction at 1M context via CSA/HCA hybrid attention + mHC residuals
- **DFlash** — Block diffusion for flash speculative decoding: non-autoregressive KV cache reuse across draft tokens

## Live Demo

View the article at: https://thepradip.github.io/KVCache-research/

## License

This research survey is for educational purposes.
