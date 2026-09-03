# Hedera Harness

Agent skills for **creating and reviewing** [hedera-harness](https://github.com/hedera-dev/hedera-harness) recipes — PRD, recipe file, validators, Playwright smoke, and evaluate checklists that drive in-place feature work under `.harness/` in a Scaffold HBAR app.

These skills are for the human or agent writing the **recipe** **before** a `run`. They are **not** generator skills: recipes do not list skills. Product plugins from this marketplace are discovered per run and the generator picks.

Targets **hedera-harness schemaVersion 3**. Older recipes hard-fail at load. There is no `migrate` command — rewrite to v3, or regenerate with `hedera-harness init` and reapply edits.

## Installation

### Claude Code

First-class. This repo is a Claude Code marketplace. After install, invoke with
`/create-harness-spec` or `/review-harness-spec`.

```bash
/plugin marketplace add hedera-dev/hedera-skills
/plugin install hedera-harness
```

Matt Pocock `/grilling` is optional and usually absent in Claude Code. The
create skill ships an inline one-question-at-a-time protocol for that case.

Omit `agent:` in the recipe — the harness default is already **claude**.

### Cursor and other agents

```bash
npx skills add hedera-dev/hedera-skills
```

Prefer `/grilling` when that skill is installed. Set `agent: cursor` in the
recipe if the run will use the Cursor CLI.

## Skills

| Skill | Purpose |
|-------|---------|
| `harness-spec-anatomy` | Shared vocabulary, the v3 defaults, and `check-spec.sh` |
| `create-harness-spec` | Grill an idea → emit a runnable **recipe** under `.harness/` |
| `review-harness-spec` | Two-axis audit (Wiring + Eval) before `hedera-harness run` |

### harness-spec-anatomy

Model-invoked vocabulary layer. Defines **recipe**, **recipe file**, **slug**,
**run**, **init**, **doctor**, **baseline**, **agent preset**,
**increment**, **blind**, **evaluate checklist**, **stage**, **finding**, and
**needle**. Houses the mechanical checker:

```bash
bash skills/harness-spec-anatomy/scripts/check-spec.sh <project-root>
```

The script delegates schema validity to `hedera-harness doctor --recipe-only`
and adds the cross-file coherence checks doctor does not perform — notably the
EVALUATE `eval` ↔ `validator.enabled` pairing, which doctor passes.

### create-harness-spec

Triggers when you say: "create a harness recipe", "turn this feature into harness inputs", "author a hedera-harness PRD", "add a recipe under .harness".

**Provides:**

- Locate or bootstrap the target (`hedera-harness init`; rewrite stale v1/v2 recipes)
- Hybrid grilling (prefer `/grilling` when installed; else inline one-question-at-a-time protocol)
- Dependency-ordered decision tree (goal → slug → Solidity → services → routes → wallet → increments → agent → stages)
- Emit ASSERT by default, leaving v3 defaults commented out; deepen optionally
- Completion criterion: `check-spec.sh` exits clean
- Handoff: `yarn harness:run` / `hedera-harness run .harness/spec.yaml`

### review-harness-spec

Triggers when you say: "review my harness recipe", "is this ready to run", "audit my evaluate checklist", "review my .harness recipe".

**Provides:**

- **Wiring** axis — `doctor` output plus coherence: defaulted paths, stage pairing, blind scan, removed keys, `REPLACE_ME`, severity budget
- **Eval** axis — journey↔assertion traceability, needles, thin Playwright, recipe padding, increment ordering and `eval:` 1:1 pairing
- Side-by-side report (axes stay separate)

## Glossary (leading words)

| Term | Meaning |
|------|---------|
| **recipe** | The coupled set of files for one run (a.k.a. "spec") |
| **recipe file** | `.harness/spec.yaml` |
| **schemaVersion** | Recipe format version; `3` is current |
| **slug** | Kebab name for the recipe file `name` field |
| **run** | The CLI command (`hedera-harness run`) |
| **init** | Bootstrap a scaffolded project + `.harness/` |
| **doctor** | Preflight; `--recipe-only` validates the recipe alone |
| **baseline** | Host-health commands run before generation |
| **agent preset** | `claude` (default) / `cursor` — CLI flags, model, MCP delivery |
| **increment** | One entry when `prd:` is a list; delivered in order |
| **blind** | Keep evaluate-checklist text out of the PRD |
| **evaluate checklist** | JSON graded by EVALUATE (`E1`, `E2`, …) |
| **stage** | ASSERT / SMOKE / EVALUATE / CHAIN |
| **finding** | One validation failure, with a stable id and open/fixed status |
| **needle** | Static text assertion |

Full definitions: `skills/harness-spec-anatomy/GLOSSARY.md`.

> "Spec" and "recipe" mean the same thing. The harness settled on **recipe**;
> these skills keep `spec` in their names and in `.harness/spec.yaml`.

## Works With

- **Claude Code** (marketplace plugin — `/plugin install hedera-harness`)
- Cursor, Codex CLI, Gemini CLI, and any agent that supports the skills/plugin format
- [hedera-harness](https://github.com/hedera-dev/hedera-harness) CLI (`init`, `run`, `doctor`, `validate`, `validate-semantic`)
- Optional: [mattpocock/skills](https://github.com/mattpocock/skills) `/grilling` (preferred in Cursor when present; Claude Code uses the inline protocol)

## License

Apache-2.0
