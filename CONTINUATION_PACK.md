# ClipMind — Master Continuation Pack

**Document type:** Master project continuity record  
**Project:** ClipMind  
**Repository:** https://github.com/afghanbark573-rgb/Clipmind  
**Live application:** https://afghanbark573-rgb.github.io/Clipmind/  
**Default branch:** `main`  
**Application entry point:** `index.html`  
**Current app version label:** `v0.16.4`  
**Pack created:** 2026-11-23  
**Pack branch:** `continuity/clipmind-master-pack`

> This file is the durable handover source for ClipMind. Update it after meaningful milestones. Do not rely on chat history alone. Keep it inside the ClipMind repository; do not mix it with the AI Development Agent project's records.

---

## 1. Project identity and purpose

ClipMind is a browser-based, mobile-first AI video clipping application. Its intended workflow:

1. Select a local video from the device.
2. Extract audio locally.
3. Transcribe speech and generate timestamped transcript segments.
4. Identify a small set of genuinely useful, interesting moments.
5. Explain why each moment stands out.
6. Let the user review and edit start/end boundaries.
7. Preview and export one clip or a batch of clips.
8. Add subtitles as a later capability, with careful attention to timestamp precision.
9. Maintain a separate audio-only extraction workflow as a future roadmap item.

**Core product principle: QUALITY OVER QUANTITY.** A few strong, contextually complete moments are better than many weak suggestions. Never invent speech, speakers, evidence, or context to make a highlight look better.

## 2. User environment and working constraints

- Primary device: Xiaomi 14, Android, Chrome mobile.
- Development access: GitHub mobile interface.
- No desktop or terminal assumed available.
- Prefer complete, mobile-friendly instructions and changes that can be made directly through GitHub.
- The user expects the assistant to act as a critical senior/principal engineer and research partner, not simply agree with every proposed change.
- Explain risks, trade-offs and uncertainty before implementation.
- Do not claim a change is live, deployed, or device-tested unless it has actually been verified.

## 3. Repository and deployment facts

- Application repository: `afghanbark573-rgb/Clipmind`
- Live Pages URL: https://afghanbark573-rgb.github.io/Clipmind/
- Default branch: `main`
- Main application file: root `index.html`
- Preserve the existing `ffmpeg/` directory and worker assets. Do not replace or remove them during source updates.
- The app is hosted using GitHub Pages.
- The application source repository is authoritative for code that exists.
- This continuation pack is authoritative for recorded decisions, requirements, project history and next steps. If pack and source disagree, investigate and reconcile; do not blindly trust either one.

### Continuity branch

- Dedicated branch for this document: `continuity/clipmind-master-pack`
- Master pack path: `CONTINUATION_PACK.md`
- Keep continuity documentation changes isolated from application source changes. Application changes should use their own feature branches.
- A continuity branch is a documentation checkpoint, not a release branch. Do not merge it into `main` unless the user explicitly wants the pack on the default branch.

## 4. Project separation

ClipMind and the AI Development Agent are separate projects.

- ClipMind code, decisions, research, checkpoints and continuation pack belong in the ClipMind repository.
- AI Development Agent code, decisions, research, checkpoints and continuation pack belong in that project's own repository.
- Do not mix their source code, continuity files, roadmaps or decisions.
- The AI Development Agent project was previously paused while ClipMind improvements were prioritized. Do not restart agent development unless the user asks.
- No separate repository is required just to hold a continuity pack. Each project's pack belongs inside that project's own repository.

## 5. Development operating agreement — mandatory

The user has requested that ClipMind follow the same discussion-first working rules as Northstar Player.

### Rule 1 — Discussion before updating

Before making code changes:
1. Understand the reported problem and reproduce/inspect the relevant source where possible.
2. Discuss the root cause, proposed solution, alternatives and risks.
3. State exactly what will change and what will remain untouched.
4. Wait for the user's approval before implementation.

Do not treat a general complaint or an uploaded screenshot as automatic approval to start editing. The user must approve the proposed plan.

### Rule 2 — Lock the plan

Once approved, record the agreed scope as a locked plan in the project record or the relevant work discussion. Do not silently expand or alter scope.

