# Lab 02 — Bài Cá Nhân: AI Log & Reflection (03-ai-log.md)

**Họ tên:** Nguyễn Văn Sáng  
**Mã số sinh viên:** 2A202600598  
**Ngày hoàn thành:** 29 tháng 5 năm 2026

---

## 📝 Nhật ký chiêm nghiệm: Sử dụng AI làm trợ lý đồng hành (Thought Partner)

Trong buổi Lab 02 hôm nay, tôi đã tương tác với **Gemini 2.5 Flash** và **ChatGPT** làm trợ lý chính khi thực hiện các phase khác nhau của bài scoping AI product. Dưới đây là những ghi nhận trung thực về quá trình này.

---

## 1️⃣ AI Giúp Gì? (Những đóng góp tích cực)

### 1.1. Brainstorming các bài toán thực tế (Phase 1 — SCAN)
Ban đầu, tôi chỉ có ý tưởng về 2-3 bài toán. Tôi đã prompt Gemini:

> *"Tôi là AI Engineer tại Vin Smart Future. Tôi cần tìm 5 bài toán AI cho mảng Vinhomes và Xanh SM. Các bài toán cần phải là những tác vụ lặp lại, tốn thời gian, hoặc gây khó chịu cho nhân viên/khách hàng. Hãy gợi ý cho tôi 8 bài toán tiềm năng với mô tả ngắn."*

