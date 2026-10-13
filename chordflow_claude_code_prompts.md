# ChordFlow Claude Code Prompt Pack

This document is a copy-paste ready prompt pack for Claude Code to build a private personal-use mobile app called **ChordFlow**.

The app goal is to create, edit, practice, and export songs with lyrics, chords, guitar tabs, piano notes, and switchable views. Expo Router is suitable for app navigation in Expo projects, `expo-print` can generate PDFs from HTML, and `expo-sharing` can hand the resulting PDF to the system share flow.[cite:18][cite:1][cite:14]

## How to use this pack

Use the prompts in order instead of sending everything at once. Large multi-feature mobile apps are usually more reliable when Claude Code builds them in phases, especially when the app includes navigation, local persistence, PDF generation, editing workflows, and audio-analysis placeholders.

A bottom sheet is a good interaction pattern for chord popups, and React Native already has mature bottom-sheet options for this kind of mobile UI.[cite:16][cite:24]

## Can Claude Code use subagents?

Yes. Claude Code can be asked to use subagents or parallel task breakdown if your workflow supports that. The practical recommendation is to assign subagents by responsibility instead of by screen only.

Suggested split:
- Subagent 1: project architecture, navigation, shared types, storage.
- Subagent 2: Song Viewer, filters, layout system, chord-over-lyrics rendering.
- Subagent 3: Song Editor, undo/redo, chord and note editing.
- Subagent 4: Practice mode, autoscroll, playback-state UI.
- Subagent 5: export pipeline, HTML print templates, PDF generation.
- Subagent 6: AI integration interfaces, mock services, microphone/listen pipeline placeholders.

The reason this helps is that Expo Router provides a clear file-based screen structure, while the PDF export and bottom-sheet interactions are isolated enough to be developed in parallel.[cite:18][cite:1][cite:16]

## Should the app use downloadable AI models?

Yes, but selectively. A fully local/offline AI stack for lyrics alignment, live chord detection, instrumental transcription, and note extraction is possible in principle, but for an MVP it is usually too heavy to do everything on-device.

The best approach is hybrid:
- Keep the **app UI, editor, practice mode, and PDF export** lightweight and local-first.
- Use **replaceable AI service interfaces**.
- Allow later support for one or two downloadable models for specific tasks, such as on-device chord detection or lightweight audio tagging.

A local model may make sense for:
- live chord detection drafts,
- quick offline key/chord estimation,
- privacy-sensitive personal workflows.

A local model is usually overkill at first for:
- full instrumental transcription,
- accurate piano-note extraction,
- full lyric-word alignment,
- high-quality arrangement generation.

## Recommended AI strategy

| Capability | Start with | Later upgrade |
|---|---|---|
| Chord suggestions over user lyrics | Cloud or mocked AI service | Optional small local assistant |
| Live microphone chord detection | Lightweight local or embedded model interface | Better downloadable local model |
| Uploaded audio analysis | Background/offline service interface | Optional device-optimized model |
| Piano note transcription | Placeholder/service interface | Cloud or advanced offline model |
| Full instrumental transcription | Placeholder only | Advanced model later |
| Section labeling / cleanup | Cloud AI or rule-based post-processing | Hybrid |

## Master instruction for Claude Code

Paste this first before the phased prompts:

```text
You are helping me build a private personal-use mobile application called ChordFlow.

General rules:
- Use React Native with Expo and TypeScript.
- Use Expo Router for navigation.
- Use a clean, production-quality architecture.
- Keep everything strongly typed.
- Build in phases and keep each phase stable before moving on.
- Prefer reusable components over duplicated code.(maybe use skills)
- Use a unified song document as the single source of truth.
- Preserve all generated data even when certain layers are hidden from view.
- All AI outputs must be editable drafts.
- Do not implement lyric scraping, stream ripping, DRM circumvention, Spotify downloading, or YouTube downloading.
- Spotify or YouTube links may be used only as metadata references unless official permitted APIs are added later.
- PDF export must be generated from dedicated print HTML/CSS templates using expo-print, not screenshots.
- Use expo-sharing for the final PDF share/save flow.
- Use bottom sheets for chord detail popups and display filters.
- Structure AI features so they can use mocked services now and replaceable real services later.
- If you use subagents, split them by architecture responsibility, not randomly.
- After each phase, summarize what was created, what files changed, what remains, and any technical risks.
```

