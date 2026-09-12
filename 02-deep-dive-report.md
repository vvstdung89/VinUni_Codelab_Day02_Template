# 02 — Deep-Dive Report

> Hoàn thiện theo **Phase 3 (DEEP-DIVE)** và **Phase 5 (EVALUATE)** của `01-worksheet.md`.
> **Bài toán:** Điều phối luồng khách & xếp hàng tại công viên giải trí (Vinpearl / VinWonders) — chọn từ `01-problem-scan.md`.

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow Mapping

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ NV trò chơi  │     │ Trưởng ca đi │     │ Ước lượng &  │     │ Quyết định   │
│ báo hàng dài │ ──→ │ vòng quan sát│ ──→ │ ghi bảng/loa │ ──→ │ điều nhân sự │
│ qua bộ đàm   │     │ bằng mắt     │     │ thời gian chờ│     │ theo kinh    │
│              │     │              │     │              │     │ nghiệm       │
│ Ai: NV trò   │     │ Ai: Trưởng ca│     │ Ai: Trưởng ca│     │ Ai: Trưởng ca│
│ chơi         │     │ 🔴           │     │ 🔴           │     │              │
│ ⏱ 1 phút     │     │ ⏱ 15 phút    │     │ ⏱ 5 phút     │     │ ⏱ 3 phút     │
│ In: Quan sát │     │ In: Đi vòng  │     │ In: Ước lượng│     │ In: Số liệu  │
│ tại chỗ      │     │ toàn khu vực │     │ bằng mắt     │     │ ước lượng    │
│ Out: Báo cáo │     │ Out: Ghi chú │     │ Out: Bảng chờ│     │ Out: Lệnh    │
│ bằng lời     │     │ tay          │     │ /loa thông báo│    │ điều chuyển  │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Cập nhật lại │
                                                               │ khi khách    │
                                                               │ phàn nàn     │
                                                               │ Ai: Trưởng ca│
                                                               │ ⏱ Phát sinh  │
                                                               │ liên tục     │
                                                               └──────────────┘
🔴 = Bottlenecks
⏱ Tổng thời gian một vòng cập nhật: ~24 phút/lượt (và luôn trễ so với thực tế).
```

*(Sơ đồ trực quan vẽ tay/công cụ vẽ đầy đủ ký hiệu Bottleneck/Handoff nằm tại `04-workflow-diagram.png`.)*

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Trưởng ca vận hành (Ops Supervisor) và nhân viên tại từng trò chơi thuộc VinWonders. |
| **2. Current Workflow** | Nhân viên tại trò chơi báo cáo hàng chờ qua bộ đàm; Trưởng ca đi vòng quan sát bằng mắt các khu vực, ước lượng thời gian chờ, ghi lên bảng/loa thông báo cho khách, và quyết định điều thêm nhân sự dựa trên kinh nghiệm cá nhân. Toàn bộ quy trình thủ công, không có dữ liệu real-time, mất ~24 phút/vòng cập nhật. |
| **3. Bottleneck** | Bước 2 & 3 (mất 20 phút): Quan sát thủ công toàn khu vực để ước lượng độ dài hàng chờ tại nhiều trò chơi cùng lúc, không chính xác và luôn trễ so với biến động thực tế (đặc biệt khi khách tăng đột biến theo giờ/thời tiết). |
| **4. Business Impact** | Thời gian chờ chiếm 60-70% tổng thời gian khách ở trong công viên (theo benchmark ngành theme park); mỗi vòng cập nhật trễ 20+ phút khiến quyết định điều phối nhân sự luôn đi sau thực tế, làm giảm số lượt trải nghiệm/khách/ngày và doanh thu F&B/retail đi kèm. *(Số liệu benchmark ngành — cần đối chiếu dữ liệu vận hành thực tế của VinWonders trước khi dùng làm business case chính thức.)* |
| **5. Success Metric** | 1. Giảm thời gian cập nhật ước tính hàng chờ từ ~15-20 phút xuống dưới 2 phút, cập nhật real-time (Efficiency).<br>2. Sai số dự đoán thời gian chờ ≤ ±3 phút so với thực tế đo được, đạt ở ≥ 90% số lượt đo (Quality). |
| **6. Operational Boundary** | AI được phép: tổng hợp dữ liệu đếm dòng người (computer vision) theo từng trò chơi, dự đoán thời gian chờ, và soạn **đề xuất điều phối nhân sự dạng nháp** cho Trưởng ca duyệt. **CẤM:** AI không được tự động thay đổi vận hành trò chơi (mở/đóng, tạm dừng) hoặc tự động điều chuyển nhân sự mà không có xác nhận của Trưởng ca (bắt buộc HITL); không được hiển thị số liệu thời gian chờ cho khách khi độ tin cậy dữ liệu thấp (camera bị che khuất, thiếu dữ liệu) — phải gắn cờ và yêu cầu đo thủ công thay thế; không thu thập/lưu trữ hình ảnh định danh khuôn mặt khách (privacy). |

## 3.3. Future-State Flow & AI Fit

* **AI Fit:** Chọn **LLM Feature** (không dùng Agentic Loop tự trị) cho lớp tổng hợp & đề xuất điều phối, vì rủi ro khi AI tự ý thay đổi vận hành trò chơi hoặc thông báo sai thời gian chờ có thể gây mất an toàn đám đông và khiếu nại khách hàng. Phần đếm dòng người dùng pipeline Computer Vision/Rule riêng (input rõ ràng, không phải LLM), LLM chỉ dùng để tổng hợp dữ liệu đa nguồn thành đề xuất ngôn ngữ tự nhiên cho con người quyết định.
* **Quy trình tương lai (Future-State):**

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Camera CV    │     │ 🔵 Model dự  │     │ 🔵 LLM tổng  │     │ 🟢 Trưởng ca │
│ đếm dòng     │ ──→ │ đoán thời    │ ──→ │ hợp & draft  │ ──→ │ duyệt & ra   │
│ người liên   │     │ gian chờ theo│     │ đề xuất điều │     │ lệnh điều    │
│ tục (real-   │     │ từng trò chơi│     │ phối nhân sự │     │ phối/thông   │
│ time)        │     │              │     │              │     │ báo khách    │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ↩️ Fallback:
                                                               Nếu camera bị che/
                                                               độ tin cậy thấp,
                                                               hệ thống gắn cờ
                                                               LOW_CONFIDENCE,
                                                               nhân viên đo và
                                                               báo cáo thủ công
                                                               như quy trình cũ.
```

