# Current Status

_Updated: 2026-06-14_

## Phase
Build Phase — Week 1 of 16 (P3 portfolio-first)

## Mode
Portfolio-first, startup-optional. Optimize cho recruiter signal, measure user signal lightly.

## Now working on
**Phase 1 — Foundation** (15/6 – 28/6)
- Next.js 14 + Supabase (Singapore) + Vercel setup
- Auth via magic link
- Schema 3 bảng đầu (users, cafes, checkins) + RLS
- Initial shadcn/ui setup

## Target milestones
- **Hè VN Beta v1 launch:** 17/8/2026 (2 ngày trước về Mỹ)
- **Decision point v1:** 31/8/2026 (đo 2 tuần, viết decision doc)
- **Beta v2 launch (sau hè):** giữa 11/2026 (chat + moderation)
- **Final portfolio polish:** đầu 12/2026 (ready cho intern application Y2)

## Time budget realistic
- Hè VN (15/6 – 19/8): ~15-20h/tuần (intern Mon-Fri)
- Sau hè US (19/8 →): ~10-15h/tuần (school)

## Open questions
- [ ] Quận nào cho 50 café đầu — đề xuất Q1 + Q3 + Bình Thạnh
- [ ] Recruit beta user: bạn bè + share trên FB cá nhân, target n=15-25
- [ ] App repo (`brewdesk-app`) — chưa setup (next chat sẽ làm)

## Recent decisions (xem `03-decisions-log.md`)
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
- 2026-06-14: Fresh repos (cũ commit history messy, start clean)