## Phase 1 — project foundation

```text
Create the initial ChordFlow project.

Requirements:
- React Native with Expo and TypeScript.
- Expo Router navigation.
- Zustand for app state.
- React Query for async state.
- Light theme and dark theme.
- Reusable UI primitives for button, card, segmented control, tab, slider, chip, switch, sheet, modal row, icon button, form field, and empty state.
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

Important:
- This is a private personal-use app.
- Do not implement scraping or unauthorized content downloading.
- Make the base project easy to extend.
```

## Phase 2 — unified song document

```text
Implement the core unified song document model.

Create strongly typed models and utilities for:
- Song metadata
- Song source information
- Song type: with_lyrics | instrumental
- Sections
- Lines
- Timed words
- Positioned chords
- Guitar tab measures
- Piano note events
- Timeline events
- Display presets
- View filters
- Export settings
- Confidence values
- AI draft flags

Requirements:
- The app must use one source of truth for all song data.
- Hiding a visual layer must never delete stored data.
- Prepare sample data for:
  1. a lyrics-based song
  2. an instrumental song
- Add utilities for transpose, section lookup, visible-layer filtering, and export preparation.
```

## Phase 3 — create song flow

```text
Build the Create Song flow.

At the beginning ask:
“Does this song have lyrics?”

Options:
1. With lyrics
2. Instrumental / no lyrics

Then build the form.

For With lyrics:
- title
- artist
- optional Spotify link
- optional YouTube link
- pasted lyrics field
- optional audio upload placeholder
- optional key
- optional tempo/BPM
- optional time signature
- optional capo
- notes

For Instrumental / no lyrics:
- title
- artist
- optional Spotify link
- optional YouTube link
- optional audio upload placeholder
- optional key
- optional tempo/BPM
- optional time signature
- notes

Requirements:
- Use Zod validation.
- Save songs locally.
- Keep the UI clean and mobile-friendly.
```

## Phase 4 — song viewer and switchable views

```text
Build the Song Viewer.

Add built-in view presets:
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
- confidence markers
- editing controls

Behavior:
- Changing view presets changes only presentation.
- Hiding layers must not delete data.
- Save the last selected view and filters per song.
- Keep the viewer responsive and readable on mobile.
```

## Phase 5 — lyrics and chord renderer

```text
Implement the lyrics and chord rendering engine.

Requirements:
- Render chord symbols above the correct lyric positions.
- Use structured chord-position data, not plain text hacks.
- Support section labels: Intro, Verse, Chorus, Bridge, Solo, Outro.
- Support font size controls.
- Support transposition up and down by semitone.
- Keep layout stable on smaller screens.
- Preserve readability when wrapping lines.
- Build reusable rendering components.
```

## Phase 6 — guitar and piano interactive chord views

```text
Add interactive chord popups.

Requirements:
- Add a Guitar / Piano switcher at the top of the Song Viewer.
- Tapping a chord opens a bottom sheet.
- In Guitar mode, show:
  - chord name
  - fretboard
  - finger dots
  - fret numbers
  - open/muted string markers
- In Piano mode, show:
  - chord name
  - keyboard diagram
  - highlighted chord tones
  - note names
  - inversion selector placeholder

Important:
- The switcher changes only representation, not stored harmony.
- Build these as reusable components.
```

## Phase 7 — editor

```text
Build the Song Editor.

Requirements:
- Edit lyric lines.
- Insert, move, replace, or delete chords.
- Tap a lyric position and insert a chord there.
- Edit guitar tab data.
- Edit piano notes, duration, octave, and hand assignment.
- Add undo and redo.
- Mark AI-detected items with confidence values.
- Reflect changes immediately in the Song Viewer.
- Preserve the unified song document as the source of truth.
```

## Phase 8 — practice mode

```text
Build Practice mode.

Requirements:
- Minimal distraction UI.
- Large readable text.
- Autoscroll with start, pause, reset.
- Adjustable speed controls.
- Optional countdown.
- Current line highlighting.
- Current chord highlighting.
- Dark-mode optimized layout.
- Optional metronome placeholder.
- Respect the currently visible content layers.

Also add a setting for scroll behavior:
1. manual speed
2. audio timeline placeholder
```

