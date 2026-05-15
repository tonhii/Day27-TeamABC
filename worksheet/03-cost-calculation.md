# 03 · Cost Calculation — Tính chi phí từng Config × 2 Scenarios

> **Mục tiêu**: Với mỗi config đã thiết kế ở `02-config-design.md`, tính cost/turn → cost/conversation → monthly cho cả 2 scenarios (low season + high season).
>
> **Thời gian**: 55 phút (phần lớn của Main phase) — checkpoint 11:00 và 11:20

---

## Cách làm

**Đừng tính tay từng turn — đó là cách thừa thời gian.** Dùng AI để tính. Dán prompt template từ `prompts/01-cost-calc.md` vào ChatGPT/Claude/Gemini, thay parameters theo config của nhóm, AI sẽ tính cho.

Tuy nhiên nhóm phải **hiểu** kết quả AI trả về, không phải copy-paste mù. Mỗi lần AI trả số, nhóm phải tự kiểm 1 lần: con số này có hợp lý không? Có vẻ quá đắt hay quá rẻ?

→ Nhóm đã dùng prompt template, dán vào Claude. Đã verify lại bằng Python script (cùng số). Kết quả ở dưới.

---

## Trước khi gọi AI — Setup chung

**Các tham số cố định cho tất cả configs** (tham khảo `cost-reference-card.md` mục 4):

```text
System prompt:              500 tokens
User message:                80 tokens
Assistant response:         180 tokens (output)
1 prior turn (history):     260 tokens (80 user + 180 assistant)
RAG top-5 chunks:         1,250 tokens (cố định)
Web search results:         800 tokens (khi bật)
Web search API call:       $0.005 / call (Tavily)
LLM classifier:            ~170 tokens (150 in + 20 out) — KHÔNG dùng (cả 3 configs dùng keyword)
```

**Scenario A — mùa thấp điểm**:

```text
Volume:            300 conversations / ngày
Turns/conv:        avg 4 lượt
Intent mix:        Guide 50%, Visa 25%, Weather 10%, Booking 10%, Complaint 5%
AI-served ratio:   85% (15% là Booking + Complaint = handoff)
```

**Scenario B — mùa cao điểm**:

```text
Volume:           1,200 conversations / ngày (×4)
Turns/conv:        avg 7 lượt
Intent mix:        Guide 30%, Visa 15%, Weather 10%, Booking 35%, Complaint 10%
AI-served ratio:   55% (45% là handoff)
```

**Human baseline để so sánh**: $0.50 / conversation cố định.

---

## Quy trình tính cho 1 config (lặp lại cho từng config)

Đã áp dụng công thức trong worksheet. Convention nhóm:
- Web ON cho intent X → web search được gọi MỖI turn của intent X (worst-case theo worksheet)
- Booking + Complaint → 1 turn rồi handoff → $0 (keyword classifier, không tốn LLM)

---

## Điền số cho từng config

### Config 1 — Budget Bot

**Setup**: GPT-4o-mini ($0.15/$0.60), Web OFF, Last 3 turns.

**Cost/turn breakdown** (web OFF → mọi intent same):

| Turn | Input tokens | Cost/turn |
|---|---|---|
| 1 | 500 + 0 + 1,250 + 80 = 1,830 | $0.000383 |
| 2 | 500 + 260 + 1,250 + 80 = 2,090 | $0.000422 |
| 3 | 500 + 520 + 1,250 + 80 = 2,350 | $0.000461 |
| 4 | 500 + 780 + 1,250 + 80 = 2,610 | $0.000500 |
| 5 | 500 + 780 + 1,250 + 80 = 2,610 (capped Last 3) | $0.000500 |
| 6 | 2,610 | $0.000500 |
| 7 | 2,610 | $0.000500 |

**Cost/conv per intent**:
```text
cost_conv_guide_4t     = $0.00176   (turn 1+2+3+4)
cost_conv_visa_4t      = $0.00176   (web OFF → same as guide)
cost_conv_weather_4t   = $0.00176   (web OFF)
cost_conv_guide_7t     = $0.00326
cost_conv_visa_7t      = $0.00326
cost_conv_weather_7t   = $0.00326
cost_1_turn_only       = $0         (Booking + Complaint, keyword classifier)
```

**Weighted avg**:
- avg_cost_A = 0.50×$0.00176 + 0.25×$0.00176 + 0.10×$0.00176 + 0.15×$0 = **$0.00150 / conv**
- avg_cost_B = 0.30×$0.00326 + 0.15×$0.00326 + 0.10×$0.00326 + 0.45×$0 = **$0.00179 / conv**

| Item | Scenario A (4 turns) | Scenario B (7 turns) |
|---|---|---|
| Cost / conversation (avg) | $0.0015 | $0.0018 |
| Monthly cost | $13.49 | $64.60 |
| Human baseline | $4,500 | $18,000 |
| **Rẻ hơn human ___×** | **334×** | **279×** |
| **Savings %** | **99.70%** | **99.64%** |

