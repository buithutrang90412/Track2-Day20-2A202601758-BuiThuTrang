# Reflection — Day 20 Lab (Personal Report)

> **Đây là báo cáo cá nhân.** Số liệu của bạn **không** so sánh được với bạn cùng lớp
> — chỉ so **before vs after trên chính máy bạn**. Rubric chấm độ rõ ràng của setup,
> đo lường và **lập luận**, không chấm tốc độ tuyệt đối.
>
> `make verify` sẽ fail nếu còn placeholder chưa điền. Đó là cố ý.

**Họ Tên:** Bùi Thu Trang
**Cohort:** _A20-K4_
**Ngày submit: 20/08/2026**

---

## 1. Hardware & runtime  *(rubric 1, 2 — 10 điểm)*

> Từ `make probe`. Paste output hoặc điền tay.

- **OS:** Linux 5.15.167.4-microsoft-standard-WSL2 (x86_64)
- **CPU:** 11th Gen Intel(R) Core(TM) i5-11400H @ 2.70GHz
- **Cores:** 6 physical / 12 logical
- **CPU extensions:** AVX-512, AVX2
- **RAM:** 7.6 GB
- **Accelerator:** NVIDIA GeForce RTX 3050 Laptop GPU, 4096 MiB (GPU offload OFF in the prebuilt runtime)
- **llama.cpp asset đã tải:** `llama-b10488-bin-ubuntu-vulkan-x64.tar.gz`
- **Model đã dùng:** Qwen3.5 0.8B (`LAB_MODEL=qwen35-0.8b`)
- **Quantization:** `Q4_K_M` + `UD-Q2_K_XL` (từ `models/active.json`)

**Chạy ở đâu:** Laptop Windows 11 qua WSL2
_(Nếu dùng cloud fallback: nói rõ vì sao — RAM < 8 GB, setup fail, v.v. Không mất điểm.)_

**Setup story** (≤ 80 chữ): điều gì cần thay đổi để lab chạy trên máy bạn? Có bước
nào fail rồi phải workaround không?

Lab chọn Qwen3.5 0.8B vì WSL2 chỉ có 7.6 GB RAM, thấp hơn mức 8 GB của Gemma. Runtime b10488 đã tải được; GPU NVIDIA được nhận diện nhưng bản prebuilt không enumerate device nên base track chạy CPU. Khi bench ban đầu, runtime thiếu `libssl.so.3`, cần cài bổ sung thư viện này.

---

## 2. Đo lường  *(rubric 3, 4, 5 — 20 điểm)*

> Paste bảng từ `benchmarks/01-quickstart-results.md` (`make bench` tự sinh).

| Quantization | Size (GB) | Load (ms) | TTFT P50/P95 (ms) | TPOT P50/P95 (ms) | E2E P50/P95/P99 (ms) | Decode (tok/s) |
| ------------ | --------: | --------: | ----------------: | ----------------: | -------------------: | -------------: |
| Q4_K_M       | 0.50      | 19344     | 190 / 214         | 18.8 / 21.6       | 1380 / 1534 / 1534  | 53.3           |
| UD-Q2_K_XL   | 0.39      | 15768     | 270 / 308         | 19.6 / 22.2       | 1512 / 1706 / 1706  | 50.9           |

**Quan sát** (≤ 60 chữ): 2-bit nhanh hơn bao nhiêu, và **có đáng không**? Bạn đã thử
hỏi cùng một câu trên cả hai (`make serve` vs `.venv/bin/python labs/02-serve/serve.py --compare`)
chưa? Chất lượng khác nhau thế nào?

Q2 nhỏ hơn 0.11 GB nhưng chậm hơn. Decode giảm từ 53.3 xuống 50.9 tok/s, TTFT P50 tăng từ 190 lên 270 ms. Trên máy này nên dùng Q4; Q2 chỉ có lợi về dung lượng.

---

## 3. Serving under load  *(rubric 8, 9, 10 — 20 điểm)*

> Từ `benchmarks/02-server-results.md` (`make load-report`).

| Users | RPS | P50 (ms) | P95 (ms) | P99 (ms) | Eff. concurrency | Failures |
| ----: | --: | -------: | -------: | -------: | ---------------: | -------: |
|    10 |     |          |          |          |                  |          |
|    50 |     |          |          |          |                  |          |

- **Offered load tăng 5×, throughput thực tăng:** _<X.XX>×_
- **P95 tăng:** _<X.XX>×_
- **Effective concurrency ở 50 users:** _<số>_ so với `--parallel` = _<số>_ slots

**Peak `llamacpp:n_busy_slots_per_decode`** (từ `make metrics` khi `make load-50` đang
chạy): _<số>_ /  slots

