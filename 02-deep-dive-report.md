# Lab 02 — Bài Nhóm: Deep-Dive Report & Evaluation

**Nhóm:** VinSmart Scoping Team  
**Ngày hoàn thành:** 29 tháng 5 năm 2026  
**Bài toán được chọn:** Xanh SM - Xử lý yêu cầu thay đổi lộ trình thực tế

---

## 🗳️ Quyết định lựa chọn của nhóm

Nhóm quyết định chọn bài toán **"Xanh SM Xử lý yêu cầu thay đổi lộ trình thực tế"** để thực hiện Deep-Dive vì:
- **Tác động lớn:** Ảnh hưởng trực tiếp đến 150+ yêu cầu thay đổi/ngày
- **Độ khả thi cao:** Dữ liệu GPS, lộ trình, tài xế sẵn có
- **ROI rõ ràng:** Giảm từ 7 phút xuống 90 giây tương đương 15-17 giờ lao động/ngày

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow Mapping

**Quy trình xử lý yêu cầu thay đổi lộ trình hiện tại:**

```text
┌──────────────────┐
│ Bước 1           │
│ Tài xế gọi       │
│ dispatcher báo   │
│ yêu cầu thay đổi │
│ lộ trình         │
│ ⏱ 1 phút         │
│ Ai: Tài xế       │
└────────┬─────────┘
         │
         ▼
┌──────────────────────┐
│ Bước 2 🔴           │
│ Dispatcher tra cứu  │
│ thủ công vị trí hiện│
│ tại, khách hàng     │
│ trong chuyến trên   │
│ hệ thống/bản đồ     │
│ ⏱ 2-3 phút          │
│ Ai: Dispatcher      │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│ Bước 3 🔴           │
│ Dispatcher tìm kiếm │
│ tài xế khác gần     │
│ những điểm liên quan│
│ ⏱ 2-3 phút          │
│ Ai: Dispatcher      │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│ Bước 4              │
│ Viết tin nhắn/gọi   │
│ điện thông báo cho  │
│ các khách hàng      │
│ ⏱ 1-2 phút          │
│ Ai: Dispatcher      │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│ Bước 5              │
│ Update hệ thống     │
│ giao điều xe        │
│ ⏱ 0.5 phút          │
│ Ai: Dispatcher      │
└──────────────────────┘

🔴 = Bottleneck (Bước 2-3 = 4-6 phút = 60% tổng thời gian)
⏱ TỔNG THỜI GIAN: 6.5-9 phút/lượt
📊 TẦN SUẤT: ~150 lượt/ngày tại Hà Nội
💼 IMPACT: 15-22.5 giờ lao động/ngày
```

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM Hà Nội. |
| **2. Current Workflow** | Khi tài xế báo yêu cầu thay đổi điểm đến hoặc đón khách khác giữa chừng, dispatcher phải: (1) Tra cứu thủ công vị trí xe định vị trên bản đồ nội bộ, (2) Mở hệ thống quản lý khách hàng để xác nhận khách trong chuyến hiện tại, (3) Tra cứu danh sách tài xế khá dụng gần những điểm liên quan trên bản đồ, (4) Viết tin nhắn hoặc gọi điện thông báo cho khách hàng cũ/mới về sự thay đổi, (5) Update lộ trình trong hệ thống giao điều. Toàn bộ 5 bước, 100% thủ công, mất 6.5-9 phút/lượt. |
| **3. Bottleneck** | Bước 2-3 (mất 4-6 phút, 60% tổng thời gian): Dispatcher phải tra cứu thủ công trong bản đồ, kiểm tra thông tin khách hàng, và duyệt danh sách tài xế. Việc tra cứu này phụ thuộc vào kinh nghiệm dispatcher, dễ gây sai sót hoặc đề xuất tài xế không tối ưu. |
| **4. Business Impact** | Mỗi ngày có ~150 yêu cầu thay đổi lộ trình tại Hà Nội. Xử lý thủ công gây lãng phí 15-22.5 giờ lao động/ngày của team điều vận. Tăng thời gian chờ đợi của khách hàng (bình quân thêm 5 phút), dẫn đến tăng tỉ lệ hủy chuyến từ 8% lên 10%, gây mất doanh thu ~50M VND/tháng. Dispatcher bị quá tải vào giờ cao điểm (17h-19h), gây stress và sai sót trong dispatcher yêu cầu thay đổi khác. |
| **5. Success Metric** | **KPI 1 - Efficiency:** Giảm thời gian xử lý yêu cầu từ 7.25 phút (trung bình) xuống dưới 90 giây. **KPI 2 - Accuracy:** Tỉ lệ đề xuất tài xế tối ưu (tài xế được chọn là gần nhất với điểm đón mới) đạt 90% (so sánh với giải pháp tối ưu bằng thuật toán). **KPI 3 - Retention:** Tăng tỉ lệ giữ chân khách từ 92% lên 95% (giảm hủy chuyến từ 8% xuống 5%). |
| **6. Operational Boundary** | **ĐƯỢC PHÉP:** AI phân tích vị trí xe (GPS real-time), danh sách khách hàng trong chuyến hiện tại (từ API), danh sách tài xế sẵn sàng (status = "available"), tính toán khoảng cách Euclidean/tuyến đường, tự động soạn nháp tin nhắn hướng dẫn (draft). **TUYỆT ĐỐI KHÔNG ĐƯỢC:** Tự động gửi tin nhắn cho tài xế/khách mà không có dispatcher phê duyệt (bắt buộc Human-in-the-Loop / HITL); Đề xuất tài xế không có bằng lái phù hợp hoặc status = "offline"; Thay đổi giá cước mà không có sự cho phép từ manager; Xóa bỏ thông tin ghi chú từ tài xế hoặc khách. |

