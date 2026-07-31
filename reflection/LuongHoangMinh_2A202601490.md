# BÁO CÁO CÁ NHÂN (REFLECTION) — DỰ ÁN CRVLEARN
**Họ và tên:** Lương Hoàng Minh  
**Mã số học viên:** 2A202601490  
**Vai trò:** Spec & Demo (Tài liệu đặc tả, Khảo sát User, Chuẩn bị Thuyết trình)

---

## 1. Vai trò và Trách nhiệm chính
Trong dự án Hackathon **CRVLearn** nhằm giải quyết lỗi mất mạch ngữ cảnh của AI Tutor trên nền tảng VLearn, tôi đảm nhận vai trò **Spec & Demo**. Nhiệm vụ chính của tôi bao gồm:
* Xây dựng và chuẩn hóa tài liệu đặc tả (`spec.md`) dựa trên các nguyên tắc thiết kế AI (HAX) và khung JTBD.
* Phân tích Data Log (585 hội thoại) và trực tiếp khảo sát người dùng để định hình Pain Point và thực hiện vòng Validation.
* Xây dựng kịch bản Pitch Deck (Thuyết trình & Demo Live) nhằm làm nổi bật ưu điểm hệ thống trước Ban giám khảo.
* Phối hợp quản lý mã nguồn trên Git, xử lý conflict khi tích hợp các bản vá từ thành viên trong nhóm, đồng thời trực tiếp tinh chỉnh Logic Guardrail trong System Prompt.

---

## 2. Các công việc và Tính năng đã triển khai (Key Deliverables)

### 2.1. Phân tích Dữ liệu, Chuẩn hóa Spec và Xây dựng Kịch bản Thuyết trình
* **Vấn đề:** Để thuyết phục được Ban Giám khảo, một sản phẩm code chạy mượt là chưa đủ, mà cần có bằng chứng rõ ràng về nỗi đau người dùng và thông số đo lường hiệu quả (Cost-of-Error, Quality Bar).
* **Giải pháp của tôi:** 
  * Cấu trúc lại toàn bộ file `spec.md` theo khuôn mẫu cực kỳ khắt khe của BTC. Khai thác số liệu (53,6% hội thoại lỗi mất ngữ cảnh) để làm bật Pain Point. 
  * Trực tiếp biên soạn tài liệu `demo_slides.md` và `speaker_script.md` bám sát luật *"không có bằng chứng thì không có slide"*, sắp xếp rành mạch lộ trình demo từ Happy Path đến Edge-case nhằm phô diễn tối đa sức mạnh của CRVLearn V2.

### 2.2. Vòng Validation User Test & Tích hợp Guardrail Chống Xung Đột
* **Vấn đề:** Trong pha Validation, anh Nguyễn Duy Bách (người test thử) đã phát hiện ra lỗ hổng: AI bị "Confuse memory" khi người dùng cố tình cung cấp thông tin mâu thuẫn để gài bẫy. Đồng thời, quá trình merge code từ nhiều nhánh gặp rủi ro đè file báo cáo `feedback_log.md`.
* **Giải pháp của tôi:**
  * Thu thập toàn bộ 5 feedback khó nhằn nhất, lập bảng `validation/feedback_log.md` chỉn chu kèm theo Action giải quyết. Xử lý gọn gàng lỗi Git tracking để giữ nguyên vẹn dữ liệu cho toàn team.
  * Mở mã nguồn `index.html` và trực tiếp cấy ghép luồng **Guardrail** vào biến `SYS_V2`: Ép AI không được hùa theo người dùng khi có thông tin mâu thuẫn lịch sử, vận dụng triệt để nguyên tắc HAX G10 (Giới hạn phạm vi & Yêu cầu xác nhận).

---

## 3. Bài học kinh nghiệm & Đóng góp cho Nhóm
* **Về mặt tư duy sản phẩm:** Tôi nhận ra việc định nghĩa rõ ràng một "Thước đo chất lượng" (Quality Bar) ngay từ Đêm 1 là cực kỳ quan trọng. Phương pháp Auto-eval bằng Probe Word Matching tuy đơn giản nhưng lại minh bạch và thuyết phục hơn rất nhiều so với những tuyên bố "AI thông minh" sáo rỗng.
* **Về mặt kỹ thuật AI:** Thiết kế AI trong giáo dục (EdTech) có Cost-of-error cực lớn. Một con AI "biết từ chối và hỏi lại khi phát hiện mâu thuẫn" (Guardrail) an toàn và đáng tin cậy hơn rất nhiều so với một con AI luôn cố gắng trả lời làm vui lòng người học. Do đó, việc ứng dụng Automation mức độ *Augment (Gợi ý)* kết hợp cùng Sliding Window ở Client-side là nước cờ kỹ thuật sáng suốt nhất của nhóm trong 48h Hackathon này.
