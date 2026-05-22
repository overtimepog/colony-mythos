   ______      __                     __  ___      __  __              
  / ____/___  / /___  ____  __  __   /  |/  /_  __/ /_/ /_  ____  _____
 / /   / __ \/ / __ \/ __ \/ / / /  / /|_/ / / / / __/ __ \/ __ \/ ___/
/ /___/ /_/ / / /_/ / / / / /_/ /  / /  / / /_/ / /_/ / / / /_/ (__  ) 
\____/\____/_/\____/_/ /_/\__, /  /_/  /_/\__, /\__/_/ /_/\____/____/  
                         /____/          /____/                        

     Evolutionary multi-agent vulnerability hunting colony
    genomes · queens · social networks · differential oracles

┌──────────────────────────────────────────────────────────────────────────┐
│  One queen. A population of LLM workers. Each carries a genome —          │
│  a hypothesis about where a vulnerability lives. The queen scores         │
│  fitness, evolves genomes, kills the weak, and promotes the sharp.        │
│                                                                          │
│  Findings flow through the 8-stage Mythos pipeline (Recon → Hunt →       │
│  Validate → Gapfill → Dedupe → Trace → Feedback → Report), but the       │
│  pipeline isn't just a conveyor belt — every stage feeds genetic          │
│  signals back into the colony. Validate CONFIRMED? Pattern-export.        │
│  Validate REJECTED? Absorb corrected scope into the genome. Trace         │
│  REACHABLE? Spawn consumer-trace workers. Not reachable? Archive.         │
│                                                                          │
│  The pipeline and the evolution are symbiotic — each feeds the other.     │
└──────────────────────────────────────────────────────────────────────────┘

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ ARCHITECTURE

┌──────────────────────────────────────────────────────────────┐
│                    COLONY-MYTHOS                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│   THE SUBSTRATE                 THE DECISION ENGINE          │
│                                                              │
│   ┌──────────────────────┐    ┌──────────────────────────┐  │
│   │ 8-Stage Pipeline     │    │ Queen Cycle              │  │
│   │                      │    │                          │  │
│   │ Recon → Hunt →       │◄──►│ ASSESS → EVOLVE →       │  │
│   │ Validate → Gapfill → │ █  │ ALLOCATE →              │  │
│   │ Dedupe → Trace →     │ █  │ STAGE_ADVANCE →         │  │
│   │ Feedback → Report    │ █  │ SYNTHESIZE              │  │
│   │                      │    │                          │  │
│   │ What happens to      │    │ What to hunt next        │  │
│   │ findings             │    │                          │  │
│   └──────────┬───────────┘    └───────────┬──────────────┘  │
│              │                            │                  │
│              └──────────┬─────────────────┘                  │
│                         │                                    │
│              ┌──────────▼──────────┐                        │
│              │  PIPELINE-QUEEN     │                        │
│              │  BRIDGE             │                        │
│              │                     │                        │
│              │  signals ↑  tasks ↓ │                        │
│              └──────────┬──────────┘                        │
│                         │                                    │
│   Pipeline stages →  genetic signals →  Queen evolves       │
│   Queen actions →  pipeline tasks →  findings advance       │
│                                                              │
└──────────────────────────────────────────────────────────────┘

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ THE PIPELINE-QUEEN BRIDGE

Pipeline stages don't just advance findings — they produce genetic signals
that the Queen uses to evolve genomes:

