# OpenGait Research Notebook

> An open-source closed-loop Functional Electrical Stimulation (FES) walking system for complete spinal cord injury.

## Project Status
**Phase:** Architecture & Documentation
**Target Cost:** <$2,000 hardware BOM
**Target Outcome:** 0.1-0.2 m/s walking speed with walker assistance
**Last Updated:** 2025-11-29

---

## Notebook Contents

### [[01-overview/Project-Overview|01. Project Overview]]
- [[01-overview/Project-Overview|Project Overview & Goals]] — Executive summary, problem statement, and approach
- [[01-overview/Clinical-Context|Clinical Context: L4 Complete SCI]] — Injury classification, physiological considerations, contraindications

### [[02-existing-devices/Market-Landscape|02. Existing Devices]]
- [[02-existing-devices/Market-Landscape|Comprehensive Market Review]] — Parastep, Cleveland FES Center, Vienna system, exoskeletons, research systems (103 citations)

### [[03-architecture/System-Architecture|03. System Architecture]]
- [[03-architecture/System-Architecture|System Architecture]] — Distributed sensing, control layer, stimulation layer, key design decisions

### [[04-constraints/Technical-Constraints|04. Technical Constraints]]
- [[04-constraints/Technical-Constraints|Technical Constraints]] — Latency budget, EMD, fatigue, surface stimulation limits, BLE constraints

### [[05-hardware/Bill-of-Materials|05. Hardware Components]]
- [[05-hardware/Bill-of-Materials|Bill of Materials]] — Complete BOM (~$1,064), component specifications, sourcing notes

### [[06-power/Power-Requirements|06. Power System]]
- [[06-power/Power-Requirements|Power Requirements Analysis]] — Component consumption, FES power calculation, battery options, safety requirements (37 citations)

### [[07-safety/Safety-Standards|07. Safety & Standards]]
- [[07-safety/Safety-Standards|Safety Standards & Requirements]] — IEC 60601-2-10, electrical limits, hardware interlocks, clinical considerations, regulatory landscape (35 citations)

### [[08-gait-sensing/Gait-Biomechanics|08. Gait & Sensing]]
- [[08-gait-sensing/Gait-Biomechanics|Gait Biomechanics & Sensing]] — Gait phases, IMU detection, FSR placement, muscle timing, electrode placement, predictive control (59 citations)

### [[09-open-problems/Open-Questions|09. Open Problems]]
- [[09-open-problems/Open-Questions|Open Research Questions]] — Critical unknowns, testing requirements, collaboration opportunities

---

## Quick Reference

| Parameter | Target Value | Source |
|-----------|--------------|--------|
| Walking Speed | 0.1-0.2 m/s | Surface FES literature |
| Trigger Latency | <60ms (pre-EMD) | Architecture design |
| Stimulation Channels | 8-10 | Surface electrode limits |
| Session Duration | 15-30 min | Fatigue-limited |
| Hardware Cost | ~$1,064 | BOM estimate |

---

## Citation Summary

| Section | Citations |
|---------|-----------|
| Existing Devices | 103 |
| Power Requirements | 37 |
| Safety Standards | 35 |
| Gait Biomechanics | 59 |
| **Total** | **234** |

---

## Key Documents
- `PROJECT_OUTLINE.md` — Public-facing project documentation
- `PROJECT_RESEARCH.md` — Technical research synthesis
- `datasets/` — Reference papers and research materials
