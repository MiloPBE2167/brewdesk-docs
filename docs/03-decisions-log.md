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

## 2026-06-15 — Bump Next.js lock: 14 → 16 (current stable, agent-aware scaffold)

**Decision:** Stack lock đổi từ "Next.js 14 (App Router)" sang "Next.js 16 (App Router + Turbopack)". Mọi reference Next.js 14 trong docs được sync.

**Context:** Lock cũ "Next.js 14" có trong tech-spec + CLAUDE.md + timeline nhưng không có entry decisions-log nào giải thích reasoning — tức là không phải decision được defend, chỉ là default đang thịnh hành thời điểm research stack. Khi chuẩn bị chạy `create-next-app` hôm nay, raise lại để kiểm tra.

**Reasoning:**
- Next.js 14 EOL 26/10/2025 — không còn security update. Portfolio repo public install framework EOL Day 1 = negative signal cho recruiter Y2.
- Next.js 16.2.9 LTS là current stable tính đến 9/6/2026; matured 8 tháng kể từ stable release 10/2025.
- `create-next-app@latest` defaults trùng exact stack mình locked: TypeScript + Tailwind + ESLint + App Router + Turbopack. Không cần customize prompts.
- Scaffold mặc định ship `AGENTS.md` + `CLAUDE.md` cho coding agents — fit với "Claude co-builder" framing, app repo CLAUDE.md sẽ hoạt động như code-pattern guide bổ sung cho docs repo CLAUDE.md (behavior guide).
- Turbopack default thay webpack — mình không có webpack custom config, irrelevant cost.
- Recruiter signal: "ship trên current stable" >>> "ship trên EOL."

**Alternatives considered:**
- **Giữ Next.js 14** (rejected — EOL, không còn security patch, negative recruiter signal).
- **Next.js 15 LTS** (rejected — support hết 21/10/2026 đúng giữa Phase 8 LLM moderation, sẽ phải upgrade ngay khi đang push Beta v2 = distraction tệ nhất time; thêm nữa 15 không có AGENTS.md/CLAUDE.md default trong scaffold).
- **Stay on 14 và migrate khi cần** (rejected — migration cost 14→16 không nhỏ, làm sớm rẻ hơn làm khi đã có code mass).

**Tradeoffs:**
- Tutorial YouTube/Medium phần lớn còn ở Next.js 14 (Khánh first-time Next.js). Mitigation: dùng docs chính thức nextjs.org (đã update 16), plus AGENTS.md/CLAUDE.md trong scaffold sẽ guide Claude generate code đúng version.
- Cache Components beta trong 16 — default off, không touch trong MVP.
- Minimum Node 20.9 — Khánh xác nhận trước scaffold.

**Reversal trigger:**
- Khả năng rất thấp. Trigger duy nhất: Next.js 17 ra trong Phase 6-7 với breaking change đáng để upgrade — sẽ raise như entry mới, không reverse decision này.

---

## 2026-06-15 — Package manager = pnpm (qua corepack)

**Decision:** Dùng `pnpm` cho `brewdesk-app`, enable qua `corepack` (built-in Node ≥16.10).

**Reasoning:**
- Next.js docs official dùng pnpm trong example commands → ít lệch tài liệu, copy-paste từ docs không cần dịch.
- pnpm disk-efficient (content-addressable store) — quan trọng khi Khánh switch máy VN ↔ US.
- Corepack có sẵn trong Node, không phải install thêm gì hệ thống.
- Recruiter signal: pnpm = modern tooling choice, > npm về tốc độ + monorepo readiness (nếu sau split docs/app cùng workspace).

**Alternatives considered:**
- **npm** (rejected — universal nhưng chậm hơn, không match docs default Next.js, recruiter signal thấp hơn).
- **yarn** (rejected — yarn 1 EOL, yarn berry phức tạp cho solo dev, không phổ biến trong Next.js community 2026).
- **bun** (rejected — bun runtime + bun install promising nhưng còn rough edges cho Next.js 16, không phải lúc test new tooling khi đã có timeline tight).

**Tradeoff:** Khánh phải nhớ chạy `corepack enable` khi setup máy mới. Một dòng, acceptable.

**Reversal trigger:** pnpm bug nghiêm trọng làm break Vercel deploy hoặc Supabase CLI — chuyển npm.

---

## 2026-06-15 — CLAUDE.md tách 2 file: docs repo (behavior) vs app repo (code patterns)

**Decision:** Giữ default `CLAUDE.md` + `AGENTS.md` mà `create-next-app@latest` generate trong `brewdesk-app`. Không replace, không merge với docs repo CLAUDE.md.

