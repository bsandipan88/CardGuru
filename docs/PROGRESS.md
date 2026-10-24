# Progress

Tracks each phase from [`BUILD_PROMPTS.md`](../BUILD_PROMPTS.md). Update this file at the end of every phase.

| Phase | Status | PR | Notes |
|---|---|---|---|
| 0. Plan | Ready to merge | [#1](https://github.com/bsandipan88/CardGuru/pull/1) | `docs/ARCHITECTURE.md` v0.3 with open questions decided, `CLAUDE.md` written. |
| 1. Monorepo foundation | Not started | | |
| 2. Rules engine and data model | Not started | | |
| 3. API | Not started | | |
| 4. Mobile app | Not started | | |
| 5. Web app | Not started | | |
| 6. Admin console | Not started | | |
| 7. Alerts worker | Not started | | |
| 8. AI copilot | Not started | | |
| 9. Document worker | Not started | | |
| 10. Production hardening | Not started | | |

## Decisions log

| Date | Decision | Where |
|---|---|---|
| 2026-10-14 | NestJS on the Fastify adapter for the API | ARCHITECTURE.md section 2.1 |
| 2026-10-14 | Drizzle ORM with PostgreSQL 16 | ARCHITECTURE.md section 2.2 |
| 2026-10-14 | Add `packages/db` and an `estimateAnnualValue` engine function | ARCHITECTURE.md Q13, Q14 |
| 2026-10-14 | Spend and points data are user-entered estimates in v1 | ARCHITECTURE.md Q3 |
| 2026-10-14 | No referral or affiliate income. Rankings stay neutral. | ARCHITECTURE.md Q5 |
| 2026-10-14 | Support both cloud sessions and local development (macOS, Windows via WSL) | ARCHITECTURE.md Q8 |
