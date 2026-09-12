# 03 — AI Log & Reflection

## AI đã hỗ trợ gì

Tôi dùng AI như thought-partner để biến ý tưởng “giúp tài xế khi gần hết pin” thành workflow, metric và boundary kiểm tra được. AI gợi ý đúng việc tách quyết định an toàn (rule) khỏi phần diễn đạt cho tài xế (LLM).

## Một câu trả lời không nên tin ngay

AI từng đề xuất hệ thống có thể “tự gửi hướng dẫn” để tiết kiệm thời gian. Tôi không dùng đề xuất đó: GPS có thể cũ, trạm có thể hết chỗ hoặc sai loại cổng sạc; tự gửi vượt quyền vận hành.

## Cách tôi sửa prompt và ranh giới

Tôi thêm ba ràng buộc: output bắt đầu bằng `[DRAFT_ONLY]`; pin dưới 5% phải yêu cầu mobile charger và không gợi ý trạm cách quá 5 km; dữ liệu thiếu hoặc mâu thuẫn phải yêu cầu người review. JSON ngắn giúp kiểm thử tự động thay vì đánh giá bằng cảm tính.

## Bài học

AI giúp tạo bản nháp nhanh, không thay thế dữ liệu vận hành hay người có quyền quyết định. Bài toán an toàn cần baseline thật, shadow mode và fallback thủ công trước khi mở rộng scope.
