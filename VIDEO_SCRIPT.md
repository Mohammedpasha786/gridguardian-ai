# Video Script — GridGuardian AI (target: 4:30–5:00)

Record your screen (terminal + `adk web` Dev UI + Antigravity) with this
as a guide, not a word-for-word script. Swap in your own voice. Timestamps
are approximate budgets, not hard cues.

---

## 0:00–0:45 — Problem Statement

> "Solar-plus-battery microgrids decide how to dispatch power based on
> today's price and today's weather. What they usually *don't* factor in
> is regional climate risk — a coastal substation heading into elevated
> flood or storm-surge probability shouldn't be drained for a few cents of
> savings; it should hold reserve to island itself if the grid drops. That
> connection — climate risk to dispatch decision — is usually made by hand,
> by an analyst, if it's made at all. GridGuardian AI automates it."

*(Show: a simple slide or terminal `cat` of the problem statement, or just
talk over the architecture diagram.)*

## 0:45–1:30 — Why Agents?

> "This isn't one script, it's four specialist agents in a pipeline. A
> Climate Risk agent interprets hazard indices into a reserve
> recommendation. A Demand Forecast agent predicts load from weather. A
> Dispatch Optimizer agent solves a linear program honoring that reserve.
> And a Resilience Advisor agent — which has *no tools at all* — turns all
> three structured outputs into one plain-English briefing. Because that
> last agent can't call any tool, it can't invent a number that isn't
> already in its input. That's the part a script can't do: turning four
> JSON blobs into a calibrated, prioritized recommendation a tired
> operator can actually trust."

## 1:30–2:30 — Architecture

*(Show `docs/images/architecture.png` or the mermaid diagram in
`docs/ARCHITECTURE.md`.)*

> "Each agent writes a typed result into shared session state — climate
> risk result, demand forecast result, dispatch plan result — and the next
> agent reads it through ADK's instruction templating. All three
> tool-using agents share one tool layer, defined once in
> `grid_tools.py`, and that exact same tool layer is exposed as a
> standalone MCP server with FastMCP — so Claude Desktop, the Gemini CLI,
> or another team's agent could call into GridGuardian without touching
> any of my agent code. Underneath, weather comes from a free public API
> with a synthetic fallback so the whole thing works offline; climate risk
> uses a synthetic model anchored to IPCC AR6 sea-level-rise ranges; the
> demand model is a bagged-regression-tree ensemble; and dispatch is a
> five-line linear program solved with scipy's HiGHS solver."

## 2:30–3:00 — Security

*(Show `security/controls.py` briefly or the SECURITY.md.)*

> "Every tool call passes through rate limiting and input validation
> before it touches the solver, secrets only ever come from environment
> variables, and every call is audit-logged with a hash of its arguments —
> never the raw operational data — so nothing sensitive ends up in
> plaintext logs."

## 3:00–4:00 — Demo

*(Live terminal. Run these in order, narrating briefly.)*

```bash
gridguardian climate-risk --region Coastal-North --lat 13.08 --lon 80.27
gridguardian forecast --lat 13.08 --lon 80.27 --hours 24
gridguardian dispatch --load 220 --solar 90 --soc 60 --capacity 500 --price 0.15 --reserve 0.2
gridguardian briefing --region Coastal-North --lat 13.08 --lon 80.27
```

> "Here's the full pipeline producing a briefing in one call... [let it
> run, show the final Markdown output]... Headline, climate outlook,
> demand outlook, the dispatch recommendation with the reserve explained,
> and action items only if something actually needs attention."

*(Then show `adk web` Dev UI for a few seconds, stepping through the
agent trace, to demonstrate Antigravity / the ADK Dev UI workflow you
used while building.)*

## 4:00–4:45 — The Build

> "Built with Google ADK for the multi-agent orchestration, FastMCP for
> the MCP server, scipy and scikit-learn for the optimization and
> forecasting, and packaged as a pip-installable CLI plus a Docker image
> with a GitHub Actions pipeline running ruff, bandit, and 37 pytest
> tests on every push — all of which pass without needing an API key,
> since they test the deterministic tool layer and agent wiring directly.
> [Mention here, honestly, how much of the iteration happened in
> Antigravity vs. your usual editor.]"

## 4:45–5:00 — Close

> "GridGuardian AI: climate risk, demand, and dispatch, synthesized into
> one trustworthy recommendation — built for the Agents for Good track of
> the Kaggle AI Agents Intensive. Code's on GitHub, link in the
> description."

---

### Recording checklist

- [ ] Screen-record at 1080p+, mic audio clear, no background noise
- [ ] Show the architecture diagram for at least 5 seconds (judges screenshot it)
- [ ] Actually run `adk web` on camera — this is your Antigravity/Dev-UI evidence
- [ ] Keep total runtime ≤ 5:00
- [ ] Upload to YouTube as **public** or **unlisted** (not private), then attach to the Kaggle Writeup's Media Gallery
