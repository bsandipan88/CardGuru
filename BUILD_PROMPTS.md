# CardGuru: Phase-by-Phase Prompts for Claude Code

How to use:
1. Create an empty Git repo and put CLAUDE.md at the root.
2. Open Claude Code in that repo.
3. Paste ONE phase prompt at a time. Review the plan, approve, let it build, review the result, then move on.
4. Do not paste all phases at once. Quality comes from reviewing each phase.

---

## Phase 0: Plan (no code yet)
```
Read CLAUDE.md fully. Do not write application code yet.

Produce docs/ARCHITECTURE.md covering:
- System context and component diagram (Mermaid)
- Choice of API framework (NestJS vs Fastify) and ORM (Prisma vs Drizzle), with reasoning
- Data model for users, consents, cards, user_cards, benefit_rules (versioned), alerts, trips, questions, evaluations
- How the rules engine, AI layer and alerts worker interact
- Security and DPDP compliance approach
- Testing strategy and CI pipeline
- Phased delivery plan matching BUILD_PROMPTS.md

Then list open questions and risks you see. Wait for my answers before Phase 1.
```

## Phase 1: Monorepo foundation
```
Following docs/ARCHITECTURE.md, scaffold the monorepo:
- pnpm workspaces + Turborepo
- apps/mobile (Expo + Expo Router), apps/web (Next.js), apps/admin (Next.js), apps/api
- workers/alerts, workers/docs (Python)
- packages/rules-engine, packages/shared, packages/ui
- Docker Compose for Postgres and Redis
- ESLint, Prettier, TypeScript strict, shared tsconfig
- GitHub Actions: install, lint, typecheck, test, build on every PR
- .env.example files, README with local setup steps

Every app should start locally with a single command and show a health check.
Update docs/PROGRESS.md when done.
```

## Phase 2: Rules engine and data model
```
Build packages/rules-engine and the database schema.

Rules engine requirements:
- Pure TypeScript, no I/O, deterministic
- Inputs: user's cards, published rules, a context (purchase amount, merchant category, channel, date, airport, trip)
- Outputs: ranked recommendations with transparent math, applicable caps, exclusions and the rule versions used
- Functions: getWalletBenefits, recommendCardForPurchase, getTripBenefits, getUpcomingDeadlines, compareRuleVersions (devaluation impact)
- 95%+ test coverage with table-driven tests

Database:
- Versioned benefit_rules with status workflow (draft, in_review, published, retired)
- Seed ONLY fictional demo cards ("Demo Bank Platinum", "Demo Bank Debit Classic", etc.). Do not invent real bank benefits.
```

## Phase 3: API
```
Build apps/api:
- Passkey (WebAuthn) authentication as the primary login; phone verification via a pluggable SMS provider interface (mock in dev) used only at sign-up and as one step of recovery
- Device binding, session risk scoring and step-up authentication for sensitive actions
- Endpoints: cards catalog, user cards, wallet, purchase recommendation, upgrade advisor, benefit availment links, trips, alerts, annual review, claims guides, consents, data export, account deletion
- zod validation, rate limiting, audit logging, OpenAPI spec
- Generate a typed client into packages/shared for web and mobile
- Integration tests for every endpoint
```

## Phase 4: Mobile app (iOS and Android)
```
Build apps/mobile with Expo:
- Screens: onboarding (phone verification, passkey creation, pick cards by name), benefits wallet with an "Avail now" action on every benefit, ask before you pay, upgrade advisor, trip mode, alerts feed, annual review, claims helper, settings (devices, consents, export, delete account)
- Security: passkeys with on-device biometrics, certificate pinning, app attestation, root/jailbreak detection, screenshot protection on sensitive screens
- Use packages/ui design tokens; clean, trustworthy fintech look; light and dark mode
- Every benefit shows "Verified on <date>" and a source link
- Push notifications via Expo Notifications
- Offline-friendly caching of the user's wallet
- Maestro E2E tests for onboarding and wallet flows
- EAS Build config for iOS and Android (do not submit to stores)
```

## Phase 5: Web app
```
Build apps/web with Next.js, mirroring the mobile flows:
- Responsive down to 360px width, WCAG 2.1 AA
- Shared API client and types
- Playwright E2E tests for onboarding, wallet, trip mode
```

## Phase 6: Admin console for rules analysts
```
Build apps/admin:
- Role-based access (analyst, reviewer, admin)
- Create and edit benefit rules with required source_url
- Side-by-side diff between rule versions
- Review queue: approve, reject, request changes
- Publish workflow with audit log
- Impact preview: which users and how much yearly value a rule change affects
```

## Phase 7: Alerts worker
```
Build workers/alerts:
- Scheduled jobs: quota resets, points expiry, milestone and fee-waiver deadlines, renewal reminders
- Event jobs: when a rule version is published, compute impact per affected user and create devaluation alerts
- Idempotent jobs, retries, dead-letter queue
- Delivery via push and email (WhatsApp channel interface stubbed for later)
- User controls for alert frequency and quiet hours
```

## Phase 8: AI copilot
```
Build the AI layer:
- Provider-agnostic interface with Claude and Gemini adapters, chosen by config
- Tool calling: the model can only call rules-engine functions; it never states numbers that did not come from a tool result
- Answers include source links and "Verified on" dates
- Refuses to guess when no verified rule exists
- Golden evaluation set: start with 100 questions in evals/, run in CI, fail the build below the configured accuracy threshold
- Log questions (with consent) to improve coverage
```

## Phase 9: Document worker (T&C monitoring)
```
Build workers/docs in Python:
- Fetch configured public bank T&C pages and PDFs on a schedule (respect robots.txt and rate limits)
- Extract text, hash and diff against the previous version
- When content changes, use the LLM to propose draft rule updates
- Drafts go ONLY to the admin review queue. Never auto-publish.
```

## Phase 10: Production hardening
```
Prepare for production:
- Terraform for an India cloud region: managed Postgres, Redis, container hosting, secrets manager, CDN for web
- OpenTelemetry tracing, Sentry, dashboards for latency, errors, alert delivery and AI answer accuracy
- Security review against OWASP ASVS Level 2; fix findings
- Load test wallet and recommendation endpoints (target p95 under 300 ms)
- Backup, restore and disaster recovery runbook in docs/RUNBOOK.md
- DPDP compliance checklist in docs/COMPLIANCE.md
- Release checklist for App Store, Play Store and web
```

---

## Reusable review prompt (use after any phase)
```
Review everything built in this phase against CLAUDE.md. List:
1. Anything that violates the non-negotiable rules (AI, data, security)
2. Missing tests or weak coverage
3. Security or privacy risks
4. Shortcuts that will hurt us in production
Then propose fixes and wait for approval.
```
