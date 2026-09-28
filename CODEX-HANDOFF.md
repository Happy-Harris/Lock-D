# Lock'd — handoff for the next agent

Date: 28 Sep 2026. You are picking up **Phase 2** of a consolidation, then Phase 3 ("win"
features). This file is the entry point: it tells you what's done, what's decided, what's next,
and the traps already found. Read it fully before touching code.

---

## 1. The job in one paragraph

**Happy-Harris/LockD** is the product: Lock'd, a training log for people who lift for years
(tagline "Keep the receipt"). It's a TanStack Start / React 19 / Tailwind v4 / Zustand app exported
from an app builder. Two older repos are **read-only donors**:
- `motivatedc-creator/Strong-Pro` (commit `c937bf9`) supplies tested engineering: Dexie storage,
  domain maths with tests, analytics, import/export, PWA.
- `motivatedc-creator/knurl-os` (commit `f7d9e3c`) supplies the visual identity: tokens, fonts,
  and the mark.

The work is to port the best of both into LockD in small PRs, without a second router, a second
Tailwind major, a second state library, app-builder scaffolding or stale branding. Then build the
Phase 3 features that make it beat Hevy and Strong.

**Never push to the donor repos.** `Happy-Harris/Lock-D` (with a hyphen) was a scratch repo. Its
PR #1 is superseded and can be closed. Ignore it otherwise.

## 2. Read these, in this order

In the LockD repo:
1. `AGENTS.md`: principles, repo map, commands, PR rules, live identifiers. (Codex reads it
   automatically; it is authoritative.)
2. `docs/HANDOVER.md`: the handover log, newest first. **Append an entry at the end of every PR.**
3. `docs/consolidation/PLAN.md`: the approved plan. §3 is the PR order, §4 the storage migration
   design, §5 running without Grok, §6 offline, §7 improvements I-1…I-41 and overrides O1–O7,
   §8 decisions.
4. `docs/consolidation/PLAN-ADDENDUM.md`: recorded decisions, the win-feature assessment for all
   11 opportunities, where the pulled-forward items land, the Phase 3 order, overrides A-1…A-8, and
   §9 **a correction** (I-42).
5. `docs/consolidation/FEATURE-MATRIX.md`: every feature across the three repos, with a decision
   per row. Read it together with the addendum's §9 correction.
6. `docs/consolidation/baseline/`: Phase 1 screenshots (390 px and 1024 px). Per-PR screenshots
   live in `docs/consolidation/prs/`.

Also ask the owner for **the original brief** (the "Consolidate Strong-Pro and knurl-os into
Lockd, then build what makes it win" prompt, including Appendix A: Text Size & Lift Math spec, and
Appendix B: 11 opportunities). The plan implements it; the appendices hold specs and test vectors
you'll need in Phase 3.

## 3. State of `main` right now (`fa3ba3e`)

| PR | What landed |
|---|---|
| #1 | The docs above plus the baselines |
| #2 (plan PR 1, guardrails) | Lockfile fixed (`npm ci` works). Vitest 5 + Testing Library + jsdom + Playwright. `npm run verify`. CI (`.github/workflows/ci.yml`: verify, brand check, Playwright at 390/1024). Lint at 0 errors. Legacy app-builder tests moved to `npm run test:legacy`. Brand check. History-never-paywalled promise, guarded by a test and an ESLint rule. Real `AGENTS.md`/`CLAUDE.md`. Six subagents in `.claude/agents/`. Handover log |
| #3 → #4 (plan PR 2, critical fixes) | I-4 unauthenticated Lab model path removed, with a per-user daily cap. I-1 Train tab crash. **I-42** seven unreachable detail screens. I-3 discard confirm + set-delete undo. I-2 safe sign-in (plan / merge / safety backup). (#3 merged into the PR 1 branch by mistake; #4 carried it to `main`.) |

**Checks at `fa3ba3e`:**
- Locally: `verify` passes (lint 0 errors, 8 old warnings; typecheck; 39 Vitest tests in 7 files;
  build), and e2e passes 20/20.
