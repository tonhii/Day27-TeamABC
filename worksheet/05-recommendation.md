# 05 · Recommendation + Justification — Kết luận & Chuẩn bị Present

> **Mục tiêu**: Chọn 1 config (hoặc combo) nhóm recommend deploy, viết justification ngắn gọn, và chuẩn bị 5 phút present.
>
> **Thời gian**: 10 phút (cuối phần Final) — Pens down lúc 12:00

---

## Bảng số ai cũng tính được. PM giỏi phải **recommend** và **justify**.

Đây là phần quan trọng nhất — phần phân biệt nhóm chỉ làm xong với nhóm thực sự hiểu sản phẩm.

---

## 4 câu hỏi nhóm phải trả lời

Mỗi câu trả lời 2–4 câu. Không lan man, không clichés. Mỗi câu phải justify được bằng số trong bảng so sánh.

### Câu 1 — Recommend config nào?

Trước khi viết, thảo luận 1 phút:

- Recommend 1 config duy nhất chạy quanh năm? Hay 2 configs khác nhau cho mùa thấp / mùa cao?
- Có nên recommend "Smart Mix model theo intent" thay vì pick 1 config cố định không?
- Nếu sếp nói "chỉ deploy 1 config thôi" — chọn cái nào?

```text
Nhóm recommend chiến lược 2 CONFIG — Balanced Concierge làm default quanh năm, switch sang
Premium Concierge trong 3 tháng peak (Tết + Sept/Oct inbound peak). KHÔNG recommend Budget
Bot vì rủi ro visa info outdated quá lớn — 1 tourist bay sang VN gặp rắc rối nhập cảnh =
1 lawsuit + 100 bad review, không justify được $155/tháng tiết kiệm so với Balanced.

Nếu sếp chỉ cho 1 config → chọn Balanced Concierge. Lý do: 96% savings cả 2 scenario,
quality Med-High đủ phục vụ 90% case, và mitigation cho 10% case khó (web ON cho Visa) đã
có sẵn. Premium chỉ thực sự cần khi volume Scenario B + có VIP segment lớn.
```

### Câu 2 — So với human baseline $0.50/conv → tiết kiệm bao nhiêu? Có đắt hơn human ở chỗ nào không?

```text
Với Balanced Concierge (recommend chính):
  - Scenario A: tiết kiệm $4,331/tháng (96.25%) vs human $4,500.
  - Scenario B: tiết kiệm $17,153/tháng (95.30%) vs human $18,000.
  - Tổng năm (giả sử 9 tháng A + 3 tháng B): tiết kiệm ~$90,400/năm.

AI KHÔNG đắt hơn human ở config nào, nhưng AI KHÔNG REPLACE 100% human được — 45% conversation
mùa cao điểm vẫn là Booking/Complaint cần sales agent thật. Implication: AI thanh lọc FAQ
giúp giảm headcount ~50% (từ 40 agent → 18 agent), tiết kiệm thêm ~$11k/tháng human cost.

AI thắng human ở: (a) 24/7 không cần ca đêm, (b) đa ngôn ngữ — chatbot English cover được
Korean/Japanese/French tourist, (c) scale instant lúc peak Tết, (d) consistency tuyệt đối.
```

### Câu 3 — Khi nào nên upgrade / downgrade config?

Trước khi viết, tự hỏi:

- Volume bao nhiêu thì cost AI scale lớn hơn benefit?
- Quality complaint rate bao nhiêu thì biết Budget Bot không đủ?
- Có signal nào báo nên chuyển sang Premium? (mùa cao điểm bắt đầu? customer feedback?)

```text
UPGRADE Balanced → Premium khi ≥2 trigger sau:
  - Volume > 1,000 conv/ngày trong 2 tuần liên tục (signal mùa cao điểm thật sự).
  - VIP segment (booking trung bình > $2k/tour) > 15% conversation — quality miss tốn doanh thu.
  - Quality complaint rate (CSAT < 4/5) > 8% — tourist phàn nàn bot trả lời "shallow".
  - 1 tuần trước Tết / Quốc khánh / hè cao điểm (preemptive switch).

DOWNGRADE Balanced → Budget khi TẤT CẢ trigger sau:
  - Volume < 200 conv/ngày trong 2 tuần.
  - 90%+ conversation là Guide intent đơn giản (KB đủ, không cần web).
  - Quality complaint rate < 3%.
  - Budget ngân sách Q bị cắt > 30%.
  → Trong trường hợp này vẫn KHUYẾN CÁO giữ web ON cho Visa intent (chỉ tốn ~$5/tháng thêm)
    để tránh rủi ro visa-info-sai.

Nếu volume > 5,000 conv/ngày liên tục → cân nhắc Smart Mix (Haiku Guide + Sonnet Visa+Weather)
thay vì full Premium — cost tiết kiệm 30–40% mà quality gần tương đương.
```

### Câu 4 — Rủi ro lớn nhất của config được chọn?

Trước khi viết, tự hỏi:

