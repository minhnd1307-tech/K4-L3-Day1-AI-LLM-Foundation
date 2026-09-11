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
Temperature thấp (0.0 - 0.5) cho câu trả lời ổn định, chính xác và nhất quán. Khi tăng lên 1.0 - 1.5, câu trả lời đa dạng hơn nhưng dễ lan man và có nguy cơ ảo giác thông tin (hallucination).

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
Đặt khoảng 0.1 - 0.3 (khuyến nghị 0.2). Vì chatbot CSKH cần độ chính xác, nhất quán cao và hạn chế tối đa việc bịa thông tin sai lệch về chính sách hay sản phẩm.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
GPT-4o đắt hơn khoảng 16.7 lần ($0.010 vs $0.0006 / 1K token output).
- Dùng GPT-4o: Suy luận phức tạp, phân tích hợp đồng pháp lý hoặc báo cáo tài chính/y tế.
- Dùng mini: Phân loại ý định, tóm tắt ngắn, trả lời các câu hỏi thường gặp (FAQ).

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
Phản hồi "giáo viên tiểu học" dùng từ ngữ bình dị, ví dụ trực quan như "cuốn sổ tay chung của cả lớp mà không ai tẩy xóa được". Ngược lại, "chuyên gia tài chính" sử dụng thuật ngữ chuyên ngành sâu như sổ cái phân tán (distributed ledger), mật mã học, cơ chế đồng thuận (consensus) và tính bất biến. System prompt đóng vai trò định hình ngữ cảnh, giọng điệu, đối tượng tiếp nhận và chiều sâu kiến thức cho toàn bộ phản hồi của model.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
Hai con số chênh nhau khoảng 10% – 20% tùy cấu trúc câu. Tiếng Việt tốn nhiều token hơn tiếng Anh vì các bộ tokenizer hiện nay (như BPE của OpenAI) được tối ưu chủ yếu trên kho ngữ liệu tiếng Anh; các từ tiếng Việt có dấu thường bị tách thành nhiều subword hoặc byte riêng lẻ thay vì là một token trọn vẹn.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
Streaming quan trọng cho chatbot tương tác trực tiếp để người dùng thấy chữ hiện ra ngay, không phải chờ lâu. Non-streaming phù hợp cho chạy batch ngầm, xuất JSON có cấu trúc hoặc khi cần kiểm duyệt xong toàn bộ mới hiển thị.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
Backoff cấp số nhân tăng dần thời gian chờ để server kịp hạ tải. Nếu dùng delay cố định, hàng nghìn máy cùng gửi lại yêu cầu vào một thời điểm sẽ tiếp tục làm sập server

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
Persona: Trợ giảng AI thân thiện. System prompt: `"Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt."`
- "ngắn gọn": Giúp tiết kiệm token đầu ra, phản hồi nhanh và tránh lan man trên terminal.
- "tiếng Việt": Đảm bảo mô hình luôn phản hồi bằng tiếng Việt nhất quán, kể cả khi câu hỏi có chứa thuật ngữ tiếng Anh.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
Hạn chế: History chỉ lưu 3 lượt gần nhất (`history[-6:]`) nên sẽ quên ngữ cảnh ban đầu khi hội thoại kéo dài.
Cải thiện: Dùng cơ chế tóm tắt ngữ cảnh (Conversation Summary). Khi vượt quá 3 lượt, gọi model nhẹ (GPT-4o-mini) tóm tắt các lượt cũ thành 1-2 câu ngắn rồi ghép vào system prompt để duy trì bối cảnh mà không tốn nhiều token.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
