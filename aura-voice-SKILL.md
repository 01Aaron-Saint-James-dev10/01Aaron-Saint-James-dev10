---
name: aura-voice
description: AURA voice upgrade patterns for Simplifii-OS. Load this skill whenever Aaron asks about making AURA sound better, more like ChatGPT voice, voice-first UI, ElevenLabs integration, TTS upgrade, interruption handling, turn-taking, or voice mode improvements. Also load when any work touches useAuraVoice.js, api/tts.js, or AuraChatOverlay voice mode. Never build voice features without reading this skill first — it contains the neurodivergent-specific voice rules that differentiate AURA from generic voice assistants.
---

# AURA Voice Upgrade — Simplifii-OS

## What Already Exists

The audit confirms these are live in production:
- `/api/tts` — browser TTS route (robotic, works as fallback)
- `src/hooks/useAuraVoice.js` — TTS hook, called from AuraChatOverlay
- `voiceMode` toggle in AuraChatOverlay
- `isListeningContinuous` state — continuous speech recognition
- `speak(clean)` called on every AURA response
- B5 fix — `m.text` always clean, no [TOOL:] tags in speech

## The Three Voice Sprints (in order)

### Voice Sprint A — Neural TTS (biggest single improvement)
Replace browser `speechSynthesis` with ElevenLabs streaming API.

**New file:** `api/tts-elevenlabs.js`
- Input: `{ text, voiceId, stability, similarityBoost, style }`
- Calls ElevenLabs `/v1/text-to-speech/{voiceId}/stream`
- Returns audio stream
- Rate limit: 30/min per user
- Quota check: max 10,000 chars per day per user (prevent abuse)
- Fallback: if ElevenLabs fails, fall back to browser TTS silently

**Modified file:** `src/hooks/useAuraVoice.js`
- Replace `window.speechSynthesis.speak()` with fetch to `/api/tts-elevenlabs`
- Use Web Audio API to play the returned audio stream
- Expose `isSpeaking` state based on audio playback, not synthesis events

**ElevenLabs voice settings for AURA:**
- Voice: Rachel or Aria (calm, clear, non-robotic)
- Stability: 0.75 (consistent but not flat)
- Similarity boost: 0.85
- Style: 0.2 (slight expressiveness, not dramatic)
- Model: `eleven_turbo_v2_5` (lowest latency)

**AURA-specific TTS pre-processing (apply before every speak() call):**
```javascript
function prepareForSpeech(text) {
  return text
    .replace(/\[TOOL:\w+\]/g, '')          // strip any remaining tool tags
    .replace(/\*\*(.*?)\*\*/g, '$1')        // strip bold markdown
    .replace(/_(.*?)_/g, '$1')              // strip italic markdown
    .replace(/\[(.*?)\]\(.*?\)/g, '$1')     // strip links, keep label
    .replace(/^#{1,4}\s+/gm, '')            // strip heading markers
    .replace(/\s+/g, ' ')                   // normalise whitespace
    .trim();
}
```

---

### Voice Sprint B — Interruption + Turn-Taking

**The turn-taking loop:**
```
Student speaks → VAD detects speech start → AURA stops speaking
→ Student finishes → 1200ms silence → AURA processes → AURA speaks
→ Repeat
```

**Key values (neurodivergent-calibrated):**
- Silence timeout before AURA responds: **1200ms** (not 300ms like ChatGPT)
- Rationale: ADHD word retrieval delays, processing differences, anxiety pauses
- This is a config value — expose in SettingsContext so students can adjust

**Modified file:** `src/hooks/useAuraVoice.js`
```javascript
// Voice activity detection
const SILENCE_TIMEOUT = 1200; // ms — neurodivergent-calibrated
let silenceTimer = null;

recognition.onresult = (event) => {
  clearTimeout(silenceTimer);
  // Stop AURA speaking if it is
  if (audioRef.current) audioRef.current.pause();
  
  silenceTimer = setTimeout(() => {
    // Student has finished — send transcript to AURA
    handleTranscript(transcript);
  }, SILENCE_TIMEOUT);
};
```

**Interruption rule:** When student starts speaking, immediately pause audio playback. Store the interrupted position. Do NOT resume automatically — the student's message takes priority.

---

### Voice Sprint C — Voice-First UI Mode

When voice mode is active, the interface changes:

**AuraChatOverlay in voice mode:**
- Chat transcript panel opacity drops to 0.3 (still readable, not primary)
- AURA orb scales up to 80px diameter (from current size)
- Orb states:
  - Idle: dim pulse, slow (2s cycle)
  - Listening: bright pulse, fast (0.5s cycle), accent colour
  - Processing: spinner animation inside orb
  - Speaking: wave animation around orb edge, synced to audio amplitude
