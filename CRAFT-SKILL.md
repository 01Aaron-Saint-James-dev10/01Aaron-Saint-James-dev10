---
name: CRAFT
description: Aaron's third master framework covering the non-engineering dimensions of Simplifii-OS. Load CRAFT whenever Aaron asks about accessibility, UDL 3.0, education design, SEO, marketing, business model, security vulnerabilities, or persistent task planning across sessions. CRAFT pairs with SHARP (thinking) and FORGE (execution) to complete the three-framework stack. SHARP decides what to build. FORGE decides how to build it. CRAFT decides whether it works for the actual humans who will use it. Trigger phrases: "CRAFT active", "run CRAFT", "CRAFT check", "accessibility check", "UDL check", "SEO check", "security check", "pedagogy check".
---

# CRAFT — Aaron's Human-Centred Build Framework

**C** — Content + SEO (marketing, launch, discoverability)
**R** — Research memory (persistent planning across CC sessions)
**A** — Accessibility + UDL 3.0 (WCAG 2.2 AA, neurodivergent design)
**F** — Fortify (security vulnerability patterns for Simplifii)
**T** — Teaching + pedagogy (education science, UDL principles)

Three frameworks. One stack.

**SHARP** = think correctly
**FORGE** = execute correctly
**CRAFT** = serve humans correctly

---

## How to trigger it

Say any of these:
- "CRAFT active"
- "CRAFT check on this"
- "run CRAFT"
- Add "CRAFT active." to the top of any session prompt

Applied alongside SHARP and FORGE:
```
SHARP active. FORGE active. CRAFT active.
```

---

## C — Content + SEO

*Marketing, discoverability, launch readiness*

Simplifii-OS is a product with a public URL. It needs to be found by the students who need it. The product is world-class for neurodivergent students — but only if they can find it.

**The four SEO surfaces for Simplifii:**

1. **The AI use page** (`/ai-use`) — already live. This is a high-intent page. Students searching "how does AI study tool work" or "is AI cheating" will land here. It needs semantic HTML, meaningful headings, and clear meta description.

2. **The landing page** (LandingPage.js) — currently passphrase auth Gemini drift. Needs real SEO: title tag, meta description, Open Graph tags, structured data (SoftwareApplication schema), canonical URL.

3. **The marketing positioning** — Simplifii competes with Notion AI, Grammarly, and EduAide. None of them serve neurodivergent students specifically. This is the differentiator that drives organic search: "study tool for ADHD", "assignment help dyslexia", "AI for neurodivergent students".

4. **The Authenticity Report** — this is a shareable PDF that students submit with their work. Every PDF that leaves Simplifii is a marketing asset. It should include the Simplifii URL and a one-line description.

**SEO checklist for every page:**
```
[ ] <title> tag: unique, under 60 chars, includes primary keyword
[ ] Meta description: unique, under 160 chars, includes call to action
[ ] H1: one per page, matches search intent
[ ] Open Graph: og:title, og:description, og:image (1200x630)
[ ] Canonical URL set
[ ] Structured data: SoftwareApplication or WebApplication schema
[ ] Core Web Vitals: LCP under 2.5s, INP under 200ms, CLS under 0.1
[ ] Mobile-first: all interactive elements min 44px touch target
```

**Content positioning rules:**
- Never use "superpower" framing — it is condescending to neurodivergent students
- Lead with the problem, not the product: "Assessment briefs are designed for neurotypical students. Simplifii decodes them for yours."
- Social proof framing: "Built by someone with ADHD and dyslexia, for students who think the same way."
- Australian English throughout — the product is AU-first

**Launch readiness checklist:**
```
[ ] Google Search Console verified
[ ] Sitemap.xml generated and submitted
[ ] robots.txt present and not blocking key pages
[ ] Analytics installed (privacy-respecting: no fingerprinting)
[ ] Privacy policy and AI use page linked in footer
[ ] Email capture for waitlist (not yet built)
[ ] Social proof: beta student testimonials (3 minimum)
```

---

## R — Research Memory

*Persistent planning: the planning-with-files pattern*