- Rủi ro về quality? (visa info outdated? language mismatch?)
- Rủi ro về cost? (provider tăng giá? volume spike?)
- Rủi ro về business? (khách bị bot trả lời sai → bad review → mất khách?)
- Có mitigation plan không?

```text
3 rủi ro chính của Balanced Concierge, kèm mitigation:

1. PROVIDER PRICE RISK — Anthropic tăng giá Haiku hoặc deprecate (Haiku 3.5 → 4.0 đã có).
   Mitigation: Setup A/B test 5% traffic với DeepSeek V4 Pro ($1.74/$3.48) sẵn sàng switch.
   Nếu Haiku tăng > 30%, switch trong 1 tuần. Monitor cost monthly với alert ở $1,200/tháng.

2. VISA INFO ERROR RISK — dù web ON cho Visa, web search có thể trả về source không reliable
   (forum, blog cũ). Tourist tin bot rồi bay sang gặp rắc rối nhập cảnh = lawsuit risk.
   Mitigation: trong system prompt Visa intent, force model output disclaimer + link tới
   trang official immigration VN (immigration.gov.vn). Set confidence threshold: nếu model
   uncertain → handoff sales thay vì trả lời confidently.

3. VOLUME SPIKE RISK — Tết + influencer post viral → traffic ×10 trong 24h → cost spike
   $500–$1,000 trong 1 ngày + rate limit Anthropic.
   Mitigation: Setup auto-scaling fallback — nếu Anthropic rate limit, switch sang Gemini
   2.5 Flash ($0.30/$2.50, gần như free comparative) cho intent Guide; giữ Haiku chỉ cho
   Visa+Weather. Cost cap hàng ngày $50 default, alert PM nếu vượt.
```

---

## Final answer — Recommendation in 1 paragraph

Tổng hợp 4 câu trên thành 1 paragraph 5–7 câu — đây là phần nhóm sẽ đọc / chiếu khi present.

```text
Nhóm recommend deploy "Balanced Concierge" làm config mặc định quanh năm (Haiku 4.5 + web ON
selective cho Visa/Weather + Last 5 turns), và switch sang "Premium Concierge" trong 3 tháng
peak (Tết + Sept–Oct inbound). Balanced tiết kiệm 96% vs human ở Scenario A ($169/tháng vs
$4,500) và 95% ở Scenario B ($847 vs $18,000) — tổng ~$90k/năm cho 9-3 month split, không tính
$11k/tháng tiết kiệm headcount nhờ AI lọc FAQ. Nhóm RỜI Budget Bot dù rẻ nhất ($65/tháng) vì
rủi ro visa info outdated quá cao — 1 lawsuit lớn hơn 50× saving. Premium chỉ activate khi có
≥2 trigger: volume > 1k/ngày 2 tuần liên tục, VIP segment > 15%, hoặc 1 tuần trước peak. 3 rủi ro
chính của Balanced — provider tăng giá, visa info wrong, volume spike — đều có mitigation cụ thể
sẵn sàng (fallback DeepSeek V4 Pro + force disclaimer + Gemini Flash fallback ở rate limit).
```

---

## Chuẩn bị Present (5 phút)

Chia 5 phút thành 5 nhịp. 1 người trong nhóm chính phụ trách 1 nhịp. Người còn lại trả lời Q&A.

### Nhịp 0:00 – 0:30 — Base flow + 3 knobs đã chọn

Ai trình bày: **Trần Tiến Long**

Nói gì:

```text
"Travel chatbot VN phục vụ tourist quốc tế — base flow gồm 4 bước: Intent Classify (keyword,
$0) → Route 5 intent → Context Assembly (sys + history + RAG + web optional) → Response.
Nhóm tweak 3 knobs: Model tier, Web search, History management. Booking + Complaint handoff
ngay → $0 LLM."
```

### Nhịp 0:30 – 1:00 — Config overview

Ai trình bày: **Trần Tiến Long**

Nói gì (đọc nhanh tên + knobs 3 configs):

```text
- Config 1 — Budget Bot:        GPT-4o-mini    · Web OFF        · Last 3
- Config 2 — Balanced Concierge: Haiku 4.5     · Web ON (Vi+We)  · Last 5
- Config 3 — Premium Concierge:  Sonnet 4.6    · Web ON (Vi+We)  · Full history
```

### Nhịp 1:00 – 2:00 — Cost comparison

Ai trình bày: **Hồ Thị Tố Nhi**

Nói gì (chiếu bảng so sánh, highlight rẻ nhất / đắt nhất):

```text
"Bảng monthly cost — Scenario B (mùa cao điểm, 1,200 conv/ngày, 7 turns):
  - Budget    $65    (rẻ hơn human 279×, savings 99.6%)
  - Balanced  $847   (rẻ hơn human 21×, savings 95.3%)
  - Premium  $1,926  (rẻ hơn human 9.3×, savings 89.3%)
Tất cả đều rẻ hơn human cost $18,000. Chênh giữa rẻ nhất và đắt nhất ~30×."
```

### Nhịp 2:00 – 3:00 — Key insight

