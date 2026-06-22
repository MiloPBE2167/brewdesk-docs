# Build Timeline — BrewDesk MVP (P3 Portfolio-first)

_Version 2.0 — 2026-06-14_

## Top-level targets

- **Beta v1 launch (VN, trước về Mỹ):** 17/8/2026
- **Decision point v1:** 31/8/2026
- **Beta v2 launch (chat + mod):** 15/11/2026
- **Final portfolio polish:** 7/12/2026

## Time budget assumed

- Hè VN: 15-20h/tuần (intern Mon-Fri, evenings + weekends)
- Sau hè US: 10-15h/tuần (school)

## Phase plan (16 weeks total)

### Hè VN — Beta v1 (9 tuần, 15/6 – 17/8)

| # | Phase | Dates | Hours budget | Done = |
|---|---|---|---|---|
| 1 | Foundation | 15/6 – 28/6 | ~30h | Auth working, deploy URL live, RLS pass 2-user test, shadcn setup |
| 2 | Café data | 29/6 – 12/7 | ~40h | 50 café manual entry, `/cafes` list view chạy mobile |
| 3 | Map view | 13/7 – 26/7 | ~40h | Mapbox + markers + "near me" sort |
| 4 | Check-in + live status | 27/7 – 9/8 | ~40h | Check-in/out flow, "X người đang ở" hiển thị (polling OK) |
| 5 | Polish + Beta v1 prep | 10/8 – 17/8 | ~30h | Mobile responsive, error states, invite whitelist, Sentry, launch |

### Sau hè US — Measure + Beta v2 (7 tuần)

| # | Phase | Dates | Hours budget | Done = |
|---|---|---|---|---|
| 6 | Measure v1 + decide | 18/8 – 31/8 | ~20h | 2 tuần measure beta users, viết `decision-v1.md` go/pivot/kill |
| 7 | Realtime chat (Beta v2 build) | 1/9 – 27/9 | ~40h | spot_messages schema + Supabase Realtime, presence indicator |
| 8 | LLM moderation | 28/9 – 18/10 | ~40h | Qwen2.5 via Groq, ≥85% precision trên test set, integrate vào message flow |
| 9 | Beta v2 launch + measure | 19/10 – 15/11 | ~40h | Launch chat, recruit ~10 user thêm, measure 2-3 tuần |
| 10 | Portfolio polish | 16/11 – 7/12 | ~30h | README pro, architecture diagram, write-up, demo video, intern application ready |

## Key milestones / gates

**End of Phase 2 (12/7):** 50 café trong DB, list view chạy. Self-check: data có quality không? Nếu phải redo > 20%, slow down.

**End of Phase 4 (9/8):** Demo core loop cho 5 bạn. Câu hỏi:
- Họ hiểu app trong 30s không?
- "Biết café đang đông" có hữu ích không?
- Cold response ≥3/5 → consider pivot trước Beta v1

**End of Phase 6 (31/8) — Decision v1:**

| Metric | Go on (build Beta v2) | Kill / Pivot |
|---|---|---|
| Active users (≥1 check-in/tuần) | ≥10 / 20 invited | <5 |
| Check-in/user/tuần (median) | ≥1.5 | <0.5 |
| 2-week return rate | ≥35% | <15% |
| Qualitative (5 user interview) | ≥3/5 "would miss it" | ≤1/5 |
| Critical bugs unfixed | <5 | >10 |

Middle zone → pivot, viết doc.

**Important:** Beta v1 fail không = project fail. Beta v1 vẫn là portfolio asset mạnh.

**End of Phase 10 (7/12):** Portfolio ready check:
- README có architecture diagram, screenshots, deployed URL
- `portfolio-narrative.md` complete
- 2-min demo video recorded
- Git history clean

## Working rhythm

- Daily: 1 commit min (weekday evenings 1-2h, weekend ~5h/day)
- Weekly Friday: status update vào `02-current-status.md`
- 1 ngày OFF/tuần bắt buộc (chống burnout intern + side project)
- Slip >3 ngày trong 1 phase → raise + re-plan, không tự nuốt

## Tasks

### Phase 1 detailed tasks (15/6 – 28/6)

**Pre-Monday setup (14/6 — Sun, before intern week starts):**
- [x] Lock decisions: Next.js 16, pnpm, double CLAUDE.md (xem 03-decisions-log.md, 2026-06-15)
- [ ] Verify Node ≥20.9 (`node -v`)
- [ ] `corepack enable && corepack prepare pnpm@latest --activate`
- [ ] `pnpm create next-app@latest brewdesk-app --yes`
- [ ] `cd brewdesk-app && pnpm dev` (smoke test localhost:3000)
- [ ] Initial commit + push to GitHub `MiloPBE2167/brewdesk-app` (public)

