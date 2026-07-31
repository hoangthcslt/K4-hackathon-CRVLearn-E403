# Log Phản hồi User Test (Validation Phase)

**Mô tả:** Tài liệu này ghi nhận lại toàn bộ phản hồi thực tế từ 5 người dùng (thành viên các nhóm khác) khi trải nghiệm thử bản CRVLearn V2 (có tích hợp Sliding Window và Query Rewriting).
Cột "Hành động khắc phục" là **đề xuất/kế hoạch** của nhóm sau khi phân tích feedback — đã đối chiếu lại với `codebase/index.html` ngày 31/07/2026 và xác nhận **chưa dòng nào được code thật** (xem cột Trạng thái). Giữ nguyên trung thực theo đúng tình trạng repo, không đánh dấu "đã xử lý" khi chưa có trong code.

### Bảng Tổng hợp Feedback & Action

| Người dùng (Tester) | Nguyên văn Phản hồi (Feedback) | Mức độ | Đề xuất khắc phục của Nhóm | Trạng thái |
|---|---|---|---|---|
| **Nguyễn Duy Bách** | *"Hệ thống đang confuse memory khi người dùng cố tình prompt thông tin conflict."* | Cao (Core Logic) | Bổ sung một chỉ thị Guardrail đặc biệt vào System Prompt (`SYS_V2`). Ép AI phải nhận diện được sự mâu thuẫn giữa lịch sử và câu hỏi hiện tại, từ chối hùa theo và yêu cầu user xác nhận lại thông tin đúng. | ⏳ Chưa xử lý — `SYS_V2` ([index.html:447-451](../codebase/index.html#L447-L451)) hiện chưa có chỉ dẫn nhận diện mâu thuẫn. Đưa vào backlog. |
| **Nguyễn Thị Thu Trang** | *"Không truy xuất được đúng trang tài liệu để test."* | Cao (Retrieval) | Viết lại và nâng cấp thuật toán MOCK `retrieve()`. Thay vì chỉ so khớp mảng từ khóa `c.key`, hệ thống giờ tách từ và đối soát trực tiếp trên toàn bộ nội dung văn bản `c.text`, tăng tỷ lệ bốc đúng tài liệu lên đáng kể. | ⏳ Chưa xử lý — `retrieve()` ([index.html:464-471](../codebase/index.html#L464-L471)) vẫn chỉ match trên mảng `c.key`, chưa đổi sang `c.text`. Đưa vào backlog. |
| **Trần Đức Bảo** | *"Bảng đánh giá hiển thị hardcode ở cột ĐẠT/KHÔNG ĐẠT."* | Trung bình (UI/UX) | Bổ sung Disclaimer (Ghi chú nổi bật màu vàng) ngay trong Tab Eval để minh oan: Hệ thống không hardcode mà đang sử dụng thuật toán chấm tự động **Probe Word Matching** đối chiếu với Golden Set. | ⏳ Chưa xử lý — tab Eval đã có 1 dòng chú thích nhỏ về cách chấm ([index.html:296](../codebase/index.html#L296)) nhưng chưa phải disclaimer nổi bật riêng, chưa dùng đúng cụm "Probe Word Matching". Đưa vào backlog. |
| **Hoàng Tuấn Hiệp** | *"Chưa rõ về độ tin cậy, benchmark đánh giá."* | Trung bình (Minh bạch) | Làm rõ phương pháp Benchmark trên UI: Đánh giá tự động dựa trên **Golden Set 20 Testcases** lấy từ log thật, với Quality Bar cam kết là ≥ 85%. | ⏳ Chưa xử lý — thông tin này hiện chỉ có trong `codebase/README.md`/`spec.md`, chưa hiển thị trên UI. Đưa vào backlog. |
| **Nguyễn Công Minh** | *"Không thấy lưu log chat cũ (tải lại trang bị mất sạch)."* | Thấp (Tiện ích) | Tích hợp `localStorage` của trình duyệt để lưu tự động mảng `S.history`. Đồng thời bổ sung nút **"🗑 Reset hội thoại"** để user chủ động xóa ngữ cảnh khi muốn chuyển chủ đề. | ✅ Đã xử lý (31/07/2026) — `saveChatSession()`/`loadChatSession()`/`clearChatSession()` + nút `#btnResetChat` trong `index.html`. Verify bằng Playwright thật: chạy preset → reload → lịch sử khôi phục đúng → Reset → reload lại vẫn trống, không lỗi console. |

---
**💡 Bài học rút ra từ vòng Validation:**
Người dùng luôn có xu hướng "test dị biệt" (edge-case) bằng cách đưa thông tin mâu thuẫn hoặc dùng ngôn ngữ khó hiểu (không có trong tài liệu). Việc xây dựng AI không chỉ nằm ở chức năng "Nhớ" (Memory), mà quan trọng hơn là phải có **Guardrail** (Hàng rào bảo vệ) để ứng phó khi AI "Nhớ nhưng bị người dùng gài bẫy". Việc áp dụng HAX G10 (Thu hẹp phạm vi khi nghi ngờ - Hỏi lại user) là phương án Cost-of-error thấp và an toàn nhất cho Giáo dục.
