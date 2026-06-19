# Current Status

_Updated: 2026-06-19_

## Phase
Build Phase — Week 1 of 16 (P3 portfolio-first)

## Mode
Portfolio-first, startup-optional. Optimize cho recruiter signal, measure user signal lightly.

## Now working on
**Phase 1 — Foundation** (14/6 – 28/6)
- Pre-Monday: Next.js 16 scaffold + push (14/6) ✅
- Supabase project Singapore + `@supabase/ssr` + 3 client files + `/test` smoke (15/6 → done 16/6, slip 1d) ✅
- Auth (18/6) ✅ — `/login` email/password **+ magic link**, `app/auth/confirm` route, `getClaims()` + redirect guard trong `proxy.ts`. Migrate sang PUBLISHABLE_KEY.
- shadcn/ui setup (18/6) ✅ — init neutral base, login UI rebuild bằng Card/Button/Input/Label + lucide-react
- Schema 3 bảng (profiles, cafes, checkins) (19/6) ✅ — `20260619000000_init_schema.sql`, đã `db push`
- RLS (19/6) ✅ — `20260619010000_rls.sql`, KÉO LÊN SỚM từ Tuần 2. Đã apply + **test 2-user PASS**.
- Apply + test RLS 2-user (19/6) ✅ — test qua publishable key (`scripts/rls-test.mjs`), bắt 2 lỗi → fix bằng 2 migration:
  - `20260619030000_grants.sql` — thiếu GRANT tầng bảng (RLS không cấp quyền, chỉ lọc dòng)
  - `20260619020000_harden_security_definer_fn.sql` — chuyển `user_active_cafe_ids()` sang schema `private` (fix 2 warning Security Advisor)
- **Vercel hookup + env vars — CHƯA LÀM** (slipped từ 16/6), là blocker kế tiếp. Cần set Supabase email template → `/auth/confirm` khi có production URL.
- **Leaked Password Protection — BỎ QUA** (warning #3): tính năng Pro plan, free tier không bật được. Accepted, không phải todo. Cân nhắc lại nếu lên Pro.
- **Next: Vercel hookup** + Sat 20/6 field day (đi café Q1, bắt đầu spreadsheet 50 quán)

## Target milestones
- **Hè VN Beta v1 launch:** 17/8/2026
- **Decision point v1:** 31/8/2026
- **Beta v2 launch (sau hè):** giữa 11/2026
- **Final portfolio polish:** đầu 12/2026

## Time budget realistic
- Hè VN: ~15-20h/tuần (intern Mon-Fri)
- Sau hè US: ~10-15h/tuần (school)

## Open questions
- [ ] Quận nào cho 50 café đầu — đề xuất Q1 + Q3 + Bình Thạnh
- [ ] Recruit beta user: target n=15-25
- [ ] When to remove `_smoke_test` table + `/test` route (auto-trigger: sau auth + real schema)
- [ ] Supabase free tier 7-day pause mitigation — defer Phase 6
- [ ] Set Supabase email template → `/auth/confirm?token_hash=...&type=...` (làm cùng Vercel hookup, cần production URL)

## Recent decisions (xem `03-decisions-log.md`)
- 2026-06-19: Apply migrations + test RLS 2-user PASS → thêm GRANTs migration + chuyển SECURITY DEFINER fn sang `private`
- 2026-06-19: Schema 3 bảng + kéo RLS lên sớm từ Tuần 2 (security: tránh hở bảng qua publishable key)
- 2026-06-18: Auth = email/password **+** magic link (amend magic-link-only 2026-06-14)
- 2026-06-18: Supabase ANON_KEY → PUBLISHABLE_KEY (SSR v2)
- 2026-06-16: Add `/test` smoke page vào Phase 1 scope
- 2026-06-16: `lib/supabase/middleware.ts` → `proxy.ts` (Next.js 16 convention sync)
- 2026-06-15: Bump Next.js lock 14 → 16
- 2026-06-15: Package manager = pnpm qua corepack
- 2026-06-15: CLAUDE.md tách 2 (docs = behavior, app = code patterns)
- 2026-06-14: Pivot to P3 (portfolio-first, startup-optional)
- 2026-06-14: Validation pass → transition to build
- 2026-06-14: Manual café entry, solo, ~50 quán
- 2026-06-14: Checkins schema minimal-first (5 field)
- 2026-06-14: Bỏ `google_place_id`
- 2026-06-14: Sequence 3+1 — Beta v1 = 3 tables, Beta v2 = +spot_messages
- 2026-06-14: Supabase region = Singapore
- 2026-06-14: UI stack = shadcn/ui + Tailwind + lucide-react
- 2026-06-14: 2 repo split (docs vs app), both public
- 2026-06-14: Domain defer đến Phase 6
- 2026-06-14: Fresh repos