Ai trình bày: **Lê Thị Phương**

Nói gì (knob nào ảnh hưởng cost nhiều nhất + tại sao):

```text
"Knob ảnh hưởng cost nhiều nhất là MODEL TIER: GPT-4o-mini → Haiku 4.5 tăng ~7×, Haiku → Sonnet
thêm 3×. Web search tăng ~80–185% cho intent đó (vì $0.005 API + 800 tokens MỖI turn). History
chỉ tăng 10–25% toàn conversation — driver yếu nhất. Insight quan trọng: Scenario B KHÔNG đắt ×7
mà chỉ ×5 lần Scenario A vì AI-served ratio giảm từ 85% xuống 55% — mùa cao điểm khách book
nhiều → handoff sales nhiều → AI làm ít hơn về tỷ lệ."
```

### Nhịp 3:00 – 4:30 — Recommendation + justification

Ai trình bày: **Trần Tiến Long** (người mạnh nhất trong nhóm — đọc paragraph "Final answer")

Nói gì (đọc paragraph "Final answer" ở trên):

```text
[Copy paragraph "Final answer" — đọc to, rõ ràng, chậm. Chiếu bảng so sánh kèm theo highlight
hàng Balanced + Premium.]
```

### Nhịp 4:30 – 5:00 — Hardest question prep

Ai trình bày: **Lê Thị Phương**

Nhóm dự đoán câu hỏi khó nhất sẽ bị hỏi là gì?

```text
"Tại sao nhóm KHÔNG chọn Budget Bot dù rẻ nhất? Saving $155/tháng so với Balanced tưởng đáng
lắm — sao bỏ qua?"
```

Câu trả lời sẵn:

```text
"Vì rủi ro asymmetric. Budget tiết kiệm $155/tháng (~$1,860/năm) — không lớn. Nhưng RỦI RO
visa info wrong + tourist bay sang gặp rắc rối nhập cảnh = 1 lawsuit ~$50k + bad review viral
trên TripAdvisor mất ~$20k revenue trong 6 tháng. Expected loss ($70k × 5% xác suất / năm) =
$3,500/năm — vẫn lớn gấp 2× saving. Math không justify rủi ro. Đây là quyết định PM, không
phải quyết định kế toán."
```

---

## Q&A — 2 phút sau khi present xong

Sẵn sàng cho 1 câu từ class + 1 câu từ instructor. Không cần lo lắng — nếu chưa biết câu trả lời, nói "đây là điểm nhóm chưa nghĩ đến — sẽ tính lại sau buổi".

**3 câu instructor thường hỏi**:

1. *"Knob nào ảnh hưởng cost nhiều nhất trong config của nhóm? Tại sao?"*
2. *"Nếu provider tăng giá API ×2 → config của nhóm còn sống được không?"*
3. *"So với nhóm X (vừa present trước) — tại sao nhóm bạn chọn khác?"*

Suy nghĩ trước câu trả lời ngắn:

```text
1. Model tier — chứng minh bằng 3 cặp so sánh: GPT-4o-mini vs Haiku ×7, Haiku vs Sonnet ×3,
   web OFF vs ON cho 1 intent +80–185%. Model tier là driver vì cost scale với tokens, mà
   tokens × price-per-M là phép nhân — model price chênh nhiều thì cost chênh nhiều.

2. Anthropic ×2 → Haiku $2/$10 → Balanced cost ~$337/$1,694 monthly. Vẫn rẻ hơn human ~10×,
   savings ~91%. Sống được — nhưng sẽ switch sang DeepSeek V4 Pro ($1.74/$3.48 < Haiku gốc)
   để ăn lợi thế giá. Mitigation đã sẵn sàng trong A/B test.

3. (phụ thuộc nhóm X — nếu họ chọn Premium 1 config: "vì cost spike risk ở Sccenario B của
   tụi mình lớn quá". Nếu họ chọn Budget: "vì rủi ro visa-info-wrong asymmetric — saving
   không đủ bù risk".)
```

---

## Bảng kiểm cuối cùng — trước 12:00 Pens Down

- [x] Đã trả lời 4 câu PM (Recommend / Savings / Threshold / Risk)
- [x] Final answer paragraph viết gọn (5–7 câu)
- [x] Phân công 5 nhịp present cho mỗi thành viên (Long ×2, Nhi ×1, Phương ×2)
- [x] Có sẵn câu trả lời cho 3 câu Q&A dự đoán
- [x] Comparison table có sẵn để chiếu / chuyền tay khi present
- [ ] Repo đã commit + push (sẽ nộp link sau buổi học)

---

## Sau buổi học

1. **Commit + push repo** với tất cả file đã điền.
2. **Dán link repo** vào Discord `#day27-evidence-boards` trước 23:59.
3. **Chuẩn bị cho D28**: peer review giữa các nhóm — sẽ bị hỏi câu chất vấn khó hơn instructor. Polish thêm bảng + recommendation tối nay.

*Hôm nay bạn chứng minh bằng số. Ngày mai bạn bảo vệ bằng logic.*
