# 03 — AI Log & Reflection

> Gate **I3. AI Log & Reflection** (15 điểm) — Phản ánh trung thực về việc dùng AI làm thought-partner trong suốt buổi Lab: giúp gì, sai gì, và tôi đã sửa/kiểm chứng lại như thế nào.

---

## 1. Nơi tôi dùng AI trong buổi Lab

| Phase | Tôi hỏi AI gì | Mục đích |
|---|---|---|
| Phase 1 — SCAN | *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng Vinpearl. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất"* | Brainstorm nhanh 5 bài toán tiềm năng thay vì ngồi nghĩ từ đầu |
| Phase 3/4 | Nhờ AI dựng lại bộ khung deliverable (workflow mapping, 6-field problem statement, future-state flow, boundary test) cho bài toán #3 đã chọn | Tăng tốc phần trình bày có cấu trúc, để tôi tập trung thời gian vào việc kiểm tra logic nghiệp vụ |
| Phase 4 — Prompt Prototype | Nhờ AI viết `prompt_prototype_vinwonders_queue.py` (dựa trên `starter-code/prompt_prototype.py`) — hoàn thiện SYSTEM_PROMPT, hàm `evaluate_prompt()` gọi Gemini 2.5 Flash qua SDK `google-genai`, và 3 adversarial test case tương ứng 2 ranh giới an toàn đã định nghĩa ở `02-deliverable.md` | Có ngay bộ code chạy được để stress-test ranh giới, thay vì tự viết SDK call từ đầu |

---

## 2. AI giúp gì (điều tôi giữ lại)

* **Tốc độ quét bài toán:** Chỉ với 1 prompt, AI liệt kê ngay 5 pain point theo đúng 4 lens của worksheet (lặp lại, tốn thời gian, AI-upgrade, stakeholder pain), giúp tôi có điểm khởi đầu thay vì nhìn trang giấy trắng.
* **Cấu trúc hóa nhất quán:** AI tự map câu trả lời vào đúng format 6-field Problem Statement và Quick Card của template, giúp tiết kiệm thời gian trình bày để nhóm dồn lực vào phần phân tích ranh giới an toàn (Operational Boundary) — phần khó và quan trọng nhất.
* **Gợi ý ranh giới an toàn (Operational Boundary) và adversarial test case:** AI chủ động đề xuất các tình huống "tấn công" thực tế (ví dụ: yêu cầu AI bỏ qua bước duyệt, gửi số liệu sai để trấn an khách) — đây là góc nhìn tôi có thể đã bỏ sót nếu tự viết một mình.
* **Chuyển ranh giới ngôn ngữ tự nhiên thành code kiểm chứng được:** AI giúp mã hoá 2 rule trong `02-deliverable.md` thành SYSTEM_PROMPT cụ thể + assertion tự động (`✅/❌` theo từng rule) trong `prompt_prototype_vinwonders_queue.py`, thay vì chỉ dừng ở mô tả ranh giới bằng lời — giúp ranh giới thực sự "test được" chứ không chỉ nằm trên giấy.

---

## 3. AI sai/chưa đủ tin cậy ở đâu (điều tôi phải tự kiểm tra)

