# NFL Player Value Metrics: Project Overview

> Working document. Last updated: November 25, 2026

---

## 1. Vision

We are building an AI-driven NFL analytics startup that evaluates player performance down to the snap level. Our core product is a **family of advanced player value metrics**, comparable in spirit to **VORP and WAR in the NBA and MLB**, adapted to the realities of football.

### A note on "minutes played"
The NFL has no minutes-played stat. The equivalent unit is the **snap** (offensive, defensive, and special teams snaps, snap share, and personnel groupings). Our analytical foundation is **play-by-play and player-level snap data**.

---

## 2. What We Are Trying to Accomplish

### Primary goal
Create a credible, defensible, and validated **value-over-replacement metric for NFL players**, built position by position and eventually rolled up into a team-comparable WAR-style number.

### Why this is hard (and why that's the opportunity)
- **Interdependence:** Football is a 22-player game. A QB's results depend on his line, receivers, and scheme.
- **Positional value differs:** A "win" from a guard and a "win" from a QB are not directly comparable without a positional adjustment.
- **Replacement level must be defined per position:** Common approaches use the next-best players by snaps (QB2, RB3 tier) or a minimum-salary baseline.
- **Uneven data quality:** Offensive line and coverage are hard to evaluate without tracking or charting data.
- **Small samples:** 17 games means noisy individual estimates.
- **No ground truth:** "True value" can't be observed, so validation is indirect.

### Core modeling approach
1. Use a **common currency**: Expected Points Added (EPA) and/or Win Probability Added (WPA).
2. **Attribute** value to individual players, adjusting for teammates, opponents, and context.
3. Convert to **wins**.
4. Subtract **position-specific replacement level**.
5. Apply a **positional adjustment**.
6. Use **Bayesian shrinkage / partial pooling** to control noise from small samples.
7. Report **uncertainty intervals**, not just point estimates.

### Feasibility assessment
We believe this is feasible for a 4-person team **if staged**. The risk is attempting all positions on day one, not the idea itself. Public work (nflfastR EPA/WP models, snap-adjusted skill metrics, pass-rush and coverage models) shows the core approach works. A QB, RB, WR/TE, and pass-rusher metric with published validation is already a valuable product.

---

## 3. Scope and Phasing

| Tier | Timeline | Positions | Data | Notes |
|---|---|---|---|---|
| **Tier 1** | Months 1-3 | QB, RB, WR/TE | Open data (nflverse) | v0 metric with stability and predictive diagnostics |
| **Tier 2** | Months 3-6 | Pass rush, run defense | Open data, snap-level models | Include uncertainty bands |
| **Tier 3** | Later | OL, coverage, special teams | Licensed tracking data or partner | If unavailable, publish with explicit confidence labels |
| **Roll-up** | After enough positions validated | All | N/A | Combine into team-comparable WAR only when validated |

Until the roll-up, **per-position value over replacement is a legitimate product on its own.**

---

## 4. Validation Standards

A good metric must be:
- **Stable year to year** (signal, not noise)
- **Predictive** of future player and team results (out-of-sample)
- **Consistent with film and scouting** assessments (sanity checks by domain expert)
- **Transparent**: methodology documented and defensible in plain English

We plan to **publish diagnostics**. Credibility in the football analytics community depends on it.

---

## 5. Team Structure and Task Distribution

| Role | Phase 1 Responsibilities | Deliverables |
|---|---|---|
| **CEO / Product Lead** | Customer discovery, strategy, licensing and partnerships, pricing, prioritizing positions and use cases | Priorities, partner conversations, wedge customer definition |
| **Data Engineer** | Ingest nflverse (play-by-play, snap counts, rosters, injuries). Build player-game-play schema with stable IDs. Schedule updates and data-quality tests. | Reproducible warehouse/tables, data dictionary |
| **ML / Data Scientist (Lead Modeler)** | Define value framework (EPA to wins), replacement-level methodology, first position models, backtesting harness | Model spec doc, v0 metric for 1-2 positions, validation report |
| **Second Modeler or Domain Expert** | Review outputs against football reality, flag nonsense results, define positional adjustments and scheme/context factors | Sanity-check lists, positional value assumptions |
| **Full-Stack Engineer** | Build internal leaderboard/explorer, then public-facing version and API | Internal dashboard, API over metric tables |

