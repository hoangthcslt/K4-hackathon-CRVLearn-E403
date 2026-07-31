# BÁO CÁO CÁ NHÂN (REFLECTION) — DỰ ÁN CRVLEARN
**Họ và tên:** Nguyễn Đình Hoàng  
**Mã số học viên:** 2A202601436  
**Vai trò:** Phát triển Prototype 

---

## 1. Vai trò và Trách nhiệm chính
Trong dự án Hackathon **CRVLearn** nhằm giải quyết lỗi mất mạch ngữ cảnh ("mù ngữ cảnh") của AI Tutor trên nền tảng VLearn, tôi đảm nhận vai trò **Phát triển Prototype**. Nhiệm vụ chính của tôi bao gồm:
* Hiện thực hóa các giải pháp lý thuyết từ tài liệu đặc tả (`spec.md`) thành mã nguồn hoạt động trực tiếp trên giao diện và backend của ứng dụng.
* Quản lý trạng thái hội thoại và tích hợp các cuộc gọi API LLM (Gemini/Claude).
* Phối hợp cùng các thành viên thu thập dữ liệu (Trần Tiến Dũng) và kỹ sư prompt (Hoàng Thị Hà Huyền, Dương Văn Kiên) để tích hợp dữ liệu/prompt tối ưu vào ứng dụng.

---

## 2. Các công việc và Tính năng đã triển khai (Key Deliverables)

### 2.1. Phát triển Cơ chế Bộ nhớ Cửa sổ Trượt (Sliding Window History)
* **Vấn đề:** Baseline cũ bị lỗi "nhớ lệch" do chỉ gửi câu trả lời cũ của AI và nội dung tài liệu trích xuất mới vào context, hoàn toàn bỏ quên câu hỏi (`user prompt`) cũ.
* **Giải pháp của tôi:** Lập trình cấu trúc bộ nhớ cache dạng mảng động lưu cả cặp `{role: "user", content: ...}` và `{role: "assistant", content: ...}` với kích thước cửa sổ trượt tối ưu từ 6–8 lượt. Nhờ đó, AI duy trì được tính đối xứng thông tin và ghi nhớ mạch hội thoại cực kỳ mượt mà.

### 2.2. Tích hợp Module Viết lại Truy vấn (Query Rewriting)
* **Vấn đề:** Khi học viên hỏi follow-up bằng các đại từ thay thế (*"nó"*, *"ý số 2"*, *"tại sao"*,...), công cụ Retrieval RAG (bản chất là so khớp keyword) sẽ trích xuất sai tài liệu dẫn đến AI phản hồi lạc đề.
* **Giải pháp của tôi:** Viết logic chặn luồng gửi câu hỏi để chạy qua module Query Rewriter trước. Hệ thống sẽ dịch và viết lại câu hỏi cộc lốc của học viên thành câu hỏi độc lập đầy đủ ý nghĩa (dựa vào lịch sử hội thoại) trước khi gửi đi truy vấn RAG. Khi bật `saveQuota`, tôi tối ưu hóa bằng cách gọi logic heuristic viết lại phía client để tránh tốn thêm cuộc gọi LLM phụ trợ.
---

## 3. Bài học kinh nghiệm & Đóng góp cho Nhóm
* **Về mặt kỹ thuật:** Việc tự tay sửa đổi codebase HTML/JS trực tiếp và đồng bộ với Git giúp tôi hiểu sâu sắc cách tổ chức một ứng dụng RAG Client-side. Cơ chế Query Rewriting thực sự là mắt xích quan trọng nhất để cứu cánh cho thuật toán RAG Keyword-based đơn giản.
* **Về mặt quy trình:** Việc phối hợp liên tục để xử lý conflict mã nguồn (`git pull`, giải quyết đè file `spec.md`, sửa đổi cấu trúc commit để đúng tiến độ kiểm soát `CP3`) đã giúp nhóm duy trì được tiến độ Hackathon cực kỳ ổn định.