If implementation reveals a materially different root cause or requires a change to the locked plan, stop and discuss it first.

### Rule 3 — Separate discussion, implementation and verification

Report these stages distinctly:
- **Discussed:** problem and options reviewed.
- **Locked:** user-approved plan.
- **Implemented:** source changes committed.
- **Merged:** changes integrated into `main`.
- **Deployed:** live Pages site contains the new source.
- **Device-tested:** user or assistant has verified actual behaviour on a real device/browser.

Never use these labels interchangeably.

### Rule 4 — Verification

For code changes, perform the checks relevant to the change:
- inspect the final diff;
- validate inline JavaScript syntax;
- check for duplicate HTML IDs and broken element references;
- inspect preservation of existing functions and assets;
- run targeted tests where tooling permits;
- verify the live GitHub Pages version separately;
- request real-device confirmation when browser/device behaviour cannot be tested directly.

Passing syntax checks does not prove runtime correctness.

### Rule 5 — Checkpoints

After each meaningful milestone:
- update this master pack;
- record completed work and commit/PR references;
- record unresolved issues and regressions;
- preserve locked decisions;
- state the next recommended task.

Avoid noisy updates for tiny cosmetic edits. Prefer coherent, useful bundles, but never bundle unrelated risky changes merely to make a larger release.

### Rule 6 — Preserve known-good behaviour

Do not casually rewrite stable architecture or remove working capabilities. Inspect the current source before editing. Keep a clear rollback point and distinguish confirmed working baselines from unverified changes.

---

## 6. Product and engineering principles

1. Quality over quantity in highlight generation.
2. Contextual completeness matters: a passage should express a complete idea without depending on missing earlier/later speech unless that dependency is clearly disclosed.
3. Preserve natural repetition when it is meaningful; do not over-deduplicate.
4. Avoid topic contamination between adjacent passages.
5. Do not infer visual context from audio-only transcription.
6. Do not invent speaker identities or diarization.
7. Whisper timestamps are phrase/chunk-level envelopes, not word-level timestamps.
8. Keep processing local in the browser where the existing architecture allows it; no external AI API is currently part of the core pipeline.
9. Mobile-first interface: compact, aligned, readable, low clutter, minimal unnecessary controls and vertical space.
10. Avoid nested scrollable containers where they cause poor mobile performance or confusing gestures.
11. Do not introduce fake progress percentages or unsupported time estimates.

## 7. Technical architecture

### Main technologies

- Transformers.js v3.7.2.
- Whisper ONNX Runtime WASM/CPU.
- Quantized model inference (q8).
- FFmpeg: `@ffmpeg/ffmpeg@0.12.15`.
- FFmpeg core: `@ffmpeg/core@0.12.10`.
- FFmpeg worker assets are under the existing `ffmpeg/` directory. Preserve them.

### Whisper models

- `onnx-community/whisper-base.en` — default English model in the accuracy experiment; larger/slower.
- `whisper-tiny.en` — smaller/faster English option.
- `whisper-tiny` — multilingual option.

WASM/CPU is the preferred deterministic inference path. WebGPU was tested previously and performed worse on the target device.

### Audio extraction

The established FFmpeg extraction command pattern:

```bash
-ss [start] -i [video] -t [duration] -vn -ac 1 -ar 16000 -c:a pcm_s16le -f wav
```

This produces mono 16 kHz WAV suitable for Whisper.

### Transcription pipeline

- Converts audio to mono 16 kHz Float32Array.
- **Method B (primary):** full-range Transformers.js long-audio pipeline; internal chunks around 30 seconds with approximately 5-second stride.
- **Method A (fallback/recovery):** sequential windows around 30 seconds with approximately 15-second stride; retries empty windows with longer context.
- Empty-section recovery and beam-search retry improvements were introduced historically.
- Phrase/chunk timestamps are not word-level timestamps.

### Clip export

- Uses MediaRecorder, canvas.captureStream(30), and video.captureStream() audio tracks.
- Attempts MP4 AVC/AAC and falls back to WebM VP9/Opus.
- Batch export and multi-clip preview exist.
- Preserve export verification and format fallback logic.

