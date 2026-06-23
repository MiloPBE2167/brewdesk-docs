# Retrospective — Phase 1: Foundation

_Viết: 2026-06-23 · Phase 1 window: 14/6 – 28/6 (đóng sớm ~22/6) · Budget: ~30h_

> Format: Plan vs Actual → What shipped → Went well → Slipped / went wrong → Lessons → Carry-over. Reference principle bằng số (`principles.md`).

---

## 1. Plan vs Actual

| Mục tiêu "Done =" (timeline) | Trạng thái | Ghi chú |
|---|---|---|
| Auth working | ✅ | email/password + magic-link (local). Magic-link-on-prod defer → Phase 5 |
| Deploy URL live | ✅ | `brewdesk-app.vercel.app`, auto-deploy on push `main` |
| RLS pass 2-user test | ✅ | **kéo lên sớm từ Tuần 2**, test qua publishable key |
| shadcn setup | ✅ | neutral base + login UI rebuild |

**Kết:** 4/4 deliverable đạt, **đóng sớm ~6 ngày** (22 vs 28/6). Còn kéo được cả phần UI của Phase 2 (`/cafes` list + home + import pipeline) vào trong cửa sổ Phase 1.

## 2. What shipped (commit-level)

- Next.js 16.2.9 scaffold (TS, Tailwind v4, App Router, Turbopack) + AGENTS.md
- Supabase client 3-file (`client/server/proxy`) — region Singapore, `@supabase/ssr`, migrate ANON → PUBLISHABLE_KEY
- Auth: `/login` (zod-validated) + `app/auth/confirm` + redirect guard trong `proxy.ts`
- shadcn/ui + lucide-react, login UI bằng Card/Button/Input/Label
- Schema 3 bảng (profiles, cafes, checkins) — minimal-first, partial indexes
- RLS lock + `grants.sql` + harden security-definer fn (`private` schema) → 2-user test PASS
- Vercel hookup + 2 env var (Prod+Preview)
- (Phase 2 kéo lên) `/cafes` list view mobile + home sau login + pipeline import xlsx→SQL

## 3. Went well 🟢

- **Security pulled forward, twice.** RLS + grants kéo từ Tuần 2 lên Tuần 1 vì publishable key lộ client-side → đóng gap đúng lúc, không để nợ. (principle #5 safety, #11 nếu có)
- **Minimal-first giữ vững.** checkins chỉ 5 field core, bỏ `google_place_id`. Schema defend được từng cột. (principle #1, #4)
- **2-user RLS test bắt lỗi thật.** Test qua publishable key (không phải SQL Editor bypass) → lộ 2 lỗi: thiếu table GRANT + fn lộ qua REST. Test đúng tầng = bắt đúng bug. (principle #2 ship-to-learn)
- **Đóng sớm → kéo Phase 2 lên.** Thay vì nghỉ, dùng buffer làm UI song song lúc chờ thu data. Tận dụng tốt momentum.

## 4. Slipped / went wrong 🟠

- **Vercel hookup slip 16/6 → 20/6 (~4 ngày).** Magic-link-on-prod vướng: route dùng token_hash flow nhưng free tier khoá email template (cần custom SMTP). Quyết defer → Phase 5 thay vì cố giải khi chưa có beta user. Slip < ngưỡng 3-ngày-raise nhưng sát.
- **lat/lng locale corruption.** Toạ độ nhập Excel (locale VN, dấu phẩy thập phân) bị nuốt dấu chấm → `10.7739` thành `10773904372349700`. 9/9 quán dính. Phải viết bước normalize (chia 10 tới khi vào range HCMC) để khôi phục.
- **Generator script xlsx→SQL chưa commit.** Nằm trong `/data` (gitignored) → không reproducible từ repo, không ai khác chạy lại được. Nợ kỹ thuật nhỏ.
- **Signed vs public URL ảnh.** Bucket ban đầu để Private → Excel sinh signed URL (`?token=`, hết hạn ~1h). Suýt lưu vào DB → ảnh sẽ vỡ sau 1h. Bắt kịp, chuyển bucket Public + dùng public URL.

## 5. Lessons (đem sang sau)

1. **Số nhập tay qua tool locale-sensitive = rủi ro luôn.** Format cột số = **Text** trong Excel, parse ở bước import. Đừng tin Excel giữ nguyên dấu thập phân.
2. **Free tier có trần ẩn** (email template, Leaked Password Protection = Pro-only). Check sớm cái gì free tier khoá trước khi build flow phụ thuộc nó.
3. **Test đúng tầng quyền.** RLS phải test qua publishable key, không phải SQL Editor (bypass RLS) — nếu không sẽ tưởng pass mà thật ra hở.
4. **Public asset → public URL, không signed URL.** Signed URL hết hạn; chỉ dùng cho file riêng tư cần kiểm soát.
5. **Script tạo data nên commit** (kể cả khi data thô gitignored) → reproducibility.

## 6. Decisions điểm danh (xem `03-decisions-log.md`)

14/6 pivot P3 · 15/6 Next 16 + pnpm + double CLAUDE.md · 16/6 proxy.ts · 18/6 auth dual + PUBLISHABLE_KEY · 19/6 schema + kéo RLS sớm · 19/6 grants + harden fn · 20/6 Vercel + defer magic-link · 22/6 kéo Phase 2 UI + pipeline · 22/6 district target + bucket ảnh.

## 7. Carry-over sang Phase 2

- [ ] Thu thập 50 quán (Q10/Q1/Q3/Q5/Tân Bình) — đang 9/50
- [ ] Batch-upload ảnh → bucket `cafe-photos` (Public) khi đủ ~50 quán
- [ ] (nợ) commit generator script xlsx→SQL để reproducible
- [ ] (defer Phase 5) polish magic-link UX + custom SMTP + UI upload ảnh
- [ ] Self-check cuối Phase 2 (12/7): data quality, redo > 20% → slow down
- [ ] Khi nào gỡ `_smoke_test` table + `/test` route (auto-trigger đã đạt: auth + real schema xong)

## 8. Portfolio signal (cho `portfolio-narrative.md`)

Phase 1 cho 2 talking point mạnh khi phỏng vấn:
- **Security-conscious:** chủ động kéo RLS lên sớm khi nhận ra publishable key lộ client-side, test đúng tầng quyền, harden security-definer fn.
- **Debug data integrity:** chẩn đoán + khôi phục lat/lng corruption bằng domain constraint (toạ độ HCMC range) — kể được như 1 mini war-story.
