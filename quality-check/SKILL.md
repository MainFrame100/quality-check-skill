---
name: quality-check
license: "CC-BY-4.0"
metadata:
  version: "1.0.0"
  author: "Dmitrii Yuzhanin"
  source: "https://github.com/MainFrame100/quality-check-skill"
description: >
  Structured quality review for AI-generated artifacts before they are trusted
  or shared. Use when the user asks to check quality, find errors, verify facts,
  review sources, validate a report, inspect a long answer, or decide whether an
  artifact can be used as-is. Do NOT use for simple one-file edits, routine
  formatting, or casual conversation without an artifact.
---

# Quality Check

Author: Dmitrii Yuzhanin  
Source: https://github.com/MainFrame100/quality-check-skill  
License: CC BY 4.0. Keep attribution and source link when sharing or adapting.

Use this skill to review an AI-generated artifact before the user trusts it, shares it, or builds on it.

The goal is not to prove that the artifact is perfect. The goal is to expose unsupported claims, missing sources, weak conclusions, drift from the original task, and unclear readiness.

## Inputs To Request Or Infer

Identify:

1. the artifact being checked;
2. the original user request or intended job;
3. the source files, URLs, transcripts, datasets, or notes used;
4. the intended use: private draft, team handoff, client-facing, public, production;
5. whether the artifact is small enough to check in one pass.

If the artifact is very large, split the review by section before scoring the whole result.

## 1. Anti-Victory

Name what is not done, not checked, or not known.

Every report must include at least one limitation or explicitly say why no material limitation was found.

Use:

- `[GAP]` for missing information;
- `[GAP: owner?]` when the responsible person or next step is unclear;
- `[ADDED]` for the agent's own interpretation that is not directly present in sources.

Avoid vague success claims such as "everything is covered" or "fully complete" unless the evidence supports them.

## 2. Honest Output

Decide whether the artifact can be used as-is.

Use one of:

- `Ready as-is` - usable with named limitations;
- `Needs revision` - useful, but specific fixes are required;
- `STOP` - do not use as a final artifact;
- `BLOCKED` - required sources or context are missing.

List concrete fixes when revision is required.

## 3. Context Drift

Compare the artifact with the original request.

Check:

- did the output answer the actual request?
- did it add goals the user did not ask for?
- did it shift from evidence gathering to strategy, recommendation, or opinion without permission?
- did it preserve the intended audience and use case?

If drift exists, describe it as: `Drift: from [original scope] to [actual output]`.

## 4. Challenge Protocol

Run four adversarial checks:

1. `Inversion`: name one scenario where the result fails.
2. `Missing search`: name information not checked that could change the conclusion.
3. `Steelman alternative`: describe what evidence would support the opposite conclusion.
4. `Assumption audit`: list facts accepted without verification.

## 5. Source Verification

If the artifact contains concrete facts, claims, numbers, quotes, dates, comparisons, or assigned owners, verify them against sources.

### 5a. Inventory

Scan the whole artifact and classify checkable claims:

- `NUMBER` - counts, amounts, intervals;
- `PERCENT` - percentages, shares, ratios;
- `MULTIPLIER` - 2x, 10x, "several times", "much more";
- `TIMESPAN` - dates, durations, exact periods;
- `QUOTE` - direct quotes or attributed wording;
- `COMPARISON` - better, worse, best, main, most important;
- `INTENSIFIER` - always, never, everyone, nobody, constantly;
- `OWNER` - who is responsible for a task, decision, or dependency;
- `AUTHORSHIP` - sections where the user's own wording or subjective judgment is expected.

If the artifact has fewer than five checkable claims, list them directly. Otherwise summarize the inventory by class.

### 5b. Coverage Rules

Check 100 percent of:

- `NUMBER`;
- `PERCENT`;
- exact `MULTIPLIER`;
- exact `TIMESPAN`;
- quote attribution;
- task `OWNER` in team or client work;
- `AUTHORSHIP` where the user must provide the wording.