## 8. Highlight quality research

### Context Dependency Benchmark

Research was undertaken to identify whether a candidate passage:
- is self-contained;
- requires missing earlier/later context;
- is ambiguous and needs human review;
- depends on question/answer structure or connective continuation.

The v3.3 exploratory benchmark recorded:
- 17 cases total (12 metric cases and 5 human-review cases, as previously documented).
- Labels: `required_context_missing`, `self_contained`, `ambiguous_human_review`.
- Exploratory metrics: precision 50%, recall 40%, specificity 71.4%, accuracy 58.3%.

These are small-sample exploratory results, not validated production performance claims.

Known research insights:
- Question/answer dependency flags can help.
- Rhetorical questions are not automatically Q&A.
- Connector continuations and topic transitions are difficult.
- Human review remains important.

### Weak or sensitive historical functions

Previous audits identified these as weak or sensitive and not to be changed randomly:
- `highlightStory`
- `classifyMoment`
- `buildPassageReason`

Past issues included label inconsistency, cross-topic contamination and explanations that did not always match the exact passage. Any future work here should begin with evidence, test cases and an agreed plan rather than arbitrary threshold changes.

## 9. Current product functionality — historical known capabilities

The following features have been implemented and tested at various points in the project's history. Their current runtime status must be rechecked before relying on them after major source changes.

- Local video selection.
- FFmpeg audio extraction.
- Whisper transcription.
- Empty-section recovery.
- Timestamped transcript chunks.
- Highlight generation and category/title/reason output.
- Editable highlight timestamps.
- Video preview.
- Batch selection and export.
- Multi-clip preview.
- MP4/WebM fallback.
- Web Worker transcription.
- Device/browser readiness checks.
- Audio diagnostics and diagnostic report copying.
- Export verification logic.

Known past fixes:
- v0.8.7 removed nested scrollable transcript regions from suggestion cards. User tested on Xiaomi 14 and confirmed sluggishness after transcription was resolved.
- v0.14.1 corrected landscape export.
- v0.15.1 had landscape/portrait/batch export confirmation.
- v0.13.5 improved timer input cursor/alignment.
- A transcript-only duplication/misrecognition issue was previously observed around 02:16–02:33 in a 4:42 educational video: repeated phrase and “cars” instead of “carbs”. Audio itself was fine. Do not assume fixed without retest.

## 10. Version history and baseline

The project has passed through multiple development versions, including v0.4, v0.5.x, v0.6.x, v0.7.x, v0.8.x, v0.13.x, v0.14.x, v0.15.x and v0.16.x.

Important corrections:
- The user explicitly confirmed **v0.16.4** as the latest completed build baseline.
- **v0.16.5 was not built.** Do not describe it as a release or restore an old artifact and call it current.
- The app version label remains **v0.16.4** until a meaningful versioned update is completed.
- Recent UI commits have been made to `main` without changing the version label. They are source changes, not a new named release.
- Keep the distinction between the last confirmed app release baseline and later unverified UI commits.

## 11. UI design system and locked preferences

### Theme

Graphite Orange:
- Background: `#171717`
- Cards: `#242424`
- Secondary surfaces: `#303030`
- Primary text: `#F5F5F5`
- Secondary text: `#A3A3A3`
- Accent: `#F59E45`
- Borders: `#414141`

### Global design principles

- Mobile-first, compact and clean.
- Avoid oversized full-width controls when a compact control can do the job.
- Avoid “brick on brick” layouts that waste vertical space.
- Prefer paired controls side-by-side when readable and usable.
- Collapsible controls should remain fixed in position when their content opens/closes.
- Only one member of a related disclosure group should be open at a time.
- Opening a disclosure should expand the containing frame naturally; do not float content over neighbouring sections.
- Horizontal carousels/rails must have their own independent scrolling line and must not appear attached to the outer page frame.
- Keep cards aligned with their parent frame; prevent transcript text and detail content from overflowing.
- Use soft chevron indicators rather than plus/minus symbols.
- Avoid nested scroll regions that compete for touch gestures.

