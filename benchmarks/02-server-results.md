# 02 - Serve: load test + saturation reading

Host `Linux-x86_64` · llama.cpp `b10488` ·
`--parallel 4` · `ctx=2048` · `threads=6` ·
`ngl=0`

| Users | Reqs | RPS | P50 (ms) | P95 (ms) | P99 (ms) | Eff. concurrency | Failures |
|:--|--:|--:|--:|--:|--:|--:|--:|
| 10 | 63 | 1.10 | 8000 | 13000 | 14000 | 8.8 | 0.0% |
| 50 | 67 | 1.16 | 27000 | 42000 | 46000 | 29.3 | 0.0% |

*Effective concurrency = RPS x average latency (Little's Law) -- how many requests were
really in flight, regardless of how many users locust simulated. It counts queued requests
too, so the occupancy/slot ratio can legitimately exceed 1.0; it is occupancy, not
utilisation. For true slot utilisation use the server's own gauges (`make metrics`).*

## What these two runs say

| Going from 10 to 50 users | |
|:--|--:|
| Offered load | 5x |
| Throughput actually delivered | **1.06x** (21% of linear) |
| P95 latency | **3.23x** |
| Effective concurrency at 50 users | 29.3 vs `--parallel 4` slots (occupancy/slot ratio 7.33) |

**Saturated.** Throughput delivered only 1.06x for 5x the offered load, and effective concurrency (29.3) is at or above all 4 decode slots. Saturation sets in somewhere at or below 50 users; the load you added beyond that point became queue time rather than throughput.

Throughput moved 1.06x while P95 moved 3.23x. That gap is the goodput argument: past saturation you buy throughput by spending latency, and if your SLO is a P95 target then the requests you added are no longer being served within it. (This lab does not fix an SLO number for you -- pick one in your write-up and state how much goodput you keep at it.)

## Your reading (required -- replace this line)

Server bão hòa ở 50 users. RPS chỉ tăng 1.06x khi tải tāng 5x, nhưng P95 tāng 3.23x từ 13 giây lên 42 giây. Effective concurrency 29.3 cao hơn 4 slots nên request phải chờ. Nên giảm prompt/output trước để tāng goodput mà không tốn thêm RAM.