**Saturation reading** (≤ 80 chữ): server của bạn bão hoà ở đâu, và **bằng chứng nào**
thuyết phục bạn? Nếu P95 tăng nhanh hơn RPS thì phần latency thêm đó là queue time hay
compute time — bạn biết bằng cách nào? Nếu bạn phải nâng goodput@SLO, bạn sẽ đổi knob
nào **trước**, và vì sao knob đó?

_Answer here._

---

## 4. Integration  *(rubric 12, 13 — 15 điểm)*

> Từ `make pipeline`. Nói thật cái nào real, cái nào stub — stub **không** mất điểm.

| Day                   | Piece            | Real hay stub? |
| --------------------- | ---------------- | -------------- |
| N16 Cloud/IaC         |                  |                |
| N17 Data pipeline     |                  |                |
| N18 Lakehouse         |                  |                |
| N19 Vector + features |                  |                |
| N20 Serving           | `llama-server` | real           |

**Latency split** (mean của 3 query, từ output của `pipeline.py`):

- embed: 
- retrieve: 
- llm: 
- **stage chiếm nhiều nhất:**  (_<%>_ của total)

**Reflection** (≤ 60 chữ): bottleneck ở đâu? Có khớp với kỳ vọng của bạn không? Nếu
phải giảm latency của pipeline này 2×, bạn sẽ tấn công vào đâu?

_Answer here._

---

## 5. The single change that mattered most  *(rubric 11 — 10 điểm)*

> **Phần quan trọng nhất của report.** Không cần bonus track: `make tune` đã cho bạn
> một before/after thật (`benchmarks/01-tuning-tg128.md`). Đổi quantization,
> `LAB_N_CTX`, hay `--parallel` rồi đo lại cũng được.

**Change:** Chọn `-t 6` thay cho `-t 1`

```
before:  19.9 tok/s (`-t 1`)
after:   51.9 tok/s (`-t 6`)
speedup: 2.61×
```

**Tại sao nó work** (1–2 đoạn — đây là phần grader đọc kỹ nhất):

_Giải thích như đang nói với bạn ngồi cạnh. Bám vào **cơ chế**, không phải "vibes":
memory bandwidth? vector width? cache residency? scheduling? queueing? Nếu kết quả
**khác** với kỳ vọng từ deck — nói rõ, và giải thích vì sao. Grader thưởng điểm cho
lập luận đúng về một kết quả bất ngờ, hơn là một con số đẹp không được giải thích._

Tốc độ tăng từ 1 đến 6 thread vì CPU có 6 physical core, nên các thread chạy song song. Đây là mức tối ưu của máy.

Tăng lên 12 và 24 thread lại chậm hơn do tranh chấp CPU, cache và memory bandwidth. 24 thread tạo scheduling overhead lớn, nên chỉ còn 3.6 tok/s.

---

## 6. Bonus  *(optional — tối đa 20 điểm)*

> Bỏ trống nếu không làm. Xem `bonus/README.md`. Đừng làm hết — **một** finding sâu
> ăn điểm hơn năm bảng nông.

**Đã làm:** _<B1 build-compare / B2 sweep nào / B4 challenge nào / B5 lựa chọn nào>_

**Numbers:**

```
before:  <số>
after:   <số>
speedup: <X.Y>×
```

**Điều này nói lên gì mà deck chưa nói:**

_(để trống nếu bạn không làm phần này)_

---

## 7. Điều làm bạn ngạc nhiên nhất  *(optional)*

_(1–2 câu. Không bắt buộc, nhưng grader đọc hết.)_

_(để trống nếu bạn không làm phần này)_

---

## 8. Self-check trước khi push

- [ ] `hardware.json` committed
- [ ] `models/active.json` committed
- [ ] `benchmarks/01-quickstart-results.md` committed (`make bench`)
- [ ] `benchmarks/01-tuning-tg128.md` committed (`make tune`)
- [ ] `benchmarks/02-server-results.md` committed (`make load-report`)
- [ ] `benchmarks/02-server-batching-u50.md` hoặc `-metrics-u50.csv` committed (`make metrics`)
- [ ] `benchmarks/locust-10_stats.csv` + `locust-50_stats.csv` committed (`make load-10` / `load-50`)
- [ ] `benchmarks/03-integration-results.md` committed (`make pipeline`)
- [ ] Mọi section **"required — replace this line"** trong các file `benchmarks/*.md`
  đã được thay bằng nhận xét của bạn
- [ ] 5 screenshots trong `submission/screenshots/`
- [ ] `make verify` → **exit 0**
- [ ] Repo GitHub ở chế độ **public**
- [ ] Đã paste public URL vào VinUni LMS
- [ ] **Không** commit `models/*.gguf` hay `runtime/` (đã có trong `.gitignore`)

**Quan trọng:** repo phải **public** đến khi điểm được công bố. Private → grader không
xem được → 0 điểm.
