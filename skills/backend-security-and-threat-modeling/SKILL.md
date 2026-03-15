---
name: backend-security-and-threat-modeling
description: Run security design and threat modeling for backend systems and data flows. Use when handling sensitive data auth boundaries and abuse risk.
---

# Backend Security and Threat Modeling

Use this skill as the primary operating playbook for this domain.

## Domain Focus
- Threat modeling workflow
- Abuse case analysis
- Control and mitigation prioritization

## Workflow
1. State technical objective, constraints, and non-negotiable quality bars.
2. Inspect current architecture, code paths, and operational telemetry.
3. Design the implementation approach with failure modes and rollback plans.
4. Implement incrementally with tests, instrumentation, and documentation.
5. Validate behavior, performance, and security against acceptance criteria.
6. Ship safely with release notes and post-deploy verification checks.

## Deliverables
- Technical design and architecture decisions
- Implementation plan with test strategy
- Operational runbook and observability checks
- Release and rollback checklist

## Quality Bar
- Tie recommendations to measurable outcomes and explicit constraints.
- Make tradeoffs explicit and name what is intentionally deprioritized.
- Keep assumptions testable and define what signal would invalidate the plan.
- Produce outputs another agent can execute without additional clarification.
