---
name: FORGE
description: Aaron's master developer workflow framework combining structured CC execution patterns, TDD discipline, Git safety, frontend design standards, and memory/context management into one acronym. Load FORGE whenever Aaron says "FORGE", "run FORGE", "use FORGE", or starts any Claude Code session for Simplifii-OS. FORGE pairs with SHARP — SHARP is the thinking layer, FORGE is the execution layer. SHARP decides what to build and why. FORGE decides how to build it without breaking anything. Use both together on every sprint.
---

# FORGE — Aaron's Master Execution Framework

**F** — Flow (structured workflow: brainstorm → plan → execute)
**O** — Origin safety (Git worktrees + clean baseline before any code)
**R** — Red first (TDD: failing test before implementation)
**G** — Grain (frontend design standards: no generic AI layouts)
**E** — Echo (memory + context continuity across sessions)

One acronym. Five execution disciplines. Applied to every CC session.

---

## How to trigger it

Say any of these:
- "FORGE active"
- "run FORGE"
- "FORGE + [task]"
- Add "FORGE active." to the top of any CC prompt

What you get: five execution guardrails that run alongside SHARP's thinking layer.

---

## F — Flow

*Structured workflow: break before building*

Never paste a full sprint into CC as one block. The correct sequence is always:

**Step 1 — Brainstorm (think before planning):**
Before writing a CC prompt, ask here first:
```
SHARP + FORGE: brainstorm [task]
```
This runs SHARP's five lenses on the problem and produces a Flow plan.

**Step 2 — Write plan (tasks, not code):**
Break the sprint into a Trello-style board before any code runs:
```
Sprint task list:
[ ] Read and report (no code)
[ ] Design/propose (no code)
[ ] Implement step A (one file)
[ ] Implement step B (one file)
[ ] Test
[ ] Session end + deploy
```
Each checkbox is one CC approval gate. CC does not move to the next box without an explicit gate word.

**Step 3 — Execute (one box at a time):**
Paste only the current box into CC. Never paste future boxes. This is the P in SHARP (Prompt Discipline) applied to the F in FORGE.

**Why this matters for Simplifii:**
Today's sessions show a clear pattern: when CC has future steps visible in the same prompt, it treats the whole prompt as a plan and executes without gates. Flow prevents this structurally — CC never sees step N+1 until step N is approved.

**Flow command in every CC prompt:**
```
FLOW: Read step. Do step. Stop. Wait for gate word. Never proceed without: "looks good, commit [name]" or "confirmed, proceed with Step N".
```

---

## O — Origin Safety

*Git safety before any code*

**The baseline rule:**
Before CC touches any file, confirm the working tree is clean:
```bash
git status
```
If anything is uncommitted, commit or stash it before starting. Never let CC write into a dirty tree — if something breaks, the rollback point is ambiguous.

**The restore rule (Simplifii-specific):**
`CanvasScreen.jsx` is fragile. Before any session that touches the canvas:
```bash
git tag pre-session-backup-$(date +%Y%m%d)
```
This creates a named restore point. If the canvas breaks mid-session:
```bash
git checkout pre-session-backup-[date] -- src/frontend/CanvasScreen.jsx
```
Then re-add only the specific change that was approved.

**The branch rule:**
For sprints that touch three or more files, create a feature branch:
```bash
git checkout -b feat/sprint-5-task-guidance
```
This means if the sprint goes wrong, `git checkout main` restores everything. No individual file restores needed.

**The worktree pattern (for parallel work):**
When running two CC sessions simultaneously (e.g., bug fixes while a sprint runs):
```bash
git worktree add ../simplifii-bugfix main
```
Each worktree is isolated. Changes in one do not affect the other.

**Origin safety checklist before every CC session:**
- [ ] `git status` — working tree clean?
- [ ] Tagged backup created for this session?
- [ ] Model header shows `Opus 4.6 · Claude Max`?
- [ ] BACKLOG.md read for P0 bugs that interrupt this sprint?

---

## R — Red First

*TDD: failing test before implementation*

**The rule:**
For every new feature or bug fix in Simplifii-OS, the test is written before the implementation. CC is instructed to:

1. Write a failing Playwright test that describes the expected behaviour
2. Show the test running and failing
3. Then implement the code that makes it pass
4. Never skip directly to implementation

**Applied to Simplifii:**
The current regression suite has 12 tests. Every new feature should add at least one test. Current coverage gaps (add these as tests before building the feature):

```
// Missing tests to add:
- TaskPhaseBar renders when taskSequence exists
- TaskPhaseBar absent when taskSequence absent
- Authenticity Report PDF download triggers correctly
- Node extraction returns correct nodeTypes for brief
- Node extraction returns correct nodeTypes for rubric
- stripMarkdown removes bold from editor insertion
- AURA context contains [TASK DESCRIPTION] label
```

