# Validation

Last Updated: 2026-05-12

Validated with `skill-conductor` package/review checks.

## Commands

```bash
uv run <skill-conductor>/scripts/quick_validate.py quality-check

uv run <skill-conductor>/scripts/eval_skill.py quality-check

uv run <skill-conductor>/scripts/package_skill.py quality-check .
```

## Results

- `quick_validate.py`: passed.
- `eval_skill.py`: 10/10, no warnings.
- `package_skill.py`: passed.
- `quality-check.skill`: contains `quality-check/SKILL.md`.

## Notes

The skill is intentionally packaged as a single `SKILL.md` file. Documentation lives outside the skill folder so agent runtimes load only the operational instructions.
