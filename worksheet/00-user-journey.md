# 00 · User Journey Simulation — Đóng vai Tourist

> **Mục tiêu**: Trước khi tính chi phí, nhóm phải hình dung được khách hàng thật sự hỏi gì, hỏi như thế nào, và 1 conversation thực tế trông ra sao.
>
> **Thời gian**: 8 phút (trong 15 phút phần Setup)

---

## Tại sao phải làm bước này?

Nếu nhóm bắt đầu tính cost mà chưa biết tourist hỏi gì → mọi con số chỉ là lý thuyết. Bước này buộc nhóm "chạm" sản phẩm trước khi mở Excel.

---

## Bước 1 — Mỗi người đóng vai 1 tourist (4 phút)

Tưởng tượng mình là 1 khách du lịch nước ngoài đang plan trip Việt Nam. Bạn vừa mở website công ty du lịch, thấy có chatbot ở góc màn hình. Bạn sẽ hỏi gì?

Trước khi viết, tự hỏi:

- Mình từ đâu đến? Mỹ, Anh, Hàn, Nhật, Úc?
- Đi 1 mình hay đi nhóm? Budget khoảng bao nhiêu?
- Đã biết gì về Việt Nam? Lần đầu đến hay đã đến rồi?
- Mình lo lắng điều gì nhất? (visa, an toàn, ngôn ngữ, thời tiết, ẩm thực, lừa đảo...)

Viết **5–7 câu hỏi bằng tiếng Anh** mình sẽ thật sự gửi cho chatbot. Viết câu hỏi tự nhiên, đúng giọng tourist — không phải đặt câu hỏi "nghe có vẻ technical".

→ Mỗi người viết vào ô dưới (chưa có gì sẵn — đừng nhìn người bên cạnh):

### Tourist #1 (Tên thành viên: Hồ Thị Tố Nhi)

> **Persona**: Mỹ, gia đình 4 người (2 con nhỏ 6 & 9 tuổi), đi 2 tuần tháng 10, budget mid-range, lần đầu đến VN.

```text
1. Hi, I'm planning a 2-week trip to Vietnam in October with my family. Where should we go?
2. Do US citizens need a visa to enter Vietnam? How long does it take to get one?
3. Is it safe to travel with young children in Hanoi and Ho Chi Minh City?
4. What's the weather like in Da Nang around late October?
5. Can you recommend some family-friendly tours in Ha Long Bay?
6. Do we need to carry a lot of cash, or are credit cards widely accepted?
7. I want to book a hotel in Hoi An for 3 nights, can you help with that?
```

### Tourist #2 (Tên thành viên: Lê Thị Phương)

> **Persona**: Anh quốc, nữ solo 32 tuổi, food blogger, đi 10 ngày, budget mid-high, đã đến Đông Nam Á nhưng chưa đến VN.

```text
1. Hi, I'm a solo female traveler from the UK planning a 10-day food-focused trip. Where should I start — Hanoi or Hoi An?
2. Is it safe for a woman to travel alone in Vietnam? Any neighborhoods I should avoid at night in Hanoi or HCMC?
3. Can you recommend some authentic local cooking classes in Hoi An — not the touristy ones?
4. What's the best way to get from Hanoi to Sapa — overnight train or sleeper bus?
5. I have a peanut allergy. How common are peanuts in Vietnamese street food?
6. Are there cultural taboos I should know about when visiting Buddhist temples?
7. Can you book me a 1-day street food tour in Hanoi Old Quarter next Tuesday evening?
```

### Tourist #3 (Tên thành viên: Trần Tiến Long)

> **Persona**: Hàn Quốc, nam solo 25 tuổi, backpacker, đi 3 tuần đầu tháng 11, budget thấp ($25/ngày), lần đầu, hơi lo về scam.

```text
1. Hello, I'm a 25-year-old solo backpacker from Korea. What's the cheapest way to spend 3 weeks in Vietnam?
2. Do Korean citizens need a visa for Vietnam? I want to stay 21 days — is that okay visa-free?
3. What's the weather like in Hanoi and Sapa in early November? Do I need a winter jacket?
4. Are there any hostels in Hanoi Old Quarter under $10 per night that you actually recommend?
5. Can I use my Korean credit card at most places, or should I withdraw cash everywhere?
6. I want to do a 2-day trek in Sapa with a local H'mong guide — can you help me book one?
7. What are the most common scams in tourist areas I should watch out for?
```

---

## Bước 2 — Gom lại và phân loại (4 phút)

Cả nhóm chụm vào, gom tất cả câu hỏi lại. Trước khi điền bảng, thảo luận 1 phút:

- Có câu hỏi nào lặp lại giữa các tourist không?
- Có chủ đề nào không ai trong nhóm nghĩ tới ban đầu nhưng quan trọng?
- Câu nào chatbot có thể trả lời được? Câu nào cần chuyển sang nhân viên thật?

5 intent có sẵn (tham khảo `cost-reference-card.md` mục 2):

- **Visa/Policy** — chính sách, thủ tục nhập cảnh
- **Điểm đến/Guide** — gợi ý đi đâu, làm gì, ăn gì
- **Thời tiết/Sự kiện** — info real-time
- **Tour/Booking** — đặt vé, đặt tour, đặt phòng → chuyển sales
- **Khiếu nại** — phàn nàn → chuyển manager

