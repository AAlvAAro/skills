---
name: cinematic-profile-video
description: Create cinematic short-form video concepts (Reels/TikTok) based on a person's social media profile. Use this skill whenever the user wants to create a video, reel, or TikTok for a client or person based on their Instagram or TikTok profile, wants to generate Higgsfield video prompts from social media analysis, or says things like "make a cinematic video for @username", "video concept for a client", "reel based on their Instagram", "create a video that reflects their personality", "Higgsfield video from their profile", "cinematic reel for this person". Also trigger when the user mentions creating visual content based on someone's social media presence, personal brand video, or profile-based creative direction. This skill combines social media intelligence (via Social Toolkit MCP) with AI video generation (via Higgsfield) to produce ready-to-execute video concepts.
---

# Cinematic Profile Video

A workflow skill that analyzes a person's social media profile (Instagram or TikTok), extracts their visual identity, aesthetic, personality, and passions, then generates a complete cinematic video concept with platform-specific prompts for image generation and video animation, including narrative transitions between scenes.

## When to Use

- Client asks for a short-form video (Reel, TikTok) that reflects someone's personality
- User wants to create a cinematic video based on a social media profile
- Any request involving: profile analysis → creative direction → video generation
- User wants to test AI video tools (Higgsfield, Kling, Seedance, Grok) with a real creative project

## Required Tools

- **Social Toolkit MCP**: `FetchInstagramProfileTool` or `FetchTiktokProfileTool`
- **Social Toolkit MCP**: `HiggsfieldImageTool` (for generating base images — currently may have API limitations)
- **Social Toolkit MCP**: `HiggsfieldVideoTool` (for animating images — currently may have API limitations)
- **ask_user_input_v0**: For gathering preferences
- **Notion MCP** or **Google Drive** or **PDF creation**: For saving final deliverable

## Workflow

### Step 0: Gather Input

Identify:
1. **Platform**: Instagram or TikTok
2. **Username**: Without the @ symbol
3. **Video duration**: Ask the user to choose:
   - **30 seconds** — 3-4 scenes, tight and punchy (TikTok/Reel)
   - **60 seconds** — 5-6 scenes, balanced narrative arc (Reel, recommended)
   - **90 seconds** — 7-8 scenes, full story with transitions (cinematic Reel)
   - **120 seconds** — 8-10 scenes, mini short film with complex narrative
4. **Creative direction decision**: Art that represents them OR they appear in the video (see Step 3A)
5. **Video generation platform**: Kling, Seedance 2.0, Grok (Aurora), or all three
6. **Output format**: Save to Notion, Google Drive, or PDF — user's choice

Use `ask_user_input_v0` to clarify. Batch questions — max 2 rounds.

```
ask_user_input_v0:
  questions:
    - question: "Video duration?"
      options: ["30 seconds", "60 seconds", "90 seconds", "120 seconds"]
      type: single_select
    - question: "Where to save the final result?"
      options: ["Notion", "Google Drive", "PDF", "Just in chat"]
      type: single_select
```

### Step 1: Fetch & Analyze Profile

Fetch the profile using the appropriate Social Toolkit tool:

**Instagram:**
```
Social Toolkit: FetchInstagramProfileTool
username: [username]
```

**TikTok:**
```
Social Toolkit: FetchTiktokProfileTool
username: [username]
```

**If the profile returns no data (private account):** Search the web for `[username] Instagram` to find any public presence, then ask the user for details about the person.

### Step 2: Profile Analysis

From the fetched data, extract and document:

#### Identity Extraction Framework

**A) Visual Aesthetic** (from post thumbnails, captions, tagged accounts):
- Color palette tendencies (warm/cool/muted/vivid/monochrome)
- Photography style (editorial, candid, artistic, commercial, lo-fi)
- Recurring visual motifs (nature, urban, fashion, abstract)
- Tagged photographers/designers/brands — infer production level
- Post aspect ratios — preferred framing style

