# Reflection cá nhân — Trần Tiến Dũng (HV001)

## 1. Vai trò và phần tôi thực hiện

Trong dự án CRVLearn, tôi đảm nhiệm vai trò **Evidence — Khai thác dữ liệu (Data Mining & Evidence Analysis)**: đào chatlog để tìm bằng chứng cho pain "nhớ lệch" của AI Tutor, trước khi nhóm chọn giải pháp Sliding Window + Query Rewriting.

Phần việc gốc tôi trực tiếp làm:

- Phân tích `chat_history_anonymized_for_hackathon.csv`: đếm được **676/1.261 (53,6%) lượt hỏi là follow-up** và **276/585 (47,2%) hội thoại có từ hai lượt trở lên** — số đếm được, kèm phương pháp đếm để người khác kiểm lại được.
- Khảo sát người thật: **14/23 (60,9%)** người tham gia xác nhận từng gặp lỗi quên/lạc mạch khi hỏi nối tiếp.
- Gom **5 quote nguyên văn** làm bằng chứng trực tiếp (spec.md §1), trong đó có case tôi tự test tay: hỏi "câu ở trên tôi hỏi là gì" thì AI Tutor thừa nhận không thể lưu trữ/theo dõi câu hỏi gần nhất — đúng triệu chứng nhóm muốn khắc phục.

Ngoài phần evidence gốc, trong ngày build và trước demo tôi làm thêm vai trò **đối chiếu bằng chứng với thực tế code** (vẫn nằm trong tinh thần "Evidence" — không chỉ đếm số một lần rồi thôi, mà giữ mọi khẳng định của nhóm trace được về nguồn thật):

- Phát hiện 5 file `eval-run-*.md` xuất từ tab Eval bị lỗi mã hoá (UTF-8 bị đọc nhầm Latin-1) khi copy ra ngoài; phục hồi đúng nguyên văn bằng cách đối chiếu ngược với `PRESETS`/`grade()` trong `index.html`, verify lại bằng cách khớp % tổng của từng lượt chạy, rồi gộp thành `codebase/eval/eval-run-consolidated-2026-07-31.md` — số liệu 90%/98% ở đó sau này được dùng làm kết quả chính thức trong spec.md §7.
- Rà code kiểm tra các rule-based guardrail (riêng tư, chính trị, câu hỏi nhạy cảm): phát hiện tại thời điểm đó guardrail chỉ nằm ở system prompt (không có filter chạy thật trong code), và hàm chấm điểm `grade()` tự động cho ĐẠT mọi case lớp ③ ngoài phạm vi vì `probe:null` — nghĩa là chưa thực sự kiểm chứng được AI có từ chối đúng hay không.
- Đối chiếu `validation/feedback_log.md` với code thật: phát hiện cả 5 dòng "Action Taken ✅ Đã xử lý" đều **không khớp code** (grep ra không có dòng nào tương ứng); sửa lại đúng trạng thái trước khi log này được dùng làm bằng chứng cho R6.
- Trực tiếp code tính năng lưu và chọn lại lịch sử hội thoại nhiều phiên (icon 🕐 xem lịch sử / ➕ mở đoạn chat mới) trong `codebase/index.html`, test bằng trình duyệt thật (Playwright + Chromium) chứ không chỉ đọc code.
- Giải quyết conflict khi 3 người cùng sửa `index.html` gần như cùng lúc (fix "mâu thuẫn thông tin" của Hoàng Minh, tính năng đa-chủ-đề/thread-label của Dương Kiên, và tính năng phiên chat của tôi) — merge tay, tích hợp đúng logic (đồng bộ trạng thái "chủ đề đang mở" theo từng phiên), test lại toàn bộ luồng rồi mới push.

## 2. AI đã hỗ trợ tôi như thế nào

Tôi dùng Claude Code như một **đồng nghiệp kỹ thuật kiểm tra chéo**, không lấy output đầu tiên làm kết luận:

- Khi tôi đưa 5 file eval bị lỗi encoding, AI không bịa lại nội dung mà đối chiếu ngược với đúng logic `PRESETS`/`grade()` trong code để phục hồi chính xác từng câu, rồi tự verify bằng cách tính lại % tổng khớp với số đã thấy trên UI trước đó.
- Khi tôi hỏi kiểm tra guardrail an toàn, AI chủ động `grep` thẳng vào code thay vì trả lời chung chung, chỉ ra đúng dòng có/không có — kể cả điểm yếu trong chính bộ chấm điểm của nhóm mà không ai để ý (case `probe:null` luôn tự động ĐẠT).
- Khi phát hiện `feedback_log.md` ghi sai trạng thái, AI dừng lại hỏi tôi muốn xử lý thế nào (sửa log cho đúng / code thật ngay / giữ nguyên và tự chịu rủi ro) thay vì tự ý sửa hoặc lờ đi — tôi chọn sửa log cho trung thực trước.
- Khi code tính năng mới, AI test bằng trình duyệt thật thay vì chỉ khẳng định "chắc chạy được"; khi merge conflict với 2 tính năng khác của đồng đội, AI đọc kỹ ý đồ từng bên rồi tích hợp đúng — kể cả phần tôi không yêu cầu, như đồng bộ trạng thái "chủ đề" theo từng phiên chat để không bị lẫn giữa các cuộc hội thoại.

