# 01 · Base Flow + Chốt 3 Knobs

> **Mục tiêu**: Hiểu chatbot hoạt động ra sao ở mức base (không chọn config gì) — và xác định 3 knobs nhóm sẽ tweak ở các bước sau.
>
> **Thời gian**: 7 phút (trong 15 phút phần Setup)

---

## Bước 1 — Đọc base flow trong cost reference card

Mở file `cost-reference-card.md` ở phần **2. Base Flow** — xem flow chatbot mặc định. Đây là cấu trúc mọi config sẽ build dựa trên.

Đọc xong, tự kiểm tra hiểu:

- Khi tourist gửi tin nhắn, AI làm gì đầu tiên?
- 5 intent dẫn đến 5 hành động khác nhau — hành động nào tốn LLM, hành động nào không?
- Sau khi route, AI ráp gì lại để generate response?

Nếu chưa hiểu → quay lại đọc lại 1 lần nữa. Đừng đi tiếp khi còn mơ hồ.

---

## Bước 2 — Vẽ lại flow theo cách hiểu của nhóm

Vẽ flow ra giấy hoặc trên bảng (1 thành viên vẽ, cả nhóm góp ý). Có thể dùng ASCII đơn giản:

```text
   [Tourist message]
          │
          ▼
   ┌──────────────────┐
   │ Intent Classify  │  ← Keyword regex ($0) cho cả 3 configs
   │ (Visa / Guide /  │     (LLM classifier là option nâng cao,
   │  Weather /       │      nhóm chốt KHÔNG dùng để giữ cost thấp)
   │  Booking /       │
   │  Complaint)      │
   └────────┬─────────┘
            │
   ┌────────┼────────────┬──────────────┬─────────────┐
   ▼        ▼            ▼              ▼             ▼
 Visa     Guide       Weather        Booking      Complaint
   │        │            │              │             │
   ▼        ▼            ▼              ▼             ▼
 ┌──────────────────────────┐    ┌──────────┐    ┌──────────┐
 │  RAG retrieval (1,250t)  │    │  HANDOFF │    │ ESCALATE │
 │  + Web search (800t)     │    │  → Sales │    │ → Manager│
 │    (chỉ khi config ON)   │    │   $0 LLM │    │  $0 LLM  │
 └──────────┬───────────────┘    └──────────┘    └──────────┘
            │
            ▼
   ┌────────────────────────────────────────────────┐
   │  Context Assembly:                              │
   │  sys (500) + history (last N × 260) + RAG (1,250)│
   │  + web results (800 nếu bật) + user msg (80)    │
   └────────────────┬───────────────────────────────┘
                    │
                    ▼
            ┌──────────────┐
            │ LLM Response │  ← knob 1 chọn model
            │  (output 180)│
            └──────────────┘
                    │
                    ▼
              [Reply to tourist]
```

Khi vẽ, đảm bảo flow có 4 điểm:

1. **Intent classification** — phân loại ý định ✅
2. **Route theo intent** — 5 nhánh đi đâu (RAG / Web search / Handoff / Escalate) ✅
3. **Context assembly** — ráp system prompt + history + RAG + web (nếu bật) + user msg ✅
4. **Response generation** — model tạo câu trả lời ✅

Nếu nhóm vẽ thiếu 1 trong 4 bước → bổ sung trước khi đi tiếp.

---

## Bước 3 — Xác định 3 Knobs

3 knobs là 3 quyết định thiết kế nhóm có thể tweak. Mỗi config nhóm thiết kế = 1 bộ chọn tại 3 knobs này.

Trước khi điền vào ô bên dưới, đọc nhanh mục **3. Decision Points** của `cost-reference-card.md`. Sau đó tự hỏi:

- Knob 1 — Model tier: model rẻ và model mạnh chênh bao nhiêu lần? Có thể mix theo intent không?
- Knob 2 — Web search: intent nào *cần* real-time? intent nào không cần?
- Knob 3 — History: 7 lượt chat cuối đắt hay rẻ? Cắt history có rủi ro gì?

### Knob 1 — Model tier

**Câu hỏi:** Chất lượng câu trả lời ở mức nào?

Options:

```text
☑ Cheap        (Gemini Flash-Lite / DeepSeek V4 Flash / GPT-4o-mini)
☑ Mid          (Gemini Flash / Claude Haiku 4.5)
☑ Strong       (DeepSeek V4 Pro / Claude Sonnet 4.6)
□ Premium      (Claude Opus 4.7 / GPT-5.5) — quá đắt cho use case này
□ Mix          (đã cân nhắc nhưng chốt không dùng để giữ comparison đơn giản)
```

**Câu hỏi gợi mở cho nhóm** (trả lời trước khi chọn):

- Mục tiêu chính là chi phí thấp hay chất lượng cao?
- Tourist hỏi câu phức tạp hay đơn giản hơn?
- Có nên dùng cheap cho phân loại + strong cho trả lời không?

