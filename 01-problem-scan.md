# Lab 02 — Bài Cá Nhân: Problem Scan & Quick Assessment

---

## 🏛️ Bối cảnh: Tôi là ai?

Tôi là **AI Engineer Trainee** tại **Vin Smart Future**. Trong buổi Lab hôm nay, tôi được giao nhiệm vụ tìm kiếm các cơ hội tối ưu hóa bằng trí tuệ nhân tạo cho các công ty thành viên Vingroup. Thông qua khảo sát, phỏng vấn nhân viên, và quan sát quy trình vận hành thực tế, tôi đã xác định được một số bottleneck quan trọng cần được cải thiện.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **VinFast** | Lặp lại | Kiểm tra và so khớp tất cả hóa đơn sạc điện hằng tuần giữa hệ thống trạm sạc và hệ thống kế toán VinFast (mất 8 tiếng/tuần). |
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên xử lý thủ công các yêu cầu đặc biệt từ tài xế khi cần thay đổi lộ trình hoặc đón khách khác giữa chừng (mất 5-7 phút/lượt, ~150 lượt/ngày). |
| 3 | **Vinhomes** | AI-upgrade | Hệ thống phân loại tự động các khiếu nại/yêu cầu từ cư dân trên App, nhưng hiện tại toàn bộ được xử lý thủ công bởi CSKH team (phản hồi trung bình mất 8-12 giờ). |
| 4 | **Vinmec** | Pain từ người khác | Bác sĩ phải mất 20-30 phút mỗi lần viết tóm tắt hồ sơ xuất viện cho bệnh nhân, gây quá tải công việc hành chính đối với đội bác sĩ (bác sĩ phàn nàn là công việc hành chính chiếm 40% thời gian làm việc). |
| 5 | **Xanh SM** | Tốn thời gian | Tóm tắt lý do khách hàng hủy chuyến từ ghi chú của tài xế và cuộc gọi ghi âm, để phân tích pattern và cải thiện retention (mỗi tuần có ~500 hủy chuyến, mất 3 ngày để tóm tắt). |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ danh sách SCAN: **#2 (Xanh SM Thay đổi lộ trình), #3 (Vinhomes Phân loại khiếu nại), #4 (Vinmec Tóm tắt xuất viện).**

---

## 📇 Thẻ bài toán #1: Xanh SM Xử lý yêu cầu thay đổi lộ trình thực tế

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM yêu cầu thay đổi điểm đến hoặc    │
│ đón khách khác giữa chừng. Điều phối viên phải xử lý thủ   │
│ công để tìm tài xế khác, cập nhật lộ trình, và thông báo   │
│ cho các khách hàng liên quan.                              │
│                                                             │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau (Actor)? 2 nhóm đau:                            │
│ - Điều phối viên (quá tải, phải xử lý nhiều vào giờ cao    │
│   điểm)                                                    │
│ - Khách hàng (chờ lâu hơn, có thể bị hủy chuyến nếu xử lý │
│   chậm)                                                    │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Dispatcher nhận cuộc gọi/tin nhắn từ tài xế yêu cầu   │
│      thay đổi điểm đến                                      │
│   → 2. Tra cứu thủ công khách hàng hiện tại trong chuyến để│
│      xác nhận lộ trình                                      │
│   → 3. Tìm kiếm tài xế khác gần những điểm đó để nhận      │
│      chuyến mới                                             │
│   → 4. Viết tin nhắn hoặc gọi điện thông báo cho các      │
│      khách hàng liên quan về sự thay đổi                   │
│   → 5. Cập nhật hệ thống giao điều xe                       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 5-7 phút/lượt) │
│ vì phải tra cứu thủ công trong bản đồ, kiểm tra trạng      │
│ thái tài xế, và xác nhận tính khả dụng.                     │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ Bước 2-3: AI có thể tự động phân tích lộ trình hiện tại,   │
│ xác định tài xế khả dụng gần nhất (dựa trên GPS thực thời), │
│ tính toán lộ trình mới, và đề xuất tài xế thay thế trong   │
│ vòng vài giây.                                             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ 1. Giảm thời gian xử lý từ 7 phút ──> dưới 90 giây.        │
│ 2. Tỉ lệ yêu cầu thay đổi được xử lý tự động (không cần   │
│    can thiệp thủ công) đạt 85%.                            │
│ 3. Tăng tỉ lệ giữ chân khách từ 92% ──> 96%.              │
│                                                             │
│ Quick Architecture: [x] LLM (Gemini phân tích lộ trình,    │
│ đề xuất tài xế) + Rule Engine (Kiểm tra ranh giới địa lý)  │
└─────────────────────────────────────────────────────────────┘
```

---

## 📇 Thẻ bài toán #2: Vinhomes Phân loại và route tự động khiếu nại cư dân

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Hàng trăm khiếu nại/yêu cầu từ cư dân trên App   │
│ Vinhomes Resident (hỏng điều hòa, gián đoạn nước, thanh    │
│ toán quỹ bảo trì, etc.). Hiện tại CSKH phải phân loại,     │
│ ưu tiên, và route thủ công cho bộ phận kỹ thuật/kế toán.   │
│                                                             │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - CSKH Specialist (quá tải, phản hồi chậm)                 │
│ - Cư dân (đợi 8-12 giờ mới được phản hồi)                  │
│ - Bộ phận kỹ thuật (nhận được khiếu nại lẫn lộn, không rõ   │
│   ưu tiên)                                                 │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi khiếu nại/yêu cầu qua App                  │
│   → 2. CSKH Specialist đọc toàn bộ tin nhắn và phân loại   │
│      thủ công (Sự cố kỹ thuật? Tài chính? Bảo trì?)        │
│   → 3. CSKH Specialist xác định mức độ ưu tiên (Cấp tốc,   │
│      bình thường, hay tái tục)                              │
│   → 4. CSKH ghi chú và forward qua email/Jira cho bộ phận   │
│      chuyên môn                                             │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 10-15 phút    │
│ cho một lô 10 khiếu nại) vì phải đọc kỹ ngôn ngữ tự nhiên, │
│ xác định ý định cư dân, và xác định quyền hạn xử lý.       │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ Bước 2-3: LLM tự động phân loại khiếu nại theo danh mục    │
│ (Kỹ thuật, Tài chính, Bảo trì, Quyền lợi cư dân), xác định │
│ mức độ ưu tiên dựa trên từ khóa/sentiment, và draft tin    │
│ phản hồi ban đầu (nếu là FAQ).                              │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ 1. Thời gian phân loại giảm từ 15 phút ──> 30 giây/lô 10.  │
│ 2. Độ chính xác phân loại đạt 92% (so với phân loại thủ     │
│    công từ CSKH chuyên gia).                               │
│ 3. Thời gian phản hồi ban đầu giảm từ 8 giờ ──> dưới 1     │
│    giờ.                                                    │
│                                                             │
│ Quick Architecture: [x] LLM (Gemini phân tích text khiếu   │
│ nại, phân loại, draft phản hồi) + Rule Engine (Kiểm tra    │
│ SLA, ưu tiên theo level)                                   │
└─────────────────────────────────────────────────────────────┘
```