## Phase 9 — listen mode

```text
Build Listen mode using microphone input placeholders.

Requirements:
- Request microphone permission.
- Add a Listen button.
- Show listening state clearly.
- Show current detected chord placeholder.
- Show previous detected chords.
- Show estimated key placeholder.
- Show estimated tempo placeholder.
- Show confidence values.

For songs with lyrics:
- Let me select a section.
- Let me assign detected chords to the lyrics.

For instrumental songs:
- Create a timestamped chord progression draft.

Important:
- Save all results as editable AI-detected drafts.
- Do not imply perfect accuracy.
- Do not implement unauthorized Spotify/YouTube capture.
- Structure the recognition engine behind a replaceable interface.
```

## Phase 10 — instrumental outputs

```text
Build instrumental output views.

For instrumental songs, create editable structures and views for:
- chord progression
- guitar chord chart
- simplified guitar tab
- piano voicings
- piano notes
- timestamps

Create internal tabs in the viewer for:
- Chords
- Guitar
- Piano
- Notes

All tabs must read from the same unified song document.
```

## Phase 11 — PDF export

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
- Preserve current filters when Current View is selected.
- Use monospaced alignment for tablature.
- Avoid bad page breaks between lyric and chord lines.
```

## Phase 12 — AI integration layer

```text
Create a clean AI integration layer.

Add typed interfaces and mocked services for:
- chord suggestion from user-supplied lyrics
- uploaded-audio analysis
- lyric alignment
- live chord detection
- instrumental transcription
- section labeling
- arrangement cleanup

Rules:
- All generated output must be clearly marked as editable AI draft.
- Never generate or complete copyrighted lyrics not explicitly supplied by the user.
- Keep the app usable even if AI services are unavailable.
- Make the provider layer replaceable.
```

## Phase 13 — polish and review

```text
Polish the entire app.

Requirements:
- Improve spacing, hierarchy, and typography.
- Make the app easy to use one-handed during practice.
- Improve empty states.
- Improve loading states.
- Improve error states.
- Add onboarding that explains:
  - with lyrics mode
  - instrumental mode
  - view switching
  - editing
  - exporting PDF
  - microphone listen mode limitations
- Review the whole codebase for consistency.
- Reduce duplication.
- Improve naming.
- Document technical risks and next steps.
```

## Final audit prompt

Use this after the build is mostly done:

```text
Run a final architecture and product audit on the ChordFlow codebase.

Check for:
- inconsistent types
- duplicated logic
- view-state bugs
- song document mutation bugs
- poor component boundaries
- weak naming
- missing loading states
- missing empty states
- missing error handling
- PDF export edge cases
- bottom-sheet usability problems
- mobile overflow issues
- dark mode issues
- future AI integration bottlenecks

Then fix the issues directly and summarize:
1. what was found
2. what was fixed
3. what still needs real AI/model integration later
```

## Subagent prompt

If you want Claude Code to coordinate subagents, paste this before a bigger phase:

```text
Use subagents for this task.

Split work by responsibility, not randomly:
- Architecture and shared types
- Viewer and display-layer system
- Editor and mutation flows
- Practice and playback UI
- Export/PDF pipeline
- AI service interfaces and mocked providers

Each subagent should:
- work within existing types and conventions
- avoid creating duplicate source-of-truth models
- reuse shared components whenever possible
- document changed files and design decisions

After subagent work is complete, merge results into one coherent implementation and resolve inconsistencies.
```

## Design image prompt

Use this with an image generator for the app design direction:

```text
Design a premium mobile music-practice app UI called “ChordFlow” for iPhone and Android.

The app helps musicians create, edit, practice, and export songs with lyrics, chords, guitar tabs, piano notes, and instrumental arrangements.

Show multiple realistic mobile app screens in one polished product presentation:
1. Library screen with saved songs
2. Create Song screen with a choice between “With lyrics” and “Instrumental”
3. Song Viewer screen with switchable views: Lyrics, Lyrics + Chords, Guitar, Piano, Notes, Practice
4. Filter bottom sheet with layer toggles
5. Practice screen with autoscrolling lyrics and chords
6. Chord detail bottom sheet showing a guitar chord diagram
7. Alternate chord detail bottom sheet showing a piano keyboard diagram
8. Export PDF screen based on current view