---

## 3.3. Future-State Flow & AI Fit

### AI Fit Matrix
**Lựa chọn: LLM Feature + Rule Engine**

- **LLM Feature (Gemini 2.5):** Phân tích yêu cầu từ tài xế (natural language), xác định loại yêu cầu (change destination, add passenger, urgent route), soạn thảo tin nhắn hướng dẫn.
- **Rule Engine:** Kiểm tra ranh giới (chỉ đề xuất tài xế available, khoảng cách < 15km, không thay đổi giá), tính toán tuyến đường tối ưu (Google Maps API).
- **Không dùng Agent:** Vì quyết định dispatch là safety-critical, không được để AI tự quyết định mà cần dispatcher review trước.

### Future-State Workflow

```text
┌──────────────────┐
│ Bước 1           │
│ 🔴 Tài xế gọi  │
│ hoặc tin nhắn    │
│ dispatcher       │
│ ⏱ 1 phút         │
└────────┬─────────┘
         │
         ▼
┌──────────────────────────────┐
│ Bước 2 🔵 AI LAYER           │
│ LLM phân tích yêu cầu tài xế │
│ (xác định: change dest,      │
│  add passenger, urgent)      │
│ Rule Engine kiểm tra scope   │
│ ⏱ <5 giây                    │
│ AI: Gemini + Rules           │
└────────┬─────────────────────┘
         │
         ▼
┌──────────────────────────────┐
│ Bước 3 🔵 AI LAYER           │
│ API tự động lấy:             │
│ - Vị trí xe GPS real-time    │
│ - Danh sách tài xế gần nhất  │
│ - Danh sách khách hiện tại   │
│ ⏱ <2 giây                    │
│ AI: API Query + Rule Engine  │
└────────┬─────────────────────┘
         │
         ▼
┌──────────────────────────────┐
│ Bước 4 🔵 AI LAYER           │
│ LLM soạn thảo nháp tin nhắn  │
│ (Vietnamese, tone friendly)  │
│ Rule Engine kiểm tra ranh giới
│ ⏱ <5 giây                    │
│ AI: Gemini + Rules           │
└────────┬─────────────────────┘
         │
         ▼
┌──────────────────────────────┐
│ Bước 5 🟢 HUMAN LAYER (HITL) │
│ Dispatcher review & approve  │
│ ✅ Approve → Auto send to xế │
│ ❌ Reject → Manual adjust    │
│ ⏱ 30-60 giây (review)        │
│ Ai: Dispatcher               │
└────────┬─────────────────────┘
         │
         ▼
┌──────────────────────────────┐
│ Bước 6                       │
│ Update hệ thống              │
│ ⏱ <1 giây                    │
│ AI: Automation               │
└──────────────────────────────┘

↩️ FALLBACK (Nếu AI recommend sai):
   Dispatcher có thể reject proposal, manual input, 
   và system sẽ log để training improvement.

🎯 TỔNG THỜI GIAN MỚI: 2-3 phút (65-70% tiết kiệm)
🎯 DISPATCHER EFFORT: 50% (chỉ review, không tra cứu)
```

---

# 💻 Phase 4 — PROMPT PROTOTYPE TESTING