Tôi vẫn tự kiểm tra lại trước mỗi bước rủi ro: chạy `git status`/`git log`/`git diff` để chắc remote chưa bị vượt thêm trước khi push, đọc lại toàn bộ nội dung file trước khi cho phép công bố hoặc gộp.

## 3. Case fail của nhóm và bài học của tôi

Case fail tôi nhớ nhất không nằm ở prompt hay model, mà ở chính **quy trình báo cáo bằng chứng** — đúng phần việc tôi phụ trách:

`validation/feedback_log.md` ban đầu ghi cả 5 phản hồi từ vòng user test là **"✅ Đã xử lý"**, nhưng khi tôi `grep` thẳng vào `codebase/index.html` để đối chiếu, không dòng nào khớp: guardrail nhận diện mâu thuẫn của Hoàng Minh chưa có trong `SYS_V2`, `retrieve()` vẫn chỉ so khớp `c.key` chứ chưa đổi sang `c.text`, không có disclaimer "Probe Word Matching" trong tab Eval, không hiển thị "Quality Bar ≥85%" trên UI, và `localStorage` lúc đó chỉ lưu provider/key/model chứ chưa lưu lịch sử hội thoại.

Nguyên nhân nhiều khả năng là bảng "Action Taken" được viết ra như một **kế hoạch sẽ làm** trong lúc gấp giờ hackathon, rồi bị hiểu nhầm/ghi nhầm thành đã hoàn thành. Nếu không phát hiện, hậu quả trực tiếp là rubric R6 (8 điểm, yêu cầu "≥1 thay đổi từ feedback ghi trong Changelog" có bằng chứng thật) sẽ bị chấm sai, và theo "vibe-coding rule" nếu giám khảo hỏi ngược "cho tôi xem guardrail đó ở đâu trong code", nhóm sẽ lộ ra là báo cáo không đúng thực tế — mất điểm R6 và ảnh hưởng cả uy tín các phần khác.

Cách xử lý: cập nhật lại đúng trạng thái trong log kèm link dòng code cụ thể chứng minh, rồi chủ động implement ngay một fix thật (tính năng lưu/khôi phục lịch sử theo yêu cầu của Nguyễn Công Minh) để log có ít nhất một dòng "đã xử lý" kiểm chứng được bằng test thật, không chỉ bằng chữ.

**Bài học lớn nhất:** trong hackathon chạy nước rút, khoảng cách giữa "đã lên kế hoạch sửa" và "đã sửa thật trong code" rất dễ bị nhoà đi khi viết báo cáo nhanh. Một bảng feedback tưởng vô hại có thể trở thành bằng chứng không trung thực nếu không ai đối chiếu ngược lại với code. Từ giờ tôi sẽ luôn gắn kèm dòng code cụ thể (hoặc kết quả test) ngay tại thời điểm đánh dấu "đã xử lý", thay vì ghi bằng lời rồi tin tưởng nó đúng.

## 4. Điều tôi rút ra sau dự án

- Evidence không dừng lại ở khâu mining đầu dự án (spec §1) — nguyên tắc "đếm được, kiểm lại được" cần áp dụng xuyên suốt cho mọi khẳng định "đã làm/đã sửa/đã đo" trong toàn bộ vòng đời dự án, không chỉ lúc tìm pain ban đầu.
- Làm việc nhóm 5 người cùng sửa một file trong những giờ cuối rất dễ đụng nhau — điều đáng sợ không phải git conflict, mà là push đè lên công sức người khác vì không kiểm tra kỹ trạng thái remote trước.
- Nếu có thêm 1 tuần, tôi sẽ ưu tiên: (1) viết một script nhỏ tự đối chiếu cột "Action Taken" trong feedback log với code, thay vì soát tay; (2) sửa `grade()` để thực sự kiểm chứng các case lớp ③ ngoài phạm vi thay vì mặc định ĐẠT khi không có probe; (3) thống nhất một nguồn số liệu duy nhất giữa `eval/golden_set.json` và bộ `PRESETS` trong `index.html` để tránh hai bộ kết quả không khớp nhau.

## Minh chứng trong repo

- Phân công vai trò: [`README.md`](../README.md) và [`spec.md`](../spec.md) §8
- Evidence gốc (số liệu mining + 5 quote nguyên văn): [`spec.md`](../spec.md) §1
- Gộp & phục hồi kết quả eval (dùng làm số liệu chính thức trong spec.md §7): [`codebase/eval/eval-run-consolidated-2026-07-31.md`](../codebase/eval/eval-run-consolidated-2026-07-31.md)
- Sửa log validation cho đúng thực tế: [`validation/feedback_log.md`](../validation/feedback_log.md)
- Tính năng lưu/chọn lại phiên chat (`S.sessions`, `#btnHistory`, `#btnNewChat`): [`codebase/index.html`](../codebase/index.html)
- Commit tính năng + commit merge tích hợp 3 nhánh: [`b80099d`](https://github.com/hoangthcslt/K4-hackathon-CRVLearn-E403/commit/b80099d), [`9e353d6`](https://github.com/hoangthcslt/K4-hackathon-CRVLearn-E403/commit/9e353d6)
