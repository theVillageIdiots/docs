---
name: backend-scalability-and-reliability
description: Improve backend scalability reliability and fault tolerance under load. Use when systems approach throughput latency or availability limits.
---

# Backend Scalability and Reliability

Use this skill as the primary operating playbook for this domain.

## Domain Focus
- Capacity and bottleneck analysis
- Resilience patterns and bulkheads
- SLO-driven reliability engineering

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
