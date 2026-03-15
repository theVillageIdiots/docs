# Risk Signoff Packet Template

**Release:** <!-- required -->  
**Build/Commit SHA:** <!-- required -->  
**Risk Reviewer:** <!-- required -->  
**Date (ET):** <!-- required -->

---

## 1) Risk Checklist Linkage (required evidence)

| Checklist ID | Release Checklist Item | Risk Review Requirement | Evidence |
|---|---|---|---|
| RC-OP-02 | 24h forward paper validation | Review stability and behavior over full run |  |
| RC-OP-03 | Extend to 48h if no regressions | Confirm continuation criteria and outcomes |  |
| RC-OP-04a | Win rate by path | Verify no pathological drift by path |  |
| RC-OP-04b | Realized PnL by path | Verify expected value profile remains acceptable |  |
| RC-OP-04c | Drawdown | Verify drawdown within approved tolerance |  |
| RC-OP-04d | Breaker halt behavior | Verify breaker triggers are consistent and protective |  |
| RC-OP-04e | Fill spread paid vs midpoint | Verify execution quality and slippage bounds |  |
| RC-BG-05 | Leakage (H1/H2) PASS | Confirm residual leakage risk acceptable |  |
| RC-BG-06 | Fill realism (H5) PASS | Confirm execution realism risk acceptable |  |
| RC-BG-07 | Breaker consistency (H3) PASS | Confirm market protection logic is reliable |  |

---

## 2) Risk Assessment Summary

### Market/Model Risk
- [ ] Post-fix behavior aligns with approved risk profile
- [ ] No new unacceptable tail-risk modes observed

**Notes:**

### Operational/Control Risk
- [ ] Monitoring visibility is sufficient for controlled rollout
- [ ] Incident response owner and escalation path confirmed

**Notes:**

### Data Integrity Risk
- [ ] No evidence of leakage reintroduction
- [ ] Training/inference data handling remains controlled

**Notes:**

---

## 3) Quantitative Evidence Attachments

Attach or link all required artifacts:
- [ ] Forward paper run summary (24h, and 48h if applicable)
- [ ] KPI before/after report covering RC-OP-04a..e
- [ ] Breaker event log review
- [ ] Fill quality/slippage analysis
- [ ] Exception/incident log for run window

Artifact index:
1. __________________________
2. __________________________
3. __________________________
4. __________________________
5. __________________________

---

## 4) Risk Conditions / Controls for Go-Live

- Position/risk limits in effect:
- Circuit breaker thresholds in effect:
- Required monitoring alerts:
- Mandatory rollback triggers:

---

## 5) Risk Approval

**Decision:**  
- [ ] APPROVE
- [ ] CONDITIONAL APPROVE
- [ ] REJECT

**Conditions / comments:**


**Risk Owner Name:** __________________________  
**Signature:** __________________________  
**Date (ET):** __________________________
