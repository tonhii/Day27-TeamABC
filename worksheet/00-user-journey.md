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

```text


```

### Tourist #3 (Tên thành viên: Trần Tiến Long)

```text

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

Sau khi gom, điền bảng phân loại:

| # | Câu hỏi (1 dòng) | Intent thuộc loại nào | Cần bao nhiêu lượt chat để xong? | Bot trả lời hay chuyển người? |
|---|---|---|---|---|
| 1 | | | | □ Bot · □ Người |
| 2 | | | | □ Bot · □ Người |
| 3 | | | | □ Bot · □ Người |
| 4 | | | | □ Bot · □ Người |
| 5 | | | | □ Bot · □ Người |
| 6 | | | | □ Bot · □ Người |
| 7 | | | | □ Bot · □ Người |
| 8 | | | | □ Bot · □ Người |
| 9 | | | | □ Bot · □ Người |
| 10 | | | | □ Bot · □ Người |

---

## Bước 3 — Rút insight cho nhóm (cuối phần Setup)

Trả lời nhanh 4 câu — sẽ dùng lại ở các bước sau:

**Tổng số câu hỏi nhóm gom được**:

```text
(điền số vào đây)
```

**Phân bố intent thực tế của nhóm** (% mỗi intent):

```text
Guide: ___%
Visa: ___%
Weather: ___%
Booking: ___%
Khiếu nại: ___%
```

**Số lượt chat trung bình để xong 1 chủ đề**:

```text
(điền số vào đây — ví dụ: "4 lượt cho info, 1 lượt cho booking")
```

**Đối chiếu với đề bài** (Scenario A = 4 lượt, Scenario B = 7 lượt):

```text
Hợp lý vì... / Khác vì... (điền vào đây)
```

**Insight bất ngờ — điều gì nhóm chỉ hiểu sau khi đóng vai?**

```text
(điền 1–2 câu vào đây — ví dụ: "tourist thường hỏi nhiều intent trong 1 conversation",
"câu hỏi visa phức tạp hơn tưởng — cần 4–5 lượt", "câu hỏi booking thường rất ngắn")
```

---

## Bảng kiểm trước khi sang file tiếp theo

- [ ] Mỗi người trong nhóm đã viết ≥5 câu hỏi tourist
- [ ] Đã gom + phân loại intent cho ≥10 câu (bảng trên)
- [ ] Đã có phân bố intent % của nhóm (so với đề bài)
- [ ] Có ít nhất 1 insight về cách tourist thật sự dùng chatbot

Xong → mở `01-base-flow.md`.