Check high-impact `COMPARISON` and `INTENSIFIER` claims. Escalate to 100 percent coverage when the recommendation depends on them.

If a number, percentage, multiplier, or exact time span has no source, remove it, soften it, or mark it as `[ADDED]`. Do not present unsupported precision as fact.

### 5c. Verdicts

For each checked claim, return one of:

- `[CONFIRMED: source]` - source supports it;
- `[CONTRADICTS: source]` - source contradicts it;
- `[NOT FOUND]` - source does not contain it;
- `[SOURCE UNAVAILABLE]` - source cannot be checked;
- `[ADDED]` - interpretation or wording added by the agent.

Each tested claim must lead to a binary action:

- keep;
- rewrite;
- remove.

Do not use "acceptable with caveat" as a final verdict. If a caveat is needed, rewrite the artifact.

## 6. D/L/C/P Scoring

Score the artifact on four dimensions:

| Criterion | Weight | Meaning |
|---|---:|---|
| D - Data | 0.4 | facts are sourced and verified |
| L - Logic | 0.3 | reasoning follows from the data |
| C - Consistency | 0.2 | no internal contradictions |
| P - Proportionality | 0.1 | conclusions are not stronger than evidence |

Use `0`, `0.5`, or `1` for each criterion, then calculate the weighted score.

Every score must include evidence. A score without evidence is invalid.

Suggested interpretation:

- `0.8+` - reliable enough to continue;
- `0.5-0.8` - draft or hypothesis, needs review;
- `<0.5` - stop and get better data;
- `<0.2` - do not create final artifacts from this evidence.

Apply penalties when relevant:

- code not tested: strong penalty;
- data not checked: strong penalty;
- required source unavailable: strong penalty.

## 7. Cognitive Bias Scan

Check for:

- `Confirmation bias`: only supporting evidence was considered;
- `Survivorship bias`: only successful or visible cases were analyzed;
- `False mental model`: structure was assumed without checking data;
- `False dichotomy`: the result says something "doesn't work" without evaluating trade-offs or extension points.

## 8. Test Results

For code, data processing, or operational claims, require actual evidence:

- stdout/stderr;
- sample rows;
- before/after examples;
- real output excerpts;
- failing command output.

If nothing was run, say: `Not tested`.

## 9. Hallucination Self-Diagnosis

After source verification, run a final scan:

- unsupported numbers or dates remain;
- tone is too confident for the evidence;
- facts conveniently support the conclusion;
- categorical language lacks a source;
- quotes or speakers were not verified.

If two or more signs appear, mark the artifact as requiring review.

## 10. Placement Check

For file-based work, check:

- are files in the expected location?
- is there a duplicate source of truth?
- does documentation need updating?
- are generated files separated from source files?

## 11. Cross-Model Verification

For important artifacts, recommend review by a different model family or a clean separate session.

Example:

- create with Claude, verify with Codex/GPT;
- create with GPT, verify with Claude.

The reviewing model must have access to the sources, not only the final artifact.

## Output Format

Return a concise report:

```markdown
# Quality Check: [artifact]

## Verdict
[Ready as-is / Needs revision / STOP / BLOCKED]

## 1. Anti-Victory
[gaps, limitations, unknowns]

## 2. Honest Output
[can this be used as-is? what must happen first?]

## 3. Context Drift
[matches request / drift found]

## 4. Challenge
[inversion, missing search, alternative, assumptions]

## 5. Source Verification
[inventory summary + only contradictions, not found, source unavailable, or added claims]

## 6. D/L/C/P
| D | L | C | P | Total | Evidence |
|---|---|---|---|---:|---|
| | | | | | |

## 7. Bias Scan
[biases found or not found]

## 8. Test Results
[actual output or Not tested]

## 9. Hallucination Check
[remaining risk signs]

## 10. Placement Check
[file/documentation issues if relevant]

## Next Fixes
[ordered list of concrete fixes]
```
