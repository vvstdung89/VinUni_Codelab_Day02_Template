# Lab 02 — Phase 1 &amp; Phase 2 (Cá nhân): AI Product Scan

> **Người làm:** Van Quoc Dung  
> **Vai trò:** AI Product Engineer — Vin Smart Future  
> **Phạm vi file này:** Phase 1 (SCAN) và Phase 2 (QUICK-ASSESS) — bài cá nhân Gate **I1. Scan &amp; Cards**.

---

# 🔍 Phase 1 — SCAN (Cá nhân)

Dùng **4 Lenses** quét vận hành VinFast, Xanh SM, Vinhomes, Vinmec, Vinpearl.


| #   | Subsidiary   | Lens               | Mô tả ngắn bài toán                                                                                                                                                                                                                                           |
| --- | ------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **Xanh SM**  | Pain từ người khác | Tài xế không tìm được điểm đón vì pin GPS lệch mô tả ngôn ngữ tự nhiên của khách (“cổng phụ Times City, gần Highlands”). Điều phối viên phải đọc chat + sửa pin thủ công, tài xế chờ 8–12 phút/cuốc, khách hủy chuyến giờ cao điểm.                           |
| 2   | **Vinhomes** | Lặp lại            | CSKH Ban quản lý đọc từng ticket trên App Vinhomes Resident (mất nước, ồn, thẻ xe, thi công nội thất), phân loại tay rồi chuyển đúng BQL tòa. Hàng trăm ticket/ngày, SLA 12 giờ thường bị trễ vì xếp nhầm hàng đợi.                                           |
| 3   | **Vinmec**   | Tốn thời gian      | Bác sĩ/điều dưỡng soạn tóm tắt xuất viện từ EMR, kết quả xét nghiệm và ghi chú lâm sàng. Trung bình 20–30 phút/bệnh nhân, ca chiều quá tải, bệnh nhân chờ giấy tờ mới ra viện.                                                                                |
| 4   | **VinFast**  | AI-upgrade         | Khách hàng mô tả tiếng Việt hiện tượng xe (“đi qua ổ gà kêu cạch cạch bánh sau trái”). KTV after-sales mất 15–20 phút diễn giải, hỏi lại, rồi mới gán nhóm hệ thống (gầm/phanh/lốp) và lịch xưởng. Chatbot hiện tại trả lời rập khuôn, không bám triệu chứng. |
| 5   | **Vinpearl** | Tốn thời gian      | Sales nội bộ đọc email đoàn (group booking) từ công ty lữ hành: mix loại phòng, ngày, suất ăn, yêu cầu đặc biệt. Phải đối chiếu quỹ phòng trên PMS rồi soạn mail xác nhận — 25–40 phút/đoàn, dễ sai ngày hoặc hạng phòng.                                     |
| 6   | **VinFast**  | Lặp lại            | Đối soát hóa đơn sạc đối tác (trụ liên kết ngoài) với log phiên sạc nội bộ mỗi tuần: biển số, kWh, đơn giá, thời điểm. Hàng nghìn dòng Excel, lệch 1–2% doanh thu sạc nhưng tốn 1–2 ngày công kế toán/tuần.                                                   |