Sau khi gom, điền bảng phân loại (chọn 10 câu đại diện từ 21 câu của nhóm):

| # | Câu hỏi (1 dòng) | Intent thuộc loại nào | Cần bao nhiêu lượt chat để xong? | Bot trả lời hay chuyển người? |
|---|---|---|---|---|
| 1 | Where should we go for 2-week family trip in October? | Guide | 4–5 lượt (cần clarify destination/age/budget) | ☑ Bot · □ Người |
| 2 | Do US citizens need visa? How long to get one? | Visa | 2–3 lượt | ☑ Bot · □ Người |
| 3 | Is it safe with young children in Hanoi/HCMC? | Guide (safety) | 2 lượt | ☑ Bot · □ Người |
| 4 | Weather in Da Nang around late October? | Weather | 1–2 lượt | ☑ Bot · □ Người |
| 5 | Book hotel in Hoi An for 3 nights | Booking | 1 lượt (handoff sales) | □ Bot · ☑ Người |
| 6 | UK solo female 10-day food trip — Hanoi or Hoi An first? | Guide | 4 lượt | ☑ Bot · □ Người |
| 7 | Safety for women, unsafe neighborhoods Hanoi/HCMC? | Guide (safety) | 2–3 lượt | ☑ Bot · □ Người |
| 8 | Korean visa-free duration — 21 days okay? | Visa | 2 lượt (cần clarify e-visa) | ☑ Bot · □ Người |
| 9 | Weather Hanoi/Sapa early November, need winter jacket? | Weather | 1–2 lượt | ☑ Bot · □ Người |
| 10 | Book 2-day Sapa trek with H'mong guide | Booking | 1 lượt (handoff sales) | □ Bot · ☑ Người |

---

## Bước 3 — Rút insight cho nhóm (cuối phần Setup)

Trả lời nhanh 4 câu — sẽ dùng lại ở các bước sau:

**Tổng số câu hỏi nhóm gom được**:

```text
21 câu (7 câu × 3 tourists)
```

**Phân bố intent thực tế của nhóm** (% mỗi intent — tính trên 21 câu gốc):

```text
Guide:      ~50% (10/21 — gợi ý điểm đến, an toàn, tour, lifestyle)
Visa:       ~14% ( 3/21 — US, UK, Korean visa rules khác nhau)
Weather:    ~10% ( 2/21 — tháng 10 Da Nang, tháng 11 Sapa)
Booking:    ~14% ( 3/21 — hotel Hoi An, food tour, Sapa trek)
Khiếu nại:    0% ( 0/21 — tourist đang plan trip, chưa có gì để complaint)
Khác (FAQ tiện ích — cash/card, scam, taboo): ~12% (3/21) → gom vào Guide
```

**Số lượt chat trung bình để xong 1 chủ đề**:

```text
- Guide (đa số): 3–5 lượt (clarify destination/budget/dates rồi mới đề xuất)
- Visa: 2–3 lượt (đa số 2 lượt — câu trả lời rõ ràng, ít clarify)
- Weather: 1–2 lượt (info đơn giản, real-time)
- Booking: 1 lượt (handoff ngay, $0 LLM)
```

**Đối chiếu với đề bài** (Scenario A = 4 lượt, Scenario B = 7 lượt):

```text
Hợp lý cho Scenario A — 4 lượt khớp với conversation Guide cơ bản.
Scenario B 7 lượt phản ánh mùa cao điểm: khách hỏi nhiều, đi sâu hơn (compare 2 destinations,
hỏi thêm itinerary, hỏi visa + weather + booking trong cùng 1 session).
Insight: trong thực tế, 1 conversation thường mix nhiều intent — bắt đầu Guide, chuyển sang
Visa/Weather, rồi cuối cùng là Booking. Đề bài giả định mỗi conversation 1 intent chính
để đơn giản hóa cost calc.
```

**Insight bất ngờ — điều gì nhóm chỉ hiểu sau khi đóng vai?**

```text
1. KHÔNG có Complaint trong 21 câu của nhóm — tourist ĐANG PLAN trip thì không complaint.
   Complaint chỉ xảy ra với khách đã đi rồi (post-trip support). Implication: ở mùa thấp điểm
   (planning phase), Complaint % thấp; ở mùa cao điểm (đang đi), Complaint tăng.
2. Câu hỏi Visa của Tourist #3 (Korean) PHỨC TẠP hơn tưởng: Korean visa-free chỉ 15 ngày,
   tourist hỏi 21 ngày → cần clarify e-visa option → 4–5 lượt chứ không phải 2.
   → Cùng intent "Visa" nhưng độ phức tạp khác nhau theo quốc tịch.
3. Booking câu hỏi RẤT NGẮN ($0 LLM) vì tourist chỉ muốn được chuyển sang sales agent
   nhanh nhất có thể. Ngược lại Guide là intent "tốn turns" nhất (4–5 lượt).
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [x] Mỗi người trong nhóm đã viết ≥5 câu hỏi tourist
- [x] Đã gom + phân loại intent cho ≥10 câu (bảng trên)
- [x] Đã có phân bố intent % của nhóm (so với đề bài)
- [x] Có ít nhất 1 insight về cách tourist thật sự dùng chatbot

Xong → mở `01-base-flow.md`.