- "Tap to stop" appears below orb when AURA is speaking

**The orb amplitude sync:**
```javascript
// Web Audio API analyser for visual feedback
const analyser = audioCtx.createAnalyser();
source.connect(analyser);
analyser.connect(audioCtx.destination);

function animateOrb() {
  const dataArray = new Uint8Array(analyser.frequencyBinCount);
  analyser.getByteTimeDomainData(dataArray);
  // Map amplitude to orb scale: 1.0 → 1.3
  const amplitude = Math.max(...dataArray) / 128;
  orbRef.current.style.transform = `scale(${1 + amplitude * 0.3})`;
  requestAnimationFrame(animateOrb);
}
```

---

## Neurodivergent Voice Rules (Non-Negotiable)

These rules differentiate AURA from ChatGPT Voice. Never remove them.

**Rule 1 — Silence tolerance:** Default 1200ms. Exposed as user preference. Never below 800ms.

**Rule 2 — LOD-aware speech rate:**
- Compass mode (sprint): `rate: 1.1`
- Map mode (overview): `rate: 0.9`
- Literal mode: `rate: 0.85` (slower, clearer pronunciation)

**Rule 3 — Never read UI elements:** `prepareForSpeech()` strips all markdown, tags, and bracketed labels before TTS.

**Rule 4 — No interruption of the student:** AURA never speaks while student is speaking. VAD detection is mandatory.

**Rule 5 — Calm tone always:** ElevenLabs stability 0.75+. Never use high-style voices. AURA is a GPS, not a presenter.

**Rule 6 — Graceful degradation:** If ElevenLabs fails, fall back to browser TTS silently. Never show an error for voice failure — just switch to text mode.

---

## Environment Variables Needed

```
ELEVENLABS_API_KEY=your_key
ELEVENLABS_VOICE_ID=21m00Tcm4TlvDq8ikWAM  # Rachel (calm, clear)
```

Add to `.env.local` (never commit) and Vercel environment variables.

---

## Sprint Order

Do Voice Sprint A first. It delivers the most noticeable improvement (robotic → human voice) with the least risk (new API route, one hook change).

Voice Sprint B second. Interruption + turn-taking makes it feel conversational.

Voice Sprint C last. UI mode change is the most visible but depends on A and B working correctly first.

---

## CC Prompt Pattern for Voice Sprint A

```
SESSION CONSTRAINT: Upgrade AURA TTS from browser speech 
synthesis to ElevenLabs neural voice. Nothing else.

CONTEXT:
App: Simplifii-OS-Main
Stack: React + Create React App + Supabase + Vercel
Local path: ~/Simplifii-OS_Master
Session log: docs/SESSION_LOG.md (read first)

STEP 1: READ BEFORE TOUCHING ANYTHING
Read src/hooks/useAuraVoice.js (entire file)
Read api/tts.js (entire file)
Report: exact lines where speak() is called, exact shape 
of current TTS call, whether any rate/pitch/volume controls 
exist

STEP 2: BUILD api/tts-elevenlabs.js
ElevenLabs streaming TTS endpoint
Input: text, voiceId (default Rachel), stability, 
similarityBoost
Rate limit: 30/min
Quota: 10,000 chars/day per user
Fallback response header: X-TTS-Fallback: true if ElevenLabs 
fails
Show system for voice selection before writing file.
Wait for approval.

STEP 3: UPDATE useAuraVoice.js
Replace window.speechSynthesis.speak() with fetch to 
/api/tts-elevenlabs
Use Web Audio API to play returned audio stream
Apply prepareForSpeech() pre-processing before every call
Expose isSpeaking state from audio playback events not 
synthesis events
Keep browser TTS as fallback if API returns X-TTS-Fallback

Show git diff --staged before committing.
Wait for: "looks good, commit aura-voice-elevenlabs"

STEP 4: SMOKE TEST
Three questions. Run regression. 
Test voice by triggering AURA in voice mode — confirm 
ElevenLabs audio plays (not robotic browser voice).

STEP 5: SESSION END
docs/SESSION_LOG.md entry.
Next: Voice Sprint B — interruption and turn-taking.
git push && npx vercel --prod

RULES:
- Only touch useAuraVoice.js and new api/tts-elevenlabs.js
- Do not touch AuraChatOverlay.jsx or CanvasScreen.jsx
- Keep browser TTS fallback intact
- Smoke test before deploy
- One deploy at end
```