**Sanity check** (trả lời cho nhóm trước khi đi tiếp):

- Cost/conv có nằm trong $0.005–$0.10 không? Nếu quá thấp → có thể quên component (RAG? web? classifier?). Nếu quá cao → có thể tính sai history.
- Monthly có hợp lý không? (cheap config thường $100–$300, premium config có thể đến $3,000+)

```text
Cost/conv = $0.0015–$0.0018 — DƯỚI ngưỡng $0.005 sanity check. Đây không phải lỗi: GPT-4o-mini
quá rẻ ($0.15/M input), Last 3 cap history nhỏ, và không có web → tất cả component đều cực rẻ.
Verify lại từng thành phần OK. Monthly $13/$65 thấp hơn ngưỡng $100 ở reference card vì cả
volume × cost đều ở mức thấp nhất có thể. Số ổn.
```

---

### Config 2 — Balanced Concierge

**Setup**: Claude Haiku 4.5 ($1.00/$5.00), Web ON cho Visa+Weather, Last 5 turns.

**Cost/turn breakdown — Guide (web OFF)**:

| Turn | Input tokens | Cost/turn (LLM only) |
|---|---|---|
| 1 | 1,830 | $0.00273 |
| 2 | 2,090 | $0.00299 |
| 3 | 2,350 | $0.00325 |
| 4 | 2,610 | $0.00351 |
| 5 | 2,870 | $0.00377 |
| 6 | 3,130 (capped Last 5) | $0.00403 |
| 7 | 3,130 | $0.00403 |

**Cost/turn breakdown — Visa/Weather (web ON, +800 tokens, +$0.005 API)**:

| Turn | Input tokens | LLM cost | + Web API | Total |
|---|---|---|---|---|
| 1 | 2,630 | $0.00353 | $0.005 | $0.00853 |
| 2 | 2,890 | $0.00379 | $0.005 | $0.00879 |
| 3 | 3,150 | $0.00405 | $0.005 | $0.00905 |
| 4 | 3,410 | $0.00431 | $0.005 | $0.00931 |
| 5 | 3,670 | $0.00457 | $0.005 | $0.00957 |
| 6 | 3,930 | $0.00483 | $0.005 | $0.00983 |
| 7 | 3,930 | $0.00483 | $0.005 | $0.00983 |

**Cost/conv per intent**:
```text
cost_conv_guide_4t     = $0.01248   (sum turn 1–4 web OFF)
cost_conv_visa_4t      = $0.03568   (sum turn 1–4 web ON)
cost_conv_weather_4t   = $0.03568
cost_conv_guide_7t     = $0.02431
cost_conv_visa_7t      = $0.06491
cost_conv_weather_7t   = $0.06491
cost_1_turn_only       = $0
```

**Weighted avg**:
- avg_cost_A = 0.50×$0.01248 + 0.25×$0.03568 + 0.10×$0.03568 + 0.15×$0 = **$0.01873 / conv**
- avg_cost_B = 0.30×$0.02431 + 0.15×$0.06491 + 0.10×$0.06491 + 0.45×$0 = **$0.02352 / conv**

| Item | Scenario A | Scenario B |
|---|---|---|
| Cost / conversation (avg) | $0.0187 | $0.0235 |
| Monthly cost | $168.55 | $846.74 |
| **Rẻ hơn human ___×** | **26.7×** | **21.3×** |
| **Savings %** | **96.25%** | **95.30%** |

**Sanity check**:

```text
Cost/conv $0.019–$0.024 nằm trong ngưỡng $0.005–$0.10 ✓. Monthly $169/$847 → trong ngưỡng
$100–$1,000 cho mid config ✓. Tăng ~12× so với Budget chủ yếu do:
  (1) Haiku 4.5 đắt hơn GPT-4o-mini ~6.5× ở input, ~8× ở output
  (2) Web search Visa+Weather tăng cost intent đó ~3×
Đúng như kỳ vọng — Balanced trade ~$155/tháng extra để có quality+freshness tốt hơn nhiều.
```

---

### Config 3 — Premium Concierge

**Setup**: Claude Sonnet 4.6 ($3.00/$15.00), Web ON cho Visa+Weather, Full history.

**Cost/turn breakdown — Guide (web OFF, full history)**:

| Turn | Input tokens | Cost/turn |
|---|---|---|
| 1 | 1,830 | $0.00819 |
| 2 | 2,090 | $0.00897 |
| 3 | 2,350 | $0.00975 |
| 4 | 2,610 | $0.01053 |
| 5 | 2,870 | $0.01131 |
| 6 | 3,130 | $0.01209 |
| 7 | 3,390 | $0.01287 |

