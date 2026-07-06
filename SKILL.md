# 🎧 AI Music Analysis + Prompt Generator

Convert audio references (descriptions, existing songs, or structured data) into **optimized prompts** for AI music generation models — Suno, Udio, and beyond.

---

## When to Use This Skill

Invoke whenever the user wants to:
- **Generate music prompts** for Suno / Udio / similar models
- **Analyze a reference song** and extract its musical DNA
- **Create remix / alternate versions** of a song concept
- **Transfer style** from one song to another
- **Batch-generate** multiple prompt variants from one idea

**Trigger phrases:** "make a prompt for Suno", "write an Udio prompt", "analyze this song", "remix this track", "generate music prompts", "song style transfer"

---

## Architecture (From Codex Spec)

```
Input: Reference song (description / structured data / user idea)
       ↓
① Analysis Layer      → Extract BPM, Key, Mood, Genre, Instruments, Structure
       ↓
② Interpretation Layer → Structured data → natural-language music description
       ↓
③ Prompt Generator    → Suno prompt · Udio prompt · Remix variants (3-5)
       ↓
④ Output              → JSON + copy-paste-ready natural language prompts
```

---

## Step 1 — Gather Input

Ask the user for their reference. Accept any of these formats:

| Format | Example |
|--------|---------|
| **Song name + artist** | "Blinding Lights by The Weeknd" |
| **YouTube / streaming link** | (describe what you hear) |
| **Free-text vibe description** | "Sad lo-fi piano with rain sounds" |
| **Structured audio JSON** | Paste analysis from external tool |
| **Partial idea** | "Something like Daft Punk but orchestral" |

If the user provides only a vague vibe, proceed directly to **Prompt Generation**. If they provide a specific reference song, go through **full Analysis first**.

---

## Step 2 — Audio Analysis Framework

When the user has a **specific reference track**, analyze it across these dimensions. If they can't provide the data, guide them with these questions:

### 2.1 Core Metrics
```
BPM: ___ (use "slow" / "mid" / "fast" / "very fast" if exact unknown)
Key: ___ (major/minor, mention if unsure — mood implies key)
Time Signature: ___ (4/4 default unless obvious)
```

### 2.2 Genre & Style
```
Primary Genre: ___
Sub-genres / Fusion Elements: ___
Era / Production Style: ___ (80s, modern, lo-fi, polished, etc.)
```

### 2.3 Mood & Emotional Arc
Use this structured mood map:

| Section | Mood | Intensity (1-10) | Descriptors |
|---------|------|-------------------|-------------|
| Intro | ___ | ___ | ___ |
| Verse | ___ | ___ | ___ |
| Pre-Chorus | ___ | ___ | ___ |
| Chorus | ___ | ___ | ___ |
| Bridge | ___ | ___ | ___ |
| Outro | ___ | ___ | ___ |

**Mood vocabulary (pick 2-4 per section):**
`dark, bright, warm, cold, tense, relaxed, uplifting, melancholic, aggressive, gentle, mysterious, euphoric, nostalgic, futuristic, organic, synthetic, intimate, expansive, driving, floating`

### 2.4 Instrumentation & Sound Design
```
Lead/Melody: ___ (vocal, synth, guitar, piano, etc.)
Rhythm Section: ___ (drums, percussion style)
Bass: ___ (sub, electric, synth, acoustic)
Harmony/Pads: ___ (strings, synth pads, choir)
Textures/FX: ___ (risers, impacts, ambience, noise)
```

### 2.5 Vocal Profile (if applicable)
```
Gender & Timbre: ___ (female/male/androgynous, airy/raspy/smooth/nasal)
Range: ___ (low, mid, high, wide range)
Delivery: ___ (breathy, belted, spoken, rapped, whispered)
Processing: ___ (dry, reverb, delay, vocoder, pitched, layered)
Language: ___
```

### 2.6 Structure / Song Sections
Map the timeline (approximate if exact unknown):

