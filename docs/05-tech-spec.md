# Tech Spec — BrewDesk MVP

_Version 1.0 — 2026-06-14_

## Stack (locked)

| Layer | Choice | Reason |
|---|---|---|
| Framework | Next.js 16 (App Router + Turbopack) | Current stable, agent-aware scaffold (AGENTS.md/CLAUDE.md), Turbopack default ⇒ build/dev nhanh hơn webpack |
| Hosting | Vercel | Zero-config với Next.js, free tier đủ closed beta |
| DB + Auth | Supabase (Singapore region) | Postgres + Auth + Realtime + RLS, free tier đủ MVP. Auth = email/password + magic link (xem decisions-log 2026-06-18). Client dùng `PUBLISHABLE_KEY` (không phải anon key). |
| UI | shadcn/ui + Tailwind CSS | Copy-paste components, accessible, recruiter signal |
| Icons | lucide-react | Free, đẹp, fit shadcn |
| Map | Mapbox GL JS | Free tier ~50k loads/month |
| LLM (Beta v2) | Qwen2.5 via Groq | Cost-effective, fast inference |

**Package manager:** pnpm (qua corepack). Lock 2026-06-15.
**Node:** ≥20.9 (Next.js 16 minimum).

**Out of stack (locked):**
- No pgvector (defer post-MVP)
- No Redis (Postgres đủ)
- No Cloudflare/Vercel KV (not needed yet)

---

## Schema — Beta v1 (3 tables)

### `profiles` (extends auth.users)

```sql
create table public.profiles (
  id uuid primary key references auth.users(id) on delete cascade,
  display_name text not null,
  created_at timestamptz default now()
);
```

### `cafes`

```sql
create table public.cafes (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  address text not null,
  lat double precision not null,
  lng double precision not null,
  district text,           -- "Q1", "Q3", "BT"
  has_power_outlets boolean default false,
  has_wifi boolean default true,
  noise_level smallint,    -- 1-5
  vibe_tags text[],        -- ["silent", "chill", "study-friendly"]
  opening_hours jsonb,     -- { "mon": "07:00-22:00", ... }
  photo_url text,
  created_at timestamptz default now()
);

create index cafes_district_idx on public.cafes(district);
create index cafes_location_idx on public.cafes(lat, lng);
```

### `checkins`

Minimal-first (5 field core).

```sql
create table public.checkins (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references auth.users(id) on delete cascade,
  cafe_id uuid not null references public.cafes(id) on delete cascade,
  checked_in_at timestamptz default now(),
  checked_out_at timestamptz,
  created_at timestamptz default now()
);

create index checkins_active_idx on public.checkins(cafe_id) where checked_out_at is null;
create index checkins_user_idx on public.checkins(user_id, checked_in_at desc);
```

**Live status query (derived, no extra table):**

```sql
select cafe_id, count(*) as active_count
from public.checkins
where checked_out_at is null
group by cafe_id;
```

---

## Schema — Beta v2 additions (sau hè)

### `spot_messages` (preliminary)

```sql
create table public.spot_messages (
  id uuid primary key default gen_random_uuid(),
  checkin_id uuid not null references public.checkins(id) on delete cascade,
  user_id uuid not null references auth.users(id) on delete cascade,
  cafe_id uuid not null references public.cafes(id) on delete cascade,
  content text not null,
  moderation_status text default 'pending',  -- 'pending', 'approved', 'blocked'
  created_at timestamptz default now()
);
```

Lock khi đầu Phase 7.

---

## RLS Policies (locked principles)

### `profiles`
- Read: anyone authenticated
- Write: only self

### `cafes`
- Read: anyone (including anonymous for landing preview)
- Write: only admin (Khánh) via service role

### `checkins`
- Read: only own checkins + checkins ở café user đang active
- Write: only own
- Update (checkout): only own

Detail SQL trong `brewdesk-app/supabase/migrations/`.

---

## Folder structure (brewdesk-app)

```text
brewdesk-app/
├── app/                    # Next.js App Router
│   ├── login/              # email/password + magic link, Server Actions
│   ├── auth/
│   │   └── confirm/        # route.ts — verifyOtp (magic link + signup confirm)
│   ├── cafes/              # (sắp tới)
│   ├── checkin/            # (sắp tới)
│   └── layout.tsx
├── proxy.ts                # Next.js 16 session refresh (thay middleware.ts)
├── components/
│   ├── ui/                 # shadcn components (button, input, label, card)
│   └── feature/            # business components
├── lib/
│   ├── supabase/
│   │   ├── client.ts
│   │   ├── server.ts
│   │   └── proxy.ts        # updateSession (Next.js 16 convention)
│   └── utils.ts            # shadcn cn() helper
├── supabase/
│   └── migrations/         # SQL migration files
├── public/
└── ...config files
```

---

## Decisions deferred

- **Real-time strategy** (Phase 7): Supabase Realtime subscriptions vs polling. Test latency trước decide.
- **LLM moderation prompt** (Phase 8): Khánh write + iterate
- **Domain + production env** (Phase 5): name decision pending
