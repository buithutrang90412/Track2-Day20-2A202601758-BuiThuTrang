# 01 - Tune: thread-count sweep

Model `Qwen3.5-0.8B-Q4_K_M.gguf` · host `Linux-x86_64` · llama.cpp `b10488`
CPU: **6 physical · 12 logical** cores · `ngl=0` · metric `tg128`

| threads (-t) | tg128 (tok/s) | vs best |
|:--|--:|--:|
| 1 | 19.9 | 38% |
| 3 | 34.0 | 65% |
| 6 | 51.9 | 100% |
| 12 | 49.6 | 96% |
| 24 | 3.6 | 7% |

**Best**: `-t 6` at 51.9 tok/s
**Slowest tested**: `-t 24` at 3.6 tok/s (14.45x spread)
**Against the physical-core default** (`-t 6`, 51.9 tok/s): 1.00x

Use this in your run:

```bash
LAB_N_THREADS=6 make bench
```

## Your explanation (required -- replace this line)

Knee nằm ở `-t 6`, bằng số physical core, đạt 51.9 tok/s. Tăng lên 12 thread giảm nhẹ còn 49.6 tok/s; 24 thread giảm mạnh còn 3.6 tok/s. Các thread thêm phải tranh CPU, cache và memory bandwidth nên scheduling overhead tăng. Máy này nên dùng 6 thread.