- CI was green on the same commits in PR #3. CI on `main` itself was still running when this was
  written, so check it first.

**Not verified anywhere:** sign-in against a real auth provider. None is configured, so the sign-in
merge logic is covered by unit tests and real-store tests only.

## 4. Decisions already made (don't re-ask)

- **D1:** `Happy-Harris/LockD` is the product repo.
- **D2–D15:** yes to the recommendations in PLAN §8. That includes:
  - O1 storage architecture;
  - guests get only the deterministic Lab, and the LLM Lab is signed-in with a daily cap;
  - drop strength-standard bands;
  - replace MEV/MAV/MRV with Strong-Pro's muscle sets;
  - gate thin-evidence insights;
  - rest precedence: routine rest > user default, with learned rest as a suggestion;
  - O2 era detection, after fixtures;
  - brand: Oxide replaces vermillion, accent themes removed, loud/calm kept;
  - critical fixes as PR 2;
  - legacy tests out of `verify`;
  - sign-in merges by id;
  - unsided knurl girths become new metrics;
  - offline via a prerendered shell;
  - the clip DB `lockd-vault` stays separate.
- **D16:** delete the multiplayer module, `counterfactual`, `wouldBePr` and `sessionCountStreak`.
  **Keep** `namedPrs` data in backups, with no UI for now.
- **D17:** port RIR mode from Strong-Pro.
- **D18:** keep the baselines.
- **O1–O7:** approved.

**Still open. Ask the owner before doing these:**
- **DA-1 / A-1:** lockers are public by default. `ensureProfile` in `src/lib/cloud/api.ts` creates
  `is_public = true`, so every signed-in user gets a public `/u/<handle>` page, and shares have no
  unpublish. The recommendation is: new profiles private, a one-time prompt for existing public
  ones, and an unpublish action. **Not approved yet. Raise it first; it's the most urgent open
  item.**
- **DA-2 / A-2:** a real Hevy CSV export for fixtures. Don't guess Hevy's columns. Build the generic
  CSV path regardless.
- **DA-3:** comeback re-entry rule, proposed as 90 / 80 / 70 % of the last pre-layoff working set
  for layoffs of 14–27 / 28–55 / 56+ days. Editable, labelled as a rule.
- **DA-4:** warm-up rest timer, default off.
- **DA-5:** overrides A-3 to A-6.

## 5. What's next: the PR queue

Follow PLAN §3, as amended by the addendum §5. PRs 1 and 2 are done.