**Ghi chú lens coverage:** Pain (#1), Lặp lại (#2, #6), Tốn thời gian (#3, #5), AI-upgrade (#4). Không nhồi mọi bài toán vào “Agent”; một số bài (#6) nghiêng Rule/deterministic matching hơn LLM.

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ SCAN: **#1 (Xanh SM điểm đón), #2 (Vinhomes ticket), #4 (VinFast chẩn đoán triệu chứng).**

Loại khỏi top 3 (giữ cho backlog, không card hóa):

- **#3 Vinmec xuất viện:** giá trị cao nhưng ranh giới y khoa + PII cực chặt; không chọn làm card “dễ prototype” tuần này.
- **#5 Vinpearl group booking:** phụ thuộc PMS/email không chuẩn, khó đo baseline trong lab.
- **#6 VinFast đối soát sạc:** matching khóa (biển số, timestamp, kWh) — **rule-based** đủ tốt, không cần LLM làm lõi.

---

## Card #1 — Xanh SM: Sửa điểm đón lệch ngôn ngữ ↔ GPS

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Điều phối viên Xanh SM phải đọc chat của  │
│ khách/tài xế rồi sửa thủ công pin đón vì GPS không khớp     │
│ mốc địa điểm tiếng Việt (cổng phụ, quán, ngõ).              │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác _______________   │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   - Điều phối viên ca cao điểm (operator chính)                  │
│   - Tài xế (chờ pin đúng)                                   │
│   - Khách (chờ xe / dễ hủy chuyến)                          │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Hệ thống gán cuốc theo pin GPS khách                   │
│   → 2. Tài xế không thấy khách, chat/gọi tổng đài           │
│   → 3. Dispatcher đọc đoạn chat + mở bản đồ nội bộ          │
│   → 4. Sửa pin / nhắn mốc đón rồi theo dõi tài xế xác nhận  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 6–8 phút/lượt;   │
│ cả vòng 8–12 phút). Sai vì đọc sót “cổng phụ / lầu 2”.      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3–4              │
│ (Parse mô tả tiếng Việt + ngữ cảnh tòa/cổng → draft pin     │
│ hiệu chỉnh + tin nhắn mốc đón cho dispatcher duyệt).        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   1. Thời gian xử lý 1 case lệch điểm đón: 10 phút → ≤ 2    │
│      phút (dispatcher chỉ duyệt draft).                     │
│   2. Tỉ lệ hủy chuyến vì “không gặp được tài xế” giờ cao    │
│      điểm giảm ≥ 20% so với baseline 4 tuần.                │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
│   LLM Feature: hiểu mô tả địa điểm tự do. Rule/GPS chỉ là   │
│   input; không cần agent tự gọi xe hay tự gửi tin.          │
└─────────────────────────────────────────────────────────────┘
```

---

## Card #2 — Vinhomes: Phân loại ticket cư dân và draft phản hồi

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): CSKH Vinhomes đọc tay từng ticket App     │
│ Resident, phân loại chủ đề rồi soạn phản hồi rập khuôn,     │
│ khiến SLA 12 giờ bị trễ và cư dân bị chuyển sai bộ phận.    │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác _______________   │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   CSKH Ban quản lý (operator); cư dân chờ; kỹ thuật tòa     │
│   nhận ticket sai luồng.                                    │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Cư dân gửi ticket (text + ảnh) trên App                │
│   → 2. CSKH đọc, gắn nhãn tay (điện/nước/ồn/thẻ xe/khác)    │
│   → 3. Forward nội bộ tới BQL tòa hoặc kỹ thuật             │
│   → 4. Soạn phản hồi lần 1 cho cư dân trên App              │
│   → 5. Theo dõi đến khi đóng ticket                         │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–4 (⏱ 8–15 phút/     │
│ ticket; hàng đợi ca tối 40–60 ticket). Lỗi: nhầm “hỏng      │
│ thẻ xe” thành “an ninh”, hoặc hứa SLA không đúng chính sách.│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 và 4           │
│ (Phân loại + trích căn hộ/tòa + draft trả lời theo template │
│ chính sách; CSKH duyệt trước khi gửi).                      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   1. ≥ 85% ticket được gắn đúng nhóm trong 30 giây          │
│      (đối chiếu nhãn CSKH).                                 │
│   2. Thời gian soạn phản hồi lần 1: 10 phút → dưới 2 phút.  │
│   3. % ticket chuyển sai bộ phận: < 5%.                     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
│   LLM Feature + HITL. Keyword rule không bắt được mô tả     │
│   vòng vo (“hôm nay hành lang tối om, em sợ đi về muộn”).   │
│   Không để agent tự đóng ticket hay tự hứa bồi thường.      │
└─────────────────────────────────────────────────────────────┘
```

---

## Card #3 — VinFast: Draft chẩn đoán sơ bộ từ mô tả triệu chứng tiếng Việt

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Kỹ thuật viên after-sales VinFast mất     │
│ 15–20 phút diễn giải mô tả tiếng Việt của khách thành nhóm  │
│ hệ thống xe và câu hỏi làm rõ trước khi đặt lịch xưởng.     │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác _______________   │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│   KTV / CSKH xưởng VinFast (operator); khách chờ lịch;      │
│   xưởng nhận xe vào sai cổng (điện vs gầm).                 │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách mô tả trên app/hotline/chat (“kêu cụp cụp…”)     │
│   → 2. CSKH hỏi lại 3–6 câu, ghi note tự do                 │
│   → 3. KTV đọc note, đoán hệ thống, chọn mã nhóm sửa chữa   │
│   → 4. Đặt lịch xưởng + nhắn khách mang xe / checklist      │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–3 (⏱ 15–20 phút/    │
│ case). Lỗi: đưa xe vào cổng điện trong khi là tiếng ồn gầm, │
│ hoặc bỏ sót “chỉ xảy ra khi phanh”).                        │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3              │
│ (LLM trích triệu chứng, điều kiện kích hoạt, đề xuất 1–3    │
│ nhóm hệ thống + câu hỏi làm rõ + checklist an toàn; KTV     │
│ duyệt. Không tự chẩn đoán chắc chắn, không tự báo giá).     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   1. Thời gian triage 1 case: 18 phút → dưới 5 phút         │
│      (gồm thời gian KTV duyệt draft).                       │
│   2. ≥ 80% draft gán đúng nhóm hệ thống top-1 so với nhãn   │
│      KTV trên 100 case lịch sử.                             │
│   3. 0 lần AI tự ý bảo khách “cứ tiếp tục lái” khi mô tả    │
│      có tín hiệu an toàn (phanh, túi khí, cháy/mùi khét).   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
│   LLM Feature + HITL bắt buộc. Không Agent: không được tự   │
│   book xưởng, tự khóa lịch, tự kết luận lỗi phần cứng.      │
│   Rule chỉ dùng cho cờ an toàn (từ khóa phanh/cháy → escalate).│
└─────────────────────────────────────────────────────────────┘
```

---



