# 02 · Configuration Design — Đặt tên + Chốt knobs cho ≥3 Configs

> **Mục tiêu**: Biến phác thảo ở `01-base-flow.md` thành ≥3 configurations chi tiết, mỗi config có tên + 3 knobs đã chốt + lý do chọn.
>
> **Thời gian**: 15 phút (đầu phần Main, trước khi tính cost)

---

## Tại sao đặt tên + viết lý do?

Khi present, nhóm sẽ nói "Config 1, Config 2, Config 3" → người nghe sẽ chán ngay. Đặt tên gợi mở (Budget Bot, Premium Concierge, Smart Mix...) giúp memorable + cho thấy nhóm hiểu rõ tradeoff. Viết lý do giúp nhóm tự kiểm tra: "Mình chọn config này vì lý do gì? Có justify được không?"

---

## Cách điền

Với mỗi config: đặt tên + chốt 3 knobs + viết 2–3 câu lý do chọn. Mỗi câu lý do phải gắn với 1 tình huống thực tế (volume thấp / khách hỏi visa nhiều / budget bị siết...).

Tham khảo bảng pricing chi tiết tại `cost-reference-card.md` mục **3. Decision Points**.

---

## Config 1

**Tên config**:

```text
Budget Bot — "Lean Mode for Low Season"
```

### 3 Knobs

**① Model tier**:

```text
Response model: GPT-4o-mini → giá $0.15 / $0.60 per 1M tokens (input/output)
Classifier model: Keyword regex → giá $0 / $0 per 1M tokens (no LLM call)
```

**② Web search**:

```text
☑ OFF
□ ON selective — bật cho intent: __________________
□ ON broad
```

**③ History management**:

```text
☑ Last 3
□ Last 5
□ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

Trước khi viết, tự hỏi:

- Config này phục vụ tình huống nào tốt nhất? (mùa thấp điểm? night-time? volume cao đột biến?)
- Trade-off chính là gì? (Rẻ nhưng kém chất lượng? Đắt nhưng chính xác?)
- Khách hàng nào sẽ hài lòng nhất với config này? Khách nào sẽ thất vọng?

```text
Phục vụ mùa thấp điểm (off-season, sau Tết → trước hè) khi volume thấp & khách hỏi câu đơn giản
(weather, "where to go"). GPT-4o-mini đủ tốt cho Guide intent — nội dung KB ổn định (Hạ Long,
Hội An, Sapa không đổi mấy). Last 3 đủ vì conversation trung bình 4 turns. Cost cực thấp
(~$13/tháng cho Scenario A) → có thể chạy 24/7 mà không lo ngân sách. Khách FAQ-only sẽ hài
lòng; khách hỏi visa policy cập nhật hoặc weather real-time sẽ không hài lòng vì web OFF.
```

### Rủi ro lớn nhất của config này

```text
Visa policy VN đổi liên tục 2024–2026 (e-visa mở rộng, miễn visa nước mới) → RAG KB outdated
sau 1–2 tháng → bot trả lời SAI về visa → tourist bay sang gặp rắc rối nhập cảnh → 1-star
review + complaint → mất danh tiếng. Mitigation: trong intent Visa, có thể đặt rule
"confidence < threshold → handoff human" để tránh trả lời sai tự tin.
```

---

## Config 2

**Tên config**:

```text
Balanced Concierge — "Year-round Default"
```

### 3 Knobs

**① Model tier**:

```text
Response model: Claude Haiku 4.5 → giá $1.00 / $5.00 per 1M tokens
Classifier model: Keyword regex → giá $0 / $0 per 1M tokens (no LLM call)
```

**② Web search**:

```text
□ OFF
☑ ON selective — bật cho intent: Visa + Weather
□ ON broad
```

**③ History management**:

```text
□ Last 3
☑ Last 5
□ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

```text
Đây là default config nhóm khuyên deploy quanh năm. Haiku 4.5 chất lượng tốt hơn GPT-4o-mini
~15% trên benchmark tiếng Anh tự nhiên — đủ để tourist cảm thấy "smart" mà không over-spend.
Web ON selective cho Visa + Weather giải quyết đúng 2 intent có rủi ro outdated/real-time
cao nhất (~25% conversation Scenario A). Last 5 capture được "budget $500", "traveling with
kids", "vegetarian" mà tourist nói ở turn 1–2 → consistency tốt khi đề xuất tour ở turn 5–6.
Cost ~$169/$847 monthly cho A/B vẫn rẻ hơn human ~21–27×, savings >95%.
```