The single most starred Claude skill in the ecosystem (13,000+ stars) solves the problem you experience every CC session: CC forgets the plan halfway through a complex task.

**The three persistent files pattern:**

Every sprint that takes more than five CC tool calls creates three files at the start:

```bash
docs/sprints/sprint-[N]-plan.md      # The plan
docs/sprints/sprint-[N]-findings.md  # What was discovered during reads
docs/sprints/sprint-[N]-progress.md  # Checkbox progress tracker
```

**CC instruction for Research Memory:**
Add to every long sprint prompt:
```
RESEARCH MEMORY:
Before starting: create docs/sprints/sprint-[N]-plan.md with:
- Session constraint
- Files to read
- Decisions pending
- Approval gates (list each gate word)

After every 2 file reads: update docs/sprints/sprint-[N]-findings.md

After every commit: check off that step in docs/sprints/sprint-[N]-progress.md

Before any major decision: re-read the plan file. Do not rely on context window memory.
```

**The 2-action rule (from planning-with-files):**
After every two read or search operations, CC saves key findings to disk before continuing. This means if the context window shifts, the findings are not lost.

**The 3-strike error protocol:**
If something fails:
1. Diagnose the exact error
2. Try one alternative approach
3. If that fails, rethink the approach
4. If three attempts fail, escalate to Aaron — do not keep trying variations

**Session recovery:**
If CC auto-compacts or a new session starts mid-sprint, add to the session prompt:
```
Read docs/sprints/sprint-[N]-progress.md and docs/sprints/sprint-[N]-findings.md
before starting. Pick up from the last unchecked item.
```

**Why this matters for Simplifii specifically:**
Sprint 5 (Task Guidance Engine) had CC write `TaskPhaseBar.jsx` and `TaskSequenceManager.js` without an approval gate because the prompt had future steps visible. With Research Memory, the plan file would have shown CC exactly where it was and which gate was next — preventing the bypass.

---

## A — Accessibility + UDL 3.0

*The non-negotiable layer. Accessibility is the floor, not the ceiling.*

Simplifii-OS serves neurodivergent students as its primary audience. This is not a marketing position — it is the product identity. Accessibility failures are product failures.

**WCAG 2.2 AA — the minimum standard (enforced, not aspirational):**

Every UI component must pass:

```
Perceivable:
[ ] Images have alt text
[ ] Colour is never the sole signifier of meaning — always pair with label or icon
[ ] Text contrast ratio: 4.5:1 for normal text, 3:1 for large text
[ ] No content flashes more than 3 times per second (seizure risk)
[ ] Text resizes to 200% without horizontal scroll
[ ] BionicText mode available
[ ] OpenDyslexic font option available

Operable:
[ ] All functionality available via keyboard (no mouse required anywhere)
[ ] Focus is always visible (FOCUS_RING token on every interactive element)
[ ] No keyboard traps
[ ] Skip navigation link present
[ ] Touch targets minimum 44x44px
[ ] No time limits without extension option
[ ] Reduced motion preference respected (prefers-reduced-motion)

Understandable:
[ ] Page language declared (lang="en-AU")
[ ] Error messages describe what went wrong and how to fix it
[ ] Labels are associated with their inputs (htmlFor / aria-labelledby)
[ ] Instructions do not rely on shape, position, or colour alone

Robust:
[ ] Works with screen readers (VoiceOver, NVDA, JAWS)
[ ] ARIA roles correct and not misused
[ ] All interactive elements have accessible names
```

**UDL 3.0 — Universal Design for Learning implementation rules:**

UDL 3.0 has three principles. Each maps to a surface in Simplifii-OS.

**Principle 1: Multiple Means of Representation**
Students must receive information in more than one format.

In Simplifii this means:
- Every AI text response has an optional audio version (AURA voice)
- Every rubric criterion has a plain English translation (LiteralMode)
- Every assessment task has a visual breakdown option (Concept Visualiser)
- Complex maths or science content gets 4 representations (Knowledge Sprint D): sequential steps, visual analogy, worked example, plain language
- No information is conveyed by colour alone — always paired with text label

**Principle 2: Multiple Means of Action and Expression**
Students must be able to demonstrate understanding in more than one way.

