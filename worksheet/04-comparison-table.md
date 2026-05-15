# 04 · Comparison Table — Bảng so sánh đầy đủ

> **Mục tiêu**: Tổng hợp tất cả số đã tính ở `03-cost-calculation.md` thành 1 bảng so sánh duy nhất — đây là artifact chính nhóm sẽ present.
>
> **Thời gian**: 10 phút (đầu phần Final)

---

## Vì sao có bảng so sánh?

Khi sếp hỏi "Nên deploy config nào?", bạn cần đặt lên bàn **1 bảng** thay vì đọc 3 báo cáo riêng. Bảng so sánh đầy đủ cho phép so sánh thẳng từng dòng, dễ nhìn ra tradeoff.

---

## Bảng chính

Điền số đã tính. Số nào chưa có → quay lại `03-cost-calculation.md` tính cho xong.

| | Config 1 | Config 2 | Config 3 |
|---|---|---|---|
| **Tên** | **Budget Bot** | **Balanced Concierge** | **Premium Concierge** |
| **① Model** | GPT-4o-mini ($0.15/$0.60) | Claude Haiku 4.5 ($1/$5) | Claude Sonnet 4.6 ($3/$15) |
| **② Web search** | OFF | ON selective (Visa + Weather) | ON selective (Visa + Weather) |
| **③ History** | Last 3 turns | Last 5 turns | Full history |
| **Intent classifier** | Keyword ($0) | Keyword ($0) | Keyword ($0) |
| **Cost / conv (Scenario A — 4 turns)** | $0.0015 | $0.0187 | $0.0422 |
| **Cost / conv (Scenario B — 7 turns)** | $0.0018 | $0.0235 | $0.0535 |
| **Monthly A** (300 conv/day × 30) | $13.49 | $168.55 | $379.66 |
| **Monthly B** (1,200 conv/day × 30) | $64.60 | $846.74 | $1,925.66 |
| **vs human $4,500/mo (A)** | rẻ **334×** | rẻ **27×** | rẻ **12×** |
| **vs human $18,000/mo (B)** | rẻ **279×** | rẻ **21×** | rẻ **9×** |
| **Savings % (A)** | **99.70%** | **96.25%** | **91.56%** |
| **Savings % (B)** | **99.64%** | **95.30%** | **89.30%** |
| **Quality estimate** | Low–Med (~72%) | Med–High (~85%) | High (~92%) |
| **Speed estimate** | High (~300ms) | Med (~1s) | Low–Med (~2s) |
| **Điểm yếu chính** | Visa info có thể outdated (web OFF) → trả lời sai → tourist khó nhập cảnh | Provider risk — Haiku có thể tăng giá hoặc deprecate (đã có tiền lệ) | Cost spike nếu super peak Tết — Scenario B có thể vượt $3k/tháng |
| **Best for** (khi nào nên dùng) | Mùa thấp điểm (Apr–Aug), volume <10k conv/tháng, FAQ-only | Default quanh năm — balance cost/quality/speed cho 90% use case | Mùa cao điểm (Sept–Mar), VIP segment, conversation phức tạp 7+ turns |

---

## Quan sát nhanh từ bảng

Trước khi sang file recommendation, trả lời 4 câu — đây là material để present:

### Câu 1 — Config rẻ nhất là gì? Đắt nhất là gì?

```text
Rẻ nhất: Budget Bot — monthly B = $64.60
Đắt nhất: Premium Concierge — monthly B = $1,925.66
Chênh: ~29.8× lần (1,925.66 / 64.60)

Ở Scenario A chênh còn lớn hơn: $379.66 / $13.49 ≈ 28.1×
```

### Câu 2 — Knob nào ảnh hưởng cost nhiều nhất?

So sánh các config khác nhau ở knob nào, chênh bao nhiêu. Thường: model tier > history > web search.

