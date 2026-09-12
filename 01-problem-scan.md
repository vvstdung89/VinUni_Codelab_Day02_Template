# 01 — Problem Scan & Quick Assess

> Các thời gian và quy mô dưới đây là giả định để thiết kế pilot; cần xác nhận bằng log vận hành trước khi triển khai.

## Phase 1 — Scan

| # | Công ty | Lens | Bài toán quan sát |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên tra vị trí, mức pin và trạm sạc khi tài xế báo nguy cơ hết pin. |
| 2 | Xanh SM | Lặp lại | Điều phối lại chuyến bị hủy: đọc lý do, tìm tài xế thay thế và liên hệ khách. |
| 3 | Xanh SM | Pain từ người khác | Phân loại báo cáo sự cố của tài xế thành an toàn, kỹ thuật, sạc pin hoặc tranh chấp. |
| 4 | Xanh SM | Tốn thời gian | Nghe ghi âm và đọc chat để tổng hợp lý do hủy chuyến theo tuần. |
| 5 | Xanh SM | AI-upgrade | Supervisor kiểm tra chất lượng phản hồi của tổng đài và mức độ tuân thủ kịch bản. |

## Phase 2 — Quick Problem Cards

### Card 1 — Hỗ trợ sự cố pin thực địa

- Công ty: Xanh SM. Actor: tài xế và điều phối viên trực ca.
- Workflow: tài xế báo sự cố → xác minh xe/GPS → tra dashboard trạm → soạn hướng dẫn hoặc gọi cứu hộ → gửi sau khi kiểm tra.
- Bottleneck: tra trạm tương thích và viết hướng dẫn, giả định 8–12 phút/lượt.
- AI: lấy dữ liệu đã xác thực và tạo bản nháp; rule an toàn xử lý ngưỡng pin.
- Metric pilot: 90% bản nháp có sẵn trong 60 giây; 100% ca pin <5% chuyển mobile charger.
- Kiến trúc: Rule + LLM feature, không dùng agent tự trị.

### Card 2 — Điều phối lại cuốc xe bị hủy

- Công ty: Xanh SM. Actor: điều phối viên, tài xế thay thế và khách chờ xe.
- Workflow: nhận hủy chuyến → đọc lý do/hành trình → lọc tài xế đủ điều kiện → liên hệ khách và tài xế → xác nhận cuốc mới.
- Bottleneck: đối chiếu ngữ cảnh cuốc xe và tài xế khả dụng, giả định 5 phút/ca.
- AI: tóm tắt lý do hủy và soạn bản nháp liên hệ; rule chọn tài xế dựa trên vị trí, loại xe và trạng thái.
- Metric pilot: giảm thời gian phân công lại xuống ≤2 phút, tỷ lệ khách chấp nhận cuốc thay thế ≥70%.
- Kiến trúc: Rule + LLM feature có HITL.

### Card 3 — Phân loại báo cáo sự cố tài xế

- Công ty: Xanh SM. Actor: tổng đài, dispatcher và đội hỗ trợ hiện trường.
- Workflow: nhận call/chat → đọc ghi chú → xác định loại/mức độ sự cố → chuyển đội xử lý → cập nhật ticket.
- Bottleneck: mô tả tiếng Việt tự do dễ nhầm giữa sự cố kỹ thuật và an toàn, giả định 8 phút/ticket.
- AI: tóm tắt và đề xuất nhãn; rule bắt buộc ưu tiên từ khóa an toàn và luôn route người duyệt.
- Metric pilot: 90% ticket được gắn nhãn đúng; 95% ticket an toàn vào hàng đợi khẩn trong ≤2 phút.
- Kiến trúc: Rule + LLM classifier có HITL.

## Lựa chọn deep-dive

Chọn Card 1 vì workflow ngắn, đầu vào xác định và boundary an toàn kiểm tra được. Quyền gửi tin hay điều xe vẫn thuộc điều phối viên.

## Stress-test như CFO và Trưởng phòng Vận hành

1. **ROI chưa được chứng minh:** “30 ca/ngày” và “15 phút/lượt” chỉ là giả định. Pilot phải lấy log thời gian xử lý, xe ngừng phục vụ và chi phí cứu hộ trước khi cam kết tiết kiệm.
2. **Metric đang thiên về tốc độ:** “bản nháp trong 60 giây” không chứng minh hiệu quả vận hành. Cần đo tỷ lệ dispatcher chấp nhận nháp, độ chính xác trạm và thời gian xe trở lại hoạt động.
3. **LLM không nên ra quyết định:** pin, khoảng cách, cổng sạc và chỗ trống là dữ liệu cấu trúc; rule-based code minh bạch và kiểm thử được. LLM chỉ nên soạn tiếng Việt sau khi rules chọn phương án hợp lệ.