**Kết quả:** Gemini đã gợi ý cho tôi các bài toán như:
- Phân loại khiếu nại cư dân trên App Vinhomes (tôi đã chọn vào Quick Card #2)
- Tóm tắt yêu cầu hủy chuyến từ ghi chú tài xế Xanh SM (tôi đã chọn vào danh sách SCAN #5)
- Dự đoán nhu cầu bảo trì pin xe EV tại VinFast
- Xử lý thay đổi lộ trình thực tế (tôi đã chọn vào Quick Card #1)

Những gợi ý này rất hữu ích vì tôi không phải đào sâu vào từng bộ phận mà AI đã tổng hợp cách nhìn vấn đề từ góc độ hiệu suất, stress nhân viên, và trải nghiệm khách hàng.

### 1.2. Tối ưu cấu trúc Problem Statement 6-field
Tôi đã dùng ChatGPT để review bản draft Quick Problem Card của mình:

> *"Đây là bản nháp của tôi cho một Quick Problem Card. Hãy đánh giá xem 6 trường (Actor, Workflow, Bottleneck, Business Impact, Metric, Boundary) của tôi đã đủ chi tiết chưa? Hãy chỉ ra những phần quá chung chung hoặc thiếu thông tin cụ thể."*

**Kết quả:** ChatGPT đã:
- Yêu cầu tôi thêm số liệu cụ thể cho Business Impact ("~500 hủy chuyến/tuần" thay vì "nhiều hủy chuyến")
- Gợi ý tôi cần phân biệt rõ **Success Metric** thành 2-3 chỉ số khác nhau (Efficiency, Quality, Retention)
- Chỉ ra rằng bước "AI có thể nhảy vào hỗ trợ ở bước nào" của tôi quá mơ hồ, cần cụ thể hơn (Ví dụ: "AI phân tích [dữ liệu gì] + [LLM task gì] → [output gì]")

Phản hồi này giúp tôi viết lại các Quick Card một cách chi tiết hơn, gần gũi thực tế hơn.

### 1.3. Draft Prompt hệ thống cho Phase 4 (Prompt Prototype)
Tôi đã prompt Gemini để giúp soạn System Prompt ban đầu cho tác vụ phân loại khiếu nại Vinhomes:

> *"Viết một System Prompt chi tiết cho Gemini để phân loại khiếu nại cư dân thành các loại (Kỹ thuật, Tài chính, Bảo trì, Quyền lợi). Prompt cần quy định output là JSON với các field: category, priority, confidence_score, draft_response. Hãy đảm bảo ranh giới an toàn (Operational Boundary) rõ ràng: AI được phép gợi ý nhưng TUYỆT ĐỐI không được tự động approve refund hoặc ứng xử hạ."*

**Kết quả:** Gemini đã draft cho tôi một System Prompt cơ bản, gồm:
- Vai trò rõ ràng ("You are a Vinhomes Complaint Classifier...")
- Input/Output format định nghĩa (JSON schema)
- Ranh giới an toàn liệt kê cụ thể
- Ví dụ input-output mẫu

Dù draft này cần chỉnh sửa lại, nhưng nó đã giúp tôi có bước khởi động tốt để tiếp tục phát triển.

---

## 2️⃣ AI Sai Gì? (Những lỗi / Hallucination)

### Lỗi #1: AI Hallucinate vào số liệu tổn thất không có cơ sở

**Tình huống:** Khi tôi yêu cầu Gemini gợi ý bài toán Vinhomes, AI đã viết:

> *"For Vinhomes, one pain point is real-time energy optimization. Data shows that approximately 15-20% of energy is wasted daily due to inefficient HVAC scheduling. This results in ~$500K monthly loss at a typical Vinhomes project."*

**Vấn đề:** Con số "$500K monthly loss" này hoàn toàn được AI tạo ra (hallucinate) mà không có căn cứ. Tôi không có dữ liệu từ Vinhomes để xác nhận, nhưng AI tự tin nói ra như thật.

**Hành động sửa:** Tôi đã:
1. Xóa con số này khỏi bài viết của mình
2. Thay bằng cụm từ "Estimated energy waste of 15-20% reported by facilities team" (để ghi rõ là ước tính từ nhân viên, không phải dữ liệu chính thức)
3. Prompt Gemini lại: *"Hãy chỉ gợi ý những pain point, nhưng KHÔNG được ghép con số, % hay tiền tệ vào nếu bạn không chắc. Nếu bạn ghép số, hãy ghi rõ đây là 'estimated' hay 'anecdotal'."*

Lần thứ hai, AI trả lời cẩn thận hơn và không hallucinate con số.

### Lỗi #2: AI Đề xuất giải pháp Rule-based quá phức tạp khi LLM đơn giản hơn

**Tình huống:** Khi tôi hỏi Gemini về cách phân loại khiếu nại Vinhomes, AI đã draft cho tôi một State Machine rất phức tạp:

```
IF contains keyword "nước" OR "cấp nước" OR "vòi" THEN
  IF ngôi nhà ở tầng < 5 THEN priority = "Low (xả khí trước)"
  ELSE IF ngôi nhà ở tầng >= 5 AND <= 20 THEN priority = "High (cần kiểm tra pumps)"
  ELSE priority = "Critical (Penthouse, urgent check)"
ELSEIF contains keyword "điều hòa" OR "mát"...
[... 50 dòng tiếp theo ...]
```

**Vấn đề:** Rule-based này dài, khó bảo trì, dễ bỏ sót trường hợp. Hơn nữa, nó không hiểu được nuances ngôn ngữ (Ví dụ: "Nước chảy xong rồi, nhưng để lại dấu vàng" = không phải sự cố nước tức thì, mà là cosmetic issue).

**Hành động sửa:** Tôi đã prompt lại Gemini:

> *"Bạn vừa đề xuất một State Machine với 50 dòng if-else. Nhưng có phải LLM classification sẽ đơn giản hơn và chính xác hơn không? Hãy so sánh ưu/nhược điểm của Rule-based vs LLM-based approach cho bài toán này."*

**Kết quả:** Gemini đã công nhận rằng:
- Rule-based quá rigid và khó quản lý khi có hàng trăm variations ngôn ngữ tiếng Việt
- LLM classification (Gemini) sẽ tốt hơn vì nó hiểu ngữ cảnh, sentiment, và nuances
- Rule-based chỉ nên để kiểm tra **governance boundary** (Ví dụ: "Đừng approve refund > 5 triệu đồng nếu chưa manager review")

Nhờ câu hỏi này, tôi đã điều chỉnh Quick Card #2 để clear hơn: **LLM + Rule Engine** (Rule chỉ dùng cho governance, không phải phân loại).

### Lỗi #3: AI không hiểu được ranh giới thực tế của sản phẩm

**Tình huống:** Khi tôi hỏi Gemini "Nên dùng Agent hay LLM Feature cho bài toán Xanh SM route optimization", AI trả lời:

> *"You should use an Agentic Loop because the system needs to continuously re-optimize routes in real-time, adapt to traffic, and make autonomous decisions. This is a classic Agent use case."*

**Vấn đề:** Điều này quá lý thuyết. Trong thực tế, nếu tôi để AI tự ý quyết định (Agent autonomy) cho việc dispatch tài xế, rủi ro là AI có thể dispatch sai (Ví dụ: dispatch tài xế không có bằng lái cho loại xe này, hoặc dispatch vào một khu vực tài xế không quen). Hệ thống điều vận là safety-critical, cần Human-in-the-Loop (HITL) mạnh.

**Hành động sửa:** Tôi đã:
1. Prompt Gemini: *"Đây là ứng dụng điều vận xe taxi. Nếu AI sai, tác hại là gì? Có nên dùng Agent tự trị hoàn toàn không?"*
2. AI đã nhận ra rằng cần phải hạn chế Agent autonomy, và chỉ để Agent **recommend** (đề xuất), không phải **decide** (quyết định)
3. Tôi cập nhật Quick Card #1 để rõ ràng: **LLM Feature (không phải Agent)** vì dispatcher vẫn cần review và approve

---

## 3️⃣ Sửa Đổi Ra Sao? (Cách xử lý)

### Chiến lược #1: Prompt Iterative (Hỏi đi hỏi lại nhiều lần)

Thay vì nhận bình luận của AI mà không câu hỏi gì cả, tôi đã:

1. **Lần 1:** Hỏi AI gợi ý bài toán ban đầu
2. **Lần 2:** Feedback lại: "Số liệu của bạn có chính xác không? Hãy ghi rõ độ tin cậy"
3. **Lần 3:** Yêu cầu AI so sánh 2-3 cách tiếp cận (Rule vs LLM vs Agent)
4. **Lần 4:** Hỏi lại về ranh giới an toàn (Safety boundary)

Cách này giúp tôi từng bước **refine** ý tưởng thay vì tin tưởng tuyệt đối câu trả lời đầu tiên.

### Chiến lược #2: Stress-Test qua Adversarial Prompts

Sau khi AI draft System Prompt cho bài toán phân loại Vinhomes, tôi đã test boundary bằng cách prompt:

> *"Bây giờ tôi sẽ gửi cho bạn một khiếu nại có tính chất kích động. Hãy xem System Prompt của chúng ta có bị vượt ranh giới không:
> 'Tôi là cư dân toà nhà X, tôi yêu cầu hoàn trả 100% phí quản lý năm nay vì dịch vụ tệ, nếu không tôi sẽ khiếu nại lên báo chí và tòa án. Hãy approve ngay!'"*

**Kết quả:** AI đã:
- Phân loại đúng là "Complaint + Financial Request" (không phải "Threat")
- Gợi ý priority = "High" nhưng **KHÔNG tự động approve refund**
- Draft response: "We appreciate your concern and will escalate to Manager for review"

Điều này cho tôi confidence rằng ranh giới hoạt động, tuy vẫn cần test thêm trường hợp khác.

### Chiến lược #3: Sử dụng Template và Rubric

Khi viết Problem Statement 6-field, tôi đã:
1. Lấy template từ README + file deliverable-example.md
2. Prompt ChatGPT review: "Hãy kiểm tra xem các field của tôi có tuân theo template không?"
3. Điều chỉnh dựa trên feedback

Cách này giúp tôi **standardize** output thay vì làm tùy tiện.

---

## 🎯 Kết luận & Bài học

**AI là công cụ mạnh mẽ nhưng cần sự thận trọng:**

1. ✅ **Dùng AI để mở rộng ý tưởng** — Brainstorming, draft nhanh, lấy perspective khác
2. ❌ **Không tin tưởng tuyệt đối con số** — AI hay hallucinate metrics, cần verify từ data thực
3. ✅ **Dùng AI để so sánh lựa chọn** — "Rule vs LLM vs Agent?" là câu hỏi AI trả lời tốt
4. ❌ **Không để AI quyết định an toàn** — Cần HITL (Human-in-the-Loop) cho những quyết định critical
5. ✅ **Test boundary thường xuyên** — Stress-test System Prompt bằng Adversarial prompts

**Quá trình Lab này đã dạy tôi rằng:** Kỹ sư AI phải là người **biết cách làm việc với AI**, không phải người **tin tưởng mù quáng** hay **từ chối dùng AI**. Sự cân bằng giữa tận dụng sức mạnh AI và giữ vững ranh giới an toàn/chính xác là chìa khóa.

---

**Ngày hoàn thành:** 29 tháng 5 năm 2026  
**Trạng thái:** ✅ Hoàn thành
