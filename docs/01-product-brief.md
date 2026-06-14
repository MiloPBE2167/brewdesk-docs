# BrewDesk — Product Brief

_Version 1.0 — original vision, kept as historical anchor. Updates live in decision log + current-status._

## One-line

App giúp sinh viên/freelancer ở HCMC chọn café để học/làm việc dựa trên real-time activity (ai đang ở đó, vibe ra sao).

## Target user

- **Primary:** Sinh viên đại học HCMC đi café học bài 1-4 buổi/tuần
- **Secondary:** Freelancer / remote worker dùng café làm workspace
- **Not target:** Tourist, casual coffee drinker, food blogger

## Pain (validated)

Survey 6/2026:
- 80% target user đi café học 1-2 hoặc 3-4 buổi/tuần (frequent pain)
- Top need #1: biết status quán trước khi đi (đông không, có chỗ không, vibe ra sao)
- Top need #2: được gợi ý quán phù hợp mood / activity hiện tại

## Core hypothesis

Người đi café học bài sẽ:
1. Check-in vào app khi tới quán (low friction)
2. Đổi lại: thấy được quán nào đang active + vibe ra sao trước khi đi
3. Optional (Beta v2): chat với người cùng quán (study buddy, coffee chat, không phải dating)

## Core loop (MVP Beta v1)
Browse map → Pick café → Check-in → See live status of cafés around

## Beta v2 addition

- Send message in spot → Receive message in spot → LLM moderation auto-filter

## Why this might work

- Pain validated (80% survey)
- VN café culture: học/làm tại café là norm, không phải edge case
- Mobile-first behavior fit (user đã pull phone ra ở café)
- Low scope MVP (3 bảng, ~15-20h/tuần feasible)

## Why this might not work

- Check-in friction: dù 2 tap vẫn là behavior mới
- Network effect: cần đủ user/café để live status có ý nghĩa (chicken-egg)
- Cold start: 50 café manual entry không scale
- Real value chỉ visible khi nhiều user dùng đồng thời

## Success criteria (Beta v1 — see 07-build-timeline for metrics)

Active users ≥10/20 invited, return rate ≥35%, qualitative ≥3/5 "would miss it."

## Out of scope (locked — see CLAUDE.md mục 6)

DM 1-1, premium, Google rating, QR check-in, focus room, mini-note, structured verify.