---
name: review-checklist
description: Reviews a product brief (or similar proposal doc) against the PM's standing six-point checklist before it goes any further. Use when the user says "review this brief", "run the review checklist", "check this brief", or points at a brief file and asks for a review.
---

# Review checklist

Run the same six checks, in the same order, on any brief the user points at. If no file is named, ask which brief. Read the whole brief first, then check it. Do not edit the brief unless asked; this is a review.

## The six checks

1. **Owner.** Does the brief name who owns it (a specific person or role, not "TBD" or "the team")? Also flag an owner that is stale or contradicted elsewhere in the doc.
2. **Success measure.** Does it say how we'll know it worked: a metric, a target or direction, and a time frame or point of measurement? A goal with no way to tell if it was met fails.
3. **Scope consistency.** Compare the scope stated at the start (goals, summary, in-scope) with the scope at the end (what is actually proposed, release plan, requirements, non-goals). List anything that appears in one but not the other, including extras that crept in and promises that quietly dropped.
4. **Problem before fix.** Is the problem explained, with evidence, before any solution is proposed? Flag a brief that opens with the solution, or whose problem section only restates the solution.
5. **Post-release learning.** Are the questions or behaviors we'd want to understand after release proposed (what to watch, what would surprise us, what would change our mind)? Distinct from the success measure in check 2: this is what we want to learn, not only whether we hit a number.
6. **Who needs to weigh in.** Does it identify the products, PMs and/or teams that must weigh in on the proposed work (dependencies, shared data, adjacent products, support, legal/security where relevant)? Name any that the content implies but the brief omits.

## Output format

Start with one line: the brief's name and an overall verdict (ready to move on / needs changes / not ready).

Then a table with one row per check:

| # | Check | Result | Evidence |
| --- | --- | --- | --- |

- Result is **Pass**, **Partial** or **Fail**.
- Evidence quotes or points to the section of the brief (heading or line). For a Fail, say what is missing.

Then **Fix before it moves on**: a short list of the concrete changes, most important first. For each, say what to add or change, not just that something is wrong. Where the fix needs a decision or fact the brief's author must supply, say who to ask rather than inventing it.

## Rules

- Judge only what the brief says. Do not fill gaps from memory or assume a missing item exists elsewhere; if it might live in another doc, say "not in this brief".
- Be specific and brief. No praise padding, no restating the brief.
- Do not add checks beyond the six. If you notice something else serious, put it in one line under the table as "Also noticed".
