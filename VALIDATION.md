# Validation

Version: 1.1.0  
Last Updated: 2026-06-25

Validated with `skill-conductor` package/review checks.

## Commands

```bash
uv run <skill-conductor>/scripts/quick_validate.py quality-check

uv run <skill-conductor>/scripts/eval_skill.py quality-check

uv run <skill-conductor>/scripts/package_skill.py quality-check .
```

## Results

- `eval_skill.py`: 10/10, 0 warnings (9 structural checks passed; body 294 lines, description 364 chars).
- `quick_validate.py`: passed.
- `package_skill.py`: passed.
- `quality-check.skill`: re-packaged for 1.1.0, contains `quality-check/SKILL.md`.

Note: structural and packaging validation re-run for the 1.1.0 update. The behavioral trigger eval (`run_eval.py`) was not re-run in this revision; the 1.1.0 changes are additive to the verified 1.0.0 methodology and do not alter the description's triggers.

## Notes

The skill is intentionally packaged as a single `SKILL.md` file. Documentation lives outside the skill folder so agent runtimes load only the operational instructions.
