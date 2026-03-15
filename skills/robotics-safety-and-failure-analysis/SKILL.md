---
name: robotics-safety-and-failure-analysis
description: Develop robotics safety cases and failure analyses for real-world deployment. Use when assessing hazards and defining safe operating limits.
---

# Robotics Safety and Failure Analysis

Use this skill as the primary operating playbook for this domain.

## Domain Focus
- Hazard analysis and severity
- Fail-safe and fallback behaviors
- Verification of safety requirements

## Workflow
1. Define mission profile, environment, and system-level constraints.
2. Break down perception, planning, control, and hardware interfaces.
3. Model safety boundaries, fault responses, and recovery behaviors.
4. Validate in simulation then hardware-in-the-loop before field use.
5. Instrument key telemetry for reliability and maintainability in operation.
6. Close the loop with incident reviews and design updates.

## Deliverables
- System architecture and interface contract
- Verification plan across simulation and real hardware
- Safety case with failure detection and recovery logic
- Operational maintenance and incident response runbook

## Quality Bar
- Tie recommendations to measurable outcomes and explicit constraints.
- Make tradeoffs explicit and name what is intentionally deprioritized.
- Keep assumptions testable and define what signal would invalidate the plan.
- Produce outputs another agent can execute without additional clarification.