### Highlight review UX

Previously agreed requirements:
- Before revealing all highlights, use a compact stacked/book-deck style preview rather than a grid of every highlight.
- Do not display every full highlight card before the user asks to show all.
- Expanded full highlight cards should be horizontal, swipeable and aligned to the available frame width.
- Each full card should be an individual, fitting card.
- Preserve the quality explanation, transcript passage, editable start/end, AI suggested range, clip duration, Preview, Use for clip, Copy and batch selection.
- Batch selection must remain available.
- Compact cards should conserve vertical space and not look like a stack of bulky bricks.
- A single global show/hide all highlights control is preferred over nested per-card expansion.

### Transcript UX

- Full transcript and timestamped segments should be clearly separated.
- The current agreed direction is a horizontal two-page layout:
  1. one page for the full recognized text;
  2. one page for timestamped segments.
- Each page should fit its own card/frame and have a usable independent vertical text scroll.
- Swipe horizontally between the two pages.
- Avoid opening transcript content as a floating overlay over the Highlights section.

### Create clips controls

- Start and end time fields remain editable.
- Large “Set start to current video time” and “Set end to current video time” buttons were removed in the latest planned UI change because they consumed too much space.
- A compact Reset range action remains.
- Duration presets (15, 30, 45, 60 seconds) remain.
- Output shape choices remain: 9:16 Portrait, 16:9 Landscape, 1:1 Square, 4:5 Feed, Original.
- Clip boundaries should remain valid and within video duration.

### Recognition settings

- Spoken language and English recognition model should be positioned at the top of the AI highlight finder section, directly below its heading.
- They should be easy to locate and should not be awkwardly detached or repeated.

## 12. Recent UI change history and source status

### PR #2 — compact highlights and readiness panel

- PR: https://github.com/afghanbark573-rgb/Clipmind/pull/2
- Branch: `feature/compact-horizontal-highlights`
- Merged to `main`.
- Intended changes: compact stacked preview rows; horizontally swipeable full highlight detail cards; transcript width constraints; collapsible Device & Browser Readiness.
- Source syntax was checked, but no actual Xiaomi device test was recorded for that exact PR.

### PR #5 — stacked highlight preview and mobile panels

- PR: https://github.com/afghanbark573-rgb/Clipmind/pull/5
- Branch: `feature/fix-highlight-stack-and-mobile-panels`
- Merged to `main`.
- Intended changes: stacked highlight preview; batch selection inside expanded cards; actual Highlight note collapsible; Recent progress/Audio diagnostics/Full transcript disclosure adjustments; full-video defaults for ranges.
- Source syntax and duplicate-ID checks passed at merge time.

### PR #6 — expanding diagnostics and mobile review layout

- PR: https://github.com/afghanbark573-rgb/Clipmind/pull/6
- Branch: `fix/disclosure-flow-highlight-width-language-card`
- Merged to `main`.
- Intended changes: disclosures in normal page flow; progress and diagnostics not forcibly disabled during analysis; highlight cards constrained to full width; recognition settings separated; more robust playhead clock synchronization.
- Source syntax and duplicate-ID checks passed at merge time.
- User's subsequent screenshots showed remaining issues: oversized controls, shifting disclosure buttons, transcript layout and card sizing.

### PR #7 — compact controls, fixed tabs and horizontal transcript

- PR: https://github.com/afghanbark573-rgb/Clipmind/pull/7
- Branch: `fix/compact-range-stable-tabs-transcript-rail`
- Merged to `main`.
- Merge commit: `7c88e1216b148ec8de4963cbe1ce7d7c2e91ed0a`.
- Main file blob verified after merge: `408ed7415360fb5a9e2f7ab9f533fca9c9fd5685`.
- Changes:
  - Removed large Set start/Set end to current video time controls.
  - Retained a compact Reset range action.
  - Moved recognition settings above AI action buttons and below AI finder heading.
  - Replaced moving native disclosures with a fixed tab strip and one content panel.
  - Rebuilt transcript as a horizontal two-page rail: full text and timestamped segments.
  - Each transcript text area has its own vertical scrolling region.