**B) Personality & Values** (from captions, bio, content themes):
- Tone of voice (poetic, minimal, bold, humorous, mysterious)
- Recurring themes or words in captions
- Emoji usage patterns — emotional register
- Bio keywords and self-description
- External links — professional identity
- **Unicode/stylized typography** — level of curation and intentionality

**C) Passions & Interests** (from content patterns):
- Music references (artists tagged, concert posts, lyrics in captions, **reel audio tracks**)
- Fashion/brand affiliations (tagged brands, concept stores)
- Activities and hobbies visible in posts
- **Cultural references** (art, film, literature — look for quotes, allusions in captions)
- Community connections (tagged friends, collaborative posts)

**D) Engagement Signature** (what resonates with their audience):
- Highest-engagement posts — what their audience values most
- Content type distribution (photo vs video vs **carousel — look at carousel length**)
- Posting frequency and consistency
- Comment sentiment patterns

**E) Symbolic Language** (CRITICAL — this drives the creative direction):
- Recurring symbols in captions and images (roses, mirrors, butterflies, moons, fire, water)
- Literary or artistic references (specific authors, painters, films)
- The "world" they're building — every curated profile tells a story about who they want to be
- Their caption language (English vs native, poetic vs casual, long vs short)

#### Analysis Output Format

Present the analysis as a concise creative brief:

```
## Profile Analysis: @[username]

**Platform**: [Instagram/TikTok]
**Name**: [display name]
**Followers**: [count] | **Posts**: [count]

### Visual Identity
- **Palette**: [e.g., "Warm golden + deep burgundy — candlelit romantic"]
- **Style**: [e.g., "Dark academia, Pre-Raphaelite, chiaroscuro"]
- **Motifs**: [e.g., "Red roses, antique mirrors, butterflies, dried flowers"]
- **Format**: [e.g., "Heavy carousel user (7-14 photos) — thinks in sequences"]

### Personality
- **Tone**: [e.g., "Poetic, English captions with Unicode styling, mysterious"]
- **Bio essence**: [e.g., "'I'm the sweetest girl in Town, so why are you so mean?'"]
- **Vibe**: [e.g., "Dark romantic softgirl — vulnerable but intentionally curated"]

### Cultural References
- **Literature**: [e.g., "Oscar Wilde (The Nightingale and the Rose)"]
- **Art**: [e.g., "Monet (Water Lilies), Pre-Raphaelite Brotherhood"]
- **Music**: [e.g., "Cigarettes After Sex — 'Cry' (used in reel)"]
- **Film/Other**: [e.g., "Bonnie & Clyde references, Sofia Coppola aesthetic"]

### Top Content (by engagement)
1. [Post description] — [X likes] — caption: "[key quote]"
2. [Post description] — [X likes]
3. [Post description] — [X likes]
```

### Step 3: Creative Direction Decision

#### Step 3A: Art vs. Appearance

Before generating anything, make a deliberate decision:

**Option A: Art that represents their universe (NO person in video)**
- Best when: the person's feed is already full of their own photos
- Approach: Generate atmospheric scenes using their symbols (objects, spaces, textures)
- Advantage: No uncanny valley risk, higher perceived value, more "artistic"
- Models: `nano_banana_2` for complex scenes, any video model

**Option B: Person appears in the video (AI-generated figure evoking their style)**
- Best when: the person has few photos or wants a "starring" piece
- Approach: Generate a figure that evokes their aesthetic but is not literally them
- Advantage: More personal, more shareable
- Risk: Uncanny valley, client may not like how they're represented
- Models: `soul_2` for portraits, `kling` for video

**Option C: Hybrid (recommended based on real project experience)**
- Mix of atmospheric art scenes + scenes with an AI figure
- Start with atmosphere, introduce the figure, end with atmosphere/art
- Best of both worlds — narrative variety + personal touch

**Be opinionated.** Analyze their profile and RECOMMEND one option with reasoning. Don't present all three as equal choices.

### Step 4: Generate Video Concept

Based on the profile analysis and duration choice, create a cinematic narrative structure.

#### Duration-Based Scene Planning

