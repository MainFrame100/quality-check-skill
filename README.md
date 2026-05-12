# Quality Check Skill

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Skill: validated](https://img.shields.io/badge/skill-validated-brightgreen.svg)](VALIDATION.md)
[![Package: .skill](https://img.shields.io/badge/package-.skill-blue.svg)](quality-check.skill)
[![Languages: EN/RU](https://img.shields.io/badge/languages-EN%20%7C%20RU-orange.svg)](README.ru.md)

**English** | [Русский](README.ru.md)

Public version of an agent skill for checking long AI-generated artifacts before you trust or share them.

It is useful when an AI agent has assembled a report, brief, research summary, specification, strategy, transcript digest, or any other document based on multiple sources.

The skill does not make the model "always correct". It makes the model slow down, list assumptions, verify claims against sources, and clearly say what is ready to use and what is not.

Last Updated: 2026-05-12

## Author And License

Author: **Dmitrii Yuzhanin**  
Source: https://github.com/MainFrame100/quality-check-skill  
License: **CC BY 4.0**

You may share and adapt this skill, including commercially, but you must keep attribution to Dmitrii Yuzhanin and link to the original repository.

## What It Helps With

Quality Check is designed to catch the most visible and dangerous failure modes in long AI work:

- facts without sources;
- numbers, percentages, dates, or time spans that were guessed;
- quotes with unclear attribution;
- conclusions that are stronger than the evidence;
- missing limitations and hidden assumptions;
- drift away from the original user request;
- confident "done" answers when the artifact still needs work;
- untested code or data transformations;
- source verification gaps in research-heavy documents.

It is especially useful when one model creates the artifact and another model checks it. For example: create a document in Claude, then ask Codex/GPT to run Quality Check against the same local source files.

Different model families often catch different mistakes.

## How To Install

Use either the ready package or the source folder.

Option 1: download the ready package if your agent runtime supports `.skill` files:

```bash
curl -L -o quality-check.skill \
  https://github.com/MainFrame100/quality-check-skill/raw/main/quality-check.skill
```

Then import `quality-check.skill` using your agent runtime's skill import flow.

Option 2: copy the source folder into your agent skills directory:

Example:

```bash
cp -R quality-check ~/.claude/skills/
```

Use the equivalent skills directory for your own agent runtime if it differs.

## How To Update

The canonical source is this repository:

```text
https://github.com/MainFrame100/quality-check-skill
```

When you adapt the skill, keep the `metadata.author`, `metadata.source`, `metadata.version`, and `license` fields in `quality-check/SKILL.md` so future updates can be traced.

## How To Invoke

Automatic triggering is not always stable, especially across different agent environments.

For best results, call the skill explicitly:

```text
/quality-check
```

Or use clear trigger phrases:

```text
Проверь качество ответа.
Проверь качество результата.
Найди ошибки в этом документе.
Проверь факты и источники.
Запусти quality-check для этого артефакта.
Можно ли использовать это as-is?
```

When possible, include:

- the artifact to check;
- source files or URLs;
- the original task;
- the intended use of the result;
- whether the output will be shared with a client, team, or public audience.

## Recommended Workflow

1. Ask an AI agent to create the artifact.
2. Save or point to the artifact and its sources.
3. Run `quality-check`.
4. Fix every `CONTRADICTS`, unsupported number, missing source, and blocking `GAP`.
5. For large documents, split the review by section.
6. Run a final short quality check on the revised version.

## Limitations

This skill does not guarantee that every mistake will be found.

It is good at catching obvious and high-risk issues: unsupported numbers, missing citations, weak source coverage, scope drift, and overconfident claims.

It may miss subtle problems such as weak hypotheses, excessive abstraction, or overgeneralization. Humans miss those too, so treat this as a structured review assistant, not an oracle.

For very large documents, do not check everything in one pass. Split the artifact into sections, because the reviewing model can also lose details when the context is too large.

## Files

- `quality-check.skill` - ready-to-share packaged skill file.
- `quality-check/SKILL.md` - the actual skill file.
- `LOGIC.md` - explanation of the checks, why they exist, and how to interpret the output.
- `README.ru.md` - Russian documentation.
- `ATTRIBUTION.md` - recommended attribution text.
- `VALIDATION.md` - validation report for the packaged skill.

## Suggested Output Standard

A useful Quality Check report should answer three questions:

1. Can I use this result as-is?
2. What exactly must be fixed before use?
3. Which claims were verified against sources, and which were not?
