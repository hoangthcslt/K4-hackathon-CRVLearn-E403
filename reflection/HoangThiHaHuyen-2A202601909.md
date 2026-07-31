# Reflection cá nhân — Hoàng Thị Hà Huyền (2A202601909)

## 1. Vai trò và phần tôi thực hiện

Trong dự án CRVLearn, tôi đảm nhiệm vai trò **Kỹ sư AI 1**, tập trung vào **System Prompt và Prompt Engineering**. Mục tiêu của phần việc này là giúp AI Tutor vừa giữ được mạch hội thoại khi học viên hỏi nối tiếp, vừa chỉ trả lời dựa trên tài liệu của khóa học và không bịa thông tin.

Các phần tôi trực tiếp tham gia gồm:

- Cùng nhóm xây dựng và điều chỉnh chỉ dẫn cho AI Tutor: trả lời bằng tiếng Việt, ngắn gọn, phù hợp với học viên đi làm; trích dẫn theo định dạng `[trang N]`; nói rõ khi tài liệu không đủ căn cứ; không tiết lộ system prompt hoặc làm theo prompt injection.
- Cải thiện cách hệ thống hiểu câu hỏi có tham chiếu ngầm như “nó”, “ý trên”, “trang này”, “slide đó”. Câu hỏi được viết lại thành một câu độc lập trước khi truy xuất tài liệu, còn lịch sử hai chiều của 6–8 lượt gần nhất được đưa vào context.
- Sửa luồng truy xuất khi học viên hỏi đích danh một trang/slide. Thay vì phụ thuộc vào trang đang mở hoặc chỉ so khớp từ khóa, hệ thống nhận diện số trang từ câu hỏi, giữ lại số trang đó sau bước Query Rewriting và đóng gói từng trang bằng delimiter rõ ràng để model không nhầm nguồn.
- Phối hợp với Dương Văn Kiên xây dựng, chạy và đọc kết quả Golden Set. Ở lượt chạy tôi ghi nhận với `gemini-3.6-flash`, Baseline đạt 8/9 case (89%), còn CRVLearn v2 đạt 9/9 case (100%). Kết quả gộp cuối cùng trên nhiều model là Baseline 36/40 (90%) và v2 39/40 (98%).
- Ngoài vai trò chính, tôi bổ sung lớp bảo vệ quyền riêng tư chạy trên trình duyệt: cảnh báo trước khi gửi mật khẩu, API key, số điện thoại, email hoặc dữ liệu định danh; cho phép người dùng che thông tin, sửa lại hoặc chủ động xác nhận vẫn gửi. Tôi cũng bổ sung tài liệu và test cho các rule này.

Tôi có thể giải thích luồng chính của phần mình làm như sau:

`Câu hỏi mới → phát hiện tham chiếu ngầm → viết lại thành câu độc lập → giữ tham chiếu trang/slide → truy xuất đúng tài liệu → ghép lịch sử hội thoại hai chiều → gọi LLM → chấm theo Golden Set`.

## 2. AI đã hỗ trợ tôi như thế nào

Tôi dùng AI như một **đồng đội phản biện và tạo bản nháp**, không xem câu trả lời của AI là kết luận cuối cùng. AI hỗ trợ tôi:

- Đề xuất các phiên bản system prompt ngắn gọn hơn và chỉ ra những chỗ chỉ dẫn có thể mâu thuẫn hoặc bị hiểu theo nhiều nghĩa.
- Mở rộng danh sách edge case cho câu hỏi nối tiếp, chẳng hạn đại từ mơ hồ, cách gọi “trang/slide”, câu hỏi thiếu chủ thể, prompt injection và thông tin nhạy cảm.
- Gợi ý cấu trúc test và hỗ trợ rà logic nhận diện số trang, Query Rewriting và các rule quyền riêng tư.
- Tóm tắt trace sau mỗi lượt chạy để tôi so sánh Baseline với v2 và tìm đúng bước gây lỗi.

Tuy nhiên, tôi không đưa nguyên output do AI sinh vào sản phẩm mà chưa kiểm tra. Tôi đối chiếu lại với tài liệu thật, xem payload/context trong prototype, chạy lại Golden Set và giữ cả kết quả không đạt. Cách làm này giúp tôi nhận ra rằng một prompt nghe hợp lý chưa chắc hoạt động ổn định trên nhiều model.