- Source checks after merge:
  - Inline JavaScript syntax passed.
  - No duplicate HTML IDs.
  - Both large playhead buttons absent in source.
  - Compact reset present.
  - Recognition settings appear before AI action grid.
  - Stable tab and horizontal transcript rail code present.
- **Deployment caveat:** a live GitHub Pages scrape immediately after the merge still returned older HTML without the new markers. The latest UI changes must be checked again on Pages before declaring them live.
- **Device test caveat:** no post-PR #7 Xiaomi 14 verification has been recorded.

## 13. Current known issues and next priorities

### Immediate verification — do first

1. Confirm GitHub Pages has deployed PR #7 changes.
2. On Xiaomi 14 / Chrome, verify the two large playhead controls are gone and Reset range is compact.
3. Verify recognition settings appear at the top of the AI finder.
4. Verify the three tabs remain fixed when switching.
5. Verify only one panel is visible at a time and content stays inside its frame.
6. Verify transcript horizontal swipe switches between full text and timestamped segments; each page scrolls vertically without moving the outer page unexpectedly.
7. Verify full highlight cards retain correct width/alignment and no horizontal overflow.
8. Test a real video to ensure editing, selecting and exporting clips still work after UI changes.

### Next broader work

- Complete a full mobile-first spacing/alignment audit across the whole application.
- Check for oversized controls, unnecessary helper text, duplicated labels, inconsistent card padding and vertical gaps.
- Group related controls side-by-side only where the mobile viewport allows clear touch targets.
- Keep the interface compact without making controls cramped or hard to tap.
- Review language/model settings and highlight finder visual hierarchy.
- Recheck the Create your clips controls and consider whether duration presets and reset action can be grouped more cleanly.
- Do not begin another isolated cosmetic pass until the latest changes are device-tested.

### Product/research priorities after UI stability

- Revisit highlight quality and context dependency with a test set before changing generation logic.
- Improve category/title consistency and explanation grounding.
- Investigate historical transcript duplication/misrecognition only with a reproducible source video and transcript/audio comparison.
- Subtitle system is a future feature; phrase-level timestamps are insufficient for accurate word-by-word karaoke subtitles.
- Audio-only extractor remains a future separate workflow (MP3, WAV, M4A).
- Engineering readiness layer: device/browser preflight, one-click diagnostics, export verification and regression test set.

## 14. Important implementation cautions

- Existing source functions and IDs are coupled. Inspect before moving DOM nodes or changing markup.
- Do not add a second diagnostic/report/export verification implementation without first checking existing functions. Known functions include `copyDiagnosticText()`, `verifyExportBlob()` and `runReadinessCheck()`.
- Preserve `ffmpeg/` and worker files.
- Avoid global CSS overrides that cause card sizing, disclosure, or horizontal rail regressions.
- Be especially careful with `min-width:0`, `box-sizing:border-box`, flex basis and overflow rules in mobile horizontal rails.
- A passing syntax check does not validate DOM lifecycle, event handling, playback, transcription, or export.
- Avoid editing the application source during a continuity-pack-only task.

## 15. Current checkpoint

**Checkpoint date:** 2026-11-23

**Project state:**
- Application repo remains `afghanbark573-rgb/Clipmind`.
- Last confirmed app baseline label: v0.16.4.
- PR #7 is merged to `main`.
- Main source includes compact Reset range, recognition settings placement, stable analysis tabs and horizontal two-page transcript rail.
- Latest Pages scrape after PR #7 still appeared to serve old HTML; deployment is unconfirmed.
- Latest Xiaomi 14 visual/runtime verification is pending.
- AI Development Agent remains separate and paused unless explicitly resumed.

**Next-chat instruction:**

When the user says, “Continue ClipMind from the last checkpoint,” first read this file from the ClipMind repository. Then inspect the current `main` source and recent commits/branches. Verify the live Pages deployment. Do not immediately change code. Start by discussing the next issue and proposing a specific plan; wait for approval before implementation.

---

## 16. Change log for this master pack

