# Release Signoff Templates

Templates for formalizing release signoff. All templates are traceable to the release checklist.

## Templates

| Template | Use |
|----------|-----|
| [engineering-signoff.md](engineering-signoff.md) | Engineering lead sign-off on code quality, test coverage, and technical readiness |
| [qa-signoff.md](qa-signoff.md) | QA sign-off on test results, gate test outcomes, and regression coverage |
| [risk-signoff.md](risk-signoff.md) | Risk sign-off on circuit breakers, drawdown limits, and capital exposure |
| [go-no-go.md](go-no-go.md) | Final Go/No-Go decision record with all conditions documented |

## Usage

1. Copy the template for the active release:
   ```
   cp engineering-signoff.md engineering-signoff-2026-03-06.md
   ```
2. Fill all required evidence fields
3. Attach artifact links (test run URLs, log files, etc.)
4. Ensure gate IDs in packets match checklist items before final Go/No-Go