```text
Travel chatbot tiếng Anh phục vụ tourist quốc tế → câu hỏi phần lớn là FAQ (90% Guide/Visa/Weather),
không yêu cầu reasoning sâu. Cheap model đủ cho Guide; nhưng Visa/Weather cần info chính xác →
cần model hiểu instruction tốt hơn và kết hợp web search.

Nhóm chốt 3 tier: Cheap (GPT-4o-mini), Mid (Haiku 4.5), Strong (Sonnet 4.6) — không dùng Premium
(Opus 4.7) vì over-engineering cho travel FAQ + cost gấp ~5× Sonnet không justify được benefit.

Classifier: keyword regex ($0) cho cả 3 configs — 5 intent đủ phân biệt được bằng keyword
("visa", "weather", "book", "complaint" + fallback Guide).
```

### Knob 2 — Web search

**Câu hỏi:** Có cần thông tin real-time không?

Options:

```text
☑ OFF              (chỉ dùng RAG — knowledge base có sẵn)         → Config Budget
☑ ON selective    (bật cho 1–2 intent cần real-time: visa, weather) → Config Balanced + Premium
□ ON broad         (bật cho hầu hết intent — không cần, gây waste)
```

**Câu hỏi gợi mở:**

- Visa policy đổi mỗi tháng — RAG có đủ không?
- Weather là thông tin real-time tự nhiên — không có lựa chọn khác đúng không?
- Web search tốn $0.005/call + 800 tokens — bật bừa có lợi không?

```text
Visa policy VN thay đổi nhiều lần trong 2024–2026 (e-visa mở rộng → 80+ quốc gia, miễn visa
mở rộng → 14 nước) → RAG sẽ outdated nhanh → web search ON cho Visa là quan trọng.

Weather là real-time intrinsically → không có lựa chọn khác, phải web search.

Guide (destination/itinerary/food) thay đổi chậm → RAG đủ. Bật web cho Guide tốn $$ không cần.

→ Chốt: 2/3 configs (Balanced + Premium) bật web cho Visa + Weather; Budget tắt hết để tối
ưu cost (chấp nhận trade-off info có thể outdated ở Visa).
```

### Knob 3 — History management

**Câu hỏi:** Chatbot cần nhớ bao nhiêu context của conversation?

Options:

```text
☑ Last 3 turns        (nhẹ nhất, dễ quên)               → Config Budget
☑ Last 5 turns        (cân bằng)                         → Config Balanced
☑ Full history        (nhớ tất cả, đắt nhất ở conv dài)  → Config Premium
□ Summarize every 5   (nâng cao — cần 1 LLM call phụ để tóm tắt — phức tạp, skip)
```

**Câu hỏi gợi mở:**

- Tourist hay nói "tôi đã nói budget là $500 ở turn 1" rồi turn 7 hỏi gợi ý — nếu quên thì sao?
- Scenario A trung bình 4 lượt → full history có tốn nhiều không?
- Scenario B trung bình 7 lượt → mỗi turn thêm 260 tokens — tổng thêm bao nhiêu?

```text
Scenario A 4 lượt: full history = 3×260 = 780 tokens extra ở turn cuối → khá nhẹ.
Scenario B 7 lượt: full history = 6×260 = 1,560 tokens extra ở turn cuối → đáng kể.

Last 3 đủ cho FAQ ngắn (Budget Bot phục vụ FAQ đơn giản, ít cần context). Tourist hỏi visa
turn 1 rồi turn 4 hỏi food → 2 chủ đề độc lập, không cần nhớ.

Last 5 phù hợp tourism: tourist hay nói budget/dates/preferences ở turn 1–2 rồi mới refine.
5 turns cover được context quan trọng mà không quá đắt.

Full chỉ justify khi conversation phức tạp (premium concierge) — khách VIP hỏi 7–10 lượt
về 1 trip, cần consistency tuyệt đối.
```

---

## Bước 4 — Sơ bộ nhóm muốn thử những combo nào?

Chưa cần quyết định cuối cùng. Chỉ cần phác thảo: nhóm dự định thử ít nhất 3 combo khác nhau. Càng khác nhau, càng dễ thấy tradeoff.

**Combo 1 (định hướng cheap)**:

```text
Model: GPT-4o-mini ($0.15/$0.60)    Web: OFF    History: Last 3    (đặt tên dự kiến: Budget Bot)
```

**Combo 2 (định hướng premium)**:

```text
Model: Claude Sonnet 4.6 ($3/$15)    Web: ON selective (Visa+Weather)    History: Full    (đặt tên dự kiến: Premium Concierge)
```

**Combo 3 (định hướng balanced / smart mix)**:

```text
Model: Claude Haiku 4.5 ($1/$5)    Web: ON selective (Visa+Weather)    History: Last 5    (đặt tên dự kiến: Balanced Concierge)
```

**Combo 4** (optional — nếu nhóm có ý tưởng khác):

```text
(bỏ qua — 3 combo trên đã đủ thấy tradeoff trên cả 3 knobs)
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Đã vẽ flow base có đủ 4 bước (Intent → Route → Context → Response)
- [x] Hiểu Booking + Khiếu nại = $0 LLM cost (chuyển con người)
- [x] Đã phác thảo ≥3 combo khác nhau (chưa cần chi tiết)
- [x] Nhóm đồng thuận về hướng đi mỗi combo

Xong → 10:25 chuyển sang **Main phase**. Mở `02-config-design.md`.
