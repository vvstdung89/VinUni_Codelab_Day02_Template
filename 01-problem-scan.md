# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Xanh SM** | Tốn thời gian | Điều phối cứu hộ xe hết pin nguy cấp (< 5%) và điều vận xe sạc pin lưu động (Mobile Charging Vehicle). |
| 2 | **Xanh SM** | Lặp lại | Phân tích nguyên nhân gốc rễ và quy kết trách nhiệm cuốc hủy từ ghi âm cuộc gọi và ghi chú tài xế. |
| 3 | **Xanh SM** | Lặp lại | Hậu kiểm và đối chiếu hóa đơn vé cầu đường (e-toll / VETC) và vé bến bãi sân bay với lộ trình GPS xe. |
| 4 | **Xanh SM** | Pain từ người khác | Tiếp nhận, truy vết cuốc xe và tự động hóa xử lý ticket tìm đồ thất lạc (Lost & Found) của hành khách. |
| 5 | **Xanh SM** | AI có thể tốt hơn | Khử nhiễu tọa độ GPS và tối ưu hóa điểm đón thông minh (Smart Staging) tại các đại đô thị Vinhomes và sân bay. |

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tài xế Xanh SM báo sự cố pin nguy cấp     │
│ (< 5%) cần chỉ dẫn trạm sạc hoặc điều xe sạc lưu động gấp.  │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế (hoang mang), ĐPV (quá tải)     │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Tài xế gọi hotline ──> 2. ĐPV định vị xe trên map      │
│   ──> 3. Tra trạm sạc trống ──> 4. Soạn tin nhắn chỉ dẫn    │
│   ──> 5. Điều xe sạc lưu động nếu pin cạn kiệt              │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 12 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4            │
│ (Tự động lấy vị trí -> Tra cứu trạm -> Draft tin/Lệnh sạc)  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm thời gian xử lý sự cố từ 15 min ──> dưới 3 min;      │
│   giảm tỉ lệ xe cạn pin về 0% trên đường xuống dưới 1%.     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Đối chiếu ảnh vé bến bãi/VETC của tài xế  │
│ với dữ liệu hành trình GPS cuốc xe để duyệt hoàn tiền.      │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Kế toán đối soát (quá tải soi ảnh),    │
│                      Tài xế (chờ hoàn phí lâu)              │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Tài xế gửi ảnh vé ──> 2. Kế toán nhập tay số tiền/biển │
│   ──> 3. Mở log GPS so khớp giờ qua trạm ──> 4. Duyệt chi   │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 4-5 min/vé)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3            │
│ (Multimodal trích xuất thông tin vé -> Match GPS cuốc xe)   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Rút ngắn thời gian duyệt vé từ 5 min ──> dưới 30 giây;    │
│   phát hiện vé trùng lặp / gian lận chính xác > 98%.        │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Tiếp nhận phản ánh, truy vết chuyến đi và │
│ điều phối tài xế kiểm tra đồ khách để quên trên xe taxi.    │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Khách quên đồ (bức xúc), CSKH (khó gọi)│
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Tiếp nhận mô tả đồ ──> 2. Tra cứu mã cuốc trên CRM     │
│   ──> 3. Gọi/nhắn tài xế kiểm tra ──> 4. Hẹn bàn giao đồ    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 45-60 min/ca)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1, 2 & 3         │
│ (NLP phân tích mô tả -> Auto-match cuốc -> Push alert tài xế│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Rút ngắn thời gian kết nối tài xế từ 45 min ──> dưới 2 min│
│   tăng tỉ lệ thu hồi đồ thất lạc trong 24h từ 70% ──> 90%.  │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [x] Agent │
└─────────────────────────────────────────────────────────────┘
```