**Reasoning:**
- 2 file có function khác nhau:
  - `brewdesk-docs/CLAUDE.md` = co-builder behavior (pushback, principles, decisions, session protocol)
  - `brewdesk-app/CLAUDE.md` = code patterns (Next.js conventions, file structure, framework versioning guidance)
- Claude khi sửa code trong `brewdesk-app` cần Next.js-specific guidance hơn là principle reminder. Khi discuss decisions trong `brewdesk-docs` cần behavior + principles, không cần Next.js conventions.
- Merge = file dài, signal-to-noise giảm. Tách = mỗi file purpose-clear.

**Reversal trigger:** Maintenance tax 2 file CLAUDE.md trở nên painful (drift, conflict). Lúc đó merge với rõ section divider.

---

## 2026-06-16 — Auth library: @supabase/ssr (deprecated auth-helpers-nextjs)

**Decision:** Dùng `@supabase/ssr` (v0.12.0) cho Next.js App Router integration. Không dùng `@supabase/auth-helpers-nextjs`.

**Context:** Khi research auth setup hôm nay, nhiều tutorial 2024 và đầu 2025 vẫn dùng `auth-helpers-nextjs`. Supabase team đã deprecate package đó và migrate sang `@supabase/ssr` từ 2024 — bất kỳ tutorial nào dùng `auth-helpers` đều stale.

**Reasoning:**
- `@supabase/auth-helpers-nextjs` deprecated bởi Supabase team, không còn maintain
- `@supabase/ssr` là official replacement, designed cho App Router + Server Components
- Cookie-based session với `getAll`/`setAll` API mới — chuẩn pattern hiện tại
- Recruiter signal: dùng official current pattern > copy outdated tutorial

**Alternatives considered:**
- `@supabase/auth-helpers-nextjs` (rejected — deprecated, không maintain)
- Manual cookie handling với `@supabase/supabase-js` only (rejected — reinventing the wheel, dễ sai cookie scope/SameSite)
- NextAuth.js + Supabase adapter (rejected — thêm 1 layer indirection không cần thiết khi đã chọn Supabase Auth làm canonical)

**Tradeoff:**
- `@supabase/ssr` còn relatively new (pre-1.0), API có thể thay đổi minor — phải watch CHANGELOG
- Mọi tutorial cũ (2024 và trước) dùng `auth-helpers` — phải skip những resource đó, dựa vào docs supabase.com/docs

**Reversal trigger:** Supabase release lib mới (ví dụ stabilize thành v1.0 với breaking changes lớn), hoặc switch khỏi Supabase Auth.

---

## 2026-06-16 — Next.js 16 file convention: proxy.ts (not middleware.ts)

**Decision:** Session refresh file ở `brewdesk-app/proxy.ts`, không phải `middleware.ts`.

**Context:** Sau khi setup `middleware.ts` theo pattern Supabase docs (docs còn dùng tên cũ), dev server warn:
> `The "middleware" file convention is deprecated. Please use "proxy" instead.`

Đây là breaking convention change trong Next.js 16, không phải deprecation lâu dài.

**Reasoning:**
- Next.js 16 rename `middleware.ts` → `proxy.ts` để làm rõ purpose: file nằm ở network boundary, không phải Express-style middleware chain
- `middleware.ts` vẫn work nhưng deprecated, sẽ remove trong future minor version → fix sớm rẻ hơn fix khi đang push feature
- Codemod `npx @next/codemod middleware-to-proxy .` rename file + rename export `middleware` → `proxy`, `config` matcher giữ nguyên
- Một note: `proxy.ts` chạy Node.js runtime, không support Edge Runtime. BrewDesk không cần Edge → ok

**Alternatives considered:**
- Giữ `middleware.ts` đến khi forced upgrade (rejected — deprecated warning xuất hiện mỗi `pnpm dev`, noise; fix 30 giây không lý do defer)
- Manual rename + manual edit (rejected — codemod official, ít error hơn manual)

**Tradeoff:** Supabase docs (state 2026-06) còn dùng tên `middleware.ts` cho ví dụ Next.js — khi reference Supabase docs phải mental-map sang `proxy.ts`. Tutorials Next.js cũ tương tự.

**Reversal trigger:** Khả năng rất thấp. Nếu cần Edge Runtime (latency-sensitive auth ở edge nodes) → quay lại `middleware.ts` cho phần đó. BrewDesk single-region (Singapore) không có use case.

---

## 2026-06-18 — Auth: email/password + magic link (amend 2026-06-14 magic-link-only)

