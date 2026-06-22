<p align="center">
  <img src="assets/double-shot.png" alt="double-shot" width="320">
</p>

<h1 align="center">double-shot</h1>

<p align="center"><em>Like one-shot, but better.</em></p>

---

**double-shot** is a Claude Code plugin for taking a substantial plan/design document all the way to a built, green, reviewed codebase — without the orchestrating agent doing the heavy lifting by hand. The human and the main agent stay in the loop to *understand, align, and gate*; two background **workflows** do the parallel building and adversarially verify their own work.

## Status

**v0.1 — patterns proven, packaged scripts not yet re-validated.** The *approach* here is battle-tested: it built a ~24k-line dual-backend product end-to-end and caught real bugs (a credential-forgery hole, a chain-corrupting timestamp bug, two audience-leak paths) via adversarial review. But the two bundled workflows were **extracted and generalized from those runs** — `build-from-blueprint` in particular consolidates several project-specific workflows into one parameterized script that hasn't itself been run end-to-end yet. **Dogfood it on a small throwaway project before relying on it.**

## The two phases

```
[ understand the plan + align with you ]          ← conversation
            │
   PHASE 1 ─ plan-to-blueprint     research the toolchain, design the hard
            │                       subsystems → a concrete BLUEPRINT.md
            ▼
[ review the blueprint ]                          ← your feedback gate
            │
   PHASE 2 ─ build-from-blueprint  scaffold → build modules in waves (each
            │                       adversarially verified) → loop to green
            ▼                       → security / correctness / simplification review
[ verify on disk + report ]                       ← back to you
```

The **feedback gate between the two phases** is the point: you steer once, at the blueprint, then the build runs beginning-to-end on its own.

## What's inside

| Path | What |
|---|---|
| `skills/double-shot/SKILL.md` | The orchestration procedure the main agent follows (understand → align → phase 1 → gate → phase 2 → verify → report). The front door (`/double-shot`). |
| `skills/double-shot/workflows/plan-to-blueprint.js` | **Phase 1** — fan-out research + design → `BLUEPRINT.md`. |
| `skills/double-shot/workflows/build-from-blueprint.js` | **Phase 2** — derive the module DAG, build the foundation + verify the crown-jewel, build modules in disjoint-file waves, loop to green, adversarial review + triage. |
| `skills/americano/SKILL.md` | **Americano** — the watered-down double-shot (`/americano`): a lighter front door for an *already-aligned, bounded* change to a *green* repo. |
| `skills/americano/workflows/americano-plan.js` | Americano Phase 1 — bounded research + one design pass/dimension + one adversarial critique → a build-ready blueprint (lighter than `plan-to-blueprint`). |
| `skills/americano/workflows/americano-build.js` | Americano Phase 2 — trimmed `build-from-blueprint`: confirm green baseline (**no** scaffold/foundation), build the waves, loop to green, adversarial review. |

The skill is the brain; the workflows are the hands.

## Americano — when the full ceremony is overkill

`double-shot` assumes a cold start and earns its heavy up-front pass (research tournament, scaffold + crown-jewel verify) on big or greenfield work. **`americano`** is its watered-down sibling for when you've *already* hashed out the design and the change is **bounded + lands in a green repo** — the full ceremony would be overkill.

| | double-shot | americano |
|---|---|---|
| Use when | greenfield · new foundation · big/risky · needs max rigor | design already aligned · bounded change to a green repo |
| Phase 1 | full `plan-to-blueprint` (design tournament, loop-until-dry) | bounded `americano-plan` (one design pass/dim, one critique) |
| Phase 2 | `build-from-blueprint` (scaffold + freeze + crown-jewel verify, then waves) | `americano-build` (confirm green baseline — **no scaffold** — then waves) |

The axis is **how much rigor the change warrants**, not greenfield-vs-brownfield — double-shot is great in mature repos too. If a bounded change turns out bigger than it looked, switch up to double-shot mid-stream. Installing this plugin gives you **both** (`/double-shot` and `/americano`).

> **Dogfooded:** `americano-build` took a real, no-merge-invariant-adjacent feature to a **505-test green** autonomously, and the adversarial review caught a genuine cross-user bug it then fixed.

## Install

As a Claude Code plugin, straight from GitHub:

```
/plugin marketplace add lucianHymer/double-shot
/plugin install double-shot@double-shot
```

Then just **tell your agent to double-shot a plan** — e.g. *"build the whole thing from `PLAN.md`"*. The skill triggers from its description (and shows up as a slash command once installed). Its two workflow scripts live inside the skill, so they install with it — no extra setup.

<details><summary>Alternatives</summary>

```bash
# add the marketplace from a local clone instead of GitHub:
claude plugin marketplace add /path/to/double-shot
claude plugin install double-shot@double-shot

# or install just the skill via the cross-agent installer (vercel-labs/skills):
npx skills add lucianHymer/double-shot
```
</details>

> The two workflows ship **inside the skill** — verified: on install they land alongside `SKILL.md` (in the plugin's skill dir), and the skill invokes them by path via `${CLAUDE_SKILL_DIR}`. No copying, no extra setup.

## Usage

Point it at a plan doc:

> `/double-shot` — then: *"Take `design/my-plan.md` to a built product."*

Or just describe it: *"build the whole thing from `PLAN.md`."* The agent reads the plan, aligns with you on the open decisions (stack, scope, deployment), produces the blueprint, **stops and shows it to you**, then — on your go — builds it to green and reports with the diff.

## Lessons baked in

These came from real runs that built ~24k lines of verified code:

- **Verify the foundation before fanning out.** A builder once shipped a credential-forgery hole that only a *fresh adversarial verifier* caught.
- **The review catches what green tests miss.** A chain-corrupting timestamp bug and two audience-leak paths survived a 276-test green suite until the adversarial review found them.
- **Spike risky dependencies first.** A "dead" embedded-DB extension turned out obtainable and buildable — but only a throwaway probe proved it before committing the port.
- **Keep heavy/optional pieces feature-gated** so the default build and CI stay fast.

## License

Functional Source License, Version 1.1, with an MIT future license — converting
to MIT three years after publication. See [LICENSE](LICENSE).
