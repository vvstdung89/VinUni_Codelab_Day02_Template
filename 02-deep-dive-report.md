# 02 — Deep-Dive Report: Xanh SM Battery Incident Co-pilot

> Baseline và mục tiêu là giả định cho pilot; cần đo lại bằng log điều vận thực tế.

## Current-state workflow

Tài xế báo sự cố → điều phối viên xác minh xe, GPS và pin → tra dashboard trạm sạc tương thích → soạn chỉ dẫn → gửi tin hoặc gọi xe sạc di động. Baseline giả định: **15 phút/lượt**. Handoff xảy ra từ tài xế sang dispatcher và từ dispatcher sang đội xe sạc. Điểm nghẽn là tra trạm và soạn chỉ dẫn, khoảng **10 phút**.

## Problem statement (6 fields)

| Field | Nội dung |
|---|---|
| Actor / operator | Điều phối viên Xanh SM trực ca; tài xế cung cấp báo cáo ban đầu. |
| Current workflow | Dispatcher đối chiếu biển số, GPS, phần trăm pin và dashboard trạm, sau đó tự soạn tin hoặc gọi đội xe sạc. |
| Bottleneck | Chuyển dữ liệu nhiều màn hình thành hướng dẫn chính xác khi tài xế cần phản hồi gấp. |
| Business impact | Baseline pilot: 30 sự cố/ngày × 15 phút = 7,5 giờ điều phối/ngày; chậm xử lý làm xe ngừng phục vụ lâu hơn. |
| Success metric | 90% bản nháp trong ≤60 giây; xử lý trung vị ≤3 phút; 100% ca pin <5% kích hoạt mobile charger; ≥80% bản nháp được dispatcher chấp nhận không cần viết lại. |
| Operational boundary | Chỉ đọc dữ liệu đã cấp quyền và tạo nháp. Không gửi tin, đặt chỗ, hay điều xe tự động. Dữ liệu thiếu/mâu thuẫn phải chuyển người duyệt. Pin <5% không gợi ý trạm >5 km. |

## AI fit và future-state flow

**Quyết định: Rule + LLM feature.** Rule xác minh pin, khoảng cách, loại cổng sạc và tính sẵn sàng; LLM chỉ diễn đạt bản nháp tiếng Việt. Agentic loop không cần vì không có bước tự trị được phép.

1. Tài xế báo sự cố; dispatcher mở incident.
2. **[Rule]** Lấy GPS, pin, loại xe và trạm từ nguồn nội bộ; dữ liệu thiếu → **[Fallback]** tra cứu thủ công.
3. **[Rule]** Pin <5% → tạo yêu cầu mobile charger, không gợi ý trạm xa.
4. **[AI]** Với dữ liệu hợp lệ, LLM tạo JSON `[DRAFT_ONLY]`.
5. **[HITL]** Dispatcher kiểm tra vị trí, lý do và bản nháp; chỉ họ được gửi hoặc điều xe.
6. **[Fallback]** LLM lỗi/JSON sai/độ tin cậy thấp → checklist thủ công và log lỗi.

## Evaluate

| Checklist | Trạng thái | Bằng chứng / việc cần làm |
|---|---|---|
| Có dữ liệu mẫu sạch? | Chưa đủ | Cần log đã ẩn danh gồm GPS, pin, loại xe, trạm và kết quả xử lý. |
| Rủi ro có kiểm soát? | Có điều kiện | HITL, rule pin <5% và fallback thủ công giới hạn rủi ro. |
| Stakeholder sẵn sàng đổi quy trình? | Cần xác nhận | Chạy shadow mode 2 tuần với một nhóm dispatcher. |

**Quyết định: NOT YET.** Có thể xây prototype prompt, nhưng chưa tự động hóa vận hành. Trước khi GO cần dữ liệu ẩn danh, baseline thật, test ca pin/GPS/trạm mâu thuẫn và tỷ lệ dispatcher chấp nhận nháp ≥80% trong shadow mode.

## Evidence plan trước quyết định GO

Trong shadow mode hai tuần, hệ thống chỉ tạo nháp và không có quyền gửi tin. Mỗi incident cần lưu dữ liệu đầu vào, rule được kích hoạt, nháp, thao tác sửa/duyệt của dispatcher, thời gian xử lý và kết quả cuối. Chỉ GO khi không có ca pin <5% vi phạm rule, độ chính xác đề xuất trạm đạt ≥98%, và các metric trên vượt baseline đã đo.