**Decision:** Beta v1 hỗ trợ **cả hai** phương thức trên cùng `/login`: email + password (`signInWithPassword` / `signUp`) **và** magic link (`signInWithOtp`). Một route `app/auth/confirm/route.ts` (`verifyOtp` theo `token_hash`) xử lý chung cho cả magic link và email confirmation của signup.

**Context:** Amend decision 2026-06-14 ("Auth = email magic link, không password"). Entry cũ reject password vì "phải build reset flow + brute-force rate limit + password hashing = 1-2 ngày, attack surface lớn". Khi implement thực tế, lý do đó không còn đứng vững.

**Reasoning:**
- Supabase Auth lo sẵn password hashing, brute-force rate limit, và reset flow out-of-box → chi phí "1-2 ngày" mà entry cũ lo ngại gần như bằng 0. Objection gốc về password đã được mitigate bởi chính platform đã chọn.
- Password = login tức thì không cần email round-trip → tốt cho dev/testing lặp lại và returning user không muốn chờ email.
- Magic link giữ lại làm lựa chọn passwordless, onboarding friction thấp cho user mới.
- Safety concern trong entry 2026-06-14 nhắm vào OAuth/strangers, không phải password per se → không xung đột.
- Portfolio signal: handle nhiều auth method + một confirm route dùng chung là pattern Supabase SSR chuẩn.

**Implications:**
- `app/auth/confirm/route.ts` mới (thay cho `/auth/callback` từng dự kiến trong tech-spec).
- Supabase email templates phải trỏ `{{ .SiteURL }}/auth/confirm?token_hash={{ .TokenHash }}&type={{ .Type }}` — **bước cấu hình dashboard, chưa làm**, cần Khánh set khi nối Vercel + production URL.
- `proxy.ts` đã whitelist path `/auth` và `/login` (redirect guard chỉ chặn route khác).

**Reversal trigger:** Email deliverability < 95% (magic link vào spam) → đẩy password lên primary; hoặc bỏ hẳn password nếu support reset/abuse trở thành gánh nặng.

---

## 2026-06-18 — Supabase key: ANON_KEY → PUBLISHABLE_KEY (SSR v2)

**Decision:** Mọi Supabase client (`client.ts`, `server.ts`, `lib/supabase/proxy.ts`) dùng `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` thay vì `ANON_KEY`. `.env.local.example` giữ cả hai key trong giai đoạn chuyển tiếp.

**Reasoning:**
- Supabase đã giới thiệu publishable/secret key model mới thay cho legacy anon/service_role JWT keys; publishable key là cách hiện tại cho client-side.
- Khớp pattern `@supabase/ssr` mới nhất → ít lệch docs, tránh agent tương lai raise "sao không dùng anon key".
- Recruiter signal: dùng current key model > legacy.

**Reversal trigger:** Supabase deprecate publishable key, hoặc cần service-role cho admin café insert (dùng secret key riêng server-side, không thay publishable cho client).

---

## 2026-06-19 — Schema 3 bảng + kéo RLS lên sớm (từ Tuần 2)

**Decision:** Viết `20260619000000_init_schema.sql` (profiles + cafes + checkins, đúng tech-spec) VÀ `20260619010000_rls.sql` ngay trong Tuần 1, thay vì để RLS sang Tuần 2 (22-28/6) như timeline gốc.

**Reasoning:**
- Bảng tắt RLS + dùng publishable key (client-side) = đọc/ghi công khai. Đặc biệt `profiles` và `checkins` bị lộ. Không nên để khoảng hở này tồn tại qua bất kỳ deploy nào.
- RLS principle đã locked sẵn trong tech-spec → không phải thiết kế mới, chỉ là hiện thực hoá → kéo lên sớm rẻ.
- Tuần 2 còn lại để test RLS với 2 user account (việc test vẫn giữ nguyên lịch).

**Quyết định kỹ thuật trong RLS:**
- `cafes` write = admin-only: KHÔNG tạo policy ghi → chỉ service_role (bỏ qua RLS) ghi được. Khớp publishable/secret key model (2026-06-18).
- `checkins` SELECT tham chiếu chính nó → bọc subquery vào function `user_active_cafe_ids()` SECURITY DEFINER để tránh infinite-recursion của RLS.
- Dùng `(select auth.uid())` thay `auth.uid()` trực tiếp (Supabase perf best practice).
- Không có policy DELETE checkins (giữ lịch sử để đo metric Decision v1).

**Reversal trigger:** Nếu cần anonymous đọc `checkins` cho landing preview (hiện chỉ `cafes` public-read), hoặc đổi rule "thấy người cùng quán".

---