In Simplifii this means:
- Tier 1 (AI Pre-Write), Tier 2 (Socratic), Tier 3 (Learner Writing) are distinct and student-controlled
- Voice input available wherever text input exists
- Block editor supports heading structure, not just prose
- Export options: PDF, DOCX, plain text (multiple formats)
- The four SteeringDrawer dials (Persona, Scaffolding, Grit, LOD) are the student's expression controls

**Principle 3: Multiple Means of Engagement**
Students must have ways to sustain motivation and manage cognitive load.

In Simplifii this means:
- AURA never interrupts — 1200ms silence tolerance
- TaskPhaseBar shows where the student is without demanding they know where to go
- Idle detection fires after 180 seconds — not 30 seconds
- Resilience Bridge (IdleNudge, SupportBridge) fires trauma-informed nudges, not countdown timers
- No deficit framing anywhere in the UI (no red progress bars, no "you're behind")
- Sensory level dial reduces text density for students in high-cognitive-load states

**The five neurodivergent-specific design rules (non-negotiable):**

1. **Plain language first.** Every label, instruction, and AURA response must be readable at Year 8 reading level (Flesch-Kincaid 60+) without LiteralMode enabled. LiteralMode should enhance, not fix.

2. **Predictable structure.** Every screen must have the same layout as the screen before it. No surprise navigation. No elements that move between interactions.

3. **Working memory externalised.** Every piece of information a student needs to hold in their head must be visible on screen simultaneously. Never rely on recall.

4. **Time is never the enemy.** No timed elements without pause/extend option. No "session expiring" warnings that create anxiety.

5. **Failure is neutral.** Error messages say what happened and what to do next, never what the student did wrong. "Your document could not be uploaded. Try a PDF or DOCX file." Not "Upload failed."

**CRAFT accessibility check — add to every PR that touches UI:**
```
CRAFT ACCESSIBILITY CHECK:
[ ] All interactive elements have visible focus (FOCUS_RING token)
[ ] Colour not used as sole signifier
[ ] Touch targets 44px minimum
[ ] Keyboard navigable
[ ] Screen reader tested (VoiceOver on Mac minimum)
[ ] Reduced motion respected
[ ] Plain language (Year 8 level)
[ ] No deficit framing in any error or status message
[ ] Working memory externalised — all needed info visible simultaneously
```

---

## F — Fortify

*Security patterns specific to Simplifii-OS*

From the article: 36% of community skills contain prompt injection. 13% have critical security flaws. This applies to any external code added to the build, including skills, MCP servers, and npm packages.

**The four security surfaces in Simplifii-OS:**

**Surface 1: Student data sovereignty**
- Layer 1 (Learner Work Content): encrypted at rest, student-only access — never visible to institutions
- Layer 2 (Behavioural Telemetry): anonymised hashes only — `user_id_hash`, not `user_id`
- Layer 3 (Account Metadata): student-controlled — full export on request

**Surface 2: AI output integrity**
- AURA never writes to Tier 3 directly — always to Tier 1 (pre-write) or Tier 2 (socratic)
- Every AI suggestion is logged to HistoryOfThought before it reaches the student
- The Authenticity Report is tamper-evident via SHA-256 signature
- `stripMarkdown()` applied to every AI output surface — no raw markdown reaches students

**Surface 3: API security**
Every Vercel serverless function must have:
```javascript
// Rate limiting (already present — verify on each new route)
const quota = await checkQuota(userId);
if (quota.exceeded) return res.status(402).json({ error: quota.error });

// Input validation
if (!text || text.length < 20) return res.status(400).json({ error: 'text required' });

// API key guard
const apiKey = process.env.ANTHROPIC_API_KEY;
if (!apiKey) return res.status(500).json({ error: 'API key not configured' });
```

**Surface 4: Dependency security**
Before any `npm install`:
```bash
npm audit
```
Before installing any Claude skill from the community:
- Read SKILL.md line by line
- Check `allowed-tools` frontmatter — any skill requesting Bash access needs review
- Check bundled scripts for command execution patterns
- Skills from unknown authors: do not install without reading every line

