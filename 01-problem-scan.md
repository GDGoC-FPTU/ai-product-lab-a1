# Lab 02 — Problem Scan & Quick Cards

> **Họ và tên:** Lê Quang Thọ
> **Vai trò:** AI Engineer tại Vin Smart Future
> **Ngày:** 29/05/2026

---

# 🔍 Phase 1 — SCAN

Sử dụng **4 Lenses** quét qua vận hành các công ty thành viên Vingroup.

## Danh sách bài toán

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **Vinmec** | Lặp lại | Bác sĩ phải viết tóm tắt hồ sơ xuất viện thủ công cho từng bệnh nhân, nội dung có cấu trúc lặp lại nhưng tốn 20-30 phút/lượt. |
| 2 | **Vinhomes** | Tốn thời gian | Nhân viên CSKH phải đọc và phân loại thủ công hàng trăm khiếu nại/feedback của cư dân mỗi ngày, mất 3-5 phút/phản hồi. |
| 3 | **VinFast** | AI-upgrade | Khách hàng gọi tổng đài hỏi về lịch bảo dưỡng, tình trạng đơn hàng phụ tùng — câu trả lời rập khuôn, thời gian chờ lâu. |
| 4 | **Xanh SM** | Pain từ người khác | Tài xế phàn nàn về việc hệ thống gợi ý điểm đón khách không tối ưu vào giờ cao điểm, gây di chuyển lãng phí và hao pin. |
| 5 | **Vinpearl** | Lặp lại | Nhân viên lễ tân xử lý thủ công các yêu cầu đặt phòng nhóm và các yêu cầu đặc biệt (dị ứng thực phẩm, phòng gần nhau) qua email. |
| 6 | **Vinmec** | Tốn thời gian | Điều dưỡng gọi điện xác nhận lịch tái khám cho từng bệnh nhân, mất 3-5 phút/cuộc gọi, khoảng 200 cuộc gọi/ngày. |

---

# 🃏 Phase 2 — QUICK-ASSESS

Chọn **top 3 bài toán** từ danh sách SCAN:
- **Card #1** — Vinmec: Tóm tắt hồ sơ xuất viện
- **Card #2** — Vinhomes: Phân loại khiếu nại cư dân
- **Card #3** — Vinmec: Xác nhận lịch tái khám

---

## Card #1 — Vinmec Tóm tắt hồ sơ xuất viện

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Bác sĩ mất 20-30 phút viết tóm tắt hồ sơ xuất     │
│ viện cho mỗi bệnh nhân, gây chậm tiến độ làm thủ tục.       │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau? Bác sĩ điều trị (quá tải), Bệnh nhân (chờ đợi) │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Bác sĩ mở hồ sơ bệnh án giấy và điện tử               │
│   → 2. Ghi chép tóm tắt diễn biến điều trị thủ công         │
│   → 3. Liệt kê kết quả xét nghiệm, chẩn đoán                │
│   → 4. Soạn hướng dẫn xuất viện và tái khám                │
│   → 5. Trình ký và nộp lại cho phòng hành chính             │
│                                                             │
│ Bước nào tốn nhất? Bước 2-4 (⏱ 15-20 phút/lượt)             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-4              │
│ (AI draft tóm tắt từ dữ liệu hồ sơ, bác sĩ chỉ review)     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian viết tóm tắt từ 25 phút ──> dưới 5 phút.     │
│                                                             │
│ Quick Architecture: [x] LLM Feature (AI draft + HITL)       │
└─────────────────────────────────────────────────────────────┘
```

---

## Card #2 — Vinhomes Phân loại khiếu nại cư dân

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Nhân viên CSKH Vinhomes mất 3-5 phút để đọc,     │
│ phân loại, và route từng khiếu nại của cư dân.              │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Nhân viên CSKH (quá tải), Cư dân (phản hồi     │
│ chậm 12-24h)                                                │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Nhận feedback từ App Vinhomes Resident / Email        │
│   → 2. Đọc nội dung và xác định loại (kỹ thuật/dịch vụ/phí) │
│   → 3. Tra cứu thủ công danh sách ban quản lý phụ trách     │
│   → 4. Chuyển tiếp email nội bộ kèm ghi chú                 │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 3-4 phút/lượt)              │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (Tự động phân loại sentiment + nội dung → route đúng ban)   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xử lý từ 5 phút ──> dưới 30 giây/phản hồi.   │
│ Tỉ lệ route đúng phòng ban đạt 95%.                         │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Classification + Route)│
└─────────────────────────────────────────────────────────────┘
```

---

## Card #3 — Vinmec Xác nhận lịch tái khám

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Điều dưỡng gọi ~200 cuộc gọi/ngày để xác nhận     │
│ lịch tái khám, tốn 5-7 giờ lao động mỗi ngày.               │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau? Điều dưỡng (mệt mỏi, lặp lại), Bệnh nhân       │
│ (quên lịch do gọi điện thoại dễ bỏ lỡ)                     │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Điều dưỡng lấy danh sách bệnh nhân hẹn tái khám từ hệ  │
│   → 2. Gọi điện cho từng bệnh nhân, nhắc lịch               │
│   → 3. Ghi chú kết quả (xác nhận/hủy/dời) vào sổ tay       │
│   → 4. Cập nhật lại hệ thống nếu có thay đổi                │
│                                                             │
│ Bước nào tốn nhất? Bước 2-3 (⏱ 3-5 phút/cuộc gọi)          │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (AI voice call hoặc SMS/App automation tự động xác nhận)    │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian xác nhận từ 5 phút ──> dưới 1 phút/bệnh nhân.│
│ Tỉ lệ bệnh nhân đến tái khám đúng hẹn tăng 20%.            │
│                                                             │
│ Quick Architecture: [x] Rule (Call automation script)        │
│                     [x] LLM (Smart voice/SMS nếu cần)        │
└─────────────────────────────────────────────────────────────┘
```
