---
name: context-engineering
description: Optimize a repository's context discoverability for coding agents and reduce token usage. Use when a user asks to set up, audit, or improve context engineering; create or repair AGENTS.md or CLAUDE.md; add context indexes, context articles, specs links, or self-maintaining agent docs; measure cold-start discovery hops; analyze recent commits to find important repository knowledge future agents need; mine repeated repo knowledge from code or git history; or make important repository information reachable by future agents in fewer hops.
---

# Context Engineering

## Mission

Make important repository knowledge reachable by a fresh agent in about 2 hops instead of 10-20, and install conventions that keep those docs and links accurate over time.

Run this loop:

```text
measure -> mine -> verify -> write -> index -> re-verify -> report
```

Non-negotiable: write a claim into docs only after verifying it against current code. Survey agents and grep-only passes can produce plausible wrong shortcuts, and a wrong shortcut is worse than none.

## Phase 0: Adopt Existing Policy

Read the repo's root `AGENTS.md` or `CLAUDE.md` first. Also read any context-dir `AGENTS.md`, specs README, or existing context index before proposing a new shape.

The repo's own rules win over defaults:

- Context directory name.
- Size budgets.
- Whether `CLAUDE.md` symlinks are expected.
- Index structure.
- Specs-vs-context split.
- Rules about file paths in specs.

Only if the repo has no self-maintained context policy, bootstrap with these defaults and say you are using defaults:

- Use per-directory `AGENTS.md`, each with a `CLAUDE.md` symlink: `ln -s AGENTS.md CLAUDE.md`.
- Keep each `AGENTS.md` under about 4000 characters, measured with `wc -m`.
- Past about 2000 characters, extract detail into a linked article.
- Use a `context/` directory for detailed navigation articles. Keep each article under about 32000 characters; split and cross-link beyond that.
- Treat context articles as navigation and decision context with `file:symbol` anchors, not normative contracts.
- If the repo has `specs/`, put normative MUST/SHOULD contracts there. Keep code anchors in context articles when the specs system forbids file paths.

## Phase 1: Measure Cold-Start Hops

Pick task domains to measure. Ask the user when domain choice is unclear; otherwise derive domains from repo structure or Phase 2 mining.

For each domain, use one read-only probe subagent when subagents are available. Run independent probes in parallel. One hop equals one file opened. Separate discovery hops from verification hops: answering from docs at hop 2 and then verifying in code through hop 9 is still a discovery success.

Probe prompt:

```text
Cold-start discovery-cost measurement in <repo-root> (branch <branch>).
Simulate a FRESH AI agent with no session context: start from root AGENTS.md,
follow its doc links (context articles -> specs -> per-directory AGENTS.md),
and grep/read code ONLY when docs fail to answer. Log every hop (file opened),
including useless ones.

Topic: <the concrete questions an agent must answer>

For each load-bearing claim you take from a doc, cross-check it against current
code and flag any statement that no longer matches (cite doc line vs code
file:line).

Report per question:
1. Verified answer with file paths.
2. Hop count.
3. Which docs helped vs misled.
4. The one doc/index addition that makes this reachable in <=2 hops.

End with a ranked gap list and any stale doc findings.
```

Always include one cross-cutting probe for seams that own no directory, such as:

- Frontend-to-backend request/message flow.
- Auth/identity from login to authorized call.
- Data change from edit to persistence.
- Sync, realtime, queue, or deployment topology.

A domain answered in <=4 hops through accurate docs is healthy. Do not fix it just to reduce the number. A 5-hop path through correct docs is better than a 3-hop path that required recovering from a wrong claim.

If a cross-cutting seam takes more than 4 hops or forces code-reading because no article maps it, create a `context/<seam>.md` article and index it.

## Phase 2: Mine Git History When Prioritizing Broadly

Use this phase when the user wants a broad audit rather than named domains. Treat everything from this phase as a hypothesis until Phase 3.

Useful mining commands:

```bash
git log -500 --no-merges --name-only --format= | sort | uniq -c | sort -rn | head -40
git log -500 --no-merges --format='%h %s' | rg -i 'actually|really|properly|again|still|revert|follow-up|missed|also fix'
```

Look for:

- Files fixed 5+ times, which often signal a missing invariant.
- Correction-language commits, which often signal a first attempt that lacked context.
- Commit bodies with 3+ lines of rationale, which often contain knowledge only findable by blame.
- High-churn directories without `AGENTS.md`, context articles, or specs coverage.

Rank gaps by `hops * recurrence`. This phase produces a priority list, not doc content. Read current code before documenting anything.

## Recent-Commit Discovery Audit

Use this mode when the user asks what important information should be easier to find based on recent work. Default to the latest 100 non-merge commits unless the user asks for another window.

Goal: identify repo knowledge that recent commits imply agents keep needing, measure how hard a fresh agent would find that knowledge, then report recommendations to the human. Do not write docs in this mode unless the user explicitly asks to apply the recommendations.

1. Mine the recent commits as hypotheses.

```bash
git log -100 --no-merges --name-only --format= | sort | uniq -c | sort -rn | head -40
git log -100 --no-merges --format='%h %s' | rg -i 'actually|really|properly|again|still|revert|follow-up|missed|also fix'
git log -100 --no-merges --format='%h %s%n%b%n---END---'
```

Look for:

- Files and directories repeatedly changed.
- Commit subjects that imply correction, missed context, or follow-up fixes.
- Commit bodies that explain non-obvious constraints.
- Cross-cutting topics spanning multiple directories.
- Code areas with repeated changes but no obvious `AGENTS.md`, context article, spec, or README pointer.

2. Convert commit evidence into discovery questions, not answers.

For each candidate, write the question a future agent would ask, such as:

- "Where is auth enforced for this route?"
- "How does this editor change reach persistence?"
- "Which module owns this invariant?"
- "What should not be extended here?"

Keep commit-derived facts labeled as hypotheses. Verify against current code only enough to avoid sending subagents meaningless or stale questions.

3. Measure hops with read-only subagents when available.

Give each subagent the question, not your suspected answer. Use this prompt:

```text
Cold-start discovery-cost measurement in <repo-root> (branch <branch>).
You are a fresh agent with no session context.

Start from root AGENTS.md or CLAUDE.md. Follow doc links first. Use grep/read
code only when docs fail to answer. Count every opened file as one hop.

Question: <question derived from recent commits>

Report:
1. Hop log.
2. Verified answer with file paths.
3. Total discovery hops before the answer was clear.
4. Which docs helped, which docs misled, and what was missing.
5. The smallest index/doc addition that would make the answer reachable in <=2-3 hops.

Stay read-only.
```

4. Rank candidates for the human.

Use this scoring model:

```text
priority = recurrence evidence * discovery hops * importance
```

Where importance is a judgment based on user-facing behavior, risk, cross-directory impact, or likelihood that future agents will touch the area again.

5. Report recommendations before editing docs.

Report a table with:

- Candidate knowledge.
- Commit evidence.
- Question measured.
- Current hop count.
- Docs that helped or misled.
- Recommended change: root index line, per-dir `AGENTS.md` pointer, `context/<topic>.md`, spec update, or `context/CONTEXT-GAPS.md` entry.
- Confidence and verification notes.

End with a short "recommended next patch" list. If the user approves implementation, continue with Phase 3 through Phase 5 for the selected items.

## Phase 3: Verify Before Writing

For each gap you plan to document, open current source files yourself and verify every load-bearing claim. Do not relay a survey agent's summary into docs.

Be extra suspicious of these patterns:

- "Endpoint X has no auth" because the auth gate may live in route wiring.
- "Y is not captured/logged/handled" because absence claims need positive proof.
- Behavior inferred from a commit title.
- "The spec/doc is stale here" because the relevant clause may already exist nearby.

If probing reveals stale docs that contradict current code, fix the stale doc in the same change.

## Phase 4: Write to the Right Place

