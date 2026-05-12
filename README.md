# Quality Check Skill

Public version of an agent skill for checking long AI-generated artifacts before you trust or share them.

It is useful when an AI agent has assembled a report, brief, research summary, specification, strategy, transcript digest, or any other document based on multiple sources.

The skill does not make the model "always correct". It makes the model slow down, list assumptions, verify claims against sources, and clearly say what is ready to use and what is not.

Last Updated: 2026-05-12

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

Copy the `quality-check/` folder into your agent skills directory.

Example:

```bash
cp -R quality-check ~/.claude/skills/
```

Use the equivalent skills directory for your own agent runtime if it differs.

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

- `quality-check/SKILL.md` - the actual skill file.
- `LOGIC.md` - explanation of the checks, why they exist, and how to interpret the output.

## Suggested Output Standard

A useful Quality Check report should answer three questions:

1. Can I use this result as-is?
2. What exactly must be fixed before use?
3. Which claims were verified against sources, and which were not?

