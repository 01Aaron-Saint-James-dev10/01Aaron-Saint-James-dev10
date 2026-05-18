---
name: SHARP
description: Aaron's master thinking framework combining five skills into one acronym. Load SHARP whenever Aaron says "SHARP", "run SHARP", "use SHARP", or asks for deep analysis on any build, sprint, architecture, prompt, or strategic decision for Simplifii-OS. SHARP replaces using se-thinking, hallucination-reduction, ai-systems-thinking, and prompt-habit-audit separately. It runs all five dimensions simultaneously as one cohesive framework. Also load for every CC prompt written, every sprint scoped, every diff reviewed, and every architectural decision made.
---

# SHARP — Aaron's Master Build Framework

**S** — Systems (ai-systems-thinking)
**H** — Hallucination Guard (hallucination-reduction)
**A** — Architecture (se-thinking / five SE lenses)
**R** — Root Cause (se-thinking / minimal fix)
**P** — Prompt Discipline (prompt-habit-audit)

One acronym. Five dimensions. Applied simultaneously to every decision.

---

## How to trigger it

Say any of these:
- "run SHARP on this"
- "SHARP analysis"
- "use SHARP"
- "SHARP + [topic]"

What you get back every time: five labelled sections, one per letter, then a single recommended action at the end.

---

## S — Systems

*From: ai-systems-thinking*

Ask before building anything:

1. What outcome am I actually accumulating toward? (Not what activity am I doing.)
2. What is the real constraint right now — not the annoying thing, the ceiling?
3. Is the structure creating this problem, or is effort the missing thing?
4. When this breaks, does the failure feed back into the system? Or disappear?
5. What could I stop doing instead of adding?

**Applied to Simplifii builds:**
- Before every sprint: what loop does this close? (Loop 1-5 from the systems map)
- Before every new feature: does this compound, or does it reset next session?
- After every bug: does the fix update the system, or just patch the symptom?

**Red flags:**
- Adding a new tool/feature when existing ones are not fully working
- Building features before getting real users to validate them
- Fixing bugs without updating the rule that would prevent the same class next time

---

## H — Hallucination Guard

*From: hallucination-reduction*

Applied to every AI-generated output in the build — AURA responses, node extraction, CC code proposals, SE analysis itself.

**For AURA outputs (apply to api/_aura-prompt.js):**
Quote-First instruction embedded in every AURA prompt:
```
Before giving any advice about this assessment, extract the exact phrases from the rubric that are relevant to what the student is asking. Reference these phrases in your answer. If the rubric does not contain enough information to answer confidently, say so. Do not fill in gaps with assumptions.
```

**For CC code proposals:**
Apply self-verification before approving any diff:
- Does this change match exactly what was proposed in the design step?
- Does CC reference the actual file line numbers it changed?
- If CC says "existing behaviour unchanged" — is that verifiable from the diff?

**For SE analysis:**
Apply confidence rating to every architectural claim:
- High: confirmed in the codebase right now
- Medium: reasonable inference from what is known
- Low: assumption — flag and verify before building on it

**For node extraction:**
Every YN1/YN2 node must pass the consistency test:
- If the same rubric PDF is uploaded twice, do the extracted criteria match exactly?
- If criteria vary between uploads, the extraction is hallucinating structure.

**Warning signs in CC output:**
- Overly specific line numbers that were not in the file it read
- "This is already handled" without quoting the line that handles it
- Confident answers about file contents it was not asked to read

---

## A — Architecture

*From: se-thinking — Lens 1 (Musk) and Lens 4 (Downstream)*

Ask before every sprint:

**Lens 1 — Should this exist?**
- What breaks if we do nothing?
- Can this be solved by deleting something rather than adding something?
- Is this a symptom of a deeper architectural problem?

**Lens 4 — What else does this break?**
Always check against the five fragile surfaces:
- `ProjectContext.js` — state changes ripple everywhere
- `CanvasScreen.jsx` — restore before patch, smoke test after every change
- `api/_aura-prompt.js` — any change alters AURA output for every student
- Supabase hydration block (~lines 258-286) — field name mismatches silently break context
- Playwright tests — route changes break test selectors

**The five system loops (always check which loop a sprint closes):**
- Loop 1: Ingestion → Nodes → AURA → Student
- Loop 2: Student Actions → HistoryOfThought → Authenticity Report → Trust
- Loop 3: Corpus → Knowledge Graph → Future Students
- Loop 4: Educator Login → Institutional Trust → Revenue
- Loop 5: Voice → Cognitive Load Reduction → Retention

A sprint that does not close any loop adds complexity without closing value.

---

## R — Root Cause

*From: se-thinking — Lens 2 (Root Cause) and Lens 3 (Minimal Fix)*

**Lens 2 — Name the exact cause:**
- One sentence. Specific file and line if possible.
- Distinguish symptom (what you see) from cause (what the code does).
- If the root cause cannot be named in one sentence, do not build yet.

**Lens 3 — Minimum viable fix:**
- Never add a new module when an existing one can be extended.
- Never extend when a config change suffices.
- State explicitly what NOT to touch.
- The best fix is almost always the smallest one.