* **Prototype minh hoạ ranh giới an toàn:** Nhóm đã lập trình và chạy thử một bản prototype riêng cho đúng bài toán này tại [`starter-code/prompt_prototype_vinwonders_queue.py`](starter-code/prompt_prototype_vinwonders_queue.py) (không phải bài code cá nhân nộp chấm điểm — bài đó nằm ở `starter-code/prompt_prototype.py` theo kịch bản Xanh SM chuẩn của khoá học). Cả 3 adversarial test case (ép AI bịa số liệu, ép AI tự phát lệnh, ép AI bỏ thẻ `[DRAFT_ONLY]`) đều PASS khi chạy thật với Gemini.

---

# 🏁 Phase 5 — EVALUATE (Nhóm)

### AI Readiness Checklist:
1. [x] Có dữ liệu mẫu/logs sạch để test? — *Cần xác nhận: hệ thống camera CCTV hiện có tại các trò chơi có đủ độ phủ và chất lượng hình ảnh để huấn luyện/kiểm thử mô hình đếm dòng người.*
2. [x] Rủi ro khi AI sai nằm trong tầm kiểm soát (qua HITL/Fallback)? — Có, nhờ ranh giới bắt buộc duyệt thủ công và cờ LOW_CONFIDENCE.
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ? — *Cần khảo sát thêm ý kiến đội vận hành thực địa trước khi triển khai diện rộng.*

### Quyết định cuối cùng:
[x] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Cần đánh giá độ phủ và chất lượng hệ thống camera hiện có, đo baseline thời gian chờ thực tế qua vài tuần trước khi xây pipeline CV + LLM đầy đủ.

**Justification:**
> Bài toán có metric rõ ràng, ranh giới an toàn kiểm soát tốt qua HITL, và giá trị kinh doanh lớn (thời gian chờ chiếm 60-70% trải nghiệm khách). Tuy nhiên, thành phần Computer Vision (đếm dòng người theo thời gian thực) là phụ thuộc kỹ thuật quan trọng nhất và cần được xác thực độ chính xác trước khi tích hợp với lớp LLM đề xuất điều phối — nếu triển khai LLM trước khi có dữ liệu đầu vào tin cậy, hệ thống sẽ đưa ra đề xuất sai lệch. Do đó chọn **NOT YET**: dành 2-4 tuần thu thập baseline dữ liệu hàng chờ thực tế và đánh giá hạ tầng camera trước khi go/no-go cho giai đoạn xây dựng.

---

## 🏁 Kết luận
Bài toán "Điều phối luồng khách & xếp hàng tại VinWonders" phù hợp với hướng LLM Feature (kết hợp CV pipeline riêng) do có cấu trúc rõ ràng và rủi ro an toàn đám đông cần kiểm soát chặt qua HITL. Quyết định **NOT YET** để xác lập baseline dữ liệu trước khi đầu tư xây dựng prototype đầy đủ.
