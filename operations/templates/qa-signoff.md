# QA Signoff Packet Template

**Release:** <!-- required -->  
**Build/Commit SHA:** <!-- required -->  
**QA Owner:** <!-- required -->  
**Date (ET):** <!-- required -->

---

## 1) Checklist Traceability Matrix (required)

Map each release checklist gate to QA evidence.

| Checklist ID | Checklist Item | Status (Pass/Fail/Blocked) | Evidence Link/Artifact |
|---|---|---|---|
| RC-BG-01 | Unit/integration tests: `57/57` pass |  |  |
| RC-BG-02 | Gate tests A/B bundle (`test_assessment_fixes.py`, `test_path_a_gate.py`) |  |  |
| RC-BG-03 | Gate tests Phase bundle (`test_phase0.py`, `test_phase1.py`, `test_graph.py`) |  |  |
| RC-BG-04 | Audit scripts `03`, `05`, `06`, `09` pass |  |  |
| RC-BG-05 | QA re-run leakage (H1/H2) PASS |  |  |
| RC-BG-06 | QA re-run fill realism (H5) PASS |  |  |
| RC-BG-07 | QA re-run breaker consistency (H3) PASS |  |  |

---

## 2) Detailed QA Execution Evidence

### RC-BG-01 Unit/integration tests
- [ ] Command and environment recorded
- [ ] Full output/log attached
- [ ] Flaky test analysis included (if any)

**Command(s):** __________________________  
**Artifact path(s):** __________________________

### RC-BG-02 / RC-BG-03 Gate suites
- [ ] Exact test list executed as specified
- [ ] No skipped tests without waiver
- [ ] Failures triaged and resolved/waived

**Artifacts:** __________________________

### RC-BG-04 Audit scripts
- [ ] Scripts `03`, `05`, `06`, `09` executed against release SHA
- [ ] Outputs retained and attached

**Artifacts:** __________________________

### RC-BG-05 Leakage validation (H1/H2)
- [ ] Leakage checks rerun
- [ ] PASS criteria documented
- [ ] Before/after comparison included

**Artifacts:** __________________________

### RC-BG-06 Fill realism validation (H5)
- [ ] Fill simulation/execution realism checks rerun
- [ ] PASS criteria documented

**Artifacts:** __________________________

### RC-BG-07 Breaker consistency validation (H3)
- [ ] Breaker behavior consistency checks rerun
- [ ] PASS criteria documented

**Artifacts:** __________________________

---

## 3) Data Quality & Regression Notes

- New defects found:
- Severity and disposition:
- Deferred defects (with ticket IDs):
- Test coverage gaps accepted for this release:

---

## 4) QA Exit Criteria for Go/No-Go Input

- [ ] All blocking gates RC-BG-01..RC-BG-07 are PASS
- [ ] No open Sev1/Sev2 release blockers
- [ ] Evidence packet complete and reproducible

---

## 5) QA Approval

**Decision:**  
- [ ] APPROVE
- [ ] CONDITIONAL APPROVE
- [ ] REJECT

**Conditions / comments:**


**QA Lead Name:** __________________________  
**Signature:** __________________________  
**Date (ET):** __________________________