```
[0:00-0:15]  Intro        — ___
[0:15-0:45]  Verse 1      — ___
[0:45-1:00]  Pre-Chorus   — ___
[1:00-1:30]  Chorus       — ___
[1:30-1:45]  Drop/Post    — ___
[1:45-2:15]  Verse 2      — ___
[2:15-2:30]  Pre-Chorus   — ___
[2:30-3:00]  Chorus       — ___
[3:00-3:30]  Bridge       — ___
[3:30-4:00]  Final Chorus — ___
[4:00-4:15]  Outro        — ___
```

### 2.7 Production Qualities
```
Mix Style: ___ (clean/polished, raw/gritty, spacious, tight/dry)
Stereo Field: ___ (wide, narrow, mono-compatible)
Dynamics: ___ (compressed/punchy, natural/dynamic, squashed)
Reverb Space: ___ (small room, large hall, plate, cathedral, none)
Era/Reference: ___ (analog warmth, digital clean, tape saturation)
```

---

## Step 3 — LLM Interpretation Layer

Convert the structured analysis into a cohesive **natural-language song description**. This is the bridge between data and prompt. Write it as a paragraph that captures the *essence* of the song.

### Template:
> This is a **[BPM]** **[genre]** track in **[key]**. The mood moves from **[intro mood]** to **[climax mood]**, creating a **[emotional journey descriptor]** arc. The production is **[production style]**, featuring **[key instruments]** with **[vocal description]**. The structure follows a **[structure type]** pattern, with the **[highlight section]** serving as the emotional peak. Sonically, it references **[comparison artists/vibes]**.

---

## Step 4 — Prompt Generator

Generate **all three prompt types** for every request. Each has a different target model and format.

### 4.1 ☀️ Suno Prompt

Suno responds best to **descriptive, natural-language prompts** with style tags. Format:

```
<Genre/style tags>, <mood descriptors>, <instrumentation>, <vocal style>, <structure notes>, <production quality>
```

**Style tag reference (commonly effective on Suno):**

| Category | Tags |
|----------|------|
| **Genre** | pop, rock, electronic, hip-hop, r&b, jazz, classical, folk, metal, punk, indie, lo-fi, ambient, synthwave, house, techno, drum and bass, trap, edm, cinematic, orchestral |
| **Mood** | emotional, energetic, dark, uplifting, melancholic, aggressive, calm, dreamy, intense, nostalgic, epic, intimate, ethereal |
| **Vocal** | male vocalist, female vocalist, choir, duet, rap, spoken word, instrumental |
| **Instrument** | piano, acoustic guitar, electric guitar, synth, strings, brass, saxophone, flute, organ, bass, drums, 808 |
| **Production** | high quality, clean mix, lo-fi, vintage, modern, atmospheric, minimal, layered, spacious, raw |
| **Tempo** | slow, mid-tempo, fast, upbeat, downtempo, ballad |
| **Era** | 80s, 90s, 2000s, retro, futuristic, timeless |

**Suno prompt template:**

```
<Primary genre> <sub-genre>, <mood 1> <mood 2>, <tempo> tempo, <key> key.
<vocal description>, <instrument 1>, <instrument 2>, <instrument 3>.
<structure highlight>.
<production quality>, <era/style reference>.
<unique distinguishing element — what makes this song special>.
```

### 4.2 🎵 Udio Prompt

Udio works better with **slightly more technical, production-focused prompts**. Include arrangement details and mix references.

**Udio prompt template:**

```
<Genre>, <BPM> BPM, <key>.
<emotional arc description>.
Detailed arrangement: <section-by-section breakdown>.
Instrumentation: <detailed instrument list with roles>.
Production: <mix style>, <mastering style>, <stereo field>, <reverb space>.
<Vocal production details>.
Reference sound: <comparison artists / tracks / eras>.
```

### 4.3 🔄 Remix / Alternate Version Prompts

