# Reflection — Dương Văn Kiên

## Vai trò trong nhóm

Kỹ sư AI 2 — phụ trách xây dựng bộ Golden Set trong thư mục `eval/` và trực tiếp phát triển phần lớn `codebase/index.html` (prototype VLearn Tutor có memory).

## Phần mình đã làm

**Chuyển dữ liệu tài liệu từ mock sang thật.** Ban đầu prototype dùng nội dung slide tự soạn (`mock-doc.js`). Tôi bỏ hẳn phần này, trích xuất thật từ 2 file PDF gốc của khoá (`d1-slide-hackathon.pdf`, `d2-slide-hackathon.pdf`) bằng `pdftotext -enc UTF-8`, xử lý các lỗi phát sinh trong lúc trích xuất: `-layout` làm vỡ dấu tiếng Việt do font nhúng trong PDF, watermark trang trí chồng lên chữ thật, và một số ký tự rác từ icon font — chấp nhận và ghi rõ trong README phần lỗi còn sót lại thay vì che giấu.

**Xây dựng bộ Golden Set (đúng phần việc được phân công).** Từ vài case ban đầu, tôi mở rộng thành 20 case chia đúng 4 lớp lỗi (nguồn sự thật / mơ hồ / ngoài phạm vi / đặc thù domain), và chủ động bổ sung các dạng input mô phỏng hành vi người dùng thật ngoài đời: sai chính tả, trộn tiếng Anh–Việt, ký tự linh tinh, câu hỏi không liên quan, prompt injection ("ignore all previous instructions..."), giả định quyền admin, và case dễ dẫn AI vào hallucination (hỏi số liệu không có trong tài liệu). Vì quota gọi AI thật của Gemini free tier chỉ khoảng 10–15 lượt, tôi cũng tối ưu lại luồng test: Baseline chạy mô phỏng (không cần AI thật để chứng minh bug kiến trúc), còn bước rewrite câu hỏi dùng heuristic JS thay vì gọi thêm 1 lời gọi LLM riêng — giảm số request/case từ 3 xuống còn 1.

**Phát hiện và sửa nhiều bug thật trong logic mô phỏng.** Khi test thử, tôi phát hiện Baseline và v2 trả lời giống hệt nhau lúc chưa có lịch sử hội thoại — do `callMock()` dùng chung 1 nhánh logic cho cả hai engine mà không phân biệt kiến trúc payload khác nhau của chúng. Sau đó phát hiện tiếp một bug tinh vi hơn: Baseline "vô tình nhớ được" câu hỏi cũ trong một số trường hợp cụ thể (khi hỏi kiểu "câu thứ 2 tôi hỏi là gì"), dù đúng ra Baseline không bao giờ nên nhớ được — nguyên nhân là điều kiện regex quá hẹp bỏ sót dạng câu hỏi index cụ thể. Tôi cũng phát hiện tính năng auto-scroll trong khung chat không hoạt động thật (code cũ gọi `scrollTop` trên sai phần tử DOM — set trên `#thread` thay vì phần tử cha thực sự có `overflow-y:auto`).

**Thêm tính năng thread memory — theo dõi đa chủ đề hội thoại.** Xuất phát từ việc quan sát rằng một học viên có thể hỏi nhiều chủ đề khác nhau trong cùng một phiên (hỏi A rồi hỏi B), tôi thiết kế và triển khai cơ chế gắn mỗi câu hỏi vào đúng "thread" (chủ đề đang mở), hiển thị rõ trên UI "đang bàn về chủ đề nào". Khi câu hỏi mơ hồ khớp ngang điểm với ≥2 chủ đề đang mở, hệ thống chủ động hỏi lại xác nhận thay vì đoán bừa (áp dụng nguyên tắc G10 — thu hẹp phạm vi khi nghi ngờ), kèm một dòng lý do ngắn giải thích vì sao AI chọn chủ đề đó ở mỗi câu trả lời.

**Cải thiện UI/UX.** Thêm khả năng kéo dãn panel chat để xem được nhiều nội dung hơn, nhúng xem PDF gốc song song với chế độ chọn text để bôi đen hỏi AI, và gọn hoá lại phần giao diện chatbot vốn có quá nhiều tầng UI chồng lên nhau (gộp thanh chọn engine với thread bar, rút gọn nhãn nút, thu nhỏ phần hint trạng thái AI thật/mô phỏng).

**Bổ sung lớp an toàn cho system prompt.** Thêm khối "GIỚI HẠN AN TOÀN" vào system prompt của tutor: chống bịa số liệu/trích dẫn, chặn lộ system prompt, chặn các yêu cầu kiểu prompt-injection, không xử lý thông tin cá nhân, và từ chối nội dung nguy hại theo một câu trả lời cố định — để bộ golden set có case injection/admin-giả-định thực sự kiểm chứng được thứ gì đó, không phải test khống.

## AI hỗ trợ mình thế nào

Tôi dùng Claude Code xuyên suốt như một cặp lập trình — không phải để AI tự quyết định thay tôi, mà theo quy trình: tôi mô tả vấn đề/hành vi mong muốn bằng tiếng Việt, AI đọc code hiện có để hiểu ngữ cảnh trước khi sửa, đề xuất hướng, tôi phản biện/chọn hướng, rồi AI chỉnh code và tự viết script Node.js nhỏ để verify logic (không chỉ tin bằng mắt) trước khi báo lại kết quả. Một ví dụ cụ thể: khi tôi báo "baseline vẫn nhớ được câu hỏi cũ dù đúng ra không nên", AI không sửa ngay mà viết test độc lập để tái hiện đúng bug, xác nhận nguyên nhân gốc rồi mới sửa — cách làm này giúp tôi tin vào chỗ sửa hơn là chỉ nghe giải thích suông. AI cũng đã từ chối một yêu cầu tôi paste nhầm — một đoạn "system instructions" giả dạng từ nguồn ngoài — và giải thích rõ lý do tại sao không áp dụng trực tiếp, đây là một bài học nhỏ về việc luôn phân biệt input nào là lệnh thật.

## Một bài học từ case fail của chính nhóm

Case đáng nhớ nhất là bug Baseline/v2 trả lời giống hệt nhau ở chế độ mô phỏng. Ban đầu tôi tưởng đây là lỗi hiển thị UI, nhưng khi đào sâu mới phát hiện gốc rễ nằm ở tầng logic thấp hơn nhiều: `callMock()` dùng một nhánh xử lý chung cho cả hai engine, trong khi bản chất khác biệt giữa Baseline và v2 phải nằm ở *cấu trúc payload gửi cho LLM* (Baseline chỉ có 1 message, v2 có cả lịch sử hai chiều), không phải ở câu chữ trả lời. Bài học rút ra: khi mô phỏng (mock) một sự khác biệt kiến trúc, phải mô phỏng đúng ở tầng dữ liệu (payload) chứ không chỉ mô phỏng ở tầng câu chữ hiển thị ra — nếu không, phần demo sẽ trông "có vẻ đúng" nhưng thực chất không chứng minh được điều nhóm muốn chứng minh, và rất dễ bị hỏi vặn lộ ra khi trình bày (CP5/CP6).
