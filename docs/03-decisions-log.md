# Decisions Log

> Every locked decision with reasoning. Append-only. Date format: YYYY-MM-DD.

---

## 2026-06-14 — Validation pass, transition to build phase

**Decision:** Chuyển từ validation phase (5-week) sang build phase.

**Reasoning:**
- Survey: 80% respondents đi café học 1-2 hoặc 3-4 buổi/tuần (pain confirmed)
- Demand confirmed: muốn biết café status + được gợi ý café
- Landing page + survey đã hoàn tất

---

## 2026-06-14 — Café data sourcing: manual, solo, ~50 quán

**Decision:** Manual entry, Khánh đi một mình, ~50 café ở Q1 + Q3 + Bình Thạnh.

**Alternatives considered:**
- Google Places API: rủi ro ToS với rating data, depend external service, không có data unique (ổ điện/độ ồn/vibe)
- Scrape Google Maps: vi phạm ToS, fragile, rejected
- Manual + đi 2 người: nhanh hơn nhưng tốn người, không đáng cho 50 quán

**Reasoning:**
- Differentiator của BrewDesk = data unique (ổ điện/độ ồn/vibe phù hợp học) — không scrape được
- Validation kép: Khánh đi 50 café = field research
- Compliant với "No Google rating usage" đã lock
- Founder bandwidth: 3-5 ngày manual < 2 ngày setup crawl + ongoing risk

**Tradeoff:** Tốn 3-5 ngày, không scale lên 500 quán. Acceptable cho closed beta n=20-50.

---

## 2026-06-14 — Checkins schema minimal-first

**Decision:** MVP `checkins` chỉ có 5 field core:

```sql
checkins (
  id UUID PK,
  user_id UUID FK → users,
  cafe_id UUID FK → cafes,
  checked_in_at TIMESTAMP,
  checked_out_at TIMESTAMP NULL,
  created_at TIMESTAMP
)
```

**Defer khỏi MVP:**
- `vibe ENUM` — chưa biết user có muốn select không, friction. Derive từ spot_messages sau.
- `planned_start / planned_end` — chưa rõ user behavior plan trước hay spontaneous
- `actual_checkout` — `checked_out_at` đủ
- `status ENUM` — `cancelled` là edge case
- `rating` — feature mới ngoài scope

**Reasoning:** Principle #4 "Each schema has a reason." Thêm sau dễ hơn xoá sau.

---

## 2026-06-14 — Bỏ `google_place_id` khỏi cafes schema

**Decision:** Schema `cafes` không có `google_place_id`.

**Reasoning:**
- Manual entry → không cần link Google Places
- "Open in Maps" UX dùng lat/lng + URL scheme, không cần place_id
- Nếu sau này cần multi-platform link, thêm `external_links JSONB NULL`

---

## 2026-06-14 — Sequence 3+1 cho 4 bảng MVP

**Decision:** Build 3 bảng (users, cafes, checkins) qua Phase 1-4. Add `spot_messages` từ Phase 7 (sau hè).

**Reasoning:**
- Split into sub-problems: core loop verify trước khi đụng real-time chat
- Real-time là phần phức tạp nhất — gate trước bằng signal user có quay lại
- Nếu user không return với core loop → kill sớm, save 4-6 tuần

---

## 2026-06-14 — Pivot to P3 (portfolio-first, startup-optional)

**Context:** Khánh là Y1 student, intern Mon-Fri đến hết hè, mục tiêu chính là apply intern Y2 (2027). BrewDesk re-framed.

**Decision:** Project optimization function:
- Primary: portfolio chất lượng cho Khánh interview
- Secondary: startup expansion if Beta v1 signal mạnh

**Implications:**
- Time budget realistic: 15-20h/week hè, 10-15h/week school
- Recruiter signal added as evaluation lens (principle #11)
- Code quality + narrative quality elevated
- User metric measurement giữ nhưng không là deal-breaker
- LLM moderation, real-time chat giữ trong scope vì technical signal
- Timeline compressed: Beta v1 = 17/8 (trước về Mỹ), Beta v2 = giữa 11/2026

**Tradeoff:**
- Có thể "over-build" so với pure startup MVP (chat + LLM mod không phải must cho 20 user)
- Nhưng cần thiết cho portfolio signal

**Superseded:** Plan 18-tuần startup-focused (earlier same day session)

---

## 2026-06-14 — Repo split: docs vs app, both public, fresh start

**Decision:**
- `brewdesk-docs` (public): docs, CLAUDE.md, decision log
- `brewdesk-app` (public): Next.js code
- Both repos fresh (cũ commit history messy, start clean)

**Reasoning:**
- Tách history (docs commit thường, code commit theo feature)
- Public = portfolio signal (recruiter thấy decision-making + code)
- Claude fetch docs repo dễ hơn
- Fresh start: clean git history > messy history với "fix typo" commits

---

## 2026-06-14 — Supabase region = Singapore

**Decision:** Singapore (ap-southeast-1).

**Reasoning:**
- Gần VN nhất (30-50ms latency), US ~200-300ms
- End users ở VN, important hơn dev latency Khánh
- Migration sau là painful, chọn đúng từ đầu

**Tradeoff:** Dev latency Khánh từ Mỹ ~200ms (acceptable cho dev workflow)

---

## 2026-06-14 — UI stack: shadcn/ui + Tailwind + lucide-react

**Decision:** shadcn/ui components + Tailwind CSS + lucide-react icons.

**Reasoning:**
- Khánh first-time Next.js, không có design skill — tự build UI from scratch tốn 30-40h + UI xấu
- shadcn = copy-paste, ownership ở repo, accessible default
- Tailwind đã default trong Next.js
- Recruiter signal: shadcn = modern stack, recognizable

**Out of scope MVP UI:**
- Custom illustrations
- Animation (trừ default)
- Dark mode toggle
- Onboarding tutorial elaborate

---

## 2026-06-14 — Domain defer đến Phase 6

**Decision:** Chưa mua domain. Dùng `brewdesk-app.vercel.app` cho dev + Beta v1. Mua khi Phase 6 (đầu 8).

**Reasoning:**
- $10-15/năm không là vấn đề; vấn đề là sunk cost vào tên trước khi confirm
- Closed beta n=20-50 không care domain
- Phase 6 = 1-2 tuần trước beta launch, đủ thời gian setup DNS

---

## 2026-06-14 — Docs cleanup: merge backlog + risks into timeline

**Decision:**
- Merge `06-tasks-backlog.md` vào `07-build-timeline.md`, delete backlog file
- Delete `risks-register.md`, risks live trong `07-build-timeline.md`
- Add `portfolio-narrative.md` (file mới cho interview prep, build dần)

**Reasoning:** Principle #7 founder bandwidth finite. Less files = less maintenance tax. Risk + task overlap nhiều với timeline.