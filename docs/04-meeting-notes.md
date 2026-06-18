# Meeting Notes

> Optional session log. Format: append-only, newest at top.

---

## 2026-06-18 — Session: Auth shipped + docs reconcile

**Topic:** Đối chiếu commit `4cd8af5` (login + Supabase publishable key) với docs, fix các conflict.

**Phát hiện & xử lý:**
- Code login dùng password nhưng decisions-log lock "magic-link-only" → **quyết: dùng cả hai**. Thêm magic link (`signInWithOtp`) + `app/auth/confirm` route vào app; amend decision-log 2026-06-18.
- ANON_KEY → PUBLISHABLE_KEY: hợp thức hóa bằng decision-log entry để agent tương lai khỏi raise.
- shadcn chưa init, login viết raw Tailwind → **quyết: backfill shadcn ngay** (hướng A). Đã `shadcn init` + rebuild login bằng Card/Button/Input/Label + lucide-react.
- Vercel hookup xác nhận **chưa làm** — blocker kế tiếp.

**Decisions locked:** See `03-decisions-log.md` 2026-06-18 (auth dual-method, publishable key).

**Open / next:**
- Vercel hookup + env vars + set Supabase email template → `/auth/confirm`
- Fri 19/6: SQL migration 3 bảng + RLS

---

## 2026-06-14 — Session: Plan transition + docs restructure

**Topic:** Validation → build pivot, time budget realistic, P3 framing, docs cleanup, fresh repos.

**Decisions locked:** See `03-decisions-log.md` entries dated 2026-06-14.

**Output:** Full docs batch v1.0 (10 files).

**Open questions:**
- App repo workspace setup (next chat)
- Quận sourcing decision (Q1 + Q3 + BT confirm)
- Beta user recruit channel

**Next session:** Setup `brewdesk-app` workspace, init Next.js 14, Supabase project Singapore region.