┌──────────────┬──────────────────────────┬──────────────────────────────┐
│ Stage        │ Signal                   │ Queen Response               │
├──────────────┼──────────────────────────┼──────────────────────────────┤
│ Recon        │ Attack-surface tiers     │ Seed genomes per surface     │
│ Hunt         │ Fitness scores, DNA      │ Score workers, evolve        │
│ Validate     │ CONFIRMED → pattern      │ Pattern-export mutation      │
│ Validate     │ REJECTED → overstated    │ Absorb corrected scope       │
│ Gapfill      │ Under-covered area       │ Lateral/random mutation      │
│ Dedupe       │ Pattern cluster found    │ Grep-walk sibling subsystems │
│ Trace        │ REACHABLE → exploitable  │ Consumer-trace spawn         │
│ Trace        │ NOT REACHABLE → dead end │ Archive, enrich dead surface │
│ Feedback     │ New consumer hunt tasks  │ Spawn consumer workers       │
│ Report       │ Structured finding       │ Hall of fame, pattern lib    │
└──────────────┴──────────────────────────┴──────────────────────────────┘

And Queen decisions generate pipeline tasks:

┌──────────────────────┬──────────────────┬────────────────┐
│ Queen Action         │ Pipeline Task    │ Stage          │
├──────────────────────┼──────────────────┼────────────────┤
│ SPAWN (hunt)         │ Hunt task        │ Stage 2        │
│ STAGE_ADVANCE        │ Validate task    │ Stage 3        │
│ KILL + absorb DNA    │ Gapfill task     │ Stage 4        │
│ BUG_ARCHIVE          │ Trace → Report   │ Stages 6 → 8   │
│ PATTERN_EXPORT       │ Hunt × N siblings│ Stage 2        │
└──────────────────────┴──────────────────┴────────────────┘

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ KEY RULES

  ◈  Never Validate without a DIFFERENT model
  ◈  Never Trace without a consumer repo list
  ◈  Validate verdicts MUST feed back into genome evolution
  ◈  Absorb DNA from killed genomes before disposal
  ◈  Narrow scope produces better findings
  ◈  "It works correctly" is a kill signal

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ THE QUEEN CYCLE

  1. ASSESS        Read social feed + pipeline signals
  2. EVOLVE        Mutate genomes using pipeline signals
  3. ALLOCATE      Portfolio: coverage vs exploit vs export
  4. STAGE_ADVANCE  Generate pipeline tasks from decisions
  5. SYNTHESIZE    Cross-worker pattern detection

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ QUICK START

  cd ~/colony-mythos

  # Start a colony on any target
  "Start a colony on /Applications/Insomnia.app — Electron app"
  "Hunt this Android APK at /path/to/app.apk"
  "Start a colony on https://github.com/org/repo — source repo"

  # Drive the colony
  "Run a cycle"
  "Show me the feed"
  "What's the colony status?"

  # Seven target scaffolds included
  source-repo · electron-app · web-app · container-image
  binary-executable · android-app · ios-app

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ STATE (everything is markdown + JSONL on disk)

  colony-runs/<id>/
  ├── colony_state.md       Pipeline status · signal log · workers · surfaces
  ├── genomes/              Per-genome: hypothesis · DNA · evolution plan
  ├── social/feed.jsonl     Worker posts: differentials · primitives · findings
  ├── findings/             Confirmed finding records
  ├── decisions/            Queen decision log per cycle
  └── reports/              Final structured reports

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ GENOME SYSTEM

  Each worker carries a genome — a living document that compounds knowledge:

  genome-NNN.md
  ├── Hypothesis     Specific multi-factor vulnerability prediction
  ├── DNA            Accumulated: mechanisms · guards · techniques · failures
  ├── Evolution Plan v1 → v2 → v3 bypass angles
  ├── Mutation History   Full lineage
  └── Pipeline Signals   What stages told us about this genome

  11 mutation types: narrow · intensify · lateral · recombine · source-pivot
  factor-add · pattern-export · chain-extend · consumer-trace · deopt-pivot · random

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

▌ CREDITS

  ◈ colony_agent by @qriousec — evolutionary colony architecture, genome system
  ◈ Project Glasswing (Cloudflare) — Mythos 8-stage pipeline
    https://blog.cloudflare.com/cyber-frontier-models/
  ◈ Hermes Agent by Nous Research — the agent runtime

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

No Python machinery. No CLI. Hermes Agent IS the Queen.
Just prompts. Just markdown. Just evolution.