Visual direction:
- premium, elegant, musician-focused
- dark mode primary presentation
- optional light mode accents
- refined typography
- subtle depth and soft shadows
- no generic neon SaaS style
- no purple-blue AI gradients
- calm, high-end creative tool aesthetic
- realistic chord-over-lyrics layout
- strong segmented controls and compact filter chips
- excellent one-handed usability

Color direction:
- charcoal
- warm off-black
- ivory
- muted teal or muted gold accent
- restrained palette
- high readability for practice mode

Composition:
- 6 to 8 phone mockups
- clean studio background
- crisp interface detail
- focus on UI, not props
- premium mobile product shot
- 16:9 aspect ratio
```

## Alternate design image prompt

```text
Create a sophisticated mobile app design concept for an app called “ChordFlow” that generates and displays lyrics, chords, guitar tabs, piano notes, and practice views for songs.

Show realistic mobile screens for:
- library
- lyrics with chords above the words
- guitar tab view
- piano note view
- practice mode with auto-scroll
- export PDF options

Style:
- Scandinavian minimalism meets professional music software
- soft neutral palette
- warm white
- graphite
- muted teal accents
- refined typography
- clean segmented controls
- minimal but premium
- serious musician tool, not playful
- polished bottom-sheet interactions
- realistic mobile UI

Present it as a premium app mockup on a clean background.
```

## Logo prompt

```text
Design a modern premium logo for a mobile app called “ChordFlow”.

The app is about music practice, lyrics, chords, guitar, piano, and smooth musical flow.

The logo should combine ideas of:
- chord structure
- musical flow
- clarity
- mobile-friendly simplicity

Creative direction:
- minimalist
- geometric
- elegant
- memorable at small sizes
- suitable for app icon, splash screen, and navigation branding
- should work in monochrome and with a subtle accent color
- avoid cliché music notes unless highly abstracted
- avoid cartoon style
- avoid busy details

Possible visual ideas:
- abstract chord grid
- flowing line forming a C or CF monogram
- minimal fretboard / keyboard hybrid symbol
- layered horizontal or vertical bars suggesting lyric lines and chord alignment
- a symbol that feels structured and musical

Deliver:
- one strong centered logo mark
- optional wordmark “ChordFlow”
- dark background presentation
- vector-like sharpness
- square composition
```

## App icon prompt

```text
Create a premium app icon for a music app called “ChordFlow”.

The icon should be bold, simple, and instantly recognizable on a phone screen.

It should suggest:
- chords
- piano or guitar structure
- flowing arrangement
- music practice

Style:
- minimalist
- geometric
- elegant
- high contrast
- not cartoonish
- no tiny details
- luxury creative-tool feel

Use:
- dark charcoal background
- muted teal or warm gold accent
- single centered symbol
- no phone mockup
- no extra objects
- no text unless extremely minimal
```

## Optional model strategy prompt

Use this if you want Claude Code to prepare the app for future downloadable/local AI models:

```text
Prepare the ChordFlow codebase for optional downloadable AI models in the future.

Requirements:
- Do not hardwire any one AI provider directly into the UI.
- Create interfaces for:
  - live chord detection
  - uploaded-audio chord analysis
  - lyric alignment
  - piano note extraction
  - instrumental transcription
- Support these provider types:
  1. mock provider
  2. remote/cloud provider
  3. local/downloadable model provider
- Add a capability registry so the app can tell which provider supports which tasks.
- Add a model-management settings placeholder for future downloadable models.
- Do not actually bundle heavy models yet.
- Keep the MVP lightweight.
```

## Recommendation on downloadable models

For this app, downloadable models are **not overkill in principle**, but they are overkill if you try to do everything locally in version 1.

A sensible path is:
- v1: mocked services + cloud/service interfaces + clean editor.
- v2: optional lightweight local chord-detection model.
- v3: optional stronger offline analysis for uploaded audio.
- later: advanced transcription models only if performance, storage, and UX are acceptable.

For your app idea, the editor and view system matter more at the beginning than perfect AI transcription. If the structure is good, you can always improve the AI later without rebuilding the whole product.