**CRAFT security checklist for new routes:**
```
[ ] Rate limit: checkQuota() present
[ ] Input validation: minimum length and type checks
[ ] API key guard: returns 500 not 200 if key missing
[ ] No raw user_id in telemetry tables
[ ] No raw content in cloud storage
[ ] ANTHROPIC_API_KEY in .env.local not committed
[ ] npm audit clean before deploy
```

---

## T — Teaching + Pedagogy

*Education science applied to Simplifii-OS design*

Simplifii is not an AI wrapper. It is an educational operating system grounded in learning science. Every feature must be evaluated against how students actually learn, not just whether it is technically functional.

**The seven learning science principles that govern Simplifii design:**

**1. Cognitive Load Theory (Sweller, 1988)**
Working memory holds 4-7 items. Extraneous cognitive load kills learning.

Application: The three-tier canvas separates AI pre-write (Tier 1), thinking (Tier 2), and writing (Tier 3). A student does not have to hold the structure in their head — it is visible.

Design rule: Never put more than three primary actions on screen simultaneously. The tool rail collapses. AURA is a single orb. TaskPhaseBar is one bar with five pills.

**2. Spaced Repetition and Retrieval Practice (Roediger & Karpicke, 2006)**
Testing yourself on material produces stronger memory than re-reading it.

Application: Tier 2 (Socratic prompts) forces retrieval before writing. AURA asks questions rather than giving answers. The rubric self-check tool asks students to rate themselves before showing the answer.

Design rule: AURA's default mode is Socratic (questions), not assistant (answers). The Grit dial lets students choose how much challenge they want — but the default should not be "give me the answer."

**3. Deliberate Practice (Ericsson, 1993)**
Improvement requires working at the edge of current ability with immediate feedback.

Application: Phase guidance (Sprint 5) sequences the task so students are always working on the next challenging step, not repeating what they already know. AURA's phase-specific opening questions are calibrated to the current phase.

Design rule: TaskPhaseBar phases lock sequentially by default. Students with executive function challenges need this — jumping to Phase 4 (Draft) without understanding (Phase 1) produces worse outcomes.

**4. Zone of Proximal Development (Vygotsky, 1978)**
Learning happens in the gap between what a student can do alone and what they can do with support.

Application: The SteeringDrawer dials let students set how much scaffolding they need. Heavy scaffolding = explicit step-by-step. Light scaffolding = guiding questions only. AURA calibrates to the dial setting.

Design rule: Default scaffolding should be Medium, not Heavy. Heavy scaffolding is available but not the default — it builds dependency, not capability.

**5. Trauma-Informed Education**
Many neurodivergent students have experienced educational harm. The system must be safe before it can be effective.

Application: Resilience Bridge fires when idle detection detects disengagement. AURA never says "you should" or "you need to." No countdown timers. No red indicators. No public progress visibility.

Design rule: Every AURA message that could be interpreted as pressure must be reviewed against this rule. "You're behind on this phase" is never acceptable. "Would it help to look at this section together?" is acceptable.

**6. Authentic Assessment (Wiggins, 1990)**
Assessment should mirror real-world tasks, not test artificial skills.

Application: The Authenticity Report documents the genuine thinking process — it shows markers what the student actually did, not what the AI did. The three-tier canvas makes this traceable.

Design rule: Every AI contribution must be logged. The student must be able to see and export a complete record of what AI helped with. This is the product's academic integrity argument.

**7. Universal Design for Learning (CAST, 2018)**
See the A section above. UDL is not a feature — it is the architecture.

**Pedagogy checklist for new features:**
```
[ ] Does this feature increase or decrease cognitive load?
[ ] Does it scaffold toward independence or toward dependency?
[ ] Is the default setting trauma-safe?
[ ] Does it work with the SteeringDrawer dials?
[ ] Is the AURA interaction Socratic-first?
[ ] Does it comply with UDL principles 1, 2, and 3?
[ ] Would a student with ADHD, dyslexia, or autism find this intuitive without training?
```

---

## What is missing — the complete gap analysis

Running CRAFT across all five dimensions reveals the gaps not yet covered by any skill:

**Content + SEO gaps:**
- Landing page has no meta tags, no structured data, no Open Graph
- No Google Search Console setup
- No privacy-respecting analytics
- No email capture or waitlist
- No student testimonials or social proof
- Authenticity Report PDF has no Simplifii branding or URL

**Research Memory gaps:**
- No persistent sprint plan files — every long session risks context drift
- No sprint recovery protocol for auto-compact sessions
- No findings log across sessions

**Accessibility gaps:**
- 18 raw rgba() warnings in every build — design token migration incomplete
- LiteralMode is built but orphaned — not wired into any surface
- BionicText mode exists in settings but not applied to AURA output
- OpenDyslexic font option in settings but not applied globally
- No skip navigation link on any page
- AudioOverview feature (Tier 1 panel) — functionality status unclear
- No keyboard navigation test in Playwright suite

**Security gaps:**
- `npm audit` not run as part of regression suite
- No dependency audit in CI/CD pipeline
- Community skills installed without documented security review
- AppShell.jsx console.log exposes user.id in development — should be removed in production

**Teaching + Pedagogy gaps:**
- Grit dial exists but AURA prompt does not differentiate response style based on it
- Scaffolding dial exists but no documented mapping to AURA prompt variations
- LOD (Compass/Sprint/Map) dial affects some surfaces but not all
- No Tier 2 (Socratic prompts) implementation — the three-tier canvas is missing Tier 2
- No evidence the AURA default is Socratic-first (it appears to be assistant-first currently)

---

## The three-framework combined prompt header

Every CC session from now on starts with:

```
SHARP active. FORGE active. CRAFT active.
```

**SHARP** — think correctly (Systems, Hallucination guard, Architecture, Root cause, Prompt discipline)

**FORGE** — execute correctly (Flow, Origin safety, Red first, Grain, Echo)

**CRAFT** — serve humans correctly (Content/SEO, Research memory, Accessibility/UDL, Fortify/security, Teaching/pedagogy)

---

## CRAFT sprint priorities (in order)

Based on the gap analysis above, these are the CRAFT-driven sprints that deliver most value:

| Priority | Sprint | What | Why |
|---|---|---|---|
| P0 | Wire LiteralMode | Connect LiteralMode to AURA output + all text surfaces | UDL Principle 1 — already built, just orphaned |
| P0 | Wire BionicText + OpenDyslexic | Apply globally when set in AccessibilityVault | Core accessibility commitment |
| P1 | Landing page SEO | Meta tags, structured data, Open Graph | Discoverability — students can't use what they can't find |
| P1 | Fix AURA to Socratic-first default | Change _aura-prompt.js default Grit dial behaviour | Core pedagogy rule |
| P2 | Build Tier 2 Socratic panel | The three-tier canvas is missing its middle tier | The integrity engine is incomplete |
| P2 | Design token migration | Clear 18 rgba() warnings permanently | Accessibility contrast ratios unreliable with raw values |
| P3 | Keyboard navigation Playwright tests | Add nav tests to regression suite | WCAG 2.2 AA enforcement |
| P3 | Email capture + waitlist | Before public launch | Marketing and user acquisition |
| P4 | Remove console.log(user.id) in AppShell | Security hygiene | PII in browser console is unacceptable in production |

---

## Installing CRAFT in CC terminal

```bash
SKILLS_DIR="/Users/adonis666/Library/Application Support/Claude/local-agent-mode-sessions/skills-plugin/6c49d35a-f83c-46dd-a38d-02fd0248270c/5175e35c-4c66-40da-9e6a-e39f8dc42cb2/skills"

mkdir -p "$SKILLS_DIR/CRAFT" && cp ~/Downloads/CRAFT-SKILL.md "$SKILLS_DIR/CRAFT/SKILL.md" && echo "CRAFT installed"
```

---

## The complete three-framework stack

```
SHARP active. FORGE active. CRAFT active.

SHARP: Should this exist? What breaks? One-sentence root cause. Gate words only.
FORGE: Flow plan. Clean git. Test first. Design tokens. Session handoff.
CRAFT: SEO ready. Planning persistent. Accessible. Secure. Pedagogically sound.
```

Three words. Fifteen dimensions. Every decision.
