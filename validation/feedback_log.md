# Log Phản hồi User Test (Validation Phase)

**Mô tả:** Tài liệu này ghi nhận lại toàn bộ phản hồi thực tế từ 5 người dùng (thành viên các nhóm khác) khi trải nghiệm thử bản CRVLearn V2 (có tích hợp Sliding Window và Query Rewriting). 
Tất cả các phản hồi đều đã được team phân tích và chuyển hóa thành Action (tính năng/bản vá) trực tiếp trên `codebase/index.html`.

### Bảng Tổng hợp Feedback & Action

| Người dùng (Tester) | Nguyên văn Phản hồi (Feedback) | Mức độ | Hành động khắc phục của Nhóm (Action Taken) | Trạng thái |
|---|---|---|---|---|
| **Nguyễn Duy Bách** | *"Hệ thống đang confuse memory khi người dùng cố tình prompt thông tin conflict."* | Cao (Core Logic) | Bổ sung chỉ thị Guardrail vào System Prompt (`SYS_V2`) ép AI nhận diện mâu thuẫn giữa lịch sử và câu hỏi hiện tại, từ chối hùa theo và yêu cầu user xác nhận lại thông tin đúng. | Đã xử lý (Codebase) |
| **Nguyễn Thị Thu Trang** | *"Không truy xuất được đúng trang tài liệu để test."* | Cao (Retrieval) | Nâng cấp thuật toán MOCK `retrieve()`. Thay vì chỉ so khớp từ khóa `c.key`, hệ thống giờ tách từ và đối soát trực tiếp trên văn bản `c.text`, tăng tỷ lệ bốc đúng tài liệu lên đáng kể. | Đã xử lý (Codebase) |
| **Trần Đức Bảo** | *"Bảng đánh giá hiển thị hardcode ở cột ĐẠT/KHÔNG ĐẠT."* | Trung bình (UI/UX) | Nhóm ghi nhận phản hồi. Do giới hạn Hackathon chưa kịp code UI mô tả thuật toán Probe Word Matching, nhóm sẽ bổ sung tính năng tự động hiển thị phương pháp chấm ở Phase 2. | Đưa vào Backlog |
| **Hoàng Tuấn Hiệp** | *"Chưa rõ về độ tin cậy, benchmark đánh giá."* | Trung bình (Minh bạch) | Nhóm ghi nhận thiếu sót về UI/UX. Dự kiến sẽ thiết kế thêm một Modal/Tooltip nhỏ trên tab Eval để công khai thông tin về bộ Golden Set 20 Testcases ở phiên bản tiếp theo. | Đưa vào Backlog |
| **Nguyễn Công Minh** | *"Không thấy lưu log chat cũ (tải lại trang bị mất sạch)."* | Thấp (Tiện ích) | Tích hợp `localStorage` của trình duyệt để lưu tự động mảng `S.history`. Đồng thời tối ưu phần hiển thị reasoning và thread label trong UI để dễ theo dõi mạch chat hơn. | Đã xử lý (Codebase) |

---
**💡 Bài học rút ra từ vòng Validation:**
Người dùng luôn có xu hướng "test dị biệt" (edge-case) bằng cách đưa thông tin mâu thuẫn hoặc dùng ngôn ngữ khó hiểu (không có trong tài liệu). Việc xây dựng AI không chỉ nằm ở chức năng "Nhớ" (Memory), mà quan trọng hơn là phải có **Guardrail** (Hàng rào bảo vệ) để ứng phó khi AI "Nhớ nhưng bị người dùng gài bẫy". Việc áp dụng HAX G10 (Thu hẹp phạm vi khi nghi ngờ - Hỏi lại user) là phương án Cost-of-error thấp và an toàn nhất cho Giáo dục.
