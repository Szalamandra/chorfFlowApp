# ChordFlow Claude Code Instructions

This document contains a copy-paste-ready build plan for Claude Code, split into:

- **Version 1:** a strong non-AI MVP.
- **Version 2:** AI-assisted features added on top of the MVP.

The app is a private personal-use mobile application called **ChordFlow**.

Expo Router is a strong fit for navigation in Expo apps, `expo-print` can generate PDFs from HTML, and `expo-sharing` can handle the save/share flow for exported PDFs.[cite:18][cite:1][cite:14]

## Product direction

The app should help a musician:
- create song sheets,
- view lyrics, chords, guitar tabs, piano notes, and practice layouts,
- switch between views,
- edit everything manually,
- practice with autoscroll,
- export the current view as a PDF.

In Version 1, the app works without AI. In Version 2, the app adds optional AI-assisted draft generation, live listening, and audio analysis.

## Claude Code global instructions

Paste this first before all version prompts:

```text
You are building a private personal-use mobile app called ChordFlow.

General rules:
- Use React Native with Expo and TypeScript.
- Use Expo Router for navigation.
- Use a clean scalable architecture with reusable components.
- Use strong typing everywhere.
- Build incrementally and keep each phase stable before moving on.
- Use one unified song document as the single source of truth.
- Store all content layers even when they are hidden from the current view.
- Keep editing fast and mobile-friendly.
- Use local-first storage for the MVP.
- PDF export must use dedicated print HTML/CSS templates with expo-print, not screenshots.
- Use expo-sharing for the PDF share/save flow.
- Use bottom sheets for chord details, filters, and some edit controls.
- Do not implement scraping, lyric scraping, stream ripping, DRM circumvention, Spotify downloads, or YouTube downloads.
- Spotify and YouTube links may only be used for metadata references unless official permitted APIs are integrated later.
- Keep code maintainable and production-quality.
- After each phase, summarize what changed, what remains, and any risks.
```

---

# Version 1 — Basic app without AI

## Version 1 goals

Version 1 must be genuinely useful without any AI features.

It should support:
- manual song creation,
- lyrics and chord editing,
- guitar and piano views,
- note/tab views,
- practice mode,
- filterable layers,
- PDF export based on the current view.

## V1 Step 1 — project foundation

```text
Create the initial ChordFlow mobile app.

Requirements:
- React Native with Expo and TypeScript.
- Expo Router.
- Zustand for app state.
- React Query ready for future async integration.
- Light and dark theme.
- Reusable UI primitives:
  - Button
  - Card
  - SegmentedControl
  - TabBar
  - Slider
  - ToggleRow
  - Chip
  - BottomSheet
  - IconButton
  - EmptyState
  - TextField
  - TextArea
- Folder structure:
  - app
  - components
  - features
  - hooks
  - lib
  - store
  - types
  - constants
  - assets
- Placeholder screens:
  - Home / Library
  - Create Song
  - Song Viewer
  - Song Editor
  - Practice
  - Export PDF
  - Settings
- Add a README with setup instructions.

Do not add AI yet.
```

## V1 Step 2 — unified song document

```text
Implement the core unified song document model.

Create strongly typed models for:
- Song metadata
- Source type
- Song type: with_lyrics | instrumental
- Song sections
- Song lines
- Timed words
- Positioned chords
- Guitar tab measures
- Piano note events
- Timeline events
- Display settings
- Display presets
- Export settings

Requirements:
- Use one source of truth for all song content.
- Hiding a visual layer must never delete data.
- Add sample local data for:
  1. one lyrics song
  2. one instrumental song
- Add helpers for transpose, filtering visible layers, and preparing exports.
```

## V1 Step 3 — create song flow

```text
Build the Create Song flow.

At the beginning ask:
“Does this song have lyrics?”

Provide two options:
1. With lyrics
2. Instrumental / no lyrics

For With lyrics:
- title
- artist
- optional Spotify link for metadata reference
- optional YouTube link for metadata reference
- pasted lyrics field
- optional key
- optional BPM
- optional time signature
- optional capo
- notes

For Instrumental / no lyrics:
- title
- artist
- optional Spotify link for metadata reference
- optional YouTube link for metadata reference
- optional key
- optional BPM
- optional time signature
- notes

Requirements:
- Use Zod validation.
- Save songs locally.
- Keep the form mobile-friendly and clean.
```

## V1 Step 4 — library and local persistence

```text
Build the Home / Library screen.

Requirements:
- List saved songs.
- Show title, artist, song type, key, BPM, and updated date.
- Add search by title or artist.
- Add simple filtering by song type.
- Add create new song button.
- Add local persistence so songs remain saved between app launches.
- Keep architecture compatible with future sync.
```

## V1 Step 5 — song viewer with switchable views

