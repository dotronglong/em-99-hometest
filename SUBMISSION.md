# Engineering Manager — Long Do

# Part A — 90-day plan

## Diagnosis (A.1)

- **Weak money safeguards:** duplicate credits and rounding errors indicate missing correctness controls; payment regressions escaping review show approval alone is insufficient.
- **Concentrated knowledge:** dependence on S. and T. increases operational risk and slows recovery. Harmful review behaviour may further discourage learning.
- **Late quality feedback:** regressions reach QA near release, causing queues, rework, and delays.
- **Incomplete learning:** informal incident ownership and missing postmortems allow failures and unreliable estimates to recur.

I will validate these hypotheses through team conversations, incident evidence, and a review of the region-3 overrun during Sprint 1.

## Priorities and trade-offs (A.2)

- **Priorities:** agree with Product and the CEO to address high-risk money failures and deliver the minimum compliant region-4 launch by week 10. Target corrective-action closure within two sprints, validating scope and estimates in Sprint 1. Reconciliation and incident response continue throughout.
- **Trade-offs:** defer discretionary promotions, large nonessential features, broad rewrites, and lower-impact backlog work. Negotiate later dates for those commitments to protect capacity and buffer. Active money exposure takes precedence; remove optional scope first when priorities compete. Unresolved mandatory safety or regulatory requirements trigger immediate escalation and an explicit launch decision.

## Two-week sprint execution (A.2–A.4)

Use Scrum with two-week sprints and clear goals. I own priorities and escalation; named Payments and region-4 owners lead delivery. Each sprint starts with estimated work, acceptance evidence, and capacity reserved for operations. This is an initial forecast, reviewed against demonstrated progress and remaining dependencies—not a fixed promise based on assumed velocity.

- **Sprint 1 — Understand failures and establish a credible plan:** contain active money exposure, reconstruct priority incidents, begin metric tracking, and estimate corrections. Confirm region-4 scope, provider access, regulatory dependencies, owners, and capacity without assuming new hires.
- **Sprint 2 — Close identified high-risk money gaps:** demonstrate corrections through failure-case tests and reconciliation, while proving initial region-4 payment journeys. If critical corrections exceed capacity, escalate immediately with scope and staffing options.
- **Sprint 3 — Make region 4 function end to end:** complete launch-critical integrations and regional configuration using verified safeguards; assess corrective effectiveness and reforecast remaining work against week 10.
- **Sprint 4 — Prove operational readiness:** demonstrate representative load, reconciliation, and recovery; confirm approvals, responder coverage, and residual-risk owners to support the launch decision.
- **Sprint 5 — Launch safely by week 10:** increase exposure gradually where permitted, monitor all regions, and pause affected flows when agreed stop conditions are reached.
- **Sprint 6 — Establish sustainable operations:** resolve critical launch issues, verify reconciliation and backup coverage, and confirm squad ownership. Use days 85–90 to assess organisational health, actual support demand, and next-quarter priorities.

Corrective work will follow postmortem findings, including durable idempotency, exact decimal arithmetic in money flows and reporting, and bounded batch workloads that preserve database capacity for live transactions. Regional deployment will use repeatable configuration and target-environment validation.

Owners raise blockers immediately. I will coordinate scope, sequencing, or capacity changes, record displaced commitments, and escalate deadline risks with a recommendation. Launch support uses planned coverage and protected capacity; deferred work resumes only as operational stability permits.

## Process changes (A.3)

- **Incident response and learning:** establish an incident commander, technical responder, and communications owner, with escalation and containment authority. Begin immediately for serious incidents. Use proportionate, blameless postmortems to identify technical and organisational contributors; corrective actions have owners and close only after effectiveness is demonstrated. Success is lower MTTR and fewer repeat incidents.
- **Earlier, risk-based testing:** developers and QA agree on critical failure scenarios before implementation. Developers test them as they build; QA independently checks high-risk behaviour. Success is lower change failure rate and fewer regression-driven release delays.
- **Ranked commitments and evidence-based reviews:** maintain one prioritised plan, expose dependencies, agree displaced work, and reforecast early using existing ceremonies. Success means risks surface while scope and sequencing can still change.

Approved AI tools will support code understanding, implementation, and test-gap discovery, with engineers validating outputs. This supports learning and delivery rather than introducing a separate transformation programme.

## Three health measures (A.4)

During Sprint 1, agree definitions and begin lightweight tracking from existing records. Establish a provisional baseline where evidence permits, validate it by Sprint 2’s review, and label gaps rather than reconstruct unreliable history.

- **MTTR:** time from incident declaration to service recovery; record containment separately and compare similar severity.
- **Repeat incidents:** recurrence of previously identified failure mechanisms despite corrective actions.
- **Change failure rate:** proportion of deployments causing an incident, rollback, or urgent fix.

I own reviews with the leads: weekly for emerging problems, at Sprint Reviews for corrective effectiveness, and at week 6 and day 90 for sustained improvement. Separate region-4 outcomes from existing regions and treat small samples cautiously. Weak results trigger case analysis and changes to work or capacity. Zero money-impacting incidents remains the objective; reconciliation is an operational control.