| # | PR | Notes for you |
|---|---|---|
| **3** | **Characterisation tests** | Pin **current** behaviour, bugs included, so later fixes show up as test diffs. First make the demo deterministic: `buildDemoLog(exercises)` uses `new Date()`, so add a clock parameter. Cover chronicle, ghost, autopsy, progression + easier-week, DNA, queue, intelligence, recovery, landmarks, standards, Lockd's own weekly verdict, moments, wrapped, programs, CSV import/export and store actions. Known bugs to pin, not fix: era rename orphans sessions; first exposure counts as a PR; recovery says "fresh" with no data; skipped exercises count as "behind" in the summary diff; the Euro CSV corrupts data. Evidence for all of these is in PLAN's evidence log |
| 4 | Domain layer | `units`, `oneRepMax`, `plateCalculator`, `warmup` and `ids` are **logically identical** to Strong-Pro's; port only their tests and comments. Real work: `time` (the **tzOffset sign is opposite**: Strong-Pro stores `-getTimezoneOffset()`, Lockd stores the raw value; keep Lockd's and flip on import), `volume` (Strong-Pro's is correct), `records`, `exerciseTaxonomy`, the union of `types`, and the evidence catalog. Also (A-5): split `estimateOneRepMax` into an unrounded core plus the current rounded wrapper, and add the 1–12 rep parity test |
| 5 | Durable storage | PLAN §4 in full. The Dexie DB **`lockd` already exists** at version 1 with the `safetyBackups` table (`src/lib/storage/safety.ts`). Add the log tables as **version 2**. Keep Zustand as the in-memory working set, with per-row write-through. Do a one-time migration from `localStorage['lockd-v1']` with a safety backup, checksum verification, and the old key left untouched. Reserve a device-only `textSize` key outside `settings` |
| 6 | Offline | SSR app, so no `index.html` fallback. Use a prerendered shell with network-first navigations. vite-plugin-pwa vs Vite 8 is unverified, so spike it first. Add an offline cold-start e2e |
| 7 | Data portability | Port Strong-Pro's Strong CSV wizard (mapping, Resolve, fingerprints, taxonomy suggestions), Bulk Classify, CSV export, and Zod-validated backup restore with a safety backup. Add importers for Strong-Pro `repforge-backup` v1 and knurl-os vault v1 (float kg → grams). Add a generic CSV importer, and a Hevy preset only once a real sample exists. Seed library: grow it to 92 exercises with a versioned top-up. Seed ids already match across repos (`seed-<slug>`) |
| 8 | Analytics | Strong-Pro's Weekly Verdict (golden fixtures), training flags, muscle sets + evidence sheets, and the deterministic Ask the Lab (`askLab*.ts`). One lens module: goal lifts come from the user's pick, never the lens's hardcoded `skillIds`. Progression merge (I-20). Tap-through provenance on every number. Recovery → factual copy. Split it into several PRs if it grows |
| 9 | Logging details | Per-set targets, supersets, unilateral sets, RIR, rest notification/vibrate, equipment editor, per-exercise increments, input fixes (I-26…I-31), **I-21 rest precedence**, separate warm-up timer (default off), Repeat last (it exists; test it and add entry points) |
| 10 | Visual identity | knurl tokens, self-hosted fonts (OFL licence), mark as `LockdMark`. The current `StampMark` draws an **"R"**. Oxide only for live/active state. Make the poster layout pure and test it with long lift names. Flip the brand check to `--strict` in CI |
| 11 | Approved improvements | One PR each (PLAN §7) |
| 12 | Scaffolding removal | `.grok/`, `scripts/grok-*`, `server/middleware/grok-pwa.ts`, the preview bridge, `src/lib/app-data/`, `src/lib/multiplayer/`, the pill. Delete `test:legacy` with them. Keep auth and cloud behind config (PLAN §5) |
| 13 | Cleanup | Unused deps (9 identified), a README/HANDOFF rewrite from the code, and final decisions in the matrix |

**Phase 3** starts only when its gate is met: Phase 2 done, CI green, the migration proven, offline
cold start passing, and a phone-width regression pass done. Its order is in addendum §6. **Phase 4**
is design docs only.

## 6. Traps already found (don't rediscover them)

- **Route files: never nest a detail route under a list route.** TanStack flat naming makes
  `history.$id.tsx` a child of `history.tsx`, and no list page renders `<Outlet />`. That bug hid
  seven screens (I-42). Use the un-nested form `name_.$id.tsx` with
  `createFileRoute("/name_/$id")`. URLs stay the same. `e2e/detail-routes.spec.ts` guards this.
- **Zustand selectors must not build new arrays or objects** (`s.x.filter(...)`). That loops React
  forever (I-1). Select the stored value and derive it in `useMemo`.
- **`authMiddleware` resolves a shared dev user (`DEV_USER_ID`) when sign-in is disabled.** Adding
  the middleware is not "requires sign-in". Check `context.userId !== DEV_USER_ID` for anything that
  costs money (see `src/lib/lab/policy.ts`).
- **Server-only modules** (`*.server.ts`, Better Auth, `pg`) must be imported **inside** server
  handlers (dynamic `import()`), never at the top of a file a route imports.
- **npm:** Vitest 5 needs `overrides.better-auth.vitest` in `package.json` (better-auth declares an
  optional `vitest ≤ 4` peer). npm's resolver crashes with "Cannot read properties of null
  (reading 'edgesOut')" if several dev deps are installed at once. Add packages one at a time.
  Always prove `rm -rf node_modules && npm ci` works before pushing a lockfile change.
- **Dev server:** clicks before hydration are lost. e2e waits for `html[data-gym-ready="true"]`
  (set in `GymGate`, `src/routes/__root.tsx`); use `e2e/helpers.ts`. A dependency that only a lazy
  route imports makes Vite re-optimise and hard-reload mid-test, so add it to `optimizeDeps.include`
  in `vite.config.ts` (currently the Radix alert dialog and dexie).
- **The demo log's session count depends on today's date.** Don't assert fixed counts in e2e.
- **Stacked PRs:** merge the base PR, let GitHub retarget, then merge. #3 merged into a feature
  branch once, and `main` missed the critical fixes until #4.
- **Live identifiers are never renamed as cleanup:**
  - the persist key `lockd-v1`;
  - IndexedDB names `lockd` and `lockd-vault`;
  - backup formats `lockd-backup` and `lockd-program`, plus the imported `repforge-backup` and
    knurl `brand: "knurl-os"`;
  - URLs `/s/$id` and `/u/$handle`;
  - tables `lockd_vaults`, `lockd_profiles`, `lockd_shares` and `lockd_lab_notes`.
- **The repo is public.** No keys, deployment URLs or secrets in code, fixtures, tests or PR text.

## 7. How to work here

```bash
npm ci
npm run verify        # the bar for every PR
npm run test:e2e      # Playwright, dev server on :8080; CHROMIUM_PATH=/path/to/chrome to reuse a local browser
npm run check:brand   # warn-only until the identity PR
```

- **Tests.** Vitest runs in Node by default. Component tests add `// @vitest-environment jsdom`.
  Storage tests `import "fake-indexeddb/auto"` and use `setLockdDb(new LockdDatabase(uniqueName))`.
  The Zustand store works in Node: `persist` has no storage there and is a no-op, so store actions
  are testable directly (see `src/lib/gym/store.test.ts`).
- **Every fix gets a test that fails first.** Every behaviour change to an engine appears as a
  characterisation-test diff explained in the PR.
- **Each PR:**
  - one area, its own branch, `verify` and e2e green in CI;
  - 390 px and 1024 px screenshots of touched screens in `docs/consolidation/prs/prN/`, compared
    with `baseline/`;
  - an entry in `docs/HANDOVER.md`;
  - never force-push `main`; ask before deleting a Lock'd feature;
  - any stored-data change ships a migration, a test and an old-format fixture.
- **Principles, in short:** logging speed never regresses; history is the source of truth (derive,
  never store aggregates); integer grams/mm/metres/seconds; number honesty (no invented targets,
  missing data never becomes zero or a positive state); guest and offline always work; history,
  charts and export are free forever. The full text is in `AGENTS.md`.

## 8. Open problems not yet fixed

This is the backlog beyond the PR queue. IDs refer to PLAN §7.

- **Safety and privacy:**
  - public-by-default lockers and no share unpublish (DA-1);
  - `pushVault` still last-writer-wins, because revision compare-and-swap isn't built (I-2
    follow-up);
  - server validators are pass-through, with no payload size caps (I-37);
  - routine **Delete** has no confirmation.
- **Honesty:**
  - I-9 to I-25, including an estimated 1RM labelled "LOAD", kg hardcoded for lb users, unsourced
    landmarks and standards, and thin-evidence insights;
  - newly visible on the summary: skipped exercises count as "behind", and guests are told "The
    session is on the locker".
- **Logging:** I-26 to I-31. The workout page re-renders every second, recomputing over the whole
  history; weights of 1,000 or more parse wrongly after edit; clearing reps stores 0; and so on.
- **Robustness:** I-32 to I-41: poster wrap, imported programs silently dropping exercises, orphaned
  clip blobs, no equipment editor, the unused seed version, and dead code.

## 9. First steps for you

1. Check that CI on `main` (`fa3ba3e`) is green. If it isn't, that's your first job.
2. Ask the owner about **DA-1** (locker privacy) and for a **Hevy export**.
3. Start **PR 3 (characterisation)** on a new branch from `main`.