```text
Build the Song Viewer.

Create built-in view presets:
- Lyrics
- Lyrics + Chords
- Guitar
- Piano
- Notes
- Practice
- Custom

Add a display/filter bottom sheet with toggles for:
- lyrics
- chord symbols
- guitar chord diagrams
- guitar tabs
- piano diagrams
- piano voicings
- piano notes
- standard notation placeholder
- note names
- section labels
- timestamps

Behavior:
- Views and filters affect display only.
- They must never modify or remove the underlying data.
- Save the last selected view for each song.
- Make the viewer readable on mobile in portrait and landscape.
```

## V1 Step 6 — lyrics and chord renderer

```text
Implement the lyrics and chord renderer.

Requirements:
- Render chord symbols above the correct lyric positions.
- Use structured position data, not plain text hacks.
- Add section labels such as Intro, Verse, Chorus, Bridge, Solo, Outro.
- Support font size controls.
- Support transposition up and down by semitone.
- Keep the layout stable and readable on mobile.
- Build reusable rendering components.
```

## V1 Step 7 — guitar and piano chord popups

```text
Add interactive chord popups.

Requirements:
- Add a Guitar / Piano switcher in the Song Viewer.
- Tapping a chord opens a bottom sheet.
- In Guitar mode show:
  - chord name
  - fretboard
  - finger dots
  - string labels
  - open/muted strings
- In Piano mode show:
  - chord name
  - keyboard diagram
  - highlighted chord tones
  - note names
  - inversion selector placeholder

Important:
- Switching instruments changes only presentation.
- It must not modify the stored harmony.
```

## V1 Step 8 — song editor

```text
Build the Song Editor.

Requirements:
- Edit lyric lines.
- Add, move, replace, or delete chords.
- Tap a lyric position and insert a chord there.
- Edit guitar tab entries.
- Edit piano notes, octave, hand assignment, and duration.
- Add undo and redo.
- Instantly reflect changes in the Song Viewer.
- Keep the unified song document as the only source of truth.
```

## V1 Step 9 — practice mode

```text
Build Practice mode.

Requirements:
- Minimal distraction interface.
- Large readable text.
- Autoscroll with start, pause, and reset.
- Adjustable speed control.
- Optional countdown before scrolling.
- Highlight current line.
- Highlight current chord.
- Dark mode optimized.
- Respect selected visible layers.
- Add optional metronome placeholder.
```

## V1 Step 10 — instrumental views

```text
Build instrumental viewing modes for songs without lyrics.

Requirements:
- Add editable views for:
  - chord progression
  - guitar chord chart
  - simplified guitar tab
  - piano voicings
  - piano notes
- Create internal tabs for:
  - Chords
  - Guitar
  - Piano
  - Notes
- All views must read from the same unified song document.
```

## V1 Step 11 — PDF export

```text
Implement PDF export.

Requirements:
- Add a Download PDF button.
- Export options:
  1. Current View
  2. Choose Content
  3. Preset Export

Preset exports:
- Lyrics sheet
- Lyrics + Chords
- Guitar practice sheet
- Guitar tab
- Piano accompaniment
- Piano notes
- Full arrangement

PDF settings:
- A4 or US Letter
- portrait or landscape
- font size
- include metadata
- include section labels
- include chord diagram page
- include page numbers

Implementation requirements:
- Generate PDFs from dedicated HTML/CSS print templates.
- Use expo-print printToFileAsync.
- Use expo-sharing for save/share.
- Preserve the visible layers when Current View is selected.
- Use monospaced alignment for guitar tablature.
- Avoid bad page breaks between chords and lyrics.
```

## V1 Step 12 — polish and onboarding

```text
Polish Version 1.

Requirements:
- Improve spacing, typography, and hierarchy.
- Improve one-handed usability.
- Add better empty states.
- Add loading states.
- Add error states.
- Add onboarding for:
  - with lyrics flow
  - instrumental flow
  - switching views
  - editing chords and notes
  - practice mode
  - PDF export
- Review the codebase for duplication and cleanup.
```

## V1 final review prompt

```text
Review the full Version 1 ChordFlow codebase.

Check for:
- duplicated logic
- poor type boundaries
- state bugs
- song document mutation issues
- mobile layout problems
- dark mode problems
- PDF export edge cases
- weak component reuse
- missing empty/loading/error states

Fix the issues directly and summarize:
1. what was found
2. what was fixed
3. what is intentionally postponed to Version 2
```

---

# Version 2 — AI-integrated upgrade

## Version 2 goals

Version 2 should add optional AI-assisted features on top of the stable manual app.

These AI features should always produce **editable drafts**, not final locked results.

## V2 AI architecture rule

Paste this before the V2 prompts:

```text
For Version 2, do not build AI features directly into UI components.

Create a provider-based AI/service architecture with typed interfaces.
Support these provider types:
1. mock provider
2. remote/cloud provider
3. local/downloadable model provider

Every AI feature must:
- return editable draft data
- include confidence values when relevant
- fail gracefully
- work without destroying existing manual edits
- preserve the unified song document as the source of truth
```

## V2 Step 1 — AI service layer

```text
Build the AI integration layer.

Create typed interfaces and mocked implementations for:
- chord suggestion from user-supplied lyrics
- uploaded-audio analysis
- lyric alignment
- live chord detection
- instrumental transcription
- section labeling and arrangement cleanup

Requirements:
- Keep the app usable without any live provider configured.
- Add clear draft status labels.
- Add confidence support.
- Add provider capability detection.
```

## V2 Step 2 — chord suggestions from lyrics

```text
Add AI-assisted chord suggestions for songs with lyrics.

Requirements:
- User pastes lyrics manually.
- AI may suggest chords for the provided lyrics only.
- Do not generate or complete copyrighted lyrics not supplied by the user.
- Return structured editable chord-position data.
- Allow regenerating a selected section only.
- Show confidence or suggestion quality where possible.
- Let the user accept, reject, or edit every chord.
```

## V2 Step 3 — uploaded audio analysis

```text
Add uploaded-audio analysis as an optional feature.

Requirements:
- Accept user-provided audio files only.
- Do not rip or capture Spotify/YouTube streams.
- Analyze uploaded audio to estimate:
  - key
  - BPM
  - beat positions
  - section boundaries placeholder
  - chord progression draft
- Save the results as editable draft data.
- Allow merging these results into an existing song.
- Preserve manual edits and avoid destructive overwrites.
```

## V2 Step 4 — live microphone listen mode

```text
Upgrade Listen mode with AI-backed live chord detection.

Requirements:
- Request microphone permission.
- Show real-time listening UI.
- Show current chord draft.
- Show previous chords.
- Show confidence, estimated key, and estimated BPM.

For songs with lyrics:
- Let me pick a section.
- Let me assign detected chords to that section.
- Place drafted chords above lyrics as editable suggestions.

For instrumental songs:
- Create timestamped chord progression drafts.

Important:
- Label all results as AI-detected drafts.
- Do not claim perfect transcription.
- Stop listening cleanly when the user exits.
```

## V2 Step 5 — instrumental transcription draft

```text
Add optional instrumental AI draft generation.

Requirements:
- For instrumental songs, generate editable drafts for:
  - chord progression
  - simplified guitar tab
  - piano voicings
  - piano notes or MIDI-like note events
- Display confidence where possible.
- Keep the outputs clearly editable.
- Preserve manual changes if the user reruns generation.
```

## V2 Step 6 — lyric alignment and timing

```text
Add optional lyric alignment support.

Requirements:
- Use only lyrics already provided by the user.
- Align lyric lines, and optionally words, to uploaded audio or detected timing.
- Save timestamps separately from displayed text.
- Allow the user to manually adjust timing.
- Use the alignment data to improve practice mode highlighting and chord placement.
```

## V2 Step 7 — provider settings and future local models

```text
Build AI/provider settings.

Requirements:
- Add a settings area for provider management.
- Show which capabilities are available from the active provider.
- Add placeholders for future downloadable/local models.
- Do not bundle heavy models yet unless explicitly requested.
- Keep the architecture ready for:
  - lightweight local chord detection
  - local note extraction
  - offline draft generation
```

## V2 Step 8 — free/open tools integration plan

```text
Prepare the app to optionally support free/open analysis tools in the future.

Design the provider interfaces so they could later integrate with tools such as:
- Essentia or Essentia.js for key, tempo, beats, and chord-related analysis
- Basic Pitch for audio-to-note or MIDI-style draft extraction

Do not overpromise reliability.
Treat all such outputs as drafts that require editing.
```

## V2 final review prompt

```text
Review the full Version 2 ChordFlow codebase.

Check for:
- AI-provider coupling problems
- destructive merges into manual song data
- performance issues
- microphone lifecycle bugs
- confidence-display issues
- bad draft labeling
- poor fallback behavior when providers are unavailable
- future local-model integration bottlenecks

Fix what can be fixed now and summarize:
1. what was improved
2. what still needs real provider integration
3. what should remain manual even after AI is added
```

---

# Optional subagent instruction

Use this when you want Claude Code to split work into parallel tracks:

```text
Use subagents for this task.

Split by responsibility:
- app architecture and state
- unified song document and shared types
- viewer and display layers
- editor and mutation flows
- practice mode and playback UI
- export/PDF pipeline
- AI/provider layer

Rules:
- do not create duplicate source-of-truth models
- reuse shared components and types
- document changed files
- resolve merge conflicts cleanly
- keep naming consistent
```

## Final recommendation

Build **Version 1 first** and finish it completely before starting Version 2. That gives you a useful app even if the AI parts take longer than expected.
