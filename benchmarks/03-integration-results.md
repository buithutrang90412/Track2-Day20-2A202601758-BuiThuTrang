# 03 - Integrate: RAG pipeline run

Host `Linux-x86_64` · llama.cpp `b10488` ·
retrieval backend: **keyword overlap** · 3 queries

| Query | Contexts retrieved | embed (ms) | retrieve (ms) | llm (ms) | total (ms) |
|:--|--:|--:|--:|--:|--:|
| Why is goodput more useful than raw throughp... | goodput, paged, radix | 0.0 | 0.0 | 3194.6 | 3194.7 |
| What problem does PagedAttention actually so... | paged, radix, disagg | 0.0 | 0.1 | 2849.0 | 2849.1 |
| When does splitting prefill and decode help?... | disagg, radix, batching | 0.0 | 0.1 | 4350.8 | 4350.9 |

Mean per stage (ms): embed **0.0** · retrieve **0.1** ·
llm **3464.8** · total **3464.9**
Dominant stage: **llm** (100% of total)

## Answers returned

**Why is goodput more useful than raw throughput?**

> Based on the provided context, **Goodput** is more useful than raw throughput because it specifically focuses on the **requests per second (RPS)** that met the Target Time-to-Fill (TTFT) and Target Time-to-Poll (TPOT) targets.

Raw throughput ignores SLOs (Service Level Objectives), whereas Goodput counts only the requests per second that actually satisfied these targets. This means Goodput provid

**What problem does PagedAttention actually solve?**

> Based on the provided context, **PagedAttention** solves the problem of **internal fragmentation in GPU memory** caused by storing the Key-Value (KV) cache in non-contiguous pages.

The context explicitly states that PagedAttention "stores the KV cache in non-contiguous pages, removing the internal fragmentation that wasted most GPU memory."

While the context also notes that RadixAttention uses a

**When does splitting prefill and decode help?**

> Based on the context provided, splitting prefill and decode helps when **both operations are compute-bound**.

The reasoning is as follows:
1.  **Prefill** is described as "compute-bound," meaning it requires significant processing power (CPU/GPU) to generate the input tokens for the model.
2.  **Decode** is described as "memory-bandwidth-bound," meaning it requires significant memory bandwidth to


## Which N16-N19 pieces are real (required -- replace this line)

N16 Cloud/IaC: stub. N17 Data pipeline: stub. N18 Lakehouse: stub. N19 Vector + features: stub; pipeline dùng keyword overlap fallback vì không có embedding server. N20 Serving: real. LLM chiếm 100% latency, đúng với kỳ vọng. Muốn giảm 2x, tập trung vào decode LLM và output tokens.