| Duration | Scenes | Seconds per scene | Narrative depth |
|----------|--------|-------------------|-----------------|
| 30s | 3-4 | 6-8s each | Single mood, punchy |
| 60s | 5-6 | 8-10s each | Full arc with turning point |
| 90s | 7-8 | 8-12s each | Complex narrative with transitions |
| 120s | 8-10 | 8-12s each | Mini film with multiple acts |

#### Narrative Arc Templates

**Template A: "Transformation" (artistic/fashion profiles)**
- Act 1: Static beauty — frozen moment
- Act 2: World shifts — environment transforms
- Act 3: Fragmentation — multiple perspectives
- Act 4: Recomposition — unified final image

**Template B: "Presence & Absence" (melancholic/romantic profiles) — PROVEN IN PRODUCTION**
- Act 1: The world (exterior, establishing shot)
- Act 2: The person in their element (contemplation)
- Act 3: The absence (they disappear, empty spaces remain)
- Act 4: The traces (objects/symbols that prove they existed)
- Act 5: Transcendence (reality becomes art)

**Template C: "Duality" (contrasting themes)**
- Act 1: One side — soft, vulnerable
- Act 2: The other side — bold, fierce
- Act 3: Collision — both worlds meeting
- Act 4: Integration — the full picture

**Template D: "Immersion" (travel/experience profiles)**
- Act 1: Wide establishing — their world
- Act 2: Details — textures, objects, hands
- Act 3: Movement — flow through space
- Act 4: Portrait — them in their element

**Template E: Custom** — Build from scratch based on unique profile elements

### Step 5: Generate Prompts — MULTI-PLATFORM

For each scene, generate prompts for EACH platform the user wants.

#### Image Generation Prompts

Use `HiggsfieldImageTool` with:
- **model**: `soul_2` for portraits, `nano_banana_2` for complex scenes/objects
- **aspect_ratio**: `9:16` (vertical) or `16:9` (cinematic)
- **resolution**: `1080p`

Prompt structure:
```
[Style keyword], [subject description], [lighting description], [color palette],
[mood/atmosphere], [composition details], [quality keywords]
```

Add `no people` to prompts for art-only scenes.

#### Video Animation Prompts — BY PLATFORM

**CRITICAL: Each platform needs different prompt styles.**

**Kling (most cinematic):**
- Technical camera directions (dolly, push-in, rack focus, locked camera)
- Example: `Slow cinematic push-in toward the subject, candlelight flickers, dust particles float, shallow depth of field, 24fps film cadence`

**Seedance 2.0 (element-specific motion):**
- Describe what EACH element does independently
- Example: `Slow camera push-in, candlelight flickers casting warm shadows across her face, she slowly brings the rose closer to her lips, dried flowers above sway subtly`

**Grok / Aurora (conversational/directorial):**
- Full scene descriptions with film references
- Example: `A cinematic scene of a woman in a white lace dress holding a red rose. Shot like a scene from a Sofia Coppola film, 35mm grain, dreamy pace.`

#### Scene Duration by Type

| Scene type | Duration | Why |
|-----------|----------|-----|
| Wide establishing (buildings, landscapes) | 6s | Clear visual, quick read |
| Person in action (walking, turning) | 6-8s | Movement needs completion |
| Intimate portrait (close-up, emotion) | 8s | Breathing room |
| Still life / objects | 8-10s | Subtle motion needs time |
| Transformation (texture/style change) | 10s | Must feel organic, not abrupt |

### Step 6: Transition Prompts — THE DIFFERENTIATOR

**Transitions are what separate a slideshow from a film. Spend as much creative energy here as on the scenes themselves.**

#### Transition Types

**1. Hard Cut (0s)**
- When: Changing worlds/states (presence → absence, day → night)
- Effect: Jarring, impactful, emotional
- Example: She exits frame → CUT → empty chair

**2. Cross Dissolve (0.5-1.5s)**
- When: Continuity of emotion, same mood
- 0.5s = subtle, 1.0s = standard, 1.5s = very dreamlike
- Example: Her face → DISSOLVE → empty mirror

