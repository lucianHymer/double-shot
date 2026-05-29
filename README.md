<p align="center">
  <img src="assets/double-shot.png" alt="double-shot" width="320">
</p>

<h1 align="center">double-shot</h1>

<p align="center"><em>Like one-shot, but better.</em></p>

---

**double-shot** takes a plan document all the way to a built, tested, reviewed
codebase. You write the plan and steer once at the blueprint; two background
workflows do the parallel building and adversarially verify their own work.

## Install

A Claude Code plugin, straight from GitHub:

```
/plugin marketplace add lucianHymer/double-shot
/plugin install double-shot@double-shot
```

## Use it

1. **Write a detailed plan** as a markdown file — with Claude Code, Claude
   Desktop, or however you like. The more concrete the better.

2. **Point double-shot at it:**

   > *Use double-shot to implement `plan_xyz.md`*

That's it. The agent reads your plan, aligns with you on the open decisions,
produces a blueprint and **stops to show you**, then — on your go — builds it
to green and reports back with the diff.

## License

Functional Source License, Version 1.1, converting to MIT three years after
publication. See [LICENSE](LICENSE).
