# 📄 02-deep-dive-report.md — Deep‑Dive Report (Vinhomes Resident Feedback)

---

## 🎯 Problem Statement (6-field)

| Field                       | Nội dung                                                                                                                                                                                                                                                                                                                             |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1. Actor / Operator**     | **Cư dân** (người dùng đã xác thực trong app **Vinhomes Residents**) và **Ban Quản Lý** (nhân viên hỗ trợ, quản trị viên).                                                                                                                                                                                                           |
| **2. Current Workflow**     | 1️⃣ Cư dân mở app, chọn “Gửi Khiếu Nại”. 2️⃣ Nhập mô tả vấn đề (văn bản, ảnh, video). 3️⃣ Nhấn “Gửi”. 4️⃣ Hệ thống hiện tại **chỉ lưu** yêu cầu vào cơ sở dữ liệu và **đánh dấu** “Chưa xử lý”. 5️⃣ Nhân viên CSKH phải **đọc thủ công** từng ticket, **phân loại** (hỏng nước, điện, vệ sinh, …) và **giao** cho bộ phận tương ứng. |
| **3. Bottleneck**           | - **Thời gian phản hồi**: trung bình **1–3 giờ** để một ticket được phân loại và chuyển.<br>- **Sai lệch phân loại**: 15% ticket được gán sai bộ phận, gây trễ xử lý.                                                                                                                                                                |
| **4. Business Impact**      | - Mất ≈ 30% độ hài lòng cư dân (NPS giảm).<br>- Tăng chi phí nhân lực CSKH khoảng **15k USD/tháng** do công việc thủ công.                                                                                                                                                                                                           |
| **5. Success Metric**       | - **Thời gian trung bình** từ nhận ticket → phân loại → giao cho bộ phận < **30 phút**.<br>- **Độ chính xác** phân loại ≥ **95%** (so với đánh giá của nhân viên).                                                                                                                                                                   |
| **6. Operational Boundary** | - **AI chỉ được** thực hiện **phân loại, ưu tiên** và **đề xuất routing**.<br>- **Không** cho phép AI **gửi tin nhắn trực tiếp** tới cư dân hoặc **thay đổi trạng thái** ticket mà không có **Human-in-the-Loop** (cần xác nhận của nhân viên).                                                                                      |

---

## 🤖 AI Fit & Architecture

| Component | Recommended Approach |
|---|---|
| **Classification & Prioritization** | **LLM Feature** – Prompt‑engineered LLM (Gemini 2.5 Flash) nhận input (tiêu đề + mô tả + ảnh metadata) → trả về **JSON** `{"category":"...","priority":"high|medium|low"}`. |
| **Routing** | **Rule‑Based** – Dựa trên `category` trả về, hệ thống tự động chuyển ticket vào **queue** tương ứng (Water, Electricity, Security, …). |
| **Chatbot Interaction** | **LLM Feature** – Khi cư dân chọn “Chat với trợ lý”, LLM thực hiện hội thoại, đồng thời **cập nhật** `priority` và `category` trong realtime. |
| **Hotline Escalation** | **Rule** – Nếu cư dân chọn “Gọi Hotline”, hệ thống **đưa ra số điện thoại** và **đánh dấu** ticket là “Escalated”. |
| **Human‑in‑the‑Loop (HITL)** | **Mandatory Review UI** – Nhân viên xem JSON đề xuất, có nút **Approve / Edit** trước khi ticket được chuyển. |

---

## ✅ AI Readiness Checklist

- [x] **Dữ liệu mẫu**: 5 000 ticket lịch sử (đã gán nhãn).  
- [x] **Rủi ro sai phân loại**: Đã thiết lập **fallback** – nếu confidence < 0.7, tự động đưa vào queue “Manual Review”.  
- [x] **Human‑in‑the‑Loop**: Giao diện phê duyệt cho nhân viên, không cho phép AI tự động thay đổi trạng thái.  
- [x] **Tuân thủ bảo mật**: Dữ liệu cư dân được lưu trong **Vinhomes Cloud** với mã hoá AES‑256, không truyền ra ngoài. |

---

## 🚦 Decision & Justification

- **Decision:** **GO** – dự án khả thi, có dữ liệu đủ, rủi ro được kiểm soát bằng HITL và fallback.
- **Justification:**  
  1. **ROI nhanh:** Giảm thời gian xử lý từ 1-3 h xuống < 30 phút, ước tính tiết kiệm **≈ 12 k USD/tháng** nhân lực.  
  2. **Công nghệ sẵn có:** Gemini 3.6 Flash hỗ trợ đa‑modal (text + image metadata) và cho phép low‑temperature prompting để ổn định.  
  3. **Rủi ro thấp:** Các ranh giới an toàn đã được mã hoá trong System Prompt và quy tắc fallback, không có khả năng AI gửi tin trực tiếp tới cư dân.

---

## 📐 Quick Architecture Diagram (Mermaid)

```mermaid
flowchart TD
    A[Resident (authenticated) opens Vinhomes Residents app] --> B{Select request type}
    B -->|Submit complaint| C[Submit ticket (text + optional image)]
    C --> D{AI Router}
    D -->|Low confidence| E[Manual Review Queue]
    D -->|High confidence| F[LLM Classification]
    F --> G{Priority?}
    G -->|High| H[Escalate to Hotline]
    G -->|Medium/Low| I[Route to appropriate department queue]
    H --> J[Hotline number displayed to resident]
    I --> K[Management UI (staff view tickets)]
    K --> L[Staff Approve / Edit classification]
    L --> M[Ticket moves to execution (maintenance, cleaning, …)]
    style A fill:#e3f2fd,stroke:#2196f3,stroke-width:2px
    style K fill:#fff3e0,stroke:#ff9800,stroke-width:2px
```

---

*File này được tạo để đưa vào branch `main` sau khi nhóm đồng ý.*
