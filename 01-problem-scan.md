# 01 — Problem Scan (Ý tưởng cá nhân & Nhóm)

> Hoàn thiện theo **Phase 1 (SCAN)** và **Phase 2 (QUICK-ASSESS)** của `01-worksheet.md`.
> **Mảng kinh doanh lựa chọn:** **Vinpearl / VinWonders — Vận hành công viên vui chơi giải trí.**

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup, ghi lại ít nhất 5 bài toán thực tế.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinpearl** | Tốn thời gian | Check-in/check-out & phân phòng tại quầy lễ tân thực hiện thủ công, nghẽn giờ cao điểm (5-8 phút/khách). |
| 2 | **Vinpearl** | AI-upgrade | Định giá phòng (revenue management) chỉnh bằng Excel/kinh nghiệm, không phản ứng kịp biến động cầu theo mùa/sự kiện. |
| 3 | **Vinpearl / VinWonders** | Pain từ người khác | Điều phối luồng khách & xếp hàng tại công viên giải trí: nhân sự phân bổ cố định, khách xếp hàng dài không có ước lượng thời gian chờ. |
| 4 | **Vinpearl / VinWonders** | Lặp lại | Bảo trì thiết bị/trò chơi theo lịch cố định thay vì theo tình trạng thực tế (predictive maintenance). |
| 5 | **Vinpearl** | Tốn thời gian | Tổng hợp phản hồi khách hàng đa kênh (OTA, Google, social) thủ công, phản hồi chậm 24-48 giờ. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

## Card #1 — (#3) Điều phối luồng khách & xếp hàng VinWonders ★ *Bài toán được chọn cho Deep-Dive*

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                        │
│                                                               │
│ Bài toán: Điều phối viên tại công viên VinWonders không có   │
│ dữ liệu real-time về độ dài hàng chờ và luồng khách, dẫn      │
│ đến phân bổ nhân sự/thông báo thời gian chờ không chính xác.  │
│ Công ty thành viên: [x] Vinpearl / VinWonders                │
│                                                               │
│ Ai đang đau? Khách (chờ lâu, rời bỏ), Trưởng ca vận hành      │
│ (quyết định điều phối nhân sự dựa trên cảm tính)              │
│                                                               │
│ Workflow thủ công hiện tại (5 bước):                          │
│   1. Nhân viên tại trò chơi báo hàng chờ dài qua bộ đàm       │
│   → 2. Trưởng ca đi vòng quan sát bằng mắt các khu vực khác   │
│   → 3. Ước lượng thời gian chờ, ghi lên bảng viết tay/loa     │
│   → 4. Quyết định điều thêm nhân sự dựa kinh nghiệm           │
│   → 5. Cập nhật lại khi khách đã phàn nàn (reactive)          │
│                                                               │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 15-20 phút/vòng kiểm tra)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3-4              │
│ (CV đếm dòng người -> Dự đoán thời gian chờ -> Gợi ý điều     │
│  phối nhân sự bằng ngôn ngữ tự nhiên cho Trưởng ca)           │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                         │
│ Giảm thời gian cập nhật ước tính chờ từ ~15 phút ──> real-    │
│ time dưới 2 phút; sai số dự đoán thời gian chờ ≤ ±3 phút.     │
│                                                               │
│ Quick Architecture: [x] LLM Feature (kèm CV pipeline riêng)   │
└─────────────────────────────────────────────────────────────┘
```

## Card #2 — (#2) Định giá phòng động (Revenue Management)

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                        │
│                                                               │
│ Bài toán: Revenue manager điều chỉnh giá phòng thủ công trên  │
│ Excel, không phản ứng kịp biến động cầu theo mùa/sự kiện.     │
│ Công ty thành viên: [x] Vinpearl                             │
│                                                               │
│ Ai đang đau? Revenue Manager, gián tiếp là doanh thu resort   │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                          │
│   1. Tổng hợp công suất phòng & giá đối thủ từ nhiều nguồn    │
│   → 2. Tính toán/điều chỉnh giá trên file Excel               │
│   → 3. Cập nhật thủ công lên từng kênh OTA                    │
│   → 4. Theo dõi booking, lặp lại chu kỳ hằng ngày             │
│                                                               │
│ Bước nào tốn nhất? Bước 1-2 (⏱ 60-90 phút/ngày)               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1-2               │
│ (Dự báo nhu cầu + gợi ý mức giá theo ngày/loại phòng)         │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                         │
│ Giảm thời gian định giá từ ~90 phút/ngày xuống dưới 15 phút;  │
│ Tăng RevPAR 3-5% so với baseline.                             │
│                                                               │
│ Quick Architecture: [x] LLM Feature (đề xuất giá dạng draft)  │
└─────────────────────────────────────────────────────────────┘
```

## Card #3 — (#5) Tổng hợp phản hồi khách hàng đa kênh

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                        │
│                                                               │
│ Bài toán: Nhân viên CSKH đọc thủ công review đa kênh (OTA,    │
│ Google, social) để tổng hợp insight, phản hồi chậm 24-48h.    │
│ Công ty thành viên: [x] Vinpearl                             │
│                                                               │
│ Ai đang đau? Nhân viên CSKH, và khách hàng chờ phản hồi       │
│                                                               │
│ Workflow thủ công hiện tại (4 bước):                          │
│   1. Đăng nhập từng kênh (Booking, Agoda, Google, Facebook)   │
│   → 2. Đọc và phân loại thủ công review theo mức độ khẩn cấp  │
│   → 3. Soạn thảo phản hồi từng review                         │
│   → 4. Gửi phản hồi & báo cáo Manager nếu có review nghiêm    │
│      trọng (an toàn, vệ sinh...)                              │
│                                                               │
│ Bước nào tốn nhất? Bước 2 (⏱ 45-60 phút/ngày)                 │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3                │
│ (Phân loại sentiment/urgency + soạn nháp phản hồi)            │
│                                                               │
│ Đo thành công bằng gì (Metric có số)?                         │
│ Giảm thời gian phản hồi review 1-sao từ 24-48h xuống <4h.     │
│                                                               │
│ Quick Architecture: [x] LLM Feature (phân loại + draft reply) │
└─────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định lựa chọn của nhóm

Nhóm chọn **Card #1 — Điều phối luồng khách & xếp hàng VinWonders** để đi tiếp Deep-Dive (xem chi tiết tại `02-deep-dive-report.md`).

**Lý do lựa chọn và loại bỏ các thẻ khác:**
* **Card #2 (Định giá phòng):** Tác động doanh thu lớn nhưng là bài toán back-office, không ảnh hưởng trực tiếp đến trải nghiệm khách real-time; cần nhiều dữ liệu lịch sử giá/booking hơn để huấn luyện trước khi có thể go-live.
* **Card #3 (CSKH đa kênh):** Rủi ro thấp hơn nhưng giá trị vận hành tức thời thấp hơn Card #1 — đây là tác vụ offline, có thể xử lý sau mà không ảnh hưởng đến khách đang có mặt tại công viên.
* **Card #1 (Điều phối luồng khách)** được chọn vì ảnh hưởng trực tiếp, real-time đến trải nghiệm khách đang ở công viên và doanh thu dịch vụ đi kèm (F&B, retail).
