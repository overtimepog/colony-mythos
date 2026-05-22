# Colony-Mythos

A prompt-driven, evolutionary multi-agent vulnerability discovery system.
No Python machinery — just prompts. Hermes Agent IS the Queen.

## What This Is

Colony-Mythos fuses two ideas into ONE symbiotic architecture:

1. **@qriousec's colony_agent** — an evolutionary colony of LLM-driven workers
   that found 5 type-confusion bugs in V8 Wasm. Each worker carries a *genome*
   (a hypothesis about a specific vulnerability). The *queen* scores fitness,
   kills weak workers, promotes strong ones, and recombines DNA.

2. **Cloudflare Project Glasswing's Mythos pipeline** — an 8-stage vulnerability
   discovery pipeline: Recon → Hunt → Validate → Gapfill → Dedupe → Trace →
   Feedback → Report.

**The key architectural insight (May 2026):** These are NOT two systems bolted
together. They are two dimensions of ONE symbiotic engine:

- **The 8-stage pipeline is the SUBSTRATE** — what happens to findings from
  discovery to structured report.
- **The Queen cycle (ASSESS→EVOLVE→ALLOCATE→STAGE_ADVANCE→SYNTHESIZE) is the
  DECISION ENGINE** — which genomes live/die, what to hunt, how to allocate.
- **The Pipeline-Queen Bridge** connects them: pipeline stages produce genetic
  signals that the Queen uses to evolve genomes; Queen decisions generate
  pipeline tasks.

A Validate CONFIRMED verdict doesn't just advance a finding — it triggers
pattern-export mutations. A Validate REJECTED verdict doesn't just discard
a finding — it absorbs the corrected scope into the genome's DNA. A dead
worker's genome isn't wasted — its DNA (guards encountered, failed approaches)
enriches the gapfill/dead-surface registry.

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    COLONY-MYTHOS ARCHITECTURE                 │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   THE SUBSTRATE: 8-Stage Pipeline                            │
│   ┌──────────────────────────────────────────────────────┐  │
│   │ Recon → Hunt → Validate → Gapfill → Dedupe →         │  │
│   │ Trace → Feedback → Report                            │  │
│   └────────────────────┬─────────────────────────────────┘  │
│                        │                                     │
│           ┌────────────▼────────────┐                       │
│           │  PIPELINE-QUEEN BRIDGE  │                       │
│           │  signals ↑  tasks ↓     │                       │
│           └────────────┬────────────┘                       │
│                        │                                     │
│   THE DECISION ENGINE: Queen + Workers + Evolution          │
│   ┌──────────────────────────────────────────────────────┐  │
│   │ Queen: ASSESS→EVOLVE→ALLOCATE→STAGE_ADVANCE→SYNTHESIZE│  │
│   │   ├── Reads social feed + pipeline signals            │  │
│   │   ├── Evolves genomes using pipeline signals          │  │
│   │   └── Generates pipeline tasks from decisions         │  │
│   │                                                      │  │
│   │ Workers (delegate_task):                              │  │
│   │   └── BUILD→TEST→OBSERVE→TROUBLESHOOT→IMPROVE        │  │
│   └──────────────────────────────────────────────────────┘  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### The Pipeline-Queen Bridge

| Pipeline Stage | → Genetic Signal | Queen Action |
|---|---|---|
| Validate CONFIRMED | → Pattern confirmed | pattern-export mutation, BUG_ARCHIVE |
| Validate REJECTED | → Scope overstated | Absorb corrected scope, narrow genome |
| Gapfill flag | → Coverage gap | lateral/random mutation on under-covered surface |
| Dedupe cluster | → Pattern detected | grep-walk sibling subsystems |
| Trace REACHABLE | → Bug exploitable | consumer-trace spawn |
| Trace NOT REACHABLE | → Dead end | Archive, enrich dead surfaces |

| Queen Action | → Pipeline Task | Stage |
|---|---|---|
| SPAWN (hunt) | → Hunt task | Stage 2 |
| STAGE_ADVANCE | → Validate task (DIFFERENT model) | Stage 3 |
| KILL + DNA absorb | → Gapfill task | Stage 4 |
| BUG_ARCHIVE | → Trace → Report | Stages 6→8 |
| PATTERN_EXPORT | → Hunt tasks × N siblings | Stage 2 |

### The Queen (AGENTS.md)

Hermes Agent loads `AGENTS.md` in this directory and BECOMES the Queen.
No CLI commands. No Python. You just tell Hermes what you want:

- "Start a colony on this Electron app"
- "Run a cycle"
- "Show me the social feed"
- "What's the colony status?"

The Queen uses Hermes's native tools:
- `delegate_task` — spawn workers as subagents
- `read_file` / `write_file` — manage colony state, genomes, decisions
- `terminal` — explore targets, run recon commands
- `memory` / `session_search` — cross-session colony memory

### Workers (delegate_task)

Each worker is a Hermes subagent spawned via `delegate_task`. It receives:
- A rendered genome (hypothesis + DNA + evolution plan + pipeline signals)
- The target path and scaffold type
- Instructions to post findings to the social feed file
- A budget (max iterations, max wall time)

### State (Files on Disk)

Everything lives in `colony-runs/<colony-id>/`:

| Path | Purpose |
|------|---------|
| `colony_state.md` | Live state: pipeline status, signal log, workers, surfaces, findings |
| `genomes/genome-NNN.md` | Per-genome: hypothesis, DNA, evolution plan, pipeline signals |
| `social/feed.jsonl` | Worker posts (JSONL) |
| `findings/F-NNN.md` | Confirmed finding records |
| `decisions/cycle-NNN.json` | Queen decision log (includes pipeline status) |
| `reports/` | Final structured reports |
| `scratch/` | Per-worker working directories |

## Target Scaffolds

`targets/*.md` files describe what kind of target you're hunting and what
tools are available. Seven scaffolds:

- `source-repo` — Git repo (any language)
- `electron-app` — Electron desktop app
- `web-app` — Live web application
- `container-image` — Docker/OCI image
- `binary-executable` — Compiled binary
- `android-app` — Android APK/AAB
- `ios-app` — iOS .app bundle

Each scaffold includes: available tools, recon steps, oracle hints,
attack classes, and key research commands.

## The 8-Stage Pipeline

```
Recon → Hunt → Validate → Gapfill → Dedupe → Trace → Feedback → Report
```

| Stage | Purpose | Genetic Signal |
|-------|---------|----------------|
| Recon | Map target, discover surfaces | Attack-surface tiers → seed genomes |
| Hunt | Parallel workers on scoped tasks | Fitness scores, DNA → evolve |
| Validate | DIFFERENT model tries to DISPROVE | CONFIRMED/REJECTED → pattern-export or absorb |
| Gapfill | Re-queue under-covered areas | Coverage gaps → lateral/random mutations |
| Dedupe | Collapse same root cause | Pattern clusters → grep-walk siblings |
| Trace | Cross-repo reachability | REACHABLE/NOT → consumer spawns or archive |
| Feedback | Traces → new hunt tasks | New scopes → consumer-trace spawns |
| Report | Structured output with PoC | → Hall of fame, pattern library |

## Key Rules

- **Never Validate without a DIFFERENT model**
- **Never Trace without a consumer repo list**
- **Validate verdicts MUST feed back into genome evolution**
- **Absorb DNA from killed genomes before disposal**

## Starting a Colony

Just tell Hermes:

```
"Start a colony on /Applications/Insomnia.app — it's an Electron app"
"Start a colony on https://github.com/org/repo — it's a source repo"
"Hunt this Android APK at /path/to/app.apk"
```

The Queen (Hermes) will:
1. Create `colony-runs/<target>-<timestamp>/`
2. Initialize colony state from the template (includes pipeline status table)
3. Run Stage 1 Recon: explore the target, build attack-surface inventory
4. Seed genomes from recon output
5. Spawn initial recon/hunt workers
6. Present the Cycle 1 decision

## Running Cycles

After a colony is started:

```
"Run a cycle" — the Queen reads pipeline signals, evolves genomes, spawns workers
"Show me the feed" — read the social feed
"What's the status?" — colony health summary (incl. pipeline status)
"Kill colony" — stop all workers, archive state
```

## Credits

- **colony_agent** by @qriousec — the evolutionary colony architecture, genome
  system, fitness scoring, mutation types, and the BUILD→TEST→OBSERVE→
  TROUBLESHOOT→IMPROVE worker loop
- **Project Glasswing (Cloudflare)** — the Mythos 8-stage pipeline, adversarial
  validation, consumer tracing, and gapfill/dedupe stages. Blog post:
  https://blog.cloudflare.com/cyber-frontier-models/
- **Hermes Agent** by Nous Research — the agent runtime that makes this
  prompt-driven architecture possible