---

## 📇 Thẻ bài toán #3: Vinmec Tóm tắt hồ sơ xuất viện tự động

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Bác sĩ Vinmec phải mất 20-30 phút sau mỗi ca     │
│ khám để viết tóm tắt hồ sơ xuất viện (Discharge Summary)   │
│ của bệnh nhân. Công việc này tốn thời gian, có thể gây     │
│ lỗi bệnh sử, và khiến bác sĩ quá tải công việc hành chính. │
│                                                             │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau (Actor)?                                        │
│ - Bác sĩ (công việc hành chính chiếm 40% thời gian, stress)│
│ - Bệnh nhân (chờ lâu hơn để nhận giấy xuất viện)           │
│ - Viện (chậm tiêu hủy hồ sơ bệnh nhân, ảnh hưởng archiving)│
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ hoàn thành khám và ghi chú trong EMR (Electronic│
│      Medical Record)                                        │
│   → 2. Bác sĩ viết tóm tắt xuất viện bằng tay hoặc gõ trên │
│      hệ thống                                               │
│   → 3. Bác sĩ kiểm tra lại để đảm bảo không có lỗi bệnh    │
│      sử hoặc liều dùng thuốc                                │
│   → 4. Hộ tá xuất bản và gửi cho bệnh nhân                  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ 20-30 phút)     │
│ vì phải tổng hợp thông tin từ nhiều phần (chẩn đoán, điều   │
│ trị, kết quả xét nghiệm, đơn thuốc) thành một đoạn văn rõ │
│ ràng, chính xác, tuân theo chuẩn y khoa.                   │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                       │
│ Bước 2: LLM tự động tóm tắt các note từ EMR, xuất hóa    │
│ thành một bản tóm tắt xuất viện (draft) theo template chuẩn │
│ y khoa. Bác sĩ chỉ cần xem lại và chỉnh sửa nếu cần.       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│ 1. Thời gian viết tóm tắt giảm từ 25 phút ──> dưới 3 phút  │
│    (gồm review+edit).                                       │
│ 2. Tỉ lệ accuracy (xác nhận bởi bác sĩ chỉnh sửa) đạt 96%. │
│ 3. Tăng thời gian bác sĩ can thiệp lâm sàng lên 10 phút/ca. │
│                                                             │
│ Quick Architecture: [x] LLM (Gemini đọc note từ EMR, tóm    │
│ tắt theo template) + Rule Engine (Kiểm tra bắt buộc có      │
│ chẩn đoán, thuốc, ngày tái khám)                            │
└─────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định lựa chọn của nhóm (được cập nhật sau khi thảo luận)

*Phần này sẽ được cập nhật khi nhóm thảo luận và chọn 1 trong 3 bài toán trên để thực hiện Deep-Dive.*