Generate **3-5 remix prompts** that keep the song's DNA but transform its genre, mood, or energy. Each remix should have a clear **concept** and **target use-case**.

**Remix categories to draw from:**

| Remix Type | Transform | Keep |
|------------|-----------|------|
| **Genre Flip** | Genre, instruments | Melody, structure, lyrics |
| **Energy Shift** | BPM, intensity, drums | Chords, vocal, mood hints |
| **Mood Flip** | Major↔minor, bright↔dark | Melody, structure |
| **Stripped/Acoustic** | Electronic→organic instruments | Chords, vocal, structure |
| **Club/ Festival** | BPM↑, drops added, energy↑ | Hook, vocal hooks |
| **Lo-fi / Chill** | BPM↓, degradation, warmth | Chords, melody |
| **Cinematic / Orchestral** | Full orchestra, dynamics | Theme, structure |
| **Era Swap** | Production style (80s/90s/etc.) | Song core |

**Remix prompt template:**

```
[REMIX TYPE]: <song title / concept> — <remix name>
Genre: <new genre>
Mood: <new mood>
Key change: <if any>
BPM: <new BPM>
Concept: <1-2 sentences describing the transformation>
Prompt: <full generated prompt for this remix>
```

---

## Step 5 — Output Format

Provide the final output as both:

### 5.1 Copy-Paste Ready (default display)
Show the Suno prompt, Udio prompt, and Remix prompts clearly labeled so the user can copy individual sections.

### 5.2 Structured JSON (on request)
```json
{
  "analysis": {
    "bpm": 128,
    "key": "C minor",
    "genre": ["electronic", "pop"],
    "mood_arc": "calm → emotional → energetic",
    "instruments": ["synth pads", "punchy drums", "deep bass"],
    "vocal_style": "female airy vocal with light reverb",
    "structure": [
      {"section": "intro", "start": "0:00", "end": "0:10", "mood": "calm"},
      {"section": "verse", "start": "0:10", "end": "0:40", "mood": "emotional"},
      {"section": "chorus", "start": "0:40", "end": "1:10", "mood": "energetic"},
      {"section": "bridge", "start": "1:10", "end": "1:30", "mood": "atmospheric"},
      {"section": "final chorus", "start": "1:30", "end": "2:00", "mood": "euphoric"}
    ],
    "production": "clean mix, wide stereo, modern mastering"
  },
  "suno_prompt": "...",
  "udio_prompt": "...",
  "remix_prompts": [
    {"type": "dark cinematic", "prompt": "..."},
    {"type": "lo-fi emotional", "prompt": "..."},
    {"type": "festival remix", "prompt": "..."},
    {"type": "acoustic stripped", "prompt": "..."},
    {"type": "orchestral", "prompt": "..."}
  ],
  "tags": ["emotional", "cinematic", "pop", "electronic", "female vocal"]
}
```

---

## Prompt Quality Checklist

Before delivering any prompt, verify:

- [ ] **Specificity:** Does it name concrete instruments, not just "band"?
- [ ] **Contrast:** Are there clear dynamic shifts (soft→loud, sparse→dense)?
- [ ] **Structure:** Is the song section flow described?
- [ ] **Vocal clarity:** Gender, style, processing all specified?
- [ ] **Unique element:** Is there ONE thing that makes this song different?
- [ ] **Production level:** Mix style, era, and mastering quality mentioned?
- [ ] **Not over-stuffed:** Suno especially works better with focused prompts — don't throw in every tag
- [ ] **No contradictions:** "Calm aggressive" or "lo-fi polished" need reconciliation

---

## Quick Reference: Common Genre Templates

### Pop
```
<sub-genre> pop, <mood>, <tempo> BPM, <key>.
<gender> vocal, <vocal style> delivery.
<lead instrument>, <rhythm>, <bass>.
Catchy hooks, memorable chorus, <production> production.
<era> influence, radio-ready mix.
```