**CC instruction for Red First:**
Add to every sprint prompt that adds a new feature:
```
Before implementing, write a failing Playwright test in tests/regression.spec.js that describes the expected behaviour. Show the test running and failing. Then implement. Then show the test passing.
```

**When Red First does not apply:**
- Pure bug fixes where the bug is already covered by an existing failing test
- Documentation-only changes
- Config or environment variable changes

**Why this compounds:**
Every test added is a permanent sentinel. When the codebase has 50 tests instead of 12, refactoring becomes safe. CC can make broader changes because the test suite catches regressions automatically.

---

## G — Grain

*Frontend design: no generic AI layouts*

**The rule:**
Every UI component built for Simplifii must feel native to the Obsidian aesthetic. Generic AI-generated UI (rounded cards, blue buttons, Material Design defaults) is not acceptable.

**The Simplifii design tokens (apply to every component):**
```javascript
// From tokens.js — reference always
SURFACE_BASE       // page background
SURFACE_CARD       // card background
SURFACE_RAISED     // elevated elements
ACCENT_PULSE       // primary green accent
ACCENT_GLOW        // glow state
ACCENT_GLASS       // glass effect
ACCENT_BORDER      // accent borders
TEXT_PRIMARY       // main text
TEXT_MUTED         // secondary text
TEXT_FAINT         // tertiary text
BORDER_RADIUS      // standard radius
FONT_DISPLAY       // display headings
FONT_BODY          // body text
FONT_SYSTEM        // system/UI text
FOCUS_RING         // accessibility focus
```

**The five Grain rules:**
1. No hardcoded hex values in component files (exception: jspdf which cannot use CSS variables)
2. No inline `color:`, `background:`, or `fontSize:` that use literal values — always tokens
3. Every interactive element must have `onFocus`/`onBlur` with `FOCUS_RING` for accessibility
4. Minimum touch target: `minHeight: 44` on all buttons and interactive controls
5. No em-dashes anywhere. Colons or parentheses instead. Pre-commit hook enforces this.

**Typography rules:**
- Australian English everywhere (organise, colour, analyse, recognise)
- Calm tone: direct, literal, ADHD-friendly — no "superpower" framing, no toxic positivity
- Never use `!` in UI text unless it is genuinely urgent
- Section labels in uppercase with letter-spacing (e.g. `AI ASSISTANCE`, `PHASE PROGRESSION`)

**CC Grain instruction:**
Add to every CC prompt that touches UI:
```
GRAIN: Use only design tokens from tokens.js. No hardcoded hex. No inline colour values. Every button minHeight: 44. Focus rings on all interactive elements. Australian English. No em-dashes.
```

---

## E — Echo

*Memory and context continuity across sessions*

**The problem it solves:**
Every CC session starts fresh. Rules set in session 1 are invisible in session 10. This is why the same mistakes recur: no markdown in output, one deploy per session, read before editing — these rules are in `CLAUDE.md` and `SESSION_LOG.md` but CC must be told to read them every session.

**The Echo protocol:**
Every CC prompt must include this context block:
```
ECHO: Read docs/SESSION_LOG.md and docs/BACKLOG.md before starting. 
The most recent session log entry contains the constraint for this session 
and any P0 bugs that interrupt sprint work.
```

**The session handoff rule:**
At the end of every CC session, `docs/SESSION_LOG.md` must include:
- Next session constraint (exact, one task)
- Any P0 bugs found this session
- Current production bundle hash
- Which loops are now closed vs still open

This means next session's CC reads the handoff and knows exactly where it is without you re-explaining context.

**The knowledge graph rule:**
Critical decisions made in CC sessions get logged to `docs/BACKLOG.md` with a decision rationale, not just a task description. Example:

```markdown
## Decision: chunking strategy for DocumentNodeService
Date: 2026-05-17
Decision: Split at paragraph breaks, rubrics get 6000 char chunks vs 4000 for briefs.
Rationale: Rubric tables linearise badly via pdfjs-dist. Larger chunks preserve table 
structure. YN1/YN2 JSON arrays merge across chunks via JSON.parse concat.
Alternative considered: Fixed 8000 char cap — rejected because brief/outline 
documents are well within 4000 chars and larger cap wastes context window.
```

**The cross-session compounding rule:**
Every friction point in a session becomes an update to CLAUDE.md or a skill. Not just BACKLOG.md. The friction point disappears for future sessions.

Examples from today:
- CC bypassed approval gates → added gate word rule to FORGE (P section of SHARP)
- Sonnet API billing mid-session → added model check rule to FORGE (O section)
- Same recurring markdown bugs → added `stripMarkdown` pattern to SHARP (H section)

