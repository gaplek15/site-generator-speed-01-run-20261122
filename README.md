# site-generator-speed-01-run-20261122

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/gaplek15/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/gaplek15/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

Faster drop-in for makefun-site 1.0.0 — static site generator, byte-exact output at ~2.9x the speed (practice set 3/3, ~215 ms median vs ~617 ms baseline).

## Run it

```sh
node main.ts
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=d9kag417&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>

A faster drop-in replacement for make.fun's own `makefun-site` 1.0.0 static
site generator. `solve.js` (an ES module, dependency-free) exports `run(input)`
which returns, for every case's input, exactly the output tree makefun-site
1.0.0 returns — every page's `index.html`, the index and tag pages,
`build-report.txt` and each static file, byte for byte. `main.ts` is the
harness driver: it reads every case from `/input/cases.json`, answers each
with `run`, and writes `/output/answers.json`.

## How it is faster

- **Markdown in one direct pass** — no lexer token array, no per-character
  `escapeHtml` calls. Plain runs are batched and escaped in bulk with native
  regex scans; blocks are rendered straight from the source lines.
- **Templates rendered into a shared output array** — no intermediate string
  per node, include or loop iteration; `{% include %}`s are expanded
  statically at parse time, depth-capped at 8 exactly like the reference.
- **Per-case caches** — `slugify` and `date_long` results are memoised
  (titles, tags and headings repeat heavily), word counting is a single
  manual scan, and each post's card is computed once and reused for its page.
- No state is kept between `run()` calls: every cache is created per case,
  so each case is answered alone.

## Verification

On the kit's practice set (3 cases, 257–486 files each) the entry passes the
kit's own scorer 3/3 (`passed: 1`), and runs the whole set in ~215 ms median
vs ~617 ms for the house baseline — about a 2.9x speedup, measured over 10
interleaved runs each.