### Electronic / EDM
```
<sub-genre>, <BPM> BPM, <key>.
<build/drop structure description>.
<synth type> lead, <bass type> bass, <drum style> drums.
<atmospheric element>, <FX details>.
<club/festival/chill> energy, <production> mix.
```

### Hip-Hop / Trap
```
<sub-genre>, <BPM> BPM, <key>.
<drum style> drums, <808/bass description>.
<melodic element>, <texture/pad>.
<vocal delivery style>, <flow description>.
<mood>, <era> production.
```

### Cinematic / Orchestral
```
<orchestral/cinematic/hybrid>, <BPM> BPM, <key>.
<orchestra sections used>.
Building from <starting dynamic> to <climax dynamic>.
<emotional journey description>.
<film/game/trailer> energy, <mix style>.
```

### Lo-fi / Chill
```
lo-fi <genre>, slow-mid tempo, <key>.
<lead instrument>, warm <bass>, <drum style> drums.
Tape saturation, vinyl crackle, <reverb type>.
<season/time of day> vibe, nostalgic, intimate.
Study/focus/chill energy.
```

### Rock / Alternative
```
<sub-genre> rock, <tempo>, <key>.
<guitar style>, <amp type>, <bass>, <drum style> drums.
<vocal style> vocals, <delivery>.
<energy level>, <era> production.
<live/studio> feel.
```

---

## Multi-Version Generator

When the user wants **variations of the same concept**, generate a matrix:

| Dimension | Version A | Version B | Version C |
|-----------|-----------|-----------|-----------|
| **Energy** | Low/Ambient | Mid/Balanced | High/Peak |
| **Mood** | Dark | Neutral | Bright |
| **Genre** | Electronic | Pop | Cinematic |
| **Tempo** | Slow (60-80) | Mid (90-120) | Fast (130-160) |

Then generate the full prompt set for each version.

---

## Song-to-Song Style Transfer

When the user says "make a song like **[Song A]** but with the vibe of **[Song B]**":

1. Analyze Song A → extract **structure, instruments, vocal style**
2. Analyze Song B → extract **mood, genre, production style**
3. **Merge:** Song A's skeleton + Song B's skin = new prompt
4. Generate all three prompt types for the hybrid

---

## Music Tag System

Auto-generate tags for every analyzed/prompted song:

```json
{
  "genre_tags": ["electronic", "pop", "synthwave"],
  "mood_tags": ["emotional", "energetic", "uplifting", "cinematic"],
  "instrument_tags": ["synth", "drum machine", "female vocal", "bass"],
  "era_tags": ["modern", "2020s"],
  "use_case_tags": ["workout", "driving", "focus", "gaming", "study", "party", "chill", "motivation"],
  "energy_level": 7,
  "danceability": 6,
  "valence": 7
}
```

---

## Example Workflow

**User:** "I want to make a song like Blinding Lights by The Weeknd"

**Skill responds:**

### 🎧 Analysis: "Blinding Lights" — The Weeknd

| Dimension | Detail |
|-----------|--------|
| **BPM** | 171 (fast) |
| **Key** | F minor |
| **Genre** | Synthwave / Synth-pop |
| **Mood Arc** | Nostalgic → Euphoric → Urgent |
| **Instruments** | Arpeggiated synth, punchy drums, driving bass, reverb-heavy male vocal |
| **Structure** | Intro → Verse → Pre-Chorus → Chorus → Verse → Chorus → Bridge → Chorus → Outro |
| **Production** | 80s-inspired, wide stereo, gated reverb, clean modern mix |
| **Vocal** | Male tenor, bright falsetto, heavy reverb, layered harmonies |
| **Unique Element** | That relentless arpeggiated synth line driving through the whole track |

### ☀️ Suno Prompt
```
Synthwave, synth-pop, nostalgic euphoric, fast tempo, F minor key.
Male tenor vocal, bright falsetto with heavy reverb, layered harmonies.
Arpeggiated analog synth lead, punchy drum machine, driving bassline, atmospheric pads.
Classic verse-chorus structure with soaring pre-chorus build.
80s-inspired production, gated reverb drums, wide stereo field, modern clean master.
Relentless driving energy — like driving through a neon-lit city at night, windows down, heart racing.
```

