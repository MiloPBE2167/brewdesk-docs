# Product Principles

> 12 principles. Reference by number in arguments.

---

## 1. Tối giản (minimal)

Bắt đầu với version đơn giản nhất chứng minh được concept. Add complexity khi có evidence cần, không phải khi tưởng tượng cần.

## 2. Ship to learn

Code chưa ship = chưa học. Real user touching real product là source of truth. Speculation về user behavior không thay thế observation.

## 3. Validate before build (relaxed for P3)

Build phải có hypothesis cần test. Trong P3 portfolio context, hypothesis có thể bao gồm "technical signal đủ mạnh cho recruiter."

## 4. Each schema has a reason

Mỗi table, mỗi column, mỗi index phải defend được bằng use case cụ thể. Thêm sau dễ hơn xoá sau (migration cost, data backfill).

## 5. Safety is product

Trong app có user-generated content (chat), moderation = product feature, không phải afterthought. Beta v2 phải có LLM moderation từ ngày 1.

## 6. No monetization theater

Không add premium tier, ads, hay paywall đến khi product có signal mạnh. Fake monetization khi không ai dùng = thời gian lãng phí + signal tệ cho recruiter.

## 7. Founder bandwidth is finite

Khánh là 1 người, intern 40h + project 15-20h. Mỗi feature, mỗi file docs, mỗi tool tích hợp = maintenance tax. Cân nhắc trước khi add.

## 8. Decisions logged with reasoning

Mọi locked decision phải vào `03-decisions-log.md` với reasoning. 3 tháng sau Khánh nhìn lại sẽ không nhớ tại sao chose Singapore region — log để nhớ.

## 9. Disagreement healthy, gridlock not

Claude và Khánh pushback lẫn nhau là feature. Nhưng sau pushback round, phải lock decision và move on. Tránh re-litigate.

## 10. User is not us

Khánh không phải target user (không học ở café thường xuyên ở US). User feedback > Khánh intuition khi conflict.

## 11. Recruiter signal first

P3 lens. Mọi decision đánh giá qua: "Khánh giải thích cái này trong 2 phút interview thế nào?" Nếu không có câu trả lời tốt → signal yếu → reconsider.

## 12. Build > polish

Done > perfect. Ship > pretty. Trong P3 với time budget tight, hoàn thành 80% feature > polish 100% feature. Polish ở Phase 10 (Portfolio polish), không trước.