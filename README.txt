ClipMind v0.4 — Fast AI Highlighting (Android-friendly web prototype)

HOW TO USE
1. Extract this ZIP on your Android phone.
2. Open index.html in a recent version of Chrome. If local-file restrictions appear, use a simple HTTPS static host instead.
3. Select a video.
4. Tap Prepare AI tools. Internet is needed the first time to download the speech model and FFmpeg WebAssembly extractor.
5. When ready, tap “Extract audio and find highlights”. This attempts to extract audio directly from the file, without playing the video in real time.
6. If extraction fails for the video format or browser, tap “Use real-time fallback instead”.
7. Review suggested timestamps, select one, then create a clip.

WHAT CHANGED FROM v0.3.2
- Adds FFmpeg WebAssembly to extract audio directly from the selected video.
- Keeps the previous real-time audio capture as a fallback.
- Speech recognition and highlight ranking remain local in the browser after model download.

IMPORTANT LIMITATIONS
- First use downloads a relatively large FFmpeg engine as well as the Whisper model; keep an internet connection during setup.
- Direct extraction is intended for files under 500 MB. Large videos can still consume substantial phone memory.
- Extraction speed depends on codec, resolution, browser and phone temperature. It is not guaranteed to be faster for every file.
- If the browser blocks module loading from file://, open the app through an HTTPS static host.
- English Whisper model only. Suggestions are ranked using transcript heuristics; no visual scene understanding, face tracking, subtitles or guaranteed viral ranking.
- Clip export still uses browser MediaRecorder and may produce WebM rather than MP4, depending on Chrome support.
- Keep Chrome open during analysis. Avoid switching apps or locking the phone.