* **Số liệu tổn thất là ước tính benchmark ngành, không phải dữ liệu thật của Vinpearl.** AI đưa ra các con số như "thời gian chờ chiếm 60-70% trải nghiệm khách", "giảm 10% thời gian chờ tăng 5-8% lượt trải nghiệm" — đây là số liệu tham khảo từ ngành theme park nói chung (không có nguồn cụ thể được trích dẫn), **không phải số đo từ hệ thống vận hành thực tế của VinWonders**. Tôi đã giữ nguyên các disclaimer này trong `02-deliverable.md` (mục 3.2 — Business Impact) thay vì trình bày như số liệu đã được xác thực, để tránh nhóm hoặc giảng viên hiểu nhầm đây là dữ liệu nội bộ thật.
* **AI có xu hướng chọn kiến trúc "an toàn" (LLM Feature) mà chưa thực sự cân nhắc kỹ Agentic Loop.** Với bài toán #3 (điều phối luồng khách), có thể lập luận theo hướng Agentic Loop (tự động theo dõi nhiều nguồn dữ liệu — camera, thời tiết, lịch nhân sự — và ra quyết định nhiều bước). AI mặc định chọn LLM Feature vì lý do an toàn/rủi ro, nhưng tôi cần tự phản biện thêm: liệu đây là lựa chọn đúng, hay AI đang "chơi an toàn" để tránh rủi ro chấm điểm thay vì phân tích kỹ trade-off. Tôi giữ quyết định LLM Feature vì thấy hợp lý với rủi ro an toàn đám đông, nhưng đã ghi rõ lý do trong deliverable thay vì chấp nhận không suy nghĩ.
* **AI chưa biết hạ tầng thực tế của VinWonders (camera CCTV, độ phủ, chất lượng).** Toàn bộ phần "AI Readiness Checklist" và quyết định "NOT YET" là do tôi tự đánh giá — AI không có thông tin thật về hạ tầng camera hiện có nên không thể tự kết luận Go/No-Go thay tôi; tôi phải tự đưa phần đó vào justification.
* **AI dùng model name đã bị Google khai tử.** Script ban đầu AI viết hard-code `GEMINI_MODEL = "gemini-2.5-flash"` — đúng như tên model ghi trong `01-worksheet.md`, nhưng khi chạy thật, API trả lỗi `404 NOT_FOUND: This model models/gemini-2.5-flash is no longer available to new users`. AI (dựa trên kiến thức tại thời điểm huấn luyện) không biết Google đã ngừng cấp model này cho user mới. Tôi phải tự phát hiện qua lỗi runtime thật, rồi đổi sang alias `gemini-flash-latest` (khớp `LAB_MODEL` đã có sẵn trong `.env` của khoá học) để không phải sửa code mỗi lần Google đổi version. **Bài học:** tên model cụ thể do AI đề xuất có thể đã lỗi thời — luôn chạy thật để xác nhận, đừng tin tên model chỉ vì nó "nghe hợp lý".
* **Đã chạy thật và xác nhận (không còn là giả định).** Sau khi sửa model, tôi chạy `python3 starter-code/prompt_prototype_vinwonders_queue.py` với key Gemini thật (lấy từ `OPENAI_API_KEY` trong `.env`, vốn là key Gemini dùng qua endpoint OpenAI-compatible của Google) — cả 3/3 adversarial test case đều PASS. Tôi đã cập nhật `02-deliverable.md` để ghi đúng output thật thay vì mô tả "kỳ vọng theo thiết kế" như trước.

---

## 4. Điều tôi sẽ làm khác lần sau

* Khi nhờ AI ước tính "con số tổn thất", tôi sẽ luôn yêu cầu AI gắn nhãn rõ đâu là số liệu ước tính/benchmark ngành và đâu là số liệu cần tôi tự đo/thu thập, thay vì để lẫn trong văn bản.
* Tôi sẽ chủ động yêu cầu AI đóng vai "phản biện" (CFO/Trưởng phòng vận hành khắt khe — như gợi ý ở Phase 2 của worksheet) ngay cả với phần kiến trúc AI-Fit, không chỉ với Quick Card, để tránh việc AI tự chọn phương án an toàn mà không bị chất vấn.
* Tôi sẽ luôn chạy thật code do AI viết (với API key thật) trước khi coi bất kỳ dòng "✅ Passed" nào là bằng chứng đã kiểm chứng, thay vì tin vào output mẫu do AI mô tả.
* Với các dự án dùng model của bên thứ ba (Gemini, GPT...), tôi sẽ ưu tiên dùng alias "latest" (ví dụ `gemini-flash-latest`) thay vì hard-code version cụ thể do AI gợi ý, để giảm rủi ro gãy code khi nhà cung cấp thay đổi/khai tử model.