| Content | Home |
| --- | --- |
| One-line "this exists, read it when..." | Root `AGENTS.md` context index, within budget |
| End-to-end map, `file:symbol` anchors, common confusions | `context/<topic>.md` article |
| Per-file responsibility split, expensive-discovery shortcuts, doc pointers | Per-directory `AGENTS.md` plus `CLAUDE.md` symlink |
| Normative MUST/SHOULD contracts and protocol shapes | Repo specs system |
| Negative knowledge like "X is not here" or "deprecated, do not extend" | The article for the area and the per-dir `AGENTS.md` where agents would wrongly edit |

Writing rules:

- Header each article with `Verified against code YYYY-MM-DD`.
- Anchor claims to files, symbols, constants, or source-of-truth docs instead of restating logic.
- Put a one-paragraph picture at the top, then specific seams below.
- Record misconceptions and missing stories explicitly.
- Keep root indexes short. Trim weaker lines when the budget is full.
- Use relative links and verify link depths from the file's actual directory.

## Phase 5: Re-Verify Hop Count

For each fixed area, use a verification subagent when available.

Verification prompt:

```text
Cold-start hop-count verification in <repo-root> (branch <branch>).
Simulate a fresh agent answering: "<domain question(s)>". Start from root
AGENTS.md, follow doc links, grep code only if docs fail. Count every opened
file as one hop.

Report:
1. Hop log.
2. Total hops.
3. Complete/correct from docs alone?
4. Cross-check 2-3 load-bearing claims of the new doc against <relevant source files>.
Flag any inaccuracy. Keep it short.
```

A fix is done when the answer is reachable in <=3 hops from root with zero incorrect claims. Fix flagged inaccuracies immediately. Treat verifier reports as survey output; route any new factual claims back through Phase 3.

## Phase 6: Report

End with a before/after table:

- Domain.
- Hops before.
- Hops after.
- What was added, with file links.
- Claims verified.
- Stale docs or survey confabulations caught.

The stale-doc and confabulation catches build more trust than the hop numbers alone.

## Install Self-Maintained Agent Context

Add or merge a `Self-Maintained Agent Context` section into root `AGENTS.md` so future agents keep context accurate.

Standing rules for all future agents:

1. Treat "why was that so hard to find?" as a discoverability tripwire. If an important invariant or architecture-level fact took real effort to discover, capture the question, answer with `file:symbol` anchors, why it was hard, and either fix the docs now or append a line to `context/CONTEXT-GAPS.md`.
2. Before searching or modifying a file, read every `AGENTS.md` along its path from root to leaf.
3. If a code change makes an `AGENTS.md` or context article stale, update it in the same change.
4. Add an `AGENTS.md` plus `CLAUDE.md` symlink to directories that need local invariants or responsibility splits.
5. When something took many tool calls to find, write the shortcut into the nearest `AGENTS.md`.
6. If a common or important answer takes more than about 4 hops, tell the human and propose an index line, `AGENTS.md` pointer, or article.
7. Index every new article in the root index and the nearest per-directory `AGENTS.md` pointer, so both navigation styles can reach it quickly.
8. Respect budgets with `wc -m`; extract and link rather than overflowing.
9. When a commit body must explain a non-obvious constraint, land that constraint in the relevant `AGENTS.md` or article too, leaving the commit body as a pointer.

Bootstrap `context/CONTEXT-GAPS.md` when it does not exist and the repo uses this context policy:

```markdown
# Context Discoverability Gaps (backlog)

Append a line when you discovered something important the hard way but could not fix
the docs in that change.

Format:
`YYYY-MM-DD | <question an agent would ask> | <answer + file anchors> | why it was hard | suggested home`
```

Wiring checklist for every new doc:

- `CLAUDE.md` symlink exists next to every new `AGENTS.md`.
- The doc is indexed in the root index and/or nearest per-directory `AGENTS.md`.
- Sibling articles that agents need together are cross-linked.
- Relative links work from the file's actual directory.
- Fast check, lint, or format gates run before committing when the repo has them.