```text
Thứ tự ảnh hưởng (verified bằng phép so sánh từng cặp config):

1. MODEL TIER (driver lớn nhất):
   - GPT-4o-mini → Haiku 4.5: input ×6.5, output ×8.3 → cost tăng ~7–8× (cho cùng intent
     Guide không web). Verify: guide_4_budget=$0.00176 vs guide_4_balanced=$0.01248 → ×7.1.
   - Haiku 4.5 → Sonnet 4.6: input ×3, output ×3 → cost tăng ~3×. Verify: guide_4_balanced
     vs guide_4_premium = $0.01248 vs $0.03744 → ×3.0.

2. WEB SEARCH (driver thứ 2 — bật/tắt tạo khác biệt lớn):
   - Bật web cho Visa/Weather: +$0.005 API + 800 input tokens MỖI turn.
   - Ở Haiku, 4-turn: $0.01248 (off) → $0.03568 (on) = +186% (×2.86).
   - Ở Sonnet, 4-turn: $0.03744 → $0.06704 = +79% (×1.79).
   - Tỷ lệ tăng nhỏ hơn ở model đắt vì $0.005 API là fixed cost — relatively nhỏ so với
     LLM cost ở Sonnet.

3. HISTORY (driver thứ 3 — nhỏ hơn nhiều):
   - Full vs Last 3 ở Sonnet, 7-turn Guide: $0.07371 (full) vs $0.04611 (last 3, ước tính)
     → chênh ~60% ở turn 7 nhưng chỉ ảnh hưởng turns 4+. Toàn conversation thường tăng 10–25%.
   - Ở conversation 4 turns: history Full vs Last 3 gần như identical (vì turn 4 chỉ cần
     3 turns history, cả 2 đều cap tại đó).
```

### Câu 3 — Tại sao Scenario B không đắt ×4 lần Scenario A?

Volume Scenario B = ×4 lần Scenario A. Turns dài hơn (7 vs 4 = ×1.75). Mong đợi monthly B ≈ A × 7. Thực tế có thể thấp hơn vì sao?

Trước khi viết, nghĩ: intent mix Scenario B có gì khác? Booking + Complaint = $0 LLM ở scenario B là bao nhiêu %?

```text
Mong đợi naive: B = A × (4 volume) × (7/4 turns) ≈ A × 7. Thực tế:
  - Budget: $13.49 → $64.60 → tỷ lệ 4.79× (chứ không 7×)
  - Balanced: $168.55 → $846.74 → tỷ lệ 5.02×
  - Premium: $379.66 → $1,925.66 → tỷ lệ 5.07×

Lý do tỷ lệ thấp hơn 7× (chứ không phải đúng 7×):
1. **AI-served ratio giảm**: Scenario A AI-served 85% (Booking+Complaint = 15%), Scenario B
   AI-served chỉ 55% (Booking+Complaint = 45%). Mùa cao điểm khách BOOK NHIỀU hơn → tỷ lệ
   handoff sales cao → AI làm ít conversation hơn về tỷ lệ → cost không tỷ lệ thuận volume.
2. **Mix intent thay đổi**: Guide giảm từ 50% → 30%, Visa giảm 25% → 15%. Nhưng turns dài
   hơn (7 vs 4) bù lại phần lớn.
3. Net effect: ×4 volume × ~1.6× cost-per-conv (vì turns dài hơn + ít AI-served hơn) ≈ ×5,
   khớp với số quan sát.
```

### Câu 4 — Có config nào AI đắt hơn human không?

So sánh monthly từng config với human baseline ($4,500 cho A, $18,000 cho B). Nếu AI rẻ hơn → savings %. Nếu đắt hơn → cần justify.

```text
KHÔNG có config nào đắt hơn human. Config đắt nhất (Premium B) = $1,925.66 vẫn rẻ hơn
human ($18,000) 9.3×, savings 89.3%.

Tuy nhiên AI KHÔNG REPLACE 100% human được — vì Booking + Complaint vẫn cần sales/manager
thật (35% + 10% = 45% conversation Scenario B). Chi phí thực tế = AI cost + reduced human cost
(vẫn cần ~3–4 sales agent cho ~540 booking/ngày của Scenario B thay vì 40 agent nếu không có AI).

→ Justification thực sự: AI không thay được human, AI THANH LỌC FAQ giúp human chỉ làm
booking/complaint (việc giá trị cao) → 1 agent xử lý ~30 conv/ngày → cần ~18 agent thay vì
40 agent → tiết kiệm ~$11k/tháng human cost ngoài $16k tiết kiệm AI.

Ngoài ra justify thêm:
  - 24/7 không cần ca đêm (1 agent đêm = ~$500/tháng × 2 ca = $1k extra)
  - Đa ngôn ngữ (chatbot English serve được Korean/Japanese/French tourist; agent VN khó)
  - Scale instant — peak spike +200% trong 1h Tết → human không kịp tuyển, AI chạy ngay
  - Consistency — không tâm trạng, không quên info, không trả lời 2 khách khác nhau
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Bảng đầy đủ — không còn ô trống
- [x] Đã có 4 câu trả lời cho 4 quan sát ở trên
- [x] Nhóm đồng thuận về số trong bảng (đã sanity check)

Xong → mở `05-recommendation.md` để viết recommendation cuối + chuẩn bị present.
