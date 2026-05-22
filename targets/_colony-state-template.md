# Colony State Template

Copy this file to `colony-runs/<colony-id>/colony_state.md` and fill in.

---

# Colony: COLONY_ID_HERE

**Target:** /absolute/path/to/target
**Scaffold:** source-repo | electron-app | web-app | container-image | binary-executable | android-app | ios-app
**Started:** YYYY-MM-DDTHH:MM:SS
**Cycle:** 0
**Phase:** phase1
**Pipeline Stage:** recon

---

## Workers

| Worker ID | Genome | Stage | Status | Fitness | Iterations | Last Activity |
|-----------|--------|-------|--------|---------|------------|---------------|
| — | — | — | — | — | — | — |

---

## Attack Surface Inventory

*Built during Stage 1 (Recon). The queen populates this from target exploration.*

### Tier 1 — Highest Priority (Known Vulnerability Patterns, Multiple CVEs)

| ID | Surface | Key Files | Risk | Coverage | Signal |
|----|---------|-----------|------|----------|--------|
| | | | | 0 workers | none |

### Tier 2 — High Priority (Structural Complexity, Under-Tested)

| ID | Surface | Key Files | Risk | Coverage | Signal |
|----|---------|-----------|------|----------|--------|
| | | | | 0 workers | none |

### Tier 3 — Emerging / Speculative

| ID | Surface | Key Files | Risk | Coverage | Signal |
|----|---------|-----------|------|----------|--------|
| | | | | 0 workers | none |

---

## Dead Surfaces

*Surfaces confirmed defended by 2+ workers with no unexhausted blind spots.*

| Surface | Why Dead | Guard Location | Confirmed By | Cycle |
|---------|----------|----------------|--------------|-------|
| | | | | |

---

## Pattern Library

*Exportable bug patterns that apply across subsystems.*

| ID | Pattern | Origin | Grep Command | Applied To | Untested Siblings |
|----|---------|--------|-------------|------------|-------------------|
| | | | | | |

---

## Hall of Fame

*Top-performing genomes and confirmed bugs.*

| Genome | Reason | Cycle |
|--------|--------|-------|
| | | |

---

## Findings

| ID | Title | Severity | Stage | PoC Status | Genome |
|----|-------|----------|-------|------------|--------|
| | | | | | |

---

## Guardrail State

- **Consecutive zero-differential cycles:** 0
- **Last pivot:** never
- **Monoculture check:** clean

---

## Colony Health Metrics

- **Differentials this cycle:** 0
- **Total differentials:** 0
- **Primitives cataloged:** 0
- **Findings confirmed:** 0
- **Avg worker fitness:** 0.0
- **Tier 1 coverage:** 0%
- **Tier 2 coverage:** 0%
