# Lab 02 — Deep-Dive Report: Vinmec Discharge Summary Automation

> **Nhóm:** A1
> **Bài toán chọn:** Card #1 — Vinmec Tóm tắt hồ sơ xuất viện
> **Ngày:** 29/05/2026
> **Thành viên:**
> | STT | Họ và tên | MSSV |
> |-----|-----------|------|
> | 1 | Lê Quang Thọ | 2A202600597 |
> | 2 |Nguyễn Văn Sáng |2A202600598 |
> | 3 | Phạm Mai Anh | 2A202600644 |
> | 4 | Phạm Ngọc Hải Dương | 2A202600629 |

---

## 🏗️ Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping

Quy trình viết tóm tắt hồ sơ xuất viện hiện tại tại Vinmec:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Mở hồ sơ     │     │ Ghi chép     │     │ Tổng hợp     │     │ Soạn hướng   │
│ bệnh án      │ ──→ │ diễn biến    │ ──→ │ kết quả xét  │ ──→ │ dẫn xuất     │
│ điện tử      │     │ điều trị     │     │ nghiệm       │     │ viên & tái   │
│              │     │              │     │              │     │ khám         │
│ Ai: Bác sĩ   │     │ Ai: Bác sĩ   │     │ Ai: Bác sĩ   │     │ Ai: Bác sĩ   │
│ ⏱ 3 phút     │     │ ⏱ 8 phút 🔴  │     │ ⏱ 7 phút 🔴  │     │ ⏱ 7 phút 🔴  │
│ In: Hồ sơ    │     │ In: Dữ liệu  │     │ In: KQXN     │     │ In: Raw data │
│ Out: Dữ liệu │     │ Out: Ghi chú │     │ Out: Tổng hợp│     │ Out: Bản tóm │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                       │
                                                                       ▼
                                                                ┌──────────────┐
                                                                │ Bước 5       │
                                                                │ Trình ký     │
                                                                │ & nộp hành   │
                                                                │ chính        │
                                                                │ Ai: Bác sĩ   │
                                                                │ ⏱ 2 phút     │
                                                                └──────────────┘
🔴 = Bottlenecks (Bước 2-4: chiếm 22/27 phút)
⏱ Tổng thời gian xử lý thủ công: 27 phút/lượt.
Số lượng: ~40 bệnh nhân xuất viện/ngày tại 1 bệnh viện Vinmec → 18 giờ làm việc/ngày.
```

---

### 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Bác sĩ điều trị tại các khoa Nội, Ngoại, Sản của bệnh viện Vinmec. |
| **2. Current Workflow** | Bác sĩ mở hồ sơ bệnh án điện tử (từ hệ thống HIS), đọc lại toàn bộ diễn biến điều trị, ghi chép tóm tắt thủ công, tổng hợp kết quả xét nghiệm và chẩn đoán hình ảnh, soạn hướng dẫn xuất viện và tái khám, sau đó trình ký và nộp phòng hành chính. 5 bước, thủ công, mất trung bình 27 phút/lượt. |
| **3. Bottleneck** | Bước 2-3-4 (22 phút): Bác sĩ phải đọc lại toàn bộ hồ sơ dài, lọc thông tin quan trọng, diễn giải kết quả xét nghiệm thành văn bản, và soạn hướng dẫn xuất viện phù hợp. Đây là tác vụ xử lý ngôn ngữ tự nhiên mà AI có thể hỗ trợ. |
| **4. Business Impact** | Mỗi bệnh viện Vinmec có ~40 ca xuất viện/ngày, tổng cộng 18 giờ làm việc của bác sĩ/ngày chỉ cho việc viết tóm tắt. Với 5 bệnh viện Vinmec trên toàn quốc, tổng lãng phí ~90 giờ/ngày. Chi phí cơ hội: bác sĩ có thể dùng thời gian đó để khám và điều trị thêm 15-20 bệnh nhân/ngày. |
| **5. Success Metric** | 1. Giảm thời gian viết tóm tắt từ 27 phút xuống dưới 5 phút (Efficiency).<br>2. Tỉ lệ bác sĩ chấp nhận bản draft mà không cần chỉnh sửa lớn đạt 85% (Quality).<br>3. Giảm tỉ lệ sai sót thông tin trong tóm tắt xuất viện (thiếu thuốc, sai liều) từ 5% xuống dưới 1%. |
| **6. Operational Boundary** | AI được phép đọc dữ liệu hồ sơ bệnh án từ HIS, tự động draft bản tóm tắt xuất viện và hướng dẫn tái khám. **CẤM:** AI không được tự động ký hoặc gửi bản tóm tắt mà không có bác sĩ review (bắt buộc HITL). AI không được tự ý thay đổi thông tin chẩn đoán hoặc liều thuốc. AI không được truy xuất hồ sơ bệnh nhân ngoài phạm vi khoa điều trị. |

---

### 3.3. Future-State Flow & AI Fit

#### AI Fit Assessment

| Tiêu chí | Đánh giá |
|---|---|
| Quy trình có cấu trúc cố định? | ✅ Có — Quy trình viết tóm tắt xuất viện theo template chuẩn của Vinmec. |
| Cần xử lý ngôn ngữ tự nhiên? | ✅ Có — Đọc hiểu hồ sơ, tóm tắt diễn biến, sinh văn bản hướng dẫn. |
| Rủi ro khi AI sai? | Cao — Sai thông tin thuốc hoặc chẩn đoán có thể ảnh hưởng sức khỏe bệnh nhân. |
| Cần HITL? | ✅ Bắt buộc — Bác sĩ phải review và ký duyệt trước khi gửi. |

**Kết luận AI Fit:** **LLM Feature** (không phải Agent tự trị vì rủi ro y khoa cao, cần con người trong vòng lặp ở mọi bước).

#### Future-State Flow

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận yêu     │     │ 🔵 AI tự     │     │ 🟢 Bác sĩ    │     │ Bác sĩ ký    │
│ cầu xuất     │ ──→ │ động đọc hồ  │ ──→ │ review chỉnh │ ──→ │ & nộp hành   │
│ viện từ      │     │ sơ HIS và    │     │ sửa bản draft│     │ chính        │
│ khoa          │     │ draft tóm tắt│     │ (HITL)       │     │              │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                    │
                                                    ▼
                                             ↩️ Fallback:
                                             Nếu AI không tự tin
                                             (< 80% confidence),
                                             bác sĩ tự viết tay
                                             như quy trình cũ.
```