**3. In-Prompt Transformation**
- When: Style/texture should change (photo → painting, day → night)
- Encode in the video prompt itself
- Example: `...the image dissolves into thick painterly oil textures, colors bleed like wet canvas`

**4. Fade to/from Black (1-2s)**
- When: Opening, closing, major act breaks
- Max 2x per video (open and close)

#### Transition Patterns (proven in production)

| From → To | Transition | Narrative effect |
|-----------|-----------|-----------------|
| Person present → Same space empty | Hard cut | Viewer feels absence instantly |
| Daylight → Candlelight | Hard cut | Crossing a threshold between worlds |
| Person → Close-up object/symbol | Cross dissolve 1.5s | Object becomes essence of person |
| Photo → Painting/oil texture | In-prompt transform (10s) | Reality becomes art |
| Exterior → Interior | In-prompt (follow through door) | Movement creates continuity |
| Intimate → Wide/empty | Hard cut | Loneliness amplified by sudden space |
| Empty space → Person appearing | Hard cut | Mysterious apparition |

For each pair of scenes, output:
```
### Transition: Scene [N] → Scene [N+1]
**Type**: [Hard cut / Cross dissolve Xs / In-prompt / Fade]
**Narrative logic**: [What the viewer feels]
**Prompt modification** (if in-prompt): [text to add to scene N or N+1 prompt]
```

### Step 7: Music Direction

1. Check the profile's reel audio tracks — they already chose music that fits
2. Look for lyrics in captions that reference songs
3. Match narrative arc to song structure

```
## Music Guide
**Primary**: [Artist — "Song"]
**Why**: [Connection to profile]
**Sync points**:
- 0:00 — Fade from black on first beat
- 0:XX — Transition aligns with [musical moment]
- 0:XX — Climax on [drop/swell/silence]
- 0:XX — Fade to black on final note
**Copyright-safe alt**: Search CapCut for "[mood] [genre] instrumental"
```

### Step 8: Save Output

Based on user preference:

**Notion:** Create page with full content, organized by scene, with to-do checklist
**Google Drive:** Create Google Doc with same structure
**PDF:** Generate formatted PDF with scene cards

Document structure:
1. Profile Analysis
2. Creative Direction + Narrative Arc
3. Timeline Table (scene, duration, transition)
4. Scene-by-Scene Prompts (image + video per platform + transition)
5. Music Guide
6. Post-Production Notes
7. Checklist

### Step 9: Post-Production Guide

**Editing workflow:**
1. Generate all clips individually (1 image → 1 clip)
2. Import into CapCut / DaVinci Resolve / InShot
3. Arrange in timeline order
4. Apply transitions as specified
5. Add music and sync to transition points
6. Add text overlays (person's own caption quotes)
7. Color grade for consistency
8. Export: MP4, 1080x1920, 30fps, 10-15 Mbps

## Key Behavior Notes

1. **Be opinionated.** ONE strong concept, not 5 options.
2. **Profile analysis is everything.** Specific cultural references > generic "likes fashion."
3. **Symbolic language drives the concept.** Roses, mirrors, butterflies aren't random.
4. **Transitions are the differentiator.** More creative energy here than on individual scenes.
5. **Platform-specific prompts.** Never same prompt across Kling/Seedance/Grok.
6. **Recommend specific songs** from their actual reel audio.
7. **The final order always changes.** Design modular scenes.
8. **Duration determines narrative structure**, not just scene count.
9. **Reference specific posts** when explaining creative choices.
10. **Art vs. appearance decision matters.** Don't default to generating faces.

## Scene Count Quick Reference

| Duration | Scenes | Avg Length | Dissolve Budget |
|----------|--------|-----------|-----------------|
| 30s | 3-4 | 7-8s | 2-3s |
| 60s | 5-6 | 8-10s | 5-6s |
| 90s | 7-8 | 9-11s | 7-8s |
| 120s | 8-10 | 10-12s | 10-12s |