### Rủi ro lớn nhất của config này

```text
Provider risk — Anthropic tăng giá Haiku 4.5 hoặc deprecate (đã có tiền lệ Haiku 3.5 → 4.0).
Mitigation: có sẵn fallback sang DeepSeek V4 Pro ($1.74/$3.48, rẻ hơn ~30% nhưng quality
tương đương) hoặc Gemini 2.5 Flash ($0.30/$2.50, rẻ hơn ~3× nhưng quality thấp hơn 1 bậc).
Setup A/B test 5% traffic mỗi quý để tự verify quality khi switch provider.
```

---

## Config 3

**Tên config**:

```text
Premium Concierge — "Peak Season + VIP"
```

### 3 Knobs

**① Model tier**:

```text
Response model: Claude Sonnet 4.6 → giá $3.00 / $15.00 per 1M tokens
Classifier model: Keyword regex → giá $0 / $0 per 1M tokens (no LLM call)
```

**② Web search**:

```text
□ OFF
☑ ON selective — bật cho intent: Visa + Weather
□ ON broad
```

**③ History management**:

```text
□ Last 3
□ Last 5
☑ Full
□ Summarize every ___ turns
```

### Lý do nhóm chọn config này

```text
Bật trong mùa cao điểm (Tết, hè, lễ hội — Sept–Apr inbound peak) khi volume tăng ×4 và 35%+
conversation là Booking → giá trị mỗi conversation cao hơn (khách thật sự định book).
Sonnet 4.6 hiểu được câu hỏi phức tạp ("compare Ha Long vs Lan Ha for honeymooners with
budget $2k" — Haiku đôi khi miss nuance). Full history quan trọng cho conversation 7+ lượt:
khách VIP nói ở turn 1 "I'm vegan, allergic to peanuts, traveling with my elderly mom" →
turn 7 đề xuất tour phải tránh hết. Last 5 sẽ quên → embarrassing miss. Cost $380/$1,926
monthly vẫn rẻ hơn human 9–12×, savings >89% — đáng đầu tư cho khách high-value.
```

### Rủi ro lớn nhất của config này

```text
Cost spike risk: Scenario B đã $1,926/tháng cho 1,200 conv/ngày. Nếu volume scale lên
3,000 conv/ngày (super peak Tết) → ~$4,800/tháng. Vẫn rẻ hơn human linear scale (~$45k/tháng)
nhưng cần budget alert tự động khi monthly cost vượt ngưỡng $3,000 để PM kịp downgrade
sang Balanced cho intent Guide (giữ Sonnet chỉ cho Visa + Booking inquiry).
```

---

## Config 4 (optional — nếu thời gian dư)

Nhóm có thể thiết kế thêm config thứ 4 để có thêm điểm so sánh. Không bắt buộc.

**Tên config**:

```text
(Bỏ qua — 3 configs hiện tại đã cover Cheap/Mid/Strong tier, web OFF/ON selective, History Last 3/5/Full.
Thêm config 4 sẽ giống config 2 hoặc 3 với 1 knob tweaked → không thấy thêm tradeoff mới.
Nếu instructor yêu cầu, sẽ thêm "Smart Mix" — Haiku cho Guide, Sonnet cho Visa — để chứng minh
mix-by-intent có lợi.)
```

### 3 Knobs

```text
Model: ___    Web: ___    History: ___
```

### Lý do

```text
(skip)
```

---

## Bảng kiểm trước khi tính cost

- [x] ≥3 configs đã đặt tên (không chỉ "Config 1/2/3")
- [x] Mỗi config đã chốt rõ 3 knobs (không còn ô trống)
- [x] Mỗi config có ≥2 câu lý do
- [x] 3 configs đủ khác biệt — không phải chỉ đổi mỗi 1 knob nhỏ
- [x] Nhóm đồng thuận đây là 3 configs đáng so sánh

**Nếu 3 configs quá giống nhau** (chỉ đổi model, knobs khác giống hệt) → quay lại tweak. Mục đích là thấy tradeoff — configs giống nhau quá → không thấy tradeoff.

→ Nhóm verify: cả 3 knobs đều đổi giữa configs → tradeoff rõ ràng (Budget cắt cả 3, Balanced cân bằng, Premium max cả 3). ✓

Xong → mở `03-cost-calculation.md` để bắt đầu tính cost.
