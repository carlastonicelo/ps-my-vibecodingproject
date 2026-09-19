# Engineering Handoff Note

> Module 4 · Production Specs. Open the black box, make the build legible to an engineer.

## What this is

_One paragraph an engineer can read in 60 seconds._

This is a frontend-only, clickable prototype of a guided week-one onboarding journey for a fictional B2B SaaS product ("Relay"), built to test one hypothesis: a guided activation path gets accounts to first value faster and reduces 90-day churn. There is no backend, no database, and no persistence — every number is either a hardcoded observed baseline (22% activation, 1.4 seats, 6.2-day TTFV, 30% churn, $1.1M ARR at risk) or content explicitly labeled as hypothetical sample data. The code is organized by feature (src/features/onboarding, src/features/experiment-results, src/features/retention-engine), each screen is its own component named after the PRD screen, and all sample content lives in data/ modules separate from display. The whole journey is state held in one container component (RetentionEngine.tsx); brief generation and invitation delivery are simulated with timers and flags. If you're picking this up to productionize it, your job is replacing the simulated data flows with real integrations and event instrumentation — the UI structure, gating logic, and copy contract are already in place and verified end-to-end.

## Architecture (plain language)

- **Frontend:** Frontend only. This is a single-page React app built on TanStack Start (file-based routing), rendered at one route: src/routes/index.tsx, which is a thin entry that mounts the app container and owns page metadata. There is no API layer, no server functions in use, and no auth.
- **Backend / data:** Backend / data: none. "Data" means two static TypeScript modules:  src/features/onboarding/data/journey.ts — the seven journey steps, sample workspace ("Atlas Freight"), the sample brief question and its scripted answer, sample teammates. src/features/experiment-results/data/experiment.ts — the five exact observed baselines, three verbatim customer quotes, and three pre-written hypothetical outcomes (promising / inconclusive / failed).
- **Key flows:** Journey flow — RetentionEngine.tsx holds all state (current step, furthest step reached, workspace name, question, brief status, invites). OnboardingJourney.tsx maps the step index to one of seven screen components: Welcome → Minimum Setup → First Value → First Value Confirmation → Team Adoption → Team Invitation → Activation Complete. A furthestStep gate prevents jumping ahead; the Relay header mark resets the journey.
Brief generation (simulated) — clicking "Create my first brief" sets briefStatus to loading for 1,400 ms, then ready (or error if the tester-only simulateBriefFailure flag is on). The brief content is a fixed sample answer; only the question text is live from user input. Exact UI copy for loading/error states is specified and must not drift: "Creating your weekly brief…" / "We couldn't generate your brief. Try again."
Invitation flow (simulated) — emails are validated by a regex helper (lib/validation.ts). At least two valid addresses are required to complete activation. An invalid address produces the exact error "Invite couldn't be sent. Check the email and try again." with Retry; editing the input clears the error. "Use two sample teammates" fills maya@example.com and jon@example.com.
Results flow — a ghost button ("Experiment results") swaps the view to the secondary results screen: observed baselines, an outcome switcher between three scripted scenarios, comparison cards, a kill-switch panel (if activation doesn't rise from 22% or TTFV doesn't fall from 6.2 days → pivot), and customer quotes.
Styling is Tailwind v4 with semantic design tokens in src/styles.css (oklch values, Space Grotesk / DM Sans / JetBrains Mono). No hardcoded colors in components.

## What's solid vs. what's duct tape

| Area | State | Notes |
|---|---|---|
| struct | solid | One file per PRD screen, data fully separated from display, shared UI primitives extracted (StepActions, JourneyNavigator, Comparison, KillSwitch). |
| Simulated data flows | rough | Brief generation and invitation delivery are simulated; state is in-memory and sample content is hardcoded. |

## Risks & assumptions for the team

The hypothesis is unvalidated. The prototype tests whether early activation is the primary churn driver; the kill switch exists precisely because it may not be. Don't treat the scripted "promising" outcome as evidence.
Numbers must stay exact. The five baselines (30% / 22% / 1.4 / 6.2 / $1.1M) came from the product owner and may not be modified, extrapolated, or rounded. Any new number must be explicitly labeled hypothetical/sample. This is a hard content contract.
No real integrations. The setup step shows a fictional connection; production needs actual data-source integrations, which will reshape Minimum Setup and First Value.
Email validation is cosmetic. The regex accepts syntactically valid addresses; deliverability, deduplication, and real invite sending are entirely open.
Measurement doesn't exist. Activation rate, TTFV, seats/account, and churn are displayed values, not instrumented events. A production build needs event tracking and cohort definitions before the kill switch can be evaluated on real data.
SSR/browser boundary. TanStack Start prerenders; nothing currently reads browser storage during render, but anyone adding persistence must keep that boundary (read in effects, not module scope) or hydration will break.
Single-tenant demo. No auth, no tenancy, no i18n. Accessibility was a stated goal but has not been audited with a screen reader.

## How to run it

```
npm install
npm run dev        # dev server (Vite) — the app is the single route at /
Dev: npm run dev → open the served URL. No env vars, no database, no secrets needed.
Build: npm run build (production) or npm run build:dev. Note this template targets an edge/Worker runtime — no Node-only packages in server code (currently moot: there is no server code).
Lint/format: npm run lint, npm run format.
Manual smoke test (5 min): Walk the journey: Welcome → setup (workspace name + question required) → create the brief (watch the "Creating your weekly brief…" state) → confirmation → Team Adoption → invite teammates (try one invalid email to see the retry error, then "Use two sample teammates") → Activation Complete → "Experiment results" → switch the three outcomes. Clicking the header mark resets.
Key files, in reading order: src/routes/index.tsx → src/features/retention-engine/RetentionEngine.tsx (all state + flows) → src/features/onboarding/OnboardingJourney.tsx (step map) → src/features/onboarding/screens/* and src/features/onboarding/data/journey.ts → src/features/experiment-results/ExperimentResultsScreen.tsx and its data/experiment.ts → src/styles.css (design tokens).

Companion docs (delivered separately, not in the repo): the PRD (retention-engine-prd.md), which defines the screen names, scope, and requirements this structure follows, and the audit README (the-retention-engine-audit-readme.md).
```
