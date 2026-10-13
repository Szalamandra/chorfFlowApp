# ChordFlow — Final Claude Code Build Instructions

Copy prompts in order into Claude Code. Complete and test each phase before continuing.

## Master instructions

```text
Build ChordFlow, a private personal-use React Native app using Expo, TypeScript and Expo Router.

V1: manual music editor, offline storage, PDF export, portable backups, OPTIONAL accounts and Firebase cloud backup/sync.
V2: optional audio/model-assisted generation integrated into the same app.

Use expo-sqlite as primary persistent storage for EVERY user. Firebase Authentication and Cloud Firestore are optional secondary services. Do NOT use Supabase. Do not require registration or internet for core functionality.

Keep all song layers in one typed song document backed by SQLite: metadata, sections, user-provided lyrics, chord positions, guitar tabs, piano notes, durations, timestamps, display preferences and draft provenance. Views are projections; hiding layers never removes content. Zustand holds UI state, not the persistent source of truth.

Use expo-file-system for backups, expo-print for dedicated HTML/CSS PDF rendering and expo-sharing for sharing. Use current compatible APIs and verify package support before installation.

No lyric scraping, unauthorized stream downloads, DRM bypass or Spotify/YouTube audio ripping. Links are optional references; permitted metadata lookup is network-dependent. Do not store audio in the cloud or include it in creation backups.

All optional AI/model output is editable draft material. Text-only lyrics cannot determine the original recorded chords; label text-based suggestions as creative accompaniment, not transcription.

Do not promise indefinite free cloud retention or app compatibility. User-controlled exported backups are essential. Internal phone snapshots alone do not protect against uninstall, lost phone or device failure.

After every phase: run relevant tests, report changed files, implemented features, remaining gaps and risks. Never describe mocks as working analysis.
```

## V1 Phase 1 — foundation

```text
Initialize the app with Expo Router, TypeScript, light/dark themes and reusable buttons, sliders, segmented controls, bottom sheets, form fields and empty/error states.
Create Library, Create Song, Viewer, Editor, Practice, PDF Export, Backup & Restore, Optional Account and Settings screens.
Use folders app, components, features, lib, repositories, store, types, assets and tests.
Bundle necessary fonts and diagram assets for offline use. Add setup README and dependency compatibility notes.
Use a restrained charcoal/ivory/teal music-tool aesthetic with large touch targets.
```

## V1 Phase 2 — database

```text
Implement SQLite migrations and typed repositories. Use stable UUIDs, transactions, foreign keys and parameterized queries.
Tables: local_profiles, songs, sections, lines, positioned_chords, guitar_tabs, piano_notes, timeline_events, display_preferences, app_preferences, activity_logs, backup_history, sync_outbox, sync_state and conflict_copies.
Add schemaVersion, record revisions and optional account workspace IDs. Keep guest and account workspaces separate.
Seed an original fictional lyrics example and an instrumental example. Implement CRUD, schema migration tests and restart persistence tests. Persist edits before scheduling cloud work.
```

## V1 Phase 3 — creation/library

```text
First launch offers equally visible Continue without account, Sign in and Create account. Guest launch must work offline; do not create anonymous Firebase accounts silently.
Creation first asks With lyrics or Instrumental/no lyrics.
Fields: title, artist, optional reference URL, key, BPM, time signature, capo and notes. Lyrics mode adds pasted lyrics; instrumental mode does not require lyrics.
Use Zod validation. Save locally. Library supports search/filter, duplicate, edit and confirmed delete.
No automatic transcription in V1.
```

## V1 Phase 4 — viewer/diagrams

```text
Implement presets Lyrics, Lyrics + Chords, Guitar, Piano, Notes, Practice and Custom.
Independent visibility switches: metadata, sections, lyrics, chords, chord diagrams, guitar tabs, piano voicings, piano notes, note names, notation and timestamps.
Remember each song's view locally. Guitar/Piano switch affects presentation, not harmony.
Align chords above lyric positions with stable wrapping. Use Unicode-aware position handling and test long lines.
Implement transpose, font size, tappable guitar fretboards and piano keyboard diagrams. Provide a verified extensible chord library; unsupported diagrams show a clear fallback instead of invented fingering.
Represent notation honestly: implement a supported renderer or label an unavailable feature; do not show fake sheet music.
```

## V1 Phase 5 — editor/instrumental

