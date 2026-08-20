# 01 - Measure: latency baseline

Model `Qwen3.5 0.8B` · host `Linux-x86_64` · llama.cpp `b10488`
Settings: `threads=6` `ngl=0` `ctx=2048`
`max_tokens=64` · warm-up discarded
Completed requests: `Q4_K_M` 10/10 · `UD-Q2_K_XL` 10/10

| Quantization | Size (GB) | Load (ms) | TTFT P50/P95 (ms) | TPOT P50/P95 (ms) | E2E P50/P95/P99 (ms) | Decode (tok/s) |
|:--|--:|--:|--:|--:|--:|--:|
| Q4_K_M | 0.50 | 19344 | 190 / 214 | 18.8 / 21.6 | 1380 / 1534 / 1534 | 53.3 |
| UD-Q2_K_XL | 0.39 | 15768 | 270 / 308 | 19.6 / 22.2 | 1512 / 1706 / 1706 | 50.9 |

- **TTFT** = prefill. Short prompts keep it small; long-context RAG is where it explodes.
- **TPOT** = per-output-token decode cost, bounded by memory bandwidth. `decode tok/s = 1000 / TPOT_p50`.
- `UD-Q2_K_XL` decodes **1.05x SLOWER** than `Q4_K_M` here, despite being 0.11 GB smaller. That is a real result, not a mistake: fewer bits only buys speed when decode is limited by memory bandwidth. On a machine that is compute-limited instead — few cores, no GPU offload — the extra dequantization work of a heavily-quantized format can cost more than the bytes it saves. Say which case yours is.

## Your observation (required -- replace this line)

Q2 nhỏ hơn 0.11 GB nhưng chậm hơn: decode 50.9 so với 53.3 tok/s, TTFT P50 270 so với 190 ms. Q2 chỉ phù hợp khi cần tiết kiệm dung lượng.