Sprint Reviews also compare forecast with demonstrated progress and remaining critical-path work, keeping delivery predictability visible without adding a dashboard.

## People and staffing (A.5)

- **Build respectful collaboration:** recognise strengths, listen, and observe interactions. Address harmful feedback privately with specific examples and clear expectations, including with S.; follow up and use formal support if behaviour persists.
- **Grow shared ownership:** protect pairing so S.’s wallet expertise and T.’s pipeline knowledge spread. Reduce T.’s competing backlog/on-call burden through reprioritisation and backup coverage. Give D., mids, and juniors supported ownership with named mentors; agree manageable mentoring capacity with L.
- **Share quality responsibility:** developers own verification and targeted automation; QA contributes early risk assessment and independent testing. Limit simultaneous release candidates and rebalance work rather than add responsibilities to overloaded teammates.
- **Strengthen capacity:** investigate why two backend roles remain open—role scope, candidate pipeline, compensation, or interview delays—and agree corrective actions with recruiting. Prioritise transactional-systems expertise and backend/data operational coverage. Plan the launch around the existing team.

# Part B — PR review and conversation

## Code review

**Request changes — I would block the merge for two reasons:**

1. **`accountId` is not used.** This run processes members across all brands, even though it is meant for one account. Please filter the relevant queries by `accountId` and add a test showing that other accounts are unaffected.

2. **The job loads all members at once.** Even with account filtering, a large account could use too much memory and take too long to process within one HTTP request. Please process members in smaller batches and test the database load and execution time with realistic member counts.

## The conversation

I would walk D. through the concerns, ask what he tested and assumed, and agree on the fixes and tests needed before merging. He would keep ownership, with pairing support if helpful.

I would reach out to S. privately to learn what criteria he uses for code review and better understand the current process. We would walk through the PR together to identify risks the criteria missed and agree on how to strengthen them.

Together, we would add tests for these failure cases and agree on a few checks for payment changes. The goal is for engineers to catch these problems through normal development and review, without relying on me to check every PR.

# Part C — Two situations

## C1 — First 24 hours

**First 15 minutes:** Tell the CEO: “Incorrect balances are reported. Financial exposure is possible; the amount and whether it continues are unconfirmed. We are investigating and containing the risk. Next update in 15 minutes.”

Assuming these severity definitions, declare P1 immediately; escalate to P0 if affected accounts grow rapidly, ongoing loss is confirmed, or containment fails.

**Coordinate:** I act as incident manager; S. leads technical investigation with another engineer. Bring T.’s backup into the investigation. If pipeline expertise is essential, make one availability check with T., without making response depend on interrupting his leave.

Maintain three communication channels:

- Leadership/board: concise, nontechnical updates every 30 minutes.
- Engineering: continuous findings, actions, and decisions in one incident record.
- Support: incoming escalations and customer guidance every two hours, sooner for material changes.

**Within one hour:** agree and execute containment, acting sooner where necessary—for example, pausing affected credits and restricting withdrawals from potentially incorrect balances. If isolation is unreliable, consider wider region-2 restrictions. Preserve logs and transaction evidence.

**Following hours:** trace affected accounts, brands, and regions. Finance/operations supports reconciliation of provider records, ledger entries, balances, and withdrawals to quantify exposure. Involve compliance to assess reporting obligations.

If the cause remains unknown, escalate to the CEO with current exposure, containment effectiveness, and recommended restrictions; investigation continues. If identified, prepare a reviewed hotfix with failure-case tests and a recovery plan.

**Recovery and follow-up:** restore flows gradually after independent verification and reconciliation review. Correct balances through approved, auditable entries. Arrange responder handover and rest. Within 24 hours, communicate status and remaining risks, and schedule a blameless postmortem with owned, verifiable corrective actions. Do not promise resolution before evidence supports it.

## C2 — A public challenge to the plan

**In the next 60 seconds**, I would stay calm, avoid reacting defensively, and say: “L., I want to understand what is slowing us down. Can you give one example of a checklist item that adds work without reducing risk? We should change steps that don’t help. Until we agree on a change, please don’t ask the squads to ignore the checklist. Let’s identify the concern and decide how to address it.”

I would hear the example without turning planning into a public argument, remove unnecessary steps, and assign an owner to clarify disputed checks.

**Afterward**, I would speak with L. privately. I would ask what experience led to his frustration, whether the team shares it, and whether I introduced too much process without enough consultation. I would also explain that his influence matters: challenging my plan is welcome; encouraging teams to bypass agreed controls undermines shared decisions.

With L., engineers, and QA, I would simplify the checklist around actual risks, assign changes, and review results next sprint. I would explain decisions to both squads, acknowledge unnecessary work I introduced, and address any continued bypassing privately.

## AI-use disclosure

I used ChatGPT to discuss the assignment, challenge and refine my reasoning, and help structure and edit this response. The final recommendations reflect my judgment, and I am responsible for defending them.