**Week 1 (15-21/6):**
- [x] Mon 15/6: Supabase project (Singapore region), get URL + key (→ done 16/6)
- [x] Mon 15/6: Install `@supabase/ssr @supabase/supabase-js`, setup `lib/supabase/{client,server,proxy}.ts` (Next.js 16: `proxy.ts` thay `middleware.ts`)
- [x] **Vercel hookup, env vars (SUPABASE_URL, PUBLISHABLE_KEY), auto deploy on push to main — DONE 20/6** (slipped từ 16/6). Prod live `brewdesk-app.vercel.app`. Magic-link-on-prod defer → Phase 5 (free tier khoá email template, cần custom SMTP; email/password vẫn chạy). Xem decisions-log 2026-06-20.
- [x] Auth (done 18/6): `/login` email/password **+ magic link** (`signInWithOtp`), `app/auth/confirm` route (`verifyOtp`), `getClaims()` + redirect guard trong `proxy.ts`. Migrate ANON_KEY → PUBLISHABLE_KEY. (xem 03-decisions-log 2026-06-18)
- [x] shadcn/ui (done 18/6): `shadcn init` neutral base + lucide-react, login UI rebuild bằng Card/Button/Input/Label
- [x] Fri 19/6: SQL migration — profiles + cafes + checkins (3 bảng) trong `supabase/migrations/20260619000000_init_schema.sql` (file written; apply lên DB còn lại)
- [x] Fri 19/6: RLS migration `20260619010000_rls.sql` — KÉO LÊN SỚM từ Tuần 2 (xem decisions-log 2026-06-19). File written; apply + test 2-user còn lại Tuần 2
- [ ] Sat 20/6: Field day — đi 5-10 café Q1 quan sát, bắt đầu café list spreadsheet
- [ ] Sun 21/6: Status update + weekly review

**Week 2 (22-28/6):**
- [x] RLS policies — viết + apply + test 2-user (verify deny/allow) **xong sớm 19/6** (Tuần 1). Test qua publishable key `scripts/rls-test.mjs`; phát sinh thêm `20260619030000_grants.sql` (table GRANTs) + `20260619020000_harden_security_definer_fn.sql` (move fn → `private`). Leaked Password Protection bỏ qua (Pro-only, free tier không có).
- [x] Auth polish: zod validation login/signup/magic-link (22/6) — `app/login/actions.ts`
- [ ] Sat-Sun: Demo + write Phase 1 retrospective

**Phase 2 KÉO LÊN SỚM (đang làm trong Tuần 2):**
- [x] `/cafes` list view chạy mobile (22/6) — card + ảnh/placeholder, badge quận/wifi/ổ cắm/độ ồn/vibe, Suspense+skeleton. Home sau login thay starter. (deliverable Phase 2 — đạt sớm)
- [x] Pipeline import xlsx → SQL → SQL Editor (22/6) — `scripts/cafes-import.sql`; Excel template ở `data/` (gitignored)
- [~] 50 café manual entry — **9/50** (22/6). District target chốt: Q10/Q1/Q3/Q5/Tân Bình (sát trung tâm). Tiếp tục thu thập.
- [ ] Ảnh quán: Supabase Storage bucket `cafe-photos` (chốt 22/6), batch-upload khi đủ ~50 quán
- [ ] (defer Phase 5) polish magic-link UX; UI upload ảnh qua app

(Các task phase sau expand khi reach phase đó, tránh stale.)

## Risks

1. **Intern burnout combo** — intern 40h + side 15-20h dễ kiệt. Mitigation: 1 ngày OFF/tuần, không sacrifice sleep
2. **Supabase Realtime latency VN** — test sớm trong Phase 7
3. **Recruit beta user khó hơn dự kiến** — start warm list từ Phase 2-3
4. **Groq pricing variance** — set budget cap $30/month, monitor weekly
5. **Solo dev khi về Mỹ** — vận hành xa, lag VN users. Mitigation: 1-2 local champion làm proxy
6. **Khánh slow ramp Next.js** — Phase 1-2 có thể slip 1 tuần. Buffer trong Phase 5
7. **Portfolio quality < code quantity** — risk thật là ship nhiều mà narrative yếu. Mitigation: maintain `portfolio-narrative.md` mỗi 2 tuần