**Cost/turn breakdown — Visa/Weather (web ON, full history)**:

| Turn | Input tokens | LLM cost | + Web | Total |
|---|---|---|---|---|
| 1 | 2,630 | $0.01059 | $0.005 | $0.01559 |
| 2 | 2,890 | $0.01137 | $0.005 | $0.01637 |
| 3 | 3,150 | $0.01215 | $0.005 | $0.01715 |
| 4 | 3,410 | $0.01293 | $0.005 | $0.01793 |
| 5 | 3,670 | $0.01371 | $0.005 | $0.01871 |
| 6 | 3,930 | $0.01449 | $0.005 | $0.01949 |
| 7 | 4,190 | $0.01527 | $0.005 | $0.02027 |

**Cost/conv per intent**:
```text
cost_conv_guide_4t     = $0.03744
cost_conv_visa_4t      = $0.06704
cost_conv_weather_4t   = $0.06704
cost_conv_guide_7t     = $0.07371
cost_conv_visa_7t      = $0.12551
cost_conv_weather_7t   = $0.12551
cost_1_turn_only       = $0
```

**Weighted avg**:
- avg_cost_A = 0.50×$0.03744 + 0.25×$0.06704 + 0.10×$0.06704 + 0.15×$0 = **$0.04218 / conv**
- avg_cost_B = 0.30×$0.07371 + 0.15×$0.12551 + 0.10×$0.12551 + 0.45×$0 = **$0.05349 / conv**

| Item | Scenario A | Scenario B |
|---|---|---|
| Cost / conversation (avg) | $0.0422 | $0.0535 |
| Monthly cost | $379.66 | $1,925.66 |
| **Rẻ hơn human ___×** | **11.9×** | **9.3×** |
| **Savings %** | **91.56%** | **89.30%** |

**Sanity check**:

```text
Cost/conv $0.042–$0.054 — vẫn dưới $0.10 ngưỡng. Monthly $380/$1,926 → trong ngưỡng
$1,000–$3,000 cho premium ✓.
Sonnet đắt gấp 3× Haiku ở input, 3× ở output → đúng tỷ lệ. Full history thêm 1×260 ở
mỗi turn so với Last 5 → ở turn 7 tăng 520 tokens × $3/M ≈ $0.0016/turn — đáng kể nhưng
không phải driver chính (model price mới là driver).
Số ổn — không có anomaly.
```

---

### Config 4 (optional)

| Item | Scenario A | Scenario B |
|---|---|---|
| Cost / conversation (avg) | (skip) | (skip) |
| Monthly cost | (skip) | (skip) |

→ Nhóm skip Config 4 vì 3 configs hiện tại đã cover đầy đủ tradeoff trên 3 knobs.

---

## Quality + Speed estimate (qualitative)

Mỗi config — estimate Low / Medium / High. Không có công cụ đo chính xác trong lab, ước tính dựa trên model tier + web search + history.

| Config | Quality (Low/Med/High) | Speed (Low/Med/High) | Lý do |
|---|---|---|---|
| 1: Budget Bot | **Low–Med** (~72%) | **High** (~200–400ms) | GPT-4o-mini đủ FAQ; nhưng Last 3 dễ quên context, web OFF → visa info có thể outdated. |
| 2: Balanced Concierge | **Medium–High** (~85%) | **Medium** (~800ms–1.5s) | Haiku 4.5 hiểu instruction tốt hơn rõ rệt; web ON cho Visa/Weather → info fresh; Last 5 đủ context. |
| 3: Premium Concierge | **High** (~92%) | **Low–Med** (~1.5–3s) | Sonnet 4.6 hiểu nuance; Full history + web → consistency tối đa; chậm hơn do model lớn. |
| 4: — | — | — | — |

**Hướng dẫn ước tính**:

- **Quality**: Cheap model → Low (70%). Strong model → High (88%). Web search bật → Quality tăng vì info real-time. History Full → Quality tốt hơn ở conversation dài.
- **Speed**: Cheap model thường nhanh (~200ms). Strong model chậm hơn (~1–3s). Web search bật → +1–2s.

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Tất cả ≥3 configs đã có cost/conv + monthly cho cả 2 scenarios
- [x] Đã so sánh từng config với human baseline ($0.50/conv)
- [x] Có quality + speed estimate cho mỗi config
- [x] Đã sanity check — không có số "quá lạ" (cost <$0.001 hoặc >$1/conv) → Budget cost/conv = $0.0015 hơi thấp nhưng đã verify, không phải lỗi

⚑ **Checkpoint 11:00**: ≥1 config đã tính cost xong ✓ &nbsp; · &nbsp; ⚑ **Checkpoint 11:20**: tất cả configs đã tính cost xong cho cả 2 scenarios ✓

Xong → mở `04-comparison-table.md`.
