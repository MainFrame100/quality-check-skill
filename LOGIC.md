# Quality Check Logic

Version: 1.1.0  
Last Updated: 2026-06-25

## Why This Skill Exists

Long AI sessions fail differently from short prompts.

In a small task, errors are usually easy to see. In a long task, the result can look polished while the underlying evidence is weak: a number is guessed, a quote loses attribution, a document drifts away from the original goal, or a strong recommendation is built on a thin source.

Quality Check is a structured review protocol for those situations.

It was built from recurring failure modes in AI-agent work:

- the model says "done" without naming what is incomplete;
- source-backed facts and the model's own interpretation get mixed together;
- numbers and time spans appear without a traceable source;
- a long discussion slowly shifts away from the original request;
- the final artifact is useful as a draft, but unsafe to share as-is;
- self-review by the same model is too forgiving.

## Core Checks

### 1. Anti-Victory

The agent must name what is not done, what is unknown, and what assumptions remain.

This prevents vague success statements such as "everything is covered" when the artifact still has gaps.

### 2. Honest Output

The agent must decide whether the result is usable as-is or needs more work.

The answer should be operational: what needs to change, who should do it, and what blocks usage.

### 3. Context Drift

The agent compares the final result with the original request.

This catches a common long-session issue: the work may become more elaborate while moving away from what the user actually asked for.

### 4. Challenge Protocol

The agent tests the result against:

- a scenario where it fails;
- missing information that could change the conclusion;
- an alternative interpretation;
- assumptions accepted without verification.

This is a lightweight adversarial review.

### 5. Source Verification

This is the most important part for research-heavy work.

The agent inventories checkable claims and classifies them:

- `NUMBER` - numbers, counts, intervals;
- `PERCENT` - percentages and shares;
- `MULTIPLIER` - exact ("2x", "10x") and rhetorical ("several times", "much more", "drastically"). Rhetorical multipliers read as facts but carry no number, so they are the blind spot of sampling-based review and must be inventoried explicitly;
- `TIMESPAN` - exact dates, durations, periods;
- `QUOTE` - direct quotes and attributed phrases;
- `COMPARISON` - better, worse, best, most important;
- `INTENSIFIER` - always, never, everyone, nobody;
- `OWNER` - who is responsible for a task or decision;
- `AUTHORSHIP` - places where the user's own wording is expected.

Numbers, percentages, exact dates, and exact time spans should be checked at 100 percent coverage. If a number has no source, it should be removed, rewritten, or marked as an interpretation.

Two rules sharpen this. Auto-escalate: any claim that appears in the artifact's acceptance criteria, definition of done, or section headings is checked at 100 percent regardless of class - structural prominence outweighs a "load-bearing" judgment call. The 30-second rule: if the agent cannot name a concrete source for a number, percentage, multiplier, or exact time span within 30 seconds, it marks the claim as added or missing immediately rather than deferring it.

In the final report, Source Verification is condensed to two lines: an inventory summary by class, and a discrepancies-only table that lists contradictions, not-found, and rewritten claims. Confirmed claims are not listed - they would bury the report.

The expected source verdicts are:

- `CONFIRMED` - the source supports the claim;
- `CONTRADICTS` - the source contradicts the claim;
- `NOT FOUND` - the source does not contain the claim;
- `SOURCE UNAVAILABLE` - the source cannot be checked;
- `ADDED` - the claim is the agent's interpretation, not source data.

### 6. D/L/C/P Scoring

The score separates four dimensions:

- `D - Data`: are the facts sourced?
- `L - Logic`: does the reasoning follow from the data?
- `C - Consistency`: are there contradictions?
- `P - Proportionality`: are conclusions proportional to the evidence?

The score is not a scientific metric. It is a forcing function: the agent must explain why the artifact is reliable or not.

Suggested interpretation:

- `0.8+` - reliable enough to continue;
- `0.5-0.8` - useful as a hypothesis or draft, needs review;
- `<0.5` - stop and get better data before using;
- `<0.2` - do not create final artifacts from this evidence.

### 7. Cognitive Bias Scan

The agent checks for confirmation bias, survivorship bias, false mental models, and false dichotomies.

This part is less deterministic than source verification, but it often catches overconfident reasoning.

### 8. Test Results

For code, scripts, data processing, or any claim that "it works", the agent must show actual output.

Status-only statements are weak. Real stdout, examples, rows, or observed results are better.

### 9. Hallucination Self-Diagnosis

The agent does one final scan for unsupported precision, too-convenient facts, overconfident tone, and unsourced attribution. It also checks for revision lineage - traces of the editing process ("previously X, now Y", "version rebuilt", "renamed") left inside an artifact that is not itself a changelog. A finished artifact should read as if written from scratch and hold only its final state; the story of what changed belongs in the reply to the user, not in the file.

### 10. Placement / Section Check

For file-based workflows, the agent checks whether new files were created in the right place and whether documentation needs updates.

### 11. Cross-Model Verification

For important artifacts, run the check with another model family if possible.

Example:

- create with Claude;
- verify with Codex/GPT;
- or create with GPT and verify with Claude.

The point is not that one model is better. The point is that independent models often have different blind spots.

## What Counts As A Good Result

Good Quality Check output is not polite reassurance.

It should produce one of these:

- `Ready as-is` - the artifact is usable with named limitations;
- `Needs revision` - useful, but specific issues must be fixed;
- `STOP` - do not use this as a final artifact;
- `BLOCKED` - required sources or context are missing.

The most valuable result is often a stop sign: "this document looks good, but it is not safe to use yet."

## Practical Advice

For short artifacts, one pass is enough.

For long artifacts:

1. check each major section separately;
2. fix issues section by section;
3. run a final top-level check;
4. do not ask the model to verify more source material than it can hold in context.

If the model cannot point to a source in 30 seconds, treat the claim as unsupported until proven otherwise.

When work is delegated to a subagent, push the same discipline upstream: the skill includes a short self-check template to append to the subagent's prompt, forcing it to attribute every quote to a source and to flag any number it produced by estimate rather than by counting. This stops unverified claims from returning to the main agent already dressed as facts.