| Date | Update |
|---|---|
| 2026-11-23 | Initial master continuation pack created, consolidating project identity, architecture, engineering rules, UI decisions, research, history, current source state and next actions. |


---

## 17. Current checkpoint — 2026-11-24 (supersedes older immediate-status notes)

This section records only work and evidence available at the 2026-11-24 handover. Earlier history remains historical; where an older “current” status conflicts with this section, this newer checkpoint takes precedence.

### Active mobile usability work

- Production application repository: `afghanbark573-rgb/Clipmind`.
- Production `main` remains protected; no production merge or deployment is authorized by this checkpoint.
- Test repository: `afghanbark573-rgb/ClipMind-Mobile-Test`.
- Test app: https://afghanbark573-rgb.github.io/ClipMind-Mobile-Test/
- Feature branch in production repo: `fix/mobile-usability-repair`.
- Draft PR: https://github.com/afghanbark573-rgb/Clipmind/pull/9
- PR #9 title: “Fix four mobile usability issues”.
- Last recorded feature branch head: `3d2ceee467efee1568284a90d68af04b96e20e0e`.
- Last recorded production `main` base SHA: `64789e7f38a7409346ae524c7e8311c06ed516b0`.
- Last recorded compare: feature branch 7 commits ahead, 0 behind; changed paths were `index.html` and `ffmpeg/worker.js`.
- Production `main` was not changed by this work.

### FFmpeg worker repair and Android end-to-end test

The FFmpeg worker was vendored from the exact `@ffmpeg/ffmpeg@0.12.15` ESM worker implementation into same-origin `ffmpeg/worker.js`, with its companion `const.js` and `errors.js`. This addresses the cross-origin Worker construction issue caused by combining the CDN-loaded FFmpeg module with a local same-origin worker URL.

User-tested on Xiaomi 14 / Android Chrome using a 20 MB, 4:42 (282-second), 1920×1080 video named `1000051037.mp4`:

- Preflight: 10 checks passed.
- FFmpeg fast extraction passed.
- Extracted WAV: 9,024,078 bytes; decoded duration 282 seconds.
- Source audio: 48 kHz mono; Whisper input: 4,512,000 samples at 16 kHz.
- Whisper model: `onnx-community/whisper-base.en`, WASM/CPU.
- Primary Method B completed in 110.61 seconds and produced 63 usable timestamp chunks (3,686 raw characters).
- Method A fallback was not invoked.
- 63 valid timestamp chunks; 0 removed.
- Timestamp span union was 261 seconds (92.6% of video duration); this is not a verified speech-coverage metric.
- User confirmed highlight generation, clip preview and export worked.

This establishes a successful end-to-end device test for this particular video, device and browser. It does not establish universal compatibility or performance.

### Highlight Copy and Copy All implementation

Implemented in the test build and mirrored to the feature branch:

- Shared `formatHighlightReport(g,index,passage)` serializer for individual Copy and Copy All.
- Report fields include highlight number, category, title, reason, current start/end, duration, original AI suggested range, current edited range, source filename and full passage.
- Added a `suggestion-passage` class to the passage element.
- Added a Copy All Highlights action when the expanded Show All Highlights panel is open.
- Copy All iterates every generated suggestion, not only batch-selected suggestions; it reads each card's current edited timestamps, validates them, collects the passage and separates reports with newline characters.
- Status/error feedback is present.

Recorded source identifiers:
- Test repo commit: `053b809f903a82ded3259eb0826021c6c77691d5`.
- Test app index blob: `d8b36ef08a20bb2895d6d480d6c41e63226fa6ed`.
- Feature branch commit: `3d2ceee467efee1568284a90d68af04b96e20e0e`.
- The test and feature branch `index.html` were recorded as identical blobs.
- Test URL used: https://afghanbark573-rgb.github.io/ClipMind-Mobile-Test/?v=053b809f
- PR #9 checkpoint comment for this implementation: comment ID `7138459827`.

Static source checks passed for the shared formatter, individual Copy integration, passage selector, Copy All action, iteration across suggestions, timestamp validation, newline separators and report metadata. These are source checks, not runtime verification.

