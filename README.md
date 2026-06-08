# Project Time Management

This repository is a lightweight project time-management dashboard for PeterKZhao's active portfolio.

GitHub Projects V2 could not be created with the current token because it lacks the `project` / `read:project` scopes. This repository uses Issues as project cards for now.

## Operating Rules

- Each issue is one project line.
- Use labels to track stage and priority.
- Keep status updates inside each issue.
- Move detailed planning documents into the corresponding business-unit repositories.
- Review this dashboard weekly and decide which projects receive time allocation.
- Keep side-business projects and official work projects clearly separated.
- Do not mix official work into side-business priority decisions; official work is tracked only for follow-up status, blockers, and next actions.

## Workstream Separation

This dashboard can track both side-business / portfolio projects and formal employment projects, but they must be reviewed separately.

Recommended labels:

- `work:side-business` — side-business, startup, portfolio, or personal business-unit work.
- `work:official` — formal employment / main-job work.
- `priority:weekly-review` — items that should appear in the weekly review.
- `stage:follow-up-only` — official work where Peter only needs to follow up, not own primary delivery.

Review rule:

1. Side-business projects can be compared against each other for weekly time allocation.
2. Official work projects should not change the side-business ranking.
3. Official work entries should record only: current status, blocker, next follow-up, and responsible PIC/user.
4. Detailed work artifacts should stay in the appropriate company/work system, not in this personal GitHub repository.

## Side-Business / Portfolio Project Lines

| Project | Stage | Notes |
|---|---|---|
| 新粮 ERP+CRM+供应链 | `active` | Ruoyi-Vue-Pro based ERP/CRM/supply-chain product line. Track product modeling, platform adaptation, deployment, and customer validation. |
| AI 软件工厂 / youlidao.ai | `promotion` | Claude Code + IPD Skill direction. Track promotion, product packaging, workflow demos, and customer acquisition. |
| 美国华人相亲 / 相亲系统 | `planning` | Serious matchmaking platform direction. Track positioning, trust mechanism, profile model, review flow, AI matchmaker, and paid service loop. |
| 同伴游 / 教练教师陪伴 + 券系统 | `planning` | Companion/teacher/coach travel and voucher service direction. Track business model, service SKU, trust, fulfillment, and payment loop. |
| 高考志愿填报 | `live` | Live gaokao volunteer bot on yuanzhoulv.cloud. Track product quality, data sources, UI, disclaimer, operations, and promotion. |
| 蓝鹦鹉在线教育 | `blocked` | Online education project currently blocked by iOS release and Apple Pay system testing. Track release blockers and payment compliance. |
| Business-Unit-for-Video | `portfolio` | Video AI business unit. Track Video2Text, video-solution, Text-Image2Video, VideoConvt2English, and future pipeline architecture. |
| Business-Unit-for-Stock | `portfolio` | Stock intelligence and quant analysis business unit. Track daily_stock_analysis, akshare, tushare, qlib, news intelligence, industry intelligence, and factor/backtest stack. |
| MCN/KOL ERPNext 架构（支付编排系统） | `architecture` | ERPNext-first MCN/KOL finance and contract architecture. Track model decisions, skill updates, and implementation stages. |

## Official Work / Main-Job Follow-Up Lines

These entries are intentionally separated from the side-business portfolio. They are for follow-up visibility only, not for side-business priority ranking.

| Project | PIC / User | Status | Note |
|---|---|---|---|
| Partial Withdrawal Processing | Kar Wai; Liau, Kian Ming; Leong, Jun Yee; Abd Rahim, Nurhana Nadia | Solution design | Autobot accepted by business; consider whether AI can reduce manual burden. |
| Revise Benefit Amount | Gan, Huey Jing | Doing transaction | Only suppression scenario currently; depends on business user manual validation. |
| Non-Medical Claim | Sae Keat | Pending detail requirement | Details not yet filled. |
| AML Agentic AI | Tan, Cheng See; Wong Sae Keat; Poo Shiah Feng | Environment preparation | Technical path basically feasible; mainly document standardization. |
| FE Skill Building | Jacky & Tonny | Improve restriction | FE design and skill initial version. |
| SnapOne AI | Phooi Kah, AGB | Preliminary design | Details not yet filled. |
| DMTM STU BO | PeterKZhao follow-up only | Follow-up only | Peter only needs to follow up, not own primary delivery. |

## Recommended Weekly Review

1. Pick 1-2 primary side-business projects for the week.
2. Review official work follow-up items in a separate pass.
3. Mark blocked projects clearly.
4. Avoid letting exploratory repositories become active commitments by default.
5. For each active side-business project, record the next concrete deliverable.
6. For each official work item, record only the next follow-up, blocker, and responsible PIC/user.