```text
Implement lyrics and section editing; insert/move/replace/delete chords at lyric positions or instrumental beats.
Implement manual guitar tab editing (string, fret, timing, duration) and piano note editing (pitch, octave, duration, hand).
Add undo/redo, autosave and validation. View changes update immediately from the repository-backed document.
Instrumental views use manually entered progressions, tabs and notes. Piano voicings derived from chords are accompaniment suggestions, not recovered performance notes. Do not equate chord charts with full guitar transcription.
```

## V1 Phase 6 — practice/PDF

```text
Practice: smooth manual autoscroll, start/pause/reset, adjustable speed, optional countdown, font size and current-line indicator. Respect active layers. Timed chord highlights require actual timing; do not invent them.
PDF: Current View, Choose Content and preset export. Include page size, orientation, font size, metadata, diagrams and page numbers. Generate print HTML/CSS from the same song data and filters, not screenshots. Escape text, bundle assets, preserve tab alignment and sensible page breaks. Offer preview/share/save. Test offline and long arrangements.
PDFs are presentation exports, not complete editable backups.
```

## V1 Phase 7 — durable backup/restore

```text
Implement versioned JSON or ZIP containing creations, preferences, optional history, backup metadata and integrity checksum. Exclude passwords, tokens, API secrets, audio and temporary analysis data.
Create durable internal snapshots plus explicit Export backup outside app via supported save/share flows. Record last external export separately; do not claim sharing proves an external backup was retained.
Use a document picker to restore: validate size/schema/checksum, preview, merge or replace with confirmation, pre-restore snapshot and transactional import. Handle UUID collisions safely and migrate old backup formats.
Provide backup reminders, multiple dated snapshots and restore tests in a fresh installation. Explain uninstall/phone-loss risk and recommend an independent computer/external-drive/user-selected cloud-folder copy. Offer password-encrypted exports only with established authenticated encryption; explain forgotten-password consequences.
```

## V1 Phase 8 — optional Firebase setup/auth

```text
Add Firebase Authentication email/password registration, sign-in, password reset and sign-out, plus Firestore cloud backup/sync. Keep Spark/free quotas as target; no Firebase Storage, Cloud Functions or paid services unless explicitly approved.
Provide setup instructions: create project, enable auth, create Firestore, deploy rules/indexes, configure SDK, test emulators. Document chosen Expo SDK compatibility; native packages may need a development build.
Create .env.example with EXPO_PUBLIC_FIREBASE_API_KEY, AUTH_DOMAIN, PROJECT_ID, APP_ID and MESSAGING_SENDER_ID using their full FIREBASE-prefixed names.
Firebase client configuration is public and bundled; it does not authorize access. Enforce security rules. Never bundle service-account JSON, Admin credentials or secret AI keys.
If Firebase configuration is absent, guest/local features still run.
On sign-in ask whether to attach/upload existing guest creations. Never silently upload. Keep separate local workspaces per UID; switching accounts must not leak data. Sign-out does not silently delete local creations; offer confirmed keep/remove account-local-data choices. Never delete guest data.
Initial authentication requires internet; local work remains available when auth expires/offline. Persist sessions with the chosen SDK's supported storage mechanism.
```

## V1 Phase 9 — cloud database/sync/security

```text
Use Firestore paths users/{uid}, users/{uid}/songs/{songId}, song section subcollections, preferences and optional activityLogs. Store only creation data, preferences and minimal opt-in logs. Bound document sizes; split large arrangements into sections.
Create deny-by-default rules requiring matching authenticated UID, valid types, allowed fields and immutable ownership. Test unauthenticated rejection and cross-user isolation in emulators.
Implement durable SQLite outbox: local commit first, stable operation IDs, idempotent retries, bounded exponential backoff and sync on launch/foreground/reconnect/manual Sync now. Do not promise background execution.
Statuses: Local only, Pending, Synced, Failed, Conflict and last successful sync.
Track revisions/base revisions, server timestamps and deletion tombstones. Do not use device clocks alone. Independent edits preserve both versions with reviewable conflict copies; missing data is not deletion. Handle partial multi-document uploads with a manifest/revision completion protocol so interrupted transfers do not appear complete.
Download remote data into SQLite; UI reads SQLite. Quota/network/auth failures never block local work. Provide explicit backup enable/disable and account deletion flows with clear local/cloud consequences.
Cloud sync is NOT an independent historical backup: accidental deletion can propagate. Keep exported versioned backups.
```

## V1 Phase 10 — acceptance audit