**Notes**
- The metric is our core IP. Methodology should be the **shared responsibility of the modeler and domain expert**, not a single person's black box.
- With only 4 people, one person may cover two roles (for example, CEO plus an engineering function), freeing a seat for a domain expert.
- Everyone, not just the CEO, should speak with customers regularly.
- Define **equity splits and vesting** (typically 4 years, 1-year cliff) in writing up front, along with **IP assignment agreements** for every contributor.

---

## 6. GitHub and Engineering Setup

### Organization and access
- Create a **GitHub Organization** (company owns the code), not personal accounts.
- Use **Teams** with minimum necessary permissions:
  - Founders/leads: Admin or Maintain
  - Engineers and modelers: Write
  - Advisors/contractors: Read, or a separate repo
- Require **two-factor authentication** for all members.

### Suggested monorepo structure
```
/data-pipelines    # ingestion, cleaning, schema, tests
/models            # metric definitions, training, backtesting
/notebooks         # exploration (not production)
/app               # frontend + API
/docs              # methodology, data dictionary, decisions
```

### Workflow rules
- Protect `main`: pull requests required, at least one review
- Short-lived feature branches and small PRs
- PR template with checklist (tests pass, backtest results attached for model changes)
- GitHub Actions for linting, tests, and data-quality checks
- Issues and a Projects board with labels: `data`, `model`, `app`, `research`
- No large datasets or secrets in the repo: use Git LFS or object storage, encrypted secrets, `.gitignore`, and secret scanning

### Reproducibility
- Pin dependencies (`uv`, `poetry`, or `pip-tools`)
- Version data snapshots
- **Version the metric itself** (v0.1, v0.2) with a changelog so any published number is traceable to its code and data
- Maintain a living **methodology document**

---

## 7. Data Strategy

- **Prototype on open data:** nflverse (nflfastR, nflreadr) for play-by-play, snap counts, rosters, and injuries.
- **Plan for licensed data:** Sportradar, Genius Sports, PFF, and NFL Next Gen Stats are options. Terms and costs vary, so confirm current terms directly.
- **Biggest strategic risk:** If differentiation depends on tracking-level data, licensing cost and redistribution rights could decide whether the business works. Settle this plan early.
- Consult a **sports-data attorney** before commercial launch.

---

## 8. First 30 Days

| Week | Focus |
|---|---|
| **1** | Set up org, repo, agreements, and CI. Data engineer begins nflverse ingestion. Modeler drafts value framework. |
| **2** | Stable player-play table with snap counts joined. Agree on replacement-level definition. |
| **3** | First prototype for one position (QB is the easiest, since EPA per play is well established). Backtest against naive baselines. |
| **4** | Internal dashboard, domain-expert review, list of where the metric breaks. Decide the next position. |

---

## 9. Longer-Term Roadmap

1. **Validate:** Customer interviews, rough prototype, early users.
2. **MVP:** Core pipeline plus one differentiated, measurably better feature. Publish backtests. Begin charging early.
3. **Traction:** Refine based on retention and willingness to pay. Secure commercial data licensing. Build distribution through content, social, and creator/podcast partnerships.
4. **Scale or fund:** Pre-seed/seed raise or lean growth on revenue. Build moat through proprietary derived metrics, brand, and workflow lock-in.

---

## 10. Risks and Pitfalls

- **Data access and licensing** (largest threat)
- **Seasonality:** the NFL is roughly a 5-month product cycle. Plan offseason revenue (draft, free agency, or other sports).
- **Free competition:** many strong free tools exist, so we must be clearly better or more convenient.
- **Overbuilding AI:** value comes from accurate, timely, actionable insights, not model complexity. Strong baselines (regression, gradient boosting) often beat deep learning on tabular sports data. Use LLMs for natural-language querying and explanations.
- **Overscoping:** avoid attempting all positions at once.

---

## 11. Open Questions

- [ ] Who is our wedge customer (fantasy, bettors, media, teams)?
- [ ] What data do we currently have access to, and what is our path to licensed tracking data?
- [ ] Which position group do we tackle first? (Recommended: QB)
- [ ] How will we define replacement level (snap-based vs. salary-based)?
- [ ] Who fills the domain-expert seat?
- [ ] Equity, vesting, and IP agreements: finalized?
