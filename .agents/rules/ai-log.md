# Quy tắc ghi log hoạt động AI

Mỗi khi thực hiện một nhiệm vụ, phân tích lỗi hoặc can thiệp mã nguồn, AI PHẢI tự động ghi lại lịch sử hoạt động vào file `03-ai-log.md` theo quy cách sau:

## 1. Định dạng Log entry:
```markdown
---
[TIMESTAMP]: YYYY-MM-DD HH:mm:ss
[TASK]: Mô tả ngắn gọn yêu cầu của người dùng.
[ANALYSIS]:
- Vấn đề cốt lõi / Luồng hoạt động hiện tại.
- Các file liên quan được đọc hoặc quét.
[CHANGES]:
- Danh sách file thay đổi kèm tóm tắt cụ thể những hàm/dòng được sửa/thêm/xóa.
[TEST & VERIFY]:
- Lệnh chạy thử nghiệm hoặc cách thức đã kiểm tra (nếu có).
- Kết quả mong đợi.
[STATUS]: SUCCESS | PENDING | FAILED
---
```

## 2. Nguyên tắc thực thi:
- **Không tạo log giả định**: Chỉ ghi log sau khi đã phân tích hoặc thao tác xong trên file.
- **Ghi đè hay nối tiếp**: Luôn thêm (append) entry mới vào cuối file log, không xóa các entry cũ.
- **Tự động tạo**: Tự động tạo thư mục/file log nếu nó chưa tồn tại trước khi cập nhật.