```text
Test airplane-mode first launch, song CRUD/restart, views/diagrams, practice, PDF and backup/restore. Test offline edits syncing later, expired auth, quota failures, interrupted upload, account switching, conflict preservation and security rules.
Test fresh-install restore and migrations. Audit secrets/logs, bounded history, accessibility, long documents and dark mode.
Finish all V1 functions before V2. Clearly list any unimplemented features; no hidden placeholders in released workflows.
```

## V2 Phase 1 — model/service architecture

```text
Create typed providers for chord recognition, beat/key estimation, note transcription, lyric alignment and optional creative suggestions.
Support mock, self-hosted/local-computer, remote and on-device providers with capability registry. Mocks only in development. Feature availability reflects provider state.
Use free/open pretrained tools where practical. Evaluate Basic Pitch for note/MIDI drafts and Essentia for tonal/tempo/chord analysis. Verify current code/model licenses separately and Expo/runtime compatibility. Do not assume Python or WebAssembly tools run directly in React Native.
Benchmark clean solo input versus full mixes before choosing a provider. Define measured quality/latency targets; do not invent confidence scores or accuracy claims.
A self-hosted Python service on the user's computer is an optional first integration, not offline phone inference. On-device models require a separately verified native/runtime integration and initial model download unless bundled.
No mandatory paid AI or language-model subscription. Secret remote credentials belong on a backend, never EXPO_PUBLIC variables.
```

## V2 Phase 2 — actual audio analysis

```text
Implement at least one real provider before claiming analysis works. Process user-selected audio temporarily with explicit consent for any remote processing. Store only derived creations; delete app-created temporary audio after completion/cancellation. Do not delete the user's original file.
Generate timestamped chords, key/tempo and note-event drafts where supported. Show provenance and uncertainty; human editing remains essential.
Basic Pitch note output is not itself a chord chart, guitar fingering or polished piano score. Implement separate rhythm quantization, chord inference and playable fret/string assignment, with user correction. Full mixes and complex voicings may be unsupported.
Persist accepted results in SQLite and optionally sync derived creation data only. No audio backup.
```

## V2 Phase 3 — lyrics alignment/live listening

```text
Align only supplied lyrics using a singing-appropriate validated pipeline. Keep original text unchanged. Offer manual line/word timing correction. Missing/low-confidence timing must not become fabricated precision.
Microphone Listen: permission, clear recording indicator, start/pause/stop, live chord history and capability-appropriate latency. Stop on exit/interruption and clean buffers. Prefer foreground listening initially. Explain ambient analysis and permissions; recording does not automatically settle copyright/platform rights.
Chord recognition alone cannot align words: offer manual selected-line capture unless real lyric alignment is available. Offline Listen only works with a verified on-device provider; otherwise label required connectivity/device service clearly.
Instrumental mode produces editable progression, tab/notes where supported, not guaranteed complete performance transcription.
```

## V2 Phase 4 — finish/audit

```text
Integrate timing into practice highlighting. Since audio is not retained, require reselecting a file for later playback or provide silent timeline practice. Do not promise persistent audio playback without a source.
Settings show capabilities, privacy, provider health and optional model download size/version/license/integrity/removal. Disabling AI never disables V1.
Audit microphone lifecycle, model latency/memory/battery, temporary audio cleanup, malformed responses, cancellation, offline fallbacks, destructive merges and secret exposure. Test on physical devices. Preserve conflicts and manual edits.
```

## Optional subagents

```text
Use subagents if available: architecture/types, SQLite/migrations, viewer/editor, practice/PDF, portable backups, Firebase/rules/sync, V2 model integration.
Agree on shared interfaces and non-overlapping file ownership first. One coordinator merges changes and runs all tests. Parallelize independent work, not shared-schema edits. Report costs/limitations and do not assume subagents fix correctness automatically.
```

## Key configuration checklist

- Local SQLite, editing, PDFs and portable backups: no cloud API keys.
- Firebase optional cloud: public client configuration plus authentication and restrictive rules.
- Free local models: no paid-service key, but runtime/license/download requirements apply.
- Remote secret-based AI: server-side secrets only; explicit optional consent.

## Long-term safety

The app must remain locally usable when cloud or AI is unavailable. No provider or phone storage guarantees two-year durability by itself. Export independent backups, verify restores periodically and preserve the backup schema/migrations in source control. Keep install/build instructions and test compatibility when upgrading OS/Expo versions.
