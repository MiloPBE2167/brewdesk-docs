# Glossary

> Term consistency. Tránh confusion giữa Khánh và Claude qua sessions.

| Term | Definition |
|---|---|
| **Spot** | Một check-in instance của user tại 1 café (1 user → 1 active spot tại 1 thời điểm). Không phải "bàn" cụ thể trong café. |
| **Live status** | Số user đang active (chưa check-out) tại café, derive từ `checkins` table. |
| **Beta v1** | MVP với 3 bảng (profiles, cafes, checkins). Launch 17/8/2026. |
| **Beta v2** | Beta v1 + spot_messages (real-time chat) + LLM moderation. Launch 15/11/2026. |
| **Closed beta** | Invite-only, whitelist email, n=15-50 user. Không public signup. |
| **Vibe** | Mood/atmosphere của café, encoded trong `cafes.vibe_tags` array. Values: `silent`, `chill`, `study-friendly`, `discussion`. |
| **Field research** | Khánh đi café thực tế quan sát + nhập data manual. |
| **Decision gate** | Pre-committed metric threshold quyết go/pivot/kill (xem 07-build-timeline). |
| **Portfolio signal** | Tính "ấn tượng với recruiter trong 2-5 phút" của 1 feature/decision. |
| **P3** | Portfolio-first + startup-optional. Project framing locked 14/6/2026. |
| **Recruiter lens** | Evaluation question: "Khánh giải thích cái này trong 2 phút interview thế nào?" |
| **Co-builder** | Claude's role — equal voice in decisions, không phải assistant. |