## 3. Case fail của nhóm và bài học của tôi

Case fail đáng nhớ nhất nằm trong kết quả đánh giá gộp với `gemini-3.5-flash`:

- Câu đầu: **“PAIR có mấy bước để quyết định dùng AI?”**
- Câu nối tiếp: **“vậy bước đầu tiên trả lời câu hỏi gì?”**
- Baseline được chấm đạt, nhưng CRVLearn v2 không nhắc lại đúng chủ thể **PAIR** nên bị chấm **KHÔNG ĐẠT**.

Vì case này, v2 chỉ đạt 39/40 (98%), không đạt trạng thái hoàn hảo và vẫn vi phạm phần kỳ vọng nghiêm ngặt “không được đánh mất ngữ cảnh dù chỉ một lần”. Khi xem lại logic, tôi nhận thấy bộ phát hiện tham chiếu chủ yếu dựa trên một danh sách cụm từ cố định. Cách diễn đạt “vậy bước đầu tiên...” không khớp rõ với các cụm như “nó”, “ý trên” hoặc “vậy còn”, nên bước Query Rewriting có thể không được kích hoạt. Dù lịch sử vẫn có trong payload, một số model vẫn không tự nối được chủ thể đúng. Ngoài ra, phép chấm bằng probe word cũng chỉ là một chỉ báo và cần được kiểm tra thêm bằng đọc thủ công.

**Bài học lớn nhất của tôi:** Prompt Engineering không phải là viết một prompt “hay” rồi dừng lại. Prompt phải được xem như một thành phần có thể kiểm thử: cần có case hồi quy cho nhiều cách diễn đạt, chạy trên nhiều model, giữ lại failure và kiểm tra cả trace trung gian. Nếu làm tiếp, tôi sẽ bổ sung các mẫu tham chiếu theo thứ tự như “bước đầu tiên”, “ý thứ hai”, “phần cuối”, thêm chính case PAIR vào bộ regression bắt buộc, đồng thời kết hợp chấm theo rule với kiểm tra ngữ nghĩa hoặc human review cho các case mơ hồ.

## 4. Điều tôi rút ra sau dự án

Qua CRVLearn, tôi hiểu rõ hơn rằng chất lượng của một hệ thống AI không chỉ nằm ở model. Nó phụ thuộc đồng thời vào prompt, dữ liệu được đưa vào context, cách truy xuất tài liệu, cơ chế bộ nhớ, guardrail và phương pháp đánh giá. Thay đổi có giá trị nhất của tôi không phải là thêm nhiều câu chữ vào prompt, mà là làm cho chỉ dẫn gắn với dữ liệu đúng trang, có test kiểm chứng và có cơ chế an toàn cho người dùng.

Nếu có thêm thời gian, tôi sẽ ưu tiên ba việc: xây regression suite riêng cho mọi lỗi từng xuất hiện; thay bộ phát hiện tham chiếu thuần từ khóa bằng bộ phân loại/viết lại có confidence và hỏi lại khi không chắc; đo thêm độ chính xác truy xuất cùng chi phí token, độ trễ thay vì chỉ nhìn tỷ lệ trả lời đúng.

## Minh chứng trong repo

- Phân công vai trò: [`README.md`](../README.md) và [`spec.md`](../spec.md)
- Golden Set: [`eval/golden_set.json`](../eval/golden_set.json)
- Lượt chạy do tôi ghi nhận: [`codebase/eval/eval-run-1785467622322.md`](../codebase/eval/eval-run-1785467622322.md)
- Kết quả đánh giá gộp: [`codebase/eval/eval-run-consolidated-2026-07-31.md`](../codebase/eval/eval-run-consolidated-2026-07-31.md)
- Prompt, Query Rewriting và truy xuất theo trang: [`codebase/index.html`](../codebase/index.html)
- Quy tắc quyền riêng tư: [`codebase/PRIVACY.md`](../codebase/PRIVACY.md)
- Phản hồi từ vòng user test: [`validation/feedback_log.md`](../validation/feedback_log.md)