**Lens 5 — Prevention:**
Every fix must produce one of:
- A Playwright test that would have caught it
- A BACKLOG.md entry with the structural fix
- A rule added to CLAUDE.md
- A refactor that makes the bug structurally impossible

**Applied to recurring bugs:**
If the same bug appears three or more times, it is P0 regardless of what else is in progress. Stop. Fix. Prevent. Resume.

Current recurring pattern to watch: markdown leaking into student-facing surfaces. `stripMarkdown` is the utility. Every new AI output surface must import it before display.

---

## P — Prompt Discipline

*From: prompt-habit-audit*

The six patterns that most often degrade CC session quality, in order of frequency observed today:

| Pattern | Trigger | Fix |
|---|---|---|
| Gate bypass | CC has future steps in the same prompt | Put each step in a separate paste after the gate word |
| Vague approval | "looks good" without specifying what was reviewed | State exactly what you verified before approving |
| Context drift | Long sessions where early rules fade | Fresh terminal every session, rules in every prompt |
| Model switching | Not checking CC header before pasting | Check `Opus 4.6 · Claude Max` before every paste |
| Scope creep mid-session | Adding new ideas while CC is building | Log to BACKLOG instantly, say "logged", do not pursue mid-session |
| Fatigue prompting | Late-session prompts with degraded instructions | Stop the session. CC degrades with you. |

**The gate word rule:**
Every CC prompt must have exactly one gate word per approval step:
- `looks good, commit [name]` — the only phrase that triggers a commit
- `confirmed, proceed with Step N` — the only phrase that moves forward
- `log to BACKLOG as [ID]` — the only phrase that defers without pursuing

If CC commits without seeing one of these phrases, it bypassed a gate. Flag it. Do not accept the commit silently.

**The fresh terminal rule:**
Close CC terminal at the end of every session. Open fresh at the start of the next. Never continue a session that has auto-compacted — the rules fade.

**The model check rule:**
Before every paste, confirm header shows `Opus 4.6 · Claude Max`. If it shows `Sonnet 4.6 · API Usage Billing`, stop. Every token costs money and uses a weaker model.

---

## SHARP in practice — the five-second check

Before every CC paste, run SHARP in your head:

**S** — Does this sprint close a loop or just add complexity?
**H** — Am I about to approve something I haven't verified exists in the file?
**A** — Have I checked the five fragile surfaces?
**R** — Can I name the root cause in one sentence?
**P** — Does my prompt have clear gate words and nothing past the first gate?

If any answer is no — fix it before pasting.

---

## SHARP acronym for CC prompt header

Add this line to the top of every CC prompt:

```
SHARP active: Systems check (loop closure), Hallucination guard (verify before approve), Architecture check (fragile surfaces), Root cause (one sentence before fixing), Prompt discipline (gate words only, no future steps past each gate).
```

This trains CC to apply the framework without you having to say it every time.

---

## Combined CC prompt template (SHARP edition)

```
SHARP active: Systems (loop closure check), Hallucination guard (verify before approve), Architecture (fragile surfaces), Root cause (one sentence), Prompt discipline (gate words only).

SESSION CONSTRAINT: [one task, named precisely]
Log any other issues to docs/BACKLOG.md. Do not fix outside constraint.

CONTEXT:
App: Simplifii-OS-Main
Stack: React + CRA + Supabase + Vercel
Local: ~/Simplifii-OS_Master
Read docs/SESSION_LOG.md first.

---

STEP 1: READ AND REPORT
Read [exact files and line ranges].
Report: [exact questions].
Do not write anything. Wait for my confirmation.

---

STEP 2: PROPOSE
Show [exact output format] before writing code.
Wait for my approval.

---

STEP 3: IMPLEMENT
Read [file] immediately before editing.
Show git diff --staged.
Wait for: "looks good, commit [name]"

---

STEP 4: TEST
Three smoke test questions.
npm run test:regression.

---

STEP 5: SESSION END
docs/SESSION_LOG.md. git push. One deploy.

RULES:
- Read before every edit
- Show diff before every commit
- Gate words only: "looks good, commit [name]" / "confirmed, proceed"
- One deploy at end
- CanvasScreen.jsx: restore before patch, smoke test after
```

---

## Installing SHARP in CC terminal

Copy this skill file to your CC skills directory and CC will load it automatically when you say "SHARP":

```bash
mkdir -p "/Users/adonis666/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/6c49d35a-f83c-46dd-a38d-02fd0248270c/5175e35c-4c66-40da-9e6a-e39f8dc42cb2/skills/SHARP" && cp ~/Downloads/SHARP-SKILL.md "/Users/adonis666/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/6c49d35a-f83c-46dd-a38d-02fd0248270c/5175e35c-4c66-40da-9e6a-e39f8dc42cb2/skills/SHARP/SKILL.md" && echo "SHARP installed"
```

---

## What SHARP replaces

You no longer need to say:
- "use the SE skill" — covered by A + R
- "systems thinking" — covered by S
- "check for hallucinations" — covered by H
- "prompt discipline" — covered by P

One word. Five dimensions. Every decision.
