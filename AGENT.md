
# AGENT.md — Roblox Game Development Bot Contract  
**Repo ID:** Jaiake/roblox  
**Version:** 0.1 (lock wording on merge)

---

## 1 · Contributor Personas
| Role | Handle | Authority | Must do | Must **not** do |
|------|--------|-----------|---------|-----------------|
| **Maintainer (Human)** | `@JWPike` | Final design & merge power | Review PRs, pull/push Studio ↔ Git, curate assets | Push unreviewed code to `main` |
| **AI Contributor** | `RobloxDevBot` | PR author only | Generate Luau scripts, modules, tests, docs | Merge its own PRs, modify binary assets |

---

## 2 · Repo Layout (source-of-truth = Git)
```

.github/
workflows/ci.yml
src/                 # Luau source mirrored from Studio
tests/               # TestEZ suites
docs/
rojo.json            # (optional) Rojo map

````
Studio holds the runtime copy; this repo holds the canonical source.

---

## 3 · Local Setup (humans)
```bash
# prereqs: Git ≥2.40, Node ≥20, Roblox Studio
npm i -g @roblox/rojo wally-cli foreman
git clone https://github.com/Jaiake/roblox.git && cd roblox
rojo serve rojo.json   # live-sync if/when rojo.json exists
````

---

## 4 · Workflow Cheat-sheet

| Task         | Human flow             | Bot flow                  |
| ------------ | ---------------------- | ------------------------- |
| New feature  | open issue `spec:`     | branch `feat/<slug>` ⇒ PR |
| Bug fix      | issue with repro steps | branch `fix/<slug>` ⇒ PR  |
| Docs         | commit directly        | PR `docs:`                |
| Asset import | Studio drag-n-drop     | never                     |

---

## 5 · Branch & Commit Rules

1. Branch naming: `feat/…`, `fix/…`, `docs/…`, `refactor/…`.
2. Commit header: `type(scope): summary` + one-line BLUF body.
3. PR template auto-includes purpose, linked issue, test note, Studio-sync note.
4. CI (§7) must pass before review.
5. Review window: 24 h or maintainer approval, whichever first.

---

## 6 · Coding Standards

* **Luau strict mode** only.
* Reusable code lives in `src/Shared`.
* No magic numbers—centralise in `src/Shared/Constants.lua`.
* Style via `stylua.toml`; static analysis via `selene`.
* Tests with TestEZ; keep fast and deterministic.
* Networking: either `Knit` or plain `Remote*`; declare remotes in `Shared/NetInit.lua`.
* Log with `warn`, not `print`.

---

## 7 · CI Pipeline (`.github/workflows/ci.yml`)

```yaml
steps:
  - uses: actions/checkout@v4
  - run: npm ci
  - run: stylua --check src
  - run: selene src
  - run: testez
```

PR fails ⇒ bot patches & re-submits.

---

## 8 · API Docs

Bot runs `docgen` after each merge to `main`; output to `docs/api/`.
Hand-editing generated docs is forbidden.

---

## 9 · Troubleshooting Table

| Symptom               | Likely cause        | Fix                                 |
| --------------------- | ------------------- | ----------------------------------- |
| `selene` global error | missing annotation  | add to `!GLOBALS` or require module |
| Studio ↔ git desync   | forgot to pull/push | `rojo sync`, commit, push           |
| Runtime stutter       | tight loop          | profile with MicroProfiler          |

---

## 10 · Escalation & Hard Limits

* Bot may **not** change assets (`.rbxm`, images).
* Any code touching monetisation, moderation, or anti-cheat needs explicit issue approval.
* Disagreements: maintainer comment `HALT` ends discussion; bot marks PR as draft.

---

## 11 · Future “Nice-to-Have” (file issues first)

* Procedural obby generator.
* Admin GUI: broadcast, kick/ban, persistent admin list.
* Cross-server leaderboards via OrderedDataStore.

*—End of contract. No fluff, no similes.*

```

### Next moves
1. **Add this file** and push.  
2. Spawn an issue: **“Bootstrap project tree”** describing desired folders + a stub `ci.yml`.  
3. When the bot raises its first PR, review & merge; then iterate feature-by-feature.

That gets the collaboration loop started without letting the repo drift from Studio or from your dry, no-nonsense style.
::contentReference[oaicite:0]{index=0}
```
