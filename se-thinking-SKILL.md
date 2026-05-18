---
name: se-thinking
description: Senior engineering thinking + CC prompt generation for Simplifii-OS. ALWAYS load this skill when Aaron says "SE thinking", "SE skill", "use the SE skill", "think like a senior engineer", "what's the architecture here", "map this out", or asks any question about how to build, structure, or engineer a feature in Simplifii-OS. Also load when Aaron is about to write a CC prompt, scope a sprint, make an architectural decision, or asks "how do we do this" in a technical context. This skill does two things: (1) applies five senior engineering lenses to the problem before answering, and (2) produces a full SE-level CC prompt if the answer requires code. Never just answer technically — always apply the lenses first. Never just give a CC prompt — always explain the architecture first so Aaron understands what CC is going to build and why.
---

# SE Thinking Skill — Simplifii-OS

## What This Skill Does

Two outputs, every time:

1. **SE Analysis** — five lenses applied to the problem before any code is written
2. **CC Prompt** — full SE-level Claude Code prompt if the answer requires engineering work

Aaron triggers this with: "SE thinking", "SE skill", "use the SE skill", "think like a senior engineer", or any technical "how do we..." question about Simplifii-OS.

---

## The Five SE Lenses

Apply all five before answering. Do not skip any. Do not batch them.

### Lens 1: Musk — Should This Exist?
Ask: "Is this the right problem to solve, or are we building around a flaw we should delete?"

Questions to answer:
- What breaks if we do nothing?
- Can we solve this by removing something rather than adding something?
- Is this a symptom of a deeper architectural problem?
- Would a first-principles redesign make this irrelevant?

### Lens 2: Root Cause — What Is Actually Broken?
Ask: "What is the exact cause, stated in one sentence?"

Rules:
- Name the root cause before proposing a fix
- If the root cause cannot be named, say so explicitly — do not guess
- Distinguish between the symptom (what Aaron sees) and the cause (what the code is doing)
- Identify the exact file and line range if possible

### Lens 3: Minimal Fix — What Is The Smallest Change?
Ask: "What is the least code that solves this correctly?"

Rules:
- Never add a new module when an existing one can be extended
- Never extend an existing module when a config change suffices
- Never patch a symptom — fix the root cause
- State what NOT to touch, explicitly

### Lens 4: Downstream Impact — What Else Does This Break?
Ask: "If we make this change, what are the three most likely things that break?"

Check against:
- ProjectContext.js (state changes ripple everywhere)
- CanvasScreen.jsx (fragile — any change requires smoke test)
- AURA prompt construction (context shape changes break output quality)
- Supabase schema (any field rename breaks hydration)
- Playwright tests (any route change breaks test selectors)

### Lens 5: Prevention — How Do We Stop This Happening Again?
Ask: "What rule, test, or architectural constraint prevents this class of bug from recurring?"

Output: one of these four:
- A Playwright test that would have caught it
- A BACKLOG.md entry with the structural fix
- A rule to add to CLAUDE.md
- A refactor that makes the bug structurally impossible

---

## SE-Level CC Prompt Structure

Every CC prompt produced by this skill must follow this exact structure. No exceptions.

```
SESSION CONSTRAINT: [one task, named precisely]

If you identify other issues during reading, log to docs/BACKLOG.md. Do not fix outside this constraint.

CONTEXT:
App: Simplifii-OS-Main
Stack: React + Create React App + Supabase + Vercel
Local path: ~/Simplifii-OS_Master
Session log: docs/SESSION_LOG.md (read this first)
Backlog: docs/BACKLOG.md
Backup tag: pre-port-backup-20260517

---

STEP 1: READ BEFORE TOUCHING ANYTHING

Read these files in full before writing a single line:
[list exact files and line ranges]

Report back:
[list exact questions CC must answer before proceeding]

Do not write anything yet. Wait for my confirmation.

---

STEP 2: [DESIGN / SCHEMA / PROPOSAL]

[describe what CC should propose, in what format, before writing code]

Show me [exact output format]. Wait for my approval before writing any code.

---

STEP 3: IMPLEMENT

After I approve [the proposal]:

1. Read [file] immediately before editing it
2. [exact change, one thing at a time]
3. [next change]

Show me git diff --staged before committing.
Wait for: "looks good, commit [name]"
Commit message: "[type(scope): description — filename]"

---

STEP 4: TEST

[specific test commands and assertions]

All three smoke test questions must pass:
1. Does the canvas open in incognito?
2. Can a PDF be uploaded?
3. Does AURA respond in chat?

---

STEP 5: SESSION END

Write docs/SESSION_LOG.md entry:
- [what changed]
- [commit SHA]
- Next session constraint: [exact next task]

git push origin main
npx vercel --prod (one deploy only, after tests pass)

---

RULES FOR THIS SESSION:
- Read every file immediately before editing it
- Show git diff --staged before every commit
- Wait for explicit "looks good, commit [name]" before committing
- Only touch [named files]
- Do not touch [named files to avoid]
- Smoke test before deploy
- One deploy at the end
- Log all other bugs to docs/BACKLOG.md, do not fix outside constraint
```

---

## Simplifii-OS Architectural Constants

These facts are always true. Reference them in every SE analysis.