### Latest user-provided Copy / Copy All output — 2026-11-24

The user supplied actual Android copy results:

- Copy All output contained three highlights (01, 02 and 03).
- Each report included category, title, reason, start/end, duration, AI suggested range, current edited range, source filename and a full passage.
- The individual Copy output for Highlight 02 was also supplied.
- A discrepancy was observed for Highlight 02:
  - Individual Copy passage begins: “You may have to curb your chronic stress.”
  - Copy All passage begins: “Want to live a long life? You may have to curb your chronic stress.”
- The rest of the passage content overlaps, but Copy All includes an additional preceding sentence.
- This indicates the two actions may be reading different passage sources/ranges for the same highlight. Root cause is not yet established.

**Verification status:**
- User has supplied real-device output for individual Copy and Copy All.
- The report formatting and metadata are visibly present in both outputs.
- Do not mark passage consistency as passed.
- Do not claim the copy feature is fully verified until the Highlight 02 discrepancy is investigated and the user confirms the corrected behaviour.
- Do not patch this discrepancy without first inspecting the exact source and discussing the cause and proposed change.

### Current locked development direction and next actions

The previously agreed quality-first, balanced sequence remains:

1. Investigate the Highlight 02 individual-vs-Copy-All passage discrepancy. Inspect exact source paths and selectors before proposing a fix. Discuss findings and proposed scope before changing code.
2. After copy consistency is resolved and device-tested, proceed to the transcript interface: Full Transcript and Timestamped Transcript as two equal-size horizontal pages/tabs, with synchronized active indicator, independent vertical scrolling and a copy action for each view. Avoid nested-scroll traps.
3. Put Start, End and Reset Range in one row on mobile where width allows, with a narrow-screen fallback.
4. Make exported clips a horizontal swipeable gallery while preserving preview, download and verification.
5. Ensure only one player is active across source preview, highlight preview and exported-clip players; starting one pauses the others. Opening or scrolling must not autoplay.
6. Run regression checks and record each meaningful stage as a separate checkpoint.

Do not begin steps 2–5 until step 1 has been discussed, agreed and completed. Do not add extra scope.

### Mandatory continuity rule clarified on 2026-11-24

The user explicitly clarified:

> “update what we discuss no more no less.”

Apply this to code, plans and project records:
- Record only what was actually discussed, approved, implemented or tested.
- Do not add unapproved features, assumptions or decisions.
- Do not silently alter previously agreed requirements.
- Keep proposed, locked, implemented, merged, deployed and device-tested states distinct.
- If a detail is uncertain or conflicting, label it unresolved rather than filling the gap.
- Ask before expanding scope.

### New-chat recovery procedure

When starting a new chat, the user can say:

“Continue ClipMind from the latest checkpoint. Read the master continuation pack from the GitHub continuity branch, inspect the current repositories/branches and source, and continue from the recorded next action.”

Then:
1. Read `CONTINUATION_PACK.md` from branch `continuity/clipmind-master-pack` in `afghanbark573-rgb/Clipmind`.
2. Inspect PR #9, its latest head and comments.
3. Inspect the test repository and test build before making claims about the running code.
4. Preserve the production protection rule.
5. Start with the unresolved Highlight 02 passage discrepancy; discuss before implementing.
6. Do not rely on prior ChatGPT chats or this chat's memory when the repository record is available.

The GitHub repository is the durable project record. Deleting ChatGPT conversations should not remove this repository, its branches, commits, PR discussion or continuation pack. This cannot guarantee that every external account or hosting service will always remain available, so the GitHub repository and its history remain the project's source of continuity.

---

## 18. Change log

| Date | Update |
|---|---|
| 2026-11-23 | Initial master continuation pack created, consolidating project identity, architecture, engineering rules, UI decisions, research, history, current source state and next actions. |
| 2026-11-24 | Added Android end-to-end test evidence, FFmpeg same-origin worker repair, Copy/Copy All implementation and source identifiers, actual user-provided copy outputs, unresolved Highlight 02 passage discrepancy, locked next actions, and the strict “no more, no less” continuity rule. |

**End of master continuation pack.**
