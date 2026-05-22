<p align="center">
  <img src="https://img.shields.io/badge/colony-mythos-8A2BE2?style=for-the-badge" alt="colony-mythos">
  <br>
  <em>Evolutionary multi-agent vulnerability hunting</em>
</p>

---

**One queen. A population of LLM workers. Each carries a genome — a hypothesis about where a vulnerability lives.**

Workers hunt. The queen scores fitness, evolves genomes, kills the weak, promotes the sharp. Findings flow through an 8-stage Mythos pipeline — but the pipeline isn't a conveyor belt. Every stage feeds genetic signals back into the colony. The pipeline and the evolution are **symbiotic**, not sequential.

No Python machinery. No CLI. Hermes Agent **is** the Queen.

---

## How It Works

```
                    PIPELINE (substrate)          QUEEN (decision engine)
                    ─────────────────────         ────────────────────────

  Recon ──→ Hunt ──→ Validate ──→ Gapfill        ASSESS → EVOLVE → ALLOCATE
                                        │              ↑           │
  Dedupe ←── Trace ←── Feedback ←─ Report              │           ↓
       │                                                 │     STAGE_ADVANCE
       │                    ┌─────────────┐              │           │
       └───────────────────→│   BRIDGE    │←─────────────┘           │
                            │ signals  tasks                         │
                            └─────────────┘                   SYNTHESIZE
```

Pipeline stages produce **genetic signals**:

| Stage | Signal | Queen responds |
|-------|--------|----------------|
| Validate CONFIRMED | Pattern detected | Pattern-export mutation, spawn siblings |
| Validate REJECTED | Scope overstated | Absorb corrected scope into genome DNA |
| Gapfill | Under-covered area | Lateral/random mutation on that surface |
| Trace REACHABLE | Bug is exploitable | Spawn consumer-trace workers |
| Trace NOT REACHABLE | Dead end | Archive, enrich dead surface registry |

Queen decisions generate **pipeline tasks**:

| Action | Creates | Stage |
|--------|---------|-------|
| SPAWN (hunt) | Hunt task | Stage 2 |
| STAGE_ADVANCE | Validate task (different model) | Stage 3 |
| KILL + absorb DNA | Gapfill task | Stage 4 |
| BUG_ARCHIVE | Trace → Report | Stages 6 → 8 |
| PATTERN_EXPORT | Hunt × N sibling subsystems | Stage 2 |

---

## The Queen Cycle

```
1. ASSESS        Read social feed + pipeline stage signals
2. EVOLVE        Mutate genomes — use pipeline signals, not just fitness
3. ALLOCATE      Portfolio: coverage sweep → signal exploitation → pattern export
4. STAGE_ADVANCE  Generate pipeline tasks from every decision
5. SYNTHESIZE    Cross-worker pattern detection
```

### Genome System

Each worker carries a living genome that compounds knowledge across generations:

```
genome-NNN.md
├── Hypothesis       Specific multi-factor vulnerability prediction
├── DNA              Mechanisms · guards encountered · failed approaches
├── Evolution Plan   v1 → v2 → v3 bypass angles
├── Mutation History Full lineage
└── Pipeline Signals What stages told us about this genome
```

**11 mutation types:** narrow · intensify · lateral · recombine · source-pivot · factor-add · pattern-export · chain-extend · consumer-trace · deopt-pivot · random

**Fitness rubric (0-5):** defense-confirming (kill) → shallow → productive → high-signal → confirmed (BUG_ARCHIVE)

---

## Rules

- **Never Validate without a DIFFERENT model**
- **Never Trace without a consumer repo list**
- **Validate verdicts MUST feed back into genome evolution**
- **Absorb DNA from killed genomes before disposal**
- Narrow scope produces better findings
- "It works correctly" is a kill signal

---

## Quick Start

```bash
cd ~/colony-mythos

# Start a colony on any target type
"Start a colony on /Applications/Insomnia.app — Electron app"
"Hunt this Android APK at /path/to/app.apk"  
"Start a colony on https://github.com/org/repo — source repo"

# Drive the colony
"Run a cycle"
"Show me the social feed"
"What's the colony status?"
```

**Seven target scaffolds:** `source-repo` · `electron-app` · `web-app` · `container-image` · `binary-executable` · `android-app` · `ios-app`

---

## Colony State

Everything is markdown + JSONL on disk:

```
colony-runs/<id>/
├── colony_state.md      Pipeline status · signal log · workers · surfaces
├── genomes/             Per-genome: hypothesis · DNA · evolution plan
├── social/feed.jsonl    Worker posts: differentials · primitives · findings
├── findings/            Confirmed finding records
├── decisions/           Queen decision log per cycle
└── reports/             Final structured reports
```

---

## Credits

- **[colony_agent](https://github.com/qriousec/colony_agent)** by @qriousec — evolutionary colony architecture, genome system, fitness scoring
- **[Project Glasswing](https://blog.cloudflare.com/cyber-frontier-models/)** (Cloudflare) — Mythos 8-stage pipeline, adversarial validation, consumer tracing
- **[Hermes Agent](https://github.com/NousResearch/hermes-agent)** — the agent runtime
