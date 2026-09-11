# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> càng tăng temperature, câu trả lời càng phong phú về mặt từ vựng, ngữ nghĩa. temperature 0.0 như một bài báo, phóng sự còn khi lên 1.5 thì kết quả trả về sử dụng nhiều cách nói cuốn hút hơn, có những đoạn nhấn nhá, so sánh thú vị.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> em sẽ đặt temperature 0.5 cho chatbot hỗ trợ khách hàng. Vì khi hỗ trợ khách hàng thì cần thông tin cô đọng, chính xác hơn là văn chương phong phú. tuy nhiên vẫn để cao hơn 0.0 một chút ít để giảm sự nhàm chán.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> ước tính 4o đắt hơn 6omini khoảng 17 lần (4o tốn khoảng 105$/ngày còn mini tốn 6$/ngày). khi cần thực hiện tác vụ lập luận phức tạp như phân tích báo cáo hay coding thì nên dùng 4o. khi chỉ cần trả lời các câu hỏi đơn giản, thường gặp hay tóm tắt tin tức ngắn thì có thể dùng 4o mini để tiết kiệm chi phí.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> phản hồi mà có system prompt là giáo viên tiểu học có độ dài ngắn hơn 1 nửa, sử dụng bộ từ vựng dễ hiểu gần gũi với trẻ em như ví dụ về câu chuyện "cuốn sổ ma thuật". còn với system chuyên gia thì giải thích chi tiết, cặn kẽ hơn, sử dụng các từ ngữ chuyên sâu, từ khóa chính xác như proof of work, consensus mechanisms.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Theo ước lượng ở part 1 của em với đoạn văn độ dài 100 từ thì tốn 135 token. còn dùng tiktoken với gpt-4o thì tính ra 158 token. Tiếng việt thường tốn token hơn tiếng anh nếu cùng độ dài là do tiếng việt còn các dấu câu cũng tính là token.
---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi tương tác trực tiếp với người dùng (như chatbot, sinh văn bản dài) giúp giảm độ trễ cảm nhận vì người dùng đọc được kết quả ngay lập tức. Ngược lại, non-streaming phù hợp hơn cho các tác vụ chạy ngầm (batch job), gọi API giữa các hệ thống hoặc khi cần phản hồi trọn vẹn để parse JSON và kiểm duyệt dữ liệu.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff tăng dần thời gian chờ sau mỗi lần thử, giúp giảm áp lực và cho server thời gian hồi phục khi quá tải. Nếu hàng nghìn client cùng retry với delay cố định, các request sẽ dồn vào cùng một thời điểm tạo thành hiệu ứng "bão retry" (thundering herd/retry storm), khiến server tiếp tục quá tải và không thể phục hồi.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona: "Bạn là trợ giảng thân thiện của khóa học AI, trả lời ngắn gọn bằng tiếng Việt." Lựa chọn từ ngữ: "trả lời ngắn gọn" giúp tiết kiệm token, giảm chi phí và độ trễ trên CLI; "bằng tiếng Việt" giúp cố định ngôn ngữ phản hồi nhất quán, phù hợp với học viên.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất: Chỉ giữ 3 lượt hội thoại gần nhất nên mất sạch ngữ cảnh cũ khi trò chuyện dài. Cải thiện: Tóm tắt ngữ cảnh (context summarization) — khi history vượt quá ngưỡng, gọi một model nhỏ (như gpt-4o-mini) tóm tắt các lượt chat cũ thành 1 đoạn ngắn đưa vào system prompt thay vì cắt bỏ hoàn toàn.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