File `prompt_prototype.py` (hoặc `prompt_prototype_demo.py`) đã được hoàn thiện để test 2 boundary rules chính:

**Rule 1 (DRAFT_ONLY):** ✅ PASSED  
**Rule 2 (Critical Battery):** ✅ PASSED

Xem chi tiết tại: [starter-code/prompt_prototype.py](../starter-code/prompt_prototype.py)

---

# 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

| Tiêu chí | Trạng thái | Ghi chú |
|----------|-----------|--------|
| **1. Dữ liệu sạch có sẵn?** | ✅ | GPS logs, tài xế status, khách hàng data sẵn có từ system hiện tại |
| **2. Rủi ro khi AI sai?** | ✅ | Kiểm soát tốt qua HITL (dispatcher review) + Fallback (manual override) |
| **3. Stakeholders sẵn sàng?** | ✅ | Team Vận hành Xanh SM đã xác nhận pain point, sẵn sàng feedback |
| **4. Integration plan rõ ràng?** | ✅ | API Gateway, Database connection, Message queue sẵn sàng |
| **5. Rollout strategy?** | ✅ | Pilot tại Hà Nội (150 yêu cầu/ngày) trước khi scale TP.HCM |

---

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

## ✅ **GO (Bắt đầu xây dựng Prototype)**

### Luận điểm kỹ thuật:

**Ưu điểm:**
1. **Scope rõ ràng:** Bài toán có ranh giới rõ (yêu cầu thay đổi lộ trình = input → nháp tin nhắn + tài xế tối ưu = output)
2. **Dữ liệu sẵn có:** GPS, tài xế status, khách hàng info đã có trong system Xanh SM
3. **Rủi ro quản lý tốt:** Human-in-the-Loop đảm bảo dispatcher luôn review trước gửi
4. **ROI cao:** 65-70% tiết kiệm thời gian = 15-22 giờ lao động/ngày × 25 ngày × 5M VND/giờ = **1.875-2.75 tỷ VND/năm**
5. **Precedent thành công:** Các công ty khác (Grab, Be, Gojek) đã áp dụng AI cho dispatcher tương tự

**Rủi ro & Giải pháp:**
1. **Rủi ro:** AI đề xuất tài xế sai (không available, offline)  
   **Giải pháp:** Rule Engine kiểm tra status = "available" real-time, test threshold 90% accuracy
2. **Rủi ro:** Dispatcher không tin AI, vẫn bypass system  
   **Giải pháp:** Training & incentive (KPI: thời gian xử lý, độ chính xác), monitor adoption
3. **Rủi ro:** Integration phức tạp, mất thêm 2-3 tuần  
   **Giải pháp:** Dùng existing API Gateway của Xanh SM, scope MVP chỉ làm "change destination" trước

---

### Ước lượng Chi phí & Timeline

| Hạng mục | Chi phí | Timeline |
|----------|---------|----------|
| Development (4 engineers × 3 tuần) | 1,500 USD | 3 tuần |
| LLM API (Gemini) @ 150 req/day × 30 day | 50 USD/tháng | Ongoing |
| Infrastructure (GPU, database) | 300 USD/tháng | Ongoing |
| Testing & QA (2 tuần) | 700 USD | 2 tuần |
| Pilot Rollout (Hà Nội, 25 days) | 500 USD (overtime, training) | 25 ngày |
| **Total Year 1 (Development + 9 tháng operation)** | **~4,500 USD** | 5 tuần dev + 9 tháng production |

### ROI Calculation
- **Annual Revenue Gain:** 1,875-2,750M VND (từ tiết kiệm lao động + giảm hủy chuyến)
- **Annual Cost:** ~135M VND (LLM API + infra)
- **Net Benefit:** 1,740-2,615M VND
- **Payback Period:** < 1 tháng ✅

---

## 📊 Kết luận

Bài toán **Xanh SM Route Change Optimization** có độ khả thi rất cao:
- ✅ Bài toán được define rõ (6-field problem statement)
- ✅ Dữ liệu sẵn có, không cần thu thập thêm
- ✅ Rủi ro được quản lý qua HITL + Fallback
- ✅ ROI cao (payback < 1 tháng)
- ✅ Stakeholders committed

**Khuyến cáo:** Bắt đầu MVP trong 1-2 tuần với scope hẹp (change destination only), pilot tại Hà Nội trước scaling.

---

**Hoàn thành:** 29 tháng 5 năm 2026  
**Trạng thái:** ✅ GO - Prepared for Prototype Development