**CC Echo instruction:**
```
ECHO: Read docs/SESSION_LOG.md first. Read docs/BACKLOG.md for P0 bugs. 
Do not ask me to re-explain context that is in these files.
```

---

## FORGE + SHARP together

**SHARP** is the thinking layer. Applied before and during decisions.
**FORGE** is the execution layer. Applied during and after building.

| Question | Skill | Letter |
|---|---|---|
| Should this sprint exist? | SHARP | S |
| Is AURA inventing criteria? | SHARP | H |
| What breaks downstream? | SHARP | A |
| What is the one-sentence root cause? | SHARP | R |
| Does my CC prompt have gate words? | SHARP | P |
| Have I broken the work into Flow steps? | FORGE | F |
| Is my git tree clean before starting? | FORGE | O |
| Did I write the failing test first? | FORGE | R |
| Am I using design tokens not raw hex? | FORGE | G |
| Did I write the session handoff? | FORGE | E |

---

## The combined CC prompt header

Every CC prompt starts with these two lines:

```
SHARP active. FORGE active.
```

This loads both frameworks. CC knows to:
- Apply five SE lenses before proposing any change (SHARP: A+R)
- Check which loop this closes (SHARP: S)
- Verify before approving (SHARP: H)
- Use gate words only (SHARP: P + FORGE: F)
- Confirm git is clean (FORGE: O)
- Write test before implementation (FORGE: R)
- Use design tokens (FORGE: G)
- Read session log first (FORGE: E)

---

## Full combined CC prompt template (SHARP + FORGE edition)

```
SHARP active. FORGE active.

SESSION CONSTRAINT: [one task, named precisely]
Log any other issues to docs/BACKLOG.md. Do not fix outside constraint.

ECHO: Read docs/SESSION_LOG.md and docs/BACKLOG.md first.

CONTEXT:
App: Simplifii-OS-Main
Stack: React + CRA + Supabase + Vercel
Local: ~/Simplifii-OS_Master

FLOW plan for this session:
[ ] Step 1: Read and report (no code, wait for confirmation)
[ ] Step 2: Propose design (no code, wait for approval)
[ ] Step 3: Implement [file A]
[ ] Step 4: Implement [file B]
[ ] Step 5: Test (smoke + regression)
[ ] Step 6: Session end + deploy

ORIGIN: Confirm git status is clean before any edit. Tag backup if touching CanvasScreen.jsx.

---

STEP 1: READ AND REPORT
Read [exact files]. Report [exact questions].
Do not write anything. Wait for: "confirmed, proceed with Step 2"

---

STEP 2: PROPOSE
Show [exact output]. Wait for: "confirmed, proceed with Step 3"

---

STEP 3: IMPLEMENT
RED FIRST: Write failing test before implementation if this is a new feature.
GRAIN: Use design tokens only. No hardcoded hex. Australian English.
Read [file] immediately before editing.
Show git diff --staged.
Wait for: "looks good, commit [name]"

---

STEP 4: TEST
Three smoke questions. npm run test:regression. Report 12/12 or list failures.

---

STEP 5: SESSION END
Write docs/SESSION_LOG.md with: what changed, commit SHAs, next session constraint, loop status.
git push origin main. npx vercel --prod (one deploy only).

RULES:
- Read every file immediately before editing
- Show git diff --staged before every commit
- Gate words only: "looks good, commit [name]" / "confirmed, proceed with Step N"
- Never proceed past a gate without the exact gate phrase
- One deploy at the end after all tests pass
- CanvasScreen.jsx: tag backup before touching, restore before patch if smoke fails
- Log all out-of-scope issues to BACKLOG.md, never fix mid-session
```

---

## Installing FORGE in CC terminal

```bash
mkdir -p "/Users/adonis666/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/6c49d35a-f83c-46dd-a38d-02fd0248270c/5175e35c-4c66-40da-9e6a-e39f8dc42cb2/skills/FORGE" && cp ~/Downloads/FORGE-SKILL.md "/Users/adonis666/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/6c49d35a-f83c-46dd-a38d-02fd0248270c/5175e35c-4c66-40da-9e6a-e39f8dc42cb2/skills/FORGE/SKILL.md" && echo "FORGE installed"
```

---

## Summary

Two words at the top of every CC prompt from now on:

```
SHARP active. FORGE active.
```

**SHARP** = think correctly (Systems, Hallucination guard, Architecture, Root cause, Prompt discipline)

**FORGE** = execute correctly (Flow, Origin safety, Red first, Grain, Echo)

Everything else follows from those two words.