🔵 = AI Step
🟢 = Human-in-the-Loop
↩️ = Fallback

**So sánh trước-sau:**

| Chỉ số | Trước (Thủ công) | Sau (AI + HITL) |
|---|---|---|
| Thời gian/lượt | 27 phút | < 5 phút |
| Số bệnh nhân xử lý/ngày/bác sĩ | 8-10 ca | 25-30 ca |
| Sai sót thông tin | ~5% | < 1% (nhờ AI template chuẩn) |
| Bác sĩ review time | 27 phút viết | 3 phút review chỉnh sửa |

---

### 3.4. Prompt Prototype Integration

Xem chi tiết tại file [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py) — module prompt được xây dựng cho bài toán Xanh SM sự cố sạc pin (theo worked example) nhưng áp dụng cùng kỹ thuật Operational Boundary enforcement cho bài toán Vinmec.

---

## 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
   - Vinmec có sẵn kho hồ sơ bệnh án điện tử HIS với >10,000 mẫu tóm tắt xuất viện đã được bác sĩ phê duyệt.

2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
   - Có: Bác sĩ bắt buộc review từng bản draft trước khi gửi. Fallback tự động khi AI < 80% confidence.

3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?
   - Bác sĩ đã phàn nàn về thủ tục hành chính nặng nề. Họ sẵn sàng dùng AI nếu giảm tải được 80% thời gian.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**

Bài toán tóm tắt hồ sơ xuất viện tại Vinmec đạt đủ điều kiện GO vì:

1. **Tác động kinh doanh rõ ràng:** Tiết kiệm 90 giờ làm việc của bác sĩ/ngày trên toàn hệ thống Vinmec, tương đương ~3.5 tỷ VND/năm chi phí cơ hội.
2. **Dữ liệu sẵn có:** Hệ thống HIS của Vinmec đã lưu trữ cấu trúc hồ sơ bệnh án, sẵn sàng làm đầu vào cho LLM.
3. **Rủi ro thấp nhờ HITL:** Bác sĩ review trước khi gửi giúp kiểm soát chất lượng, fallback khi AI không tự tin.
4. **Công nghệ phù hợp:** LLM Feature (không cần Agent phức tạp) — sử dụng Gemini 2.5 Flash với operational boundaries chặt chẽ.
5. **Stakeholder ủng hộ:** Bác sĩ đang quá tải và sẵn sàng áp dụng công nghệ giảm gánh nặng hành chính.

**Kế hoạch triển khai:**
- **Tháng 1-2:** Xây dựng MVP draft tại 1 khoa (Nội tổng hợp) với 5 bác sĩ pilot.
- **Tháng 3:** Mở rộng lên 3 khoa, thu thập feedback, tinh chỉnh prompt.
- **Tháng 4-5:** Triển khai toàn bệnh viện Vinmec Times City.
- **Tháng 6:** Đánh giá và mở rộng ra các bệnh viện Vinmec khác.
