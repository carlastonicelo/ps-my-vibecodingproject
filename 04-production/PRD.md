# Living PRD

> Module 4 · Production Specs. Refactor for readability; extract a living PRD that stays true as the build evolves.

## Problem

_What user problem does this solve? Tie to the validated hypothesis._

New B2B SaaS accounts churn inside their first 90 days because they never reach first value and usage never spreads beyond the original buyer.

## Users & jobs

- **Primary user:** A new B2B SaaS account admin (ops lead, eng manager, or founder persona) in their first week with the product.
- **Job to be done:** When I sign up, guide me to one useful, shareable output in week one, so I can prove the product's value to myself and get my team using it.

## Scope

- **In:** In scope (built in the prototype):

A seven-step guided onboarding journey: Welcome → Minimum Setup → First Value → First Value Reached → Team Adoption → Team Invitation → Activation Complete.
Welcome screen communicating the core value proposition ("Turn scattered updates into a weekly brief your team can act on").
Minimum setup: workspace name and the question the first brief must answer (both required to proceed).
First Value: generating the first weekly operations brief, with a loading state, an error state with retry, and the brief as the visual focus once created.
First Value Reached: a confirmation transition stating that the brief delivered a useful answer.
Team Adoption: a transition explaining that activation requires turning individual value into shared team value by inviting at least two teammates.
Team Invitation: add/remove teammate emails with validation, empty state, per-invite error with retry, and a hard requirement of at least two valid emails before completion.
Activation Complete: confirmation that the workspace is connected, the first brief was created, and the team was invited.
A secondary Experiment Results view: the five exact observed baselines, three switchable simulated outcomes (promising / inconclusive / failed) that recompute a continue / keep-testing / pivot decision, the kill-switch criteria, and the three customer quotes.
Clear data-provenance labeling throughout: observed baselines, provided targets, and hypothetical sample data are visually and textually distinct.
- **Out (explicitly):** Out of scope (not built):

No backend, database, or persistence across sessions. All state is client-side and resets with the journey.
No real integrations: the "Weekly project updates" connection is shown as pre-connected sample content.
No real email sending: invitations are validated in-memory only.
No real brief generation: the brief content is scripted sample data behind a simulated delay.
No real analytics or cohort tracking: experiment outcomes are fixed simulated scenarios, not measured data.
No authentication, multi-account management, or billing.

## Requirements

| # | Requirement | Priority | Acceptance criteria |
|---|---|---|---|
| 1 | Brief generation shows | Must | be the exact loading message "Creating your weekly brief…" |
| 2 | First Value Reached transition | Should | confirms the brief answered the user's question |

## Data & events

_What gets stored, what gets tracked._

Real (validated input): the five observed baseline metrics, the 1-day time-to-first-value target, and the three customer quotes. These come from the experiment context and are used exactly, never extrapolated.

Mocked (must be labeled hypothetical/sample): the sample connection ("Weekly project updates"), the generated brief content (blocker, next action, value delivered), sample teammate addresses, the three simulated outcome scenarios and their numbers (e.g. 46% activation in the promising case), and the sample workspace/question defaults.

Client-side state (no persistence): current view, current step, furthest-reached step, workspace name, live question, brief status (idle / loading / error / ready), simulation toggle, invite draft, invite list, invite error, selected outcome scenario.

Events the prototype models (in-memory only, not instrumented):

journey_started — user leaves Welcome.
setup_completed — workspace and question submitted (gates step 2).
brief_requested / brief_succeeded / brief_failed / brief_retried — generation lifecycle.
first_value_confirmed — user continues past First Value Reached.
invite_added / invite_failed / invite_retried / invite_removed — invitation lifecycle.
activation_completed — two valid invites present and user finishes.
outcome_scenario_selected — reviewer switches simulated results.
A production build would need real event instrumentation for steps 2–6, since activation rate and time-to-first-value — the hypothesis metrics — are computed from exactly these events.

## Open questions

What real data sources power the brief (which integrations, what sync latency is acceptable)?
How is "first value reached" detected in production — brief generated, brief viewed, or brief shared? The prototype treats generation as the milestone.
Is the two-teammate minimum the right activation gate, and does it vary by segment (the baseline is 1.4 seats/account)?
What sample size and experiment duration are needed to call the 22% → 40% activation lift conclusive?
How are simulated outcome scenarios replaced with real cohort measurement, and who owns the continue/keep-testing/pivot decision?
What happens to users who stall at the brief error state — is there a human follow-up path?
Does the guided journey apply to all new accounts or only the mid-market segment implied by the churn evidence?
