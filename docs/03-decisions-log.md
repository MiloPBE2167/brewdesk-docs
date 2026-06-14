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

---

## 2026-06-14 — Session start protocol: pin raw URLs in opening message

**Decision:** Session start ritual yêu cầu paste raw URL cụ thể của docs critical, không dùng URL template.

**Reasoning:**
- web_fetch tool require URLs xuất hiện trong prior search/fetch results
- Search engine có thể chưa index commit mới (lag vài giờ → ngày)
- User-provided URLs trong message được allow-list ngay
- Blob URL (github.com/.../blob/...) trả HTML noisy + có thể serve stale cache

**Alternatives considered:**
- Đợi Google index → unreliable, block session
- Paste content trực tiếp → tốn token, không scale
- Dùng blob URL → noisy, từng serve stale cache (xảy ra trong session này)

**Reasoning:** Principle #7 founder bandwidth finite. Less files = less maintenance tax. Risk + task overlap nhiều với timeline.

---

## 2026-06-14 — Defer pgvector khỏi MVP

**Decision:** Không bật `pgvector` extension trong Supabase cho MVP. Lock as "Out of scope" cho Beta v1 và Beta v2.

**Reasoning:**
- Schema MVP 3 bảng (users, cafes, checkins) thuần relational — không có use case nào cần vector search
- AI features ban đầu (vibe analyzer, café recommendation) dùng prompt-engineering trực tiếp với Qwen2.5 qua Groq, không cần embedding store
- pgvector add complexity: extension install, embedding generation pipeline, chọn HNSW vs IVFFlat index, dimension tuning — mỗi cái là một cuối tuần
- Time budget 15-20h/week không cho phép học thêm primitive khi chưa có data scale chứng minh need
- Portfolio signal: "pick tool đúng khi cần" mạnh hơn "xài tool cool" — recruiter sẽ hỏi *tại sao* dùng pgvector, không phải *có* pgvector hay không

**Alternatives considered:**
- Bật pgvector ngay Day 1 (rejected — premature, không có use case cụ thể trong MVP)
- Dùng external vector DB như Pinecone (rejected — thêm dependency + cost + lock-in)
- Hybrid: bật extension nhưng chưa dùng (rejected — extension idle vẫn là technical debt, và làm Supabase free tier resource counter chạy)

**Reversal trigger:**
- Số cafés > 200 và full-text search relational không đủ cho discovery
- AI recommendation cần personalization based on check-in history (similarity matching)
- Free-text semantic search ("café yên tĩnh có ổ điện") trở thành core user flow

---

## 2026-06-14 — No DM 1-1, chỉ spot-bound chat (Beta v2)

**Decision:** Không bao giờ implement DM 1-1 giữa users. Real-time chat chỉ spot-bound (group chat gắn với một café cụ thể), auto-delete 24h, và defer hoàn toàn sang Beta v2 (sau hè 2026). Beta v1 không có chat.

**Reasoning:**
- Safety: DM 1-1 giữa strangers + mixed user_types (high_school, university, working) = grooming/harassment risk, đặc biệt khi có minor
- Moderation cost: full DM moderation cần content classifier + reporting workflow + human review cho edge cases — out of scope cho solo founder
- Legal exposure: DM platforms có CSAM reporting obligations (PDPA Singapore, COPPA nếu serve minor) — chi phí compliance cao
- Spot-bound = natural moderation: chat gắn physical location, ephemeral (24h), group only → attack surface nhỏ hơn nhiều
- Beta v1 timeline (Phase 1 Foundation = 15/6–28/6) không có ngân sách build Supabase Realtime + LLM moderation cùng lúc với schema + auth
- Portfolio narrative: "tôi design safety vào architecture từ đầu" > "tôi add chat feature" — recruiter Trust & Safety hỏi đúng câu này

**Alternatives considered:**
- Full DM với moderation (rejected: solo founder không build được mod infra cho MVP)
- DM giới hạn verified-only users (rejected: verification system bản thân = whole feature, free-text verify dễ gamed)
- Không chat gì cả (rejected: chat là differentiator, là cách users tụ lại quanh một spot)
- Spot-bound chat trong Beta v1 (rejected: chồng Realtime + Qwen moderation lên Phase 1 = scope creep, fail risk cao)

**Reversal trigger:**
- Beta v1 + v2 cho thấy demand chat 1-1 cụ thể AND có T&S engineer/contractor tham gia
- User research cho thấy spot-bound chat không đủ để community form
- Pivot khỏi social model (lúc đó chat không liên quan)

---

## 2026-06-14 — Auth = email magic link

**Decision:** Auth method = email magic link (Supabase Auth default). Không OAuth, không password.

**Reasoning:**
- Zero password infrastructure: không build reset flow, không hash, không rate-limit brute-force
- Supabase Auth có magic link out-of-box → 1-2h setup vs 1-2 ngày build OAuth multi-provider hoặc password full
- Beta v1 closed n=20-50 → friction "check email mỗi lần login" acceptable
- Target audience Vietnamese students/young professionals đa số check email thường xuyên (university + working)
- Portfolio signal: "pick auth phù hợp với scale + scope" > "implement OAuth multi-provider"

**Alternatives considered:**
- OAuth (Google/Facebook/Apple): familiar 1-click nhưng cần register OAuth app từng provider, vendor lock-in, GDPR/PDPA consent flow phức tạp, recruiter có thể assume copy template
- Email + Password: control hoàn toàn nhưng phải build reset flow + brute-force rate limit + password hashing + strength validation = 1-2 ngày, attack surface lớn, không thêm portfolio signal
- Phone OTP: VN phổ biến nhưng SMS cost, vendor dependency (Twilio/Vonage), free tier kém
- Magic link + OAuth hybrid: choice paralysis cho user MVP, defer được sang Beta v2 nếu cần

**Reversal trigger:**
- Email deliverability rate < 95% trong Beta v1 (magic link rơi vào spam thành critical issue)
- User feedback cho thấy login friction quá cao → return rate thấp
- Mở rộng sang high_school demographic mà email không phải kênh primary (Zalo/Messenger phổ biến hơn ở lứa này)