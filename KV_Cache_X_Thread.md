# X Thread: The Evolution of KV Cache

1. The hidden bottleneck in modern LLMs is not just model size.

It is **KV cache memory**.

That is the memory the model keeps while generating token by token.

And at long context, it can become bigger than the model itself.

2. That is why so much recent model architecture work is really a memory story.

MHA -> MQA -> GQA -> MLA -> TurboQuant

This is not just acronym inflation.

It is the industry trying to beat the memory wall.

3. Start with the old baseline:

**MHA**

Every attention head stores its own Keys and Values.

Great quality.
Terrible memory use.

4. Example:

A naive 7B FP16 setup can need about **512 KB per token** of KV cache.

At **128K context**, that can become **64 GB** of memory.

That is just for the cache.

5. Then came:

**MQA**

Keep many query heads, but share one K/V set.

Huge memory drop.
But sometimes a quality tradeoff.

6. Then:

**GQA**

Share K/V within groups instead of across every head.

This became the practical sweet spot.

That is why so many major models adopted it.

7. Then:

**MLA**

Do not cache the full K/V state directly.

Cache a much smaller compact state and reconstruct what you need during decoding.

That is how models like DeepSeek changed the game.

8. But architecture was only half the story.

The next wave was:

**KV cache quantization**

Same cache idea.
Fewer bits per number.

9. That is where methods like:

- KIVI
- KVQuant
- TurboQuant

come in.

The goal is simple:

keep the cache useful, but make it much smaller.

10. One of my favorite examples is **Bonsai 8B**.

It shows something important:

**1-bit weights do NOT automatically solve runtime memory.**

The weights can be tiny while the KV cache still dominates.

11. In local benchmark artifacts I reviewed:

Bonsai-8B at **32K context**

- FP16 KV total memory: about **6011 MiB**
- Q4/TurboQuant-style KV total memory: about **2699 MiB**

That is the difference between barely fitting and running comfortably.

12. Another strong example:

**Gemma 4 E4B**

Interesting not because it is flashy, but because it looks engineered for efficient long context:

- low KV head count
- KV sharing across layers
- hybrid local/global attention

13. In local `turboquant-llamacpp` measurements on Gemma 4 E4B:

- 4K: **104 MiB -> 29 MiB**
- 16K: **296 MiB -> 83 MiB**
- 32K: **552 MiB -> 155 MiB**

Roughly **3.56x** KV reduction.

14. Big takeaway:

The long-context revolution was not just about making models smarter.

It was about making memory cheaper.

15. So the real story of KV cache is this:

- better attention layouts
- fewer KV heads
- compressed latent caches
- smarter paging
- fewer bits per value

That stack is what made long-context AI practical.

16. If you build LLM infra, edge AI, or inference systems:

understanding KV cache is no longer optional.

It is one of the core levers behind cost, latency, and context length.

17. I turned the full story into a visual blog:

**From the first Transformer to TurboQuant, how AI beat the memory wall and made long-context models possible.**

If people want, I can also share:

- a short LinkedIn version
- a visual recap
- or a clean engineer-focused PDF thread summary

18. #AI #LLM #Transformers #Inference #Quantization #MLOps #EdgeAI
