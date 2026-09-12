# 🔍 Phase 1 — SCAN & Phase 2 — QUICK-ASSESS
**Họ và tên:** AI Product Engineer (Vin Smart Future)  
**Mảng hoạt động:** Hệ thống Vận hành Vingroup (VinFast, Xanh SM, Vinhomes, Vinpearl, Vinmec)

---

## 🔍 Phase 1 — SCAN: Danh Sách 5 Bài Toán Vận Hành Vingroup

Dưới đây là 5 bài toán thực tế được quét qua các công ty thành viên Vingroup dựa trên **4 Lenses**:

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Xanh SM (GSM)** | Tốn thời gian / Pain từ người khác | Điều phối viên xử lý thủ công các báo cáo khẩn cấp từ tài xế taxi điện về sự cố hết pin/sạc pin giữa đường (mất 12-15 phút/lượt). |
| 2 | **Vinhomes** | Lặp lại | Phân loại và chuyển giao (route) tự động các phản ánh/khiếu nại của cư dân (hỏng đèn, mất nước, ồn ào) gửi qua App Vinhomes Resident đến đúng BQL từng tòa nhà. |
| 3 | **VinFast** | AI-upgrade | Trợ lý tư vấn & Đề xuất lịch sạc tối ưu phù hợp với dung lượng pin thực tế và loại cổng sạc (CCS2/GBT) cho các dòng xe VF5, VF8, VF9. |
| 4 | **Vinpearl** | Pain từ người khác | Tự động tổng hợp và lọc các phàn nàn khẩn cấp (phòng chưa vệ sinh, thái độ phục vụ) từ review của khách hàng trên Booking.com/Agoda để gửi cảnh báo tới Hotel Manager. |
| 5 | **Vinmec** | Tốn thời gian | Phân tích triệu chứng ban đầu do bệnh nhân mô tả qua chatbot để gợi ý đúng chuyên khoa (Tim mạch vs Hô hấp vs Tiêu hóa) trước khi đặt lịch khám. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

### 📌 QUICK PROBLEM CARD #1 (Bài toán được chọn thử nghiệm Prototype)

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                       │
│                                                                             │
│ Bài toán: Tài xế Xanh SM báo cáo sự cố sắp hết pin / cạn kiệt pin           │
│ thực địa cần hướng dẫn trạm sạc gần nhất hoặc điều xe cứu hộ pin.          │
│ Công ty thành viên: [x] Xanh SM (GSM)                                       │
│                                                                             │
│ Ai đang đau (Actor)? Tài xế (chờ đợi stress), Điều phối viên (quá tải)      │
│                                                                             │
│ Workflow thủ công hiện tại (5 bước):                                        │
│   1. Tài xế gọi điện/báo sự cố pin cạn kiệt về tổng đài điều vận           │
│   ──> 2. Điều phối viên tra cứu thủ công tọa độ GPS xe trên bản đồ         │
│   ──> 3. Tra cứu thủ công các trạm sạc VinFast trống phù hợp loại xe        │
│   ──> 4. Soạn tin nhắn văn bản chỉ dẫn đường đi & trụ sạc gửi qua App       │
│   ──> 5. Liên hệ đội xe cứu hộ pin di động nếu xe dưới 5% pin               │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4                            │
│ (Tự động lấy vị trí -> Tra cứu trạm trống -> Soạn nháp SMS chỉ dẫn)         │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút (Giảm 80% thời gian). │
│                                                                             │
│ Quick Architecture: [x] LLM Feature (Draft chỉ dẫn + Kiểm tra ranh giới)    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 📌 QUICK PROBLEM CARD #2

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                       │
│                                                                             │
│ Bài toán: Phân loại & Điều hướng phản ánh khiếu nại cư dân Vinhomes.         │
│ Công ty thành viên: [x] Vinhomes                                            │
│                                                                             │
│ Ai đang đau (Actor)? Cư dân (chờ lâu), Ban Quản Lý tòa nhà (quá tải email)  │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Cư dân gửi phản ánh dạng văn bản/hình ảnh lên App Vinhomes Resident    │
│   ──> 2. Nhân viên CSKH đọc thủ công từng phản ánh để xác định loại sự cố   │
│   ──> 3. Tra cứu thủ công Ban Quản Lý (Kỹ thuật/Vệ sinh/An ninh) của tòa nhà│
│   ──> 4. Chuyển tiếp (forward) ticket và nhắn tin nhắc nhở kĩ thuật viên    │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 8-12 tiếng để route ticket)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3                            │
│ (Đọc hiểu phản ánh -> Phân loại tự động -> Route trực tiếp đến đúng BQL)   │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ Thời gian phân loại & chuyển ticket giảm từ 12 giờ ──> dưới 5 phút.        │
│                                                                             │
│ Quick Architecture: [x] Rule + LLM Classifier                               │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 📌 QUICK PROBLEM CARD #3

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                       │
│                                                                             │
│ Bài toán: Tổng hợp & Cảnh báo phàn nàn khẩn cấp từ Review khách Vinpearl.   │
│ Công ty thành viên: [x] Vinpearl / VinWonders                               │
│                                                                             │
│ Ai đang đau (Actor)? Hotel Manager (thiếu thông tin), Khách hàng (bức xúc) │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Khách hàng viết review đánh giá trên Booking.com, Agoda, Google Maps   │
│   ──> 2. Chuyên viên Marketing gom file Excel review về hằng tuần           │
│   ──> 3. Đọc thủ công hàng nghìn dòng review để tìm ý kiến tiêu cực         │
│   ──> 4. Soạn báo cáo tổng hợp gửi Ban Giám Đốc Khách sạn                  │
│                                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (Trễ 3-7 ngày mới xử lý phàn nàn)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4                         │
│ (Crawl review -> Phân tích sentiment & trích xuất sự cố khẩn -> Alert ngay) │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ Phát hiện và gửi cảnh báo sự cố 1-star/2-star về Manager dưới 15 phút.     │
│                                                                             │
│ Quick Architecture: [x] LLM Sentiment Analysis & Summarizer                 │
└─────────────────────────────────────────────────────────────────────────────┘
```
