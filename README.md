# Colony-Mythos

A prompt-driven, evolutionary multi-agent vulnerability discovery system.
No Python machinery — just prompts. Hermes Agent IS the Queen.

## What This Is

Colony-Mythos fuses two ideas:

1. **@qriousec's colony_agent** — an evolutionary colony of LLM-driven workers
   that found 5 type-confusion bugs in V8 Wasm. Each worker carries a *genome*
   (a hypothesis about a specific vulnerability). The *queen* scores fitness,
   kills weak workers, promotes strong ones, and recombines DNA.

2. **Cloudflare Project Glasswing's Mythos pipeline** — an 8-stage vulnerability
   discovery pipeline: Recon → Hunt → Validate → Gapfill → Dedupe → Trace →
   Feedback → Report.

The key insight from the original colony_agent repo: the Python scripts are
just a harness. The real intelligence is in the *prompts*. Colony-Mythos strips
away the harness and runs purely on Hermes Agent's native capabilities.

## Architecture

```
User says "start a colony on target X"
              │
              ▼
    ┌─────────────────────┐
    │   AGENTS.md         │  ← Queen persona (YOU)
    │   (Hermes Agent)    │
    │                     │
    │  Cycle:             │
    │  ASSESS → EVOLVE → │
    │  ALLOCATE → ADVANCE│
    │  → SYNTHESIZE      │
    └──┬──────────┬───────┘
       │          │
       │ spawns   │ reads/writes
       ▼          ▼
┌──────────┐  ┌────────────────┐
│ Workers  │  │ Colony State   │
│ (via     │  │ (markdown +    │
│ delegate │  │  JSONL files)  │
│ _task)   │  │                │
└──────────┘  └────────────────┘
```

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
- A rendered genome (hypothesis + DNA + evolution plan)
- The target path and scaffold type
- Instructions to post findings to the social feed file
- A budget (max iterations, max wall time)

### State (Files on Disk)

Everything lives in `colony-runs/<colony-id>/`:

| Path | Purpose |
|------|---------|
| `colony_state.md` | Live state: workers, surfaces, findings, hall of fame |
| `genomes/genome-NNN.md` | Per-genome: hypothesis, DNA, evolution plan |
| `social/feed.jsonl` | Worker posts (JSONL) |
| `findings/F-NNN.md` | Confirmed finding records |
| `decisions/cycle-NNN.json` | Queen decision log |
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

## Starting a Colony

Just tell Hermes:

```
"Start a colony on /Applications/Insomnia.app — it's an Electron app"
"Start a colony on https://github.com/org/repo — it's a source repo"
"Hunt this Android APK at /path/to/app.apk"
```

The Queen (Hermes) will:
1. Create `colony-runs/<target>-<timestamp>/`
2. Initialize colony state from the template
3. Run Stage 1 Recon: explore the target, build attack-surface inventory
4. Spawn initial recon workers
5. Present the Cycle 1 decision

## Running Cycles

After a colony is started:

```
"Run a cycle" — the Queen assesses workers, evolves genomes, allocates slots
"Show me the feed" — read the social feed
"What's the status?" — colony health summary
"Kill colony" — stop all workers, archive state
```

## Credits

- **colony_agent** by @qriousec — the evolutionary colony architecture, genome
  system, fitness scoring, mutation types, and the BUILD→TEST→OBSERVE→
  TROUBLESHOOT→IMPROVE worker loop
- **Project Glasswing (Cloudflare)** — the Mythos 8-stage pipeline, adversarial
  validation, consumer tracing, and gapfill/dedupe stages
- **Hermes Agent** by Nous Research — the agent runtime that makes this
  prompt-driven architecture possible
