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
> Temperature càng thấp (0.0) thì câu trả lời càng ổn định, dễ giống nhau. Temperature càng cao (1.0–1.5) thì câu trả lời càng đa dạng, sáng tạo hơn nhưng cũng dễ lệch và không thống nhất.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> khoảng 0.0-0.3 vì chatbot hỗ trợ khách hàng cần trả lời nhất quán, đúng thông tin, ít bịa. Temperature cao sẽ làm mỗi lần hỏi ra câu khác nhau và dễ lệch chính sách.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> GPT-4o đắt hơn GPT-4o-mini khoảng 16.7 lần. Nên dùng GPT-4o khi cần chất lượng cao, ví dụ như tư vấn pháp lý hay các tác vụ liên quan đến điều luật. Nên dùng mini cho việc đơn giản, lặp lại nhiều, ví dụ như chatbot FAQ hoặc tóm tắt ngắn để tiết kiệm chi phí.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Persona giáo viên tiểu học thì câu trả lời ngắn, dễ hiểu và có các ví dụ như sổ ghi chép của cả lớp.... Persona chuyên gia thì dài hơn, dùng các thuật ngữ (ledger, hash, consensus) và ví dụ kỹ thuật. Cùng một câu hỏi nhưng system prompt đổi vai trò nên model đổi giọng, độ khó và cách giải thích.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Với đoạn ~100 từ, ước lượng Part 1 là 100/0.75 ≈ 133 token, còn count_tokens (tiktoken) thường ra khoảng 180–200 token, chênh khoảng 40–50%. Tiếng Việt tốn nhiều token hơn tiếng Anh vì bộ mã hóa được tối ưu cho tiếng Anh, còn từ tiếng Việt có dấu (ă, ê, ộ...) thường bị tách thành nhiều token nhỏ.



---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất khi chat hoặc câu trả lời dài: token hiện dần nên người dùng không phải chờ cả câu xong mới đọc. Non-streaming phù hợp hơn khi cần kết quả đầy đủ trước khi dùng tiếp, ví dụ lấy JSON, dịch máy, hoặc gọi API hàng loạt ở backend — lúc đó hiện từng chữ không giúp gì mà còn phức tạp hơn.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff chờ lâu dần sau mỗi lần lỗi (0.1s → 0.2s → 0.4s...) nên server đang quá tải có thời gian hồi, không bị đánh lại liên tục. Nếu hàng nghìn client cùng retry với delay cố định giống nhau, họ sẽ gọi lại cùng một lúc (thundering herd) và làm API nghẽn nặng hơn.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> > Mình chọn persona trợ giảng khóa AI. System prompt: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt." "Trả lời ngắn gọn" để câu trả lời vừa màn hình CLI, ít token nên rẻ hơn và history 3 lượt không bị đầy quá nhanh. "Bằng tiếng Việt" vì người dùng lab nói tiếng Việt — nếu không chỉ định, model dễ trả lời tiếng Anh.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là history chỉ giữ 3 lượt cuối (history = history[-6:]), nên trợ lý quên hết ngữ cảnh cũ — ví dụ đã nói tên hay chủ đề ở lượt 1 thì lượt 5 không còn nhớ. Cách cải thiện: tóm tắt các lượt bị cắt thành một đoạn ngắn, rồi luôn gửi kèm system prompt, ví dụ messages = [system, {"role": "system", "content": "Tóm tắt: " + summary}] + history + [tin nhắn mới]. Khi cắt history thì gọi model (hoặc gpt-4o-mini) để cập nhật summary, như vậy vừa nhớ lâu hơn vừa không đẩy hết token vào mỗi request.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