### 🎵 Udio Prompt
```
Synthwave with pop structure, 171 BPM, F minor.
Emotional journey from nostalgic longing to euphoric release to urgent desperation.
Detailed arrangement: atmospheric filtered intro → tight verse with sparse drums → soaring pre-chorus build → explosive chorus with full instrumentation → stripped second verse → bigger second chorus → atmospheric bridge with pad swell → final double chorus → cold outro with fading synth.
Instrumentation: Arpeggiated analog-style synth (lead, continuous 16th notes), punchy gated-reverb drum machine (kick, snare, hi-hats), driving melodic bass guitar, warm synth pads for harmonic bed, occasional bell/texture hits.
Production: 80s-inspired but modern — gated reverb on snare, wide stereo synths, clean digital master, bright high-end, tight low-end.
Reference sound: The Weeknd's After Hours era, Kavinsky, modern retro-pop crossover.
```

### 🔄 Remix Prompts

**1. DARK CINEMATIC VERSION**
```
Genre: Dark cinematic / trailer electronic
Mood: Menacing, tense, apocalyptic
BPM: 85 (half-time feel)
Concept: Same melodic DNA slowed to half-time, orchestral strings replacing synths, heavy impacts, building to a massive trailer-drop climax.
Prompt: Dark cinematic trailer music, 85 BPM, F minor. Orchestral strings, braaam impacts, tense synth pulses. Slow-burn build from sparse piano to massive full-orchestra climax. Apocalyptic, urgent, powerful. Hans Zimmer meets modern trailer hybrid.
```

**2. LO-FI STUDY VERSION**
```
Genre: Lo-fi hip-hop / chill beats
Mood: Warm, cozy, melancholic-nostalgic
BPM: 80
Concept: The iconic arp line played on a warm Rhodes piano, soft boom-bap drums, vinyl crackle, perfect for rainy-day studying.
Prompt: Lo-fi hip-hop, 80 BPM, F minor. Warm Rhodes piano playing the melody, soft boom-bap drums, vinyl crackle and tape hiss, subtle rain ambience. Cozy, nostalgic, intimate. Perfect study beat — familiar but completely transformed.
```

**3. FESTIVAL BIG-ROOM REMIX**
```
Genre: Big-room house / festival EDM
Mood: Euphoric, explosive, hands-up
BPM: 128
Concept: The verse melody becomes a vocal chop hook, massive supersaw drops replace the chorus, build-ups with snare rolls, designed for mainstage.
Prompt: Big-room progressive house, 128 BPM, F minor. Vocal chop hook from the original melody, massive supersaw leads, punchy sidechain compression. Extended builds with snare rolls, explosive drops with white-noise risers. Euphoric, hands-in-the-air festival energy. Martin Garrix meets synthwave nostalgia.
```

---

## Edge Cases & Guardrails

- **Vague input ("make something cool"):** Ask ONE clarifying question about genre/mood, then generate with reasonable defaults. Don't interrogate.
- **Contradictory requests ("heavy metal lullaby"):** Embrace the fusion — these often produce the most interesting prompts. Reconcile by describing *how* the contradiction resolves (e.g., "blast beats played at whisper volume").
- **Instrumental-only:** Explicitly tag `[Instrumental]` in Suno prompts and note "no vocals" in Udio.
- **Non-English lyrics:** Specify language clearly in both prompts. Suno supports many languages.
- **Copyright concerns:** Never claim to replicate a specific artist's voice. Use "in the style of" or "reference vibe" language. Generated prompts should be original compositions inspired by, not copies of, existing works.
- **Very long prompts:** Suno has effective prompt length limits. If a prompt exceeds ~300 characters, provide a "core" short version AND the detailed version, letting the user choose.