**Stack:**
- React 18 + Create React App (NOT Vite — env prefix is REACT_APP_ not VITE_)
- Supabase (Postgres + auth)
- Vercel (hosting + serverless API routes under /api/)
- Playwright (tests/regression.spec.js, 12 tests, npm run test:regression)
- Auth bypass: REACT_APP_TEST_MODE=true in .env.test (never in production)

**Fragile surfaces (extra caution required):**
- CanvasScreen.jsx — always restore before patch; smoke test after every change
- ProjectContext.js — state changes ripple to every component
- api/_aura-prompt.js — any change alters AURA output quality for every user
- Supabase hydration block (ProjectContext.js ~lines 258-286) — field name mismatches silently break context

**Non-negotiable rules:**
- No markdown in AURA chat output (rule enforced in api/_aura-prompt.js, commit 81142494)
- No [TOOL:tag] leakage into canvas editor (B5, open bug)
- One deploy per session, at the end, after tests pass
- Show git diff --staged before every commit
- Wait for explicit approval before committing

**Document node schema (extraction architecture):**
Every uploaded document is parsed into typed, addressable nodes:
- Z: Course Outline → Z1 (metadata), Z2 (learning outcomes), Z3 (schedule), Z4 (assessment overview), Z5 (policies)
- XN: Assessment N Brief → XN1 (task description), XN2 (format requirements), XN3 (due date), XN4 (mapped LOs), XN5 (hidden curriculum)
- YN: Rubric for Assessment N → YN1 (criteria + weightings), YN2 (grade band descriptors), YN3 (scale detected), YN4 (hidden curriculum)

AURA receives only the nodes relevant to the active assessment. Never the full document set.

**Active sprint state (update this mentally each session):**
- Assessment Scaffolder: done (commit 27e13a1d)
- Rubric Simplifier: in progress (commit pending rubric approval)
- Exam Paper structured output: next sprint
- Assessment context scoping: queued
- Document node tree architecture: designed, not yet built

---

## Mid-Session Prompt Patterns

These short patterns replace full SE prompts when CC is mid-task.

**Schema approval:**
```
Schema approved. Proceed with Steps 3 and 4 together.
[Any additional requirements].
Show me git diff --staged for both files together before committing. Wait for "looks good, commit [name]" before committing either file.
```

**Commit approval:**
```
looks good, commit [name]
Then immediately proceed to Step [N] without stopping.
```

**Diagnosis confirmation:**
```
Confirmed. Proceed with [step].
[Any constraint or additional requirement].
Wait for my approval before writing any code.
```

**Bug found mid-constraint:**
```
Log that to docs/BACKLOG.md as [ID]: [description]. Do not fix in this session. Continue with the current constraint.
```

**Session end trigger:**
```
Proceed to Step [N] (session end). Write SESSION_LOG.md, push, and one deploy. Report when done.
```

---

## When to Use Full SE Prompt vs Short Pattern

| Situation | Use |
|---|---|
| Starting a new session or new constraint | Full SE prompt |
| Approving a schema or design proposal | Short schema approval |
| Approving a staged diff | Short commit approval |
| Confirming CC's diagnosis before it writes code | Short diagnosis confirmation |
| Bug found that is out of scope | Short bug log pattern |
| CC finished and needs to wrap up | Short session end trigger |
| Something broke and needs God Mode | Load god-mode skill instead |

---

## Document Extraction Architecture (for SE analysis on ingestion questions)

When Aaron asks about parsing, extraction levels, document nodes, or "how does the AI know what part of the document to use," apply this framework before answering.

**The three extraction levels:**

**Level 1 — Document classification** (already exists, partially)
What type is this document? Brief / rubric / syllabus / reading / exam / outline.
Output: `documentType` field on the course record.

**Level 2 — Section parsing** (partially exists, needs extension)
What are the named sections within this document?
Output: typed node array with `nodeType`, `nodeId`, `content`, `confidence`, `sourcePageRange`.
Example: `{ nodeType: "XA1", content: "Write a 2000-word critical analysis...", confidence: 0.94, sourcePageRange: [1,2] }`

**Level 3 — Semantic extraction** (not yet built)
What does each section mean in terms of what the student must do?
Output: derived fields — `hiddenCurriculum`, `implicitRequirements`, `mappedLearningOutcomes`, `conflictFlags`.

**Cross-document linking rules:**
- XA4 (LOs for this task) must reference Z2 node IDs, not raw text
- YN1 criteria weightings must sum to 100% — flag if they do not
- Z4 assessment dates must match XN3 due dates — flag conflicts
- If a rubric exists for an assessment, YN nodes must be linked to the XN node by `assessmentId`

**AURA context assembly rule:**
AURA receives a `contextPacket` object, not raw document text:
```json
{
  "activeAssessment": { "id": "XA", "title": "Essay", "dueDate": "2026-06-01" },
  "taskDescription": "[XA1 content]",
  "formatRequirements": "[XA2 content]",
  "hiddenCurriculum": "[XA5 content]",
  "rubricCriteria": "[YA1 content]",
  "gradeBands": "[YA2 content]",
  "courseSchedule": "[Z3 content — current week only]",
  "learningOutcomes": "[Z2 nodes referenced by XA4]"
}
```

This packet is assembled fresh on every AURA prompt construction. It never contains nodes from other assessments.
