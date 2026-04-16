# Guided Tour — In Development

This folder contains the in-app guided tour for new users. It is **not loaded in production yet** and lives only on the `feature/guided-tour` branch until the work is complete.

## Plan

A ~3-minute quickstart that plays when a first-time visitor lands on the app, driving the UI programmatically while pre-rendered ElevenLabs narration plays over the top. Covers: add a widget → move/resize → bind a signal → hit Sim → save.

Later, a 10-minute expanded tour will add chapters for DBC import, marketplace, data logger, and advanced property editing.

## Architecture (planned)

```
public/tour/
├── README.md           ← this file
├── SCRIPT.md           ← narration script (draft, pre-audio)
├── tour.js             ← step engine, overlay renderer, fake cursor, audio sync
├── tour.css            ← spotlight + narration card + controls
├── steps.desktop.js    ← step definitions for desktop layout
├── steps.mobile.js     ← step definitions for mobile layout
└── audio/              ← pre-rendered ElevenLabs MP3s, one per chapter
    ├── 01-welcome.mp3
    ├── 02-add-widget.mp3
    ├── ...
```

## Build order

1. ✅ Scaffold directory + script draft (this commit)
2. Script review and lock the copy
3. Voice selection (Rachel / Adam / Jessica samples)
4. Audio generation from ElevenLabs
5. Step engine + overlay system
6. Step arrays for desktop and mobile
7. Hook into `public/index.html` behind a menu entry + first-run prompt
8. QA on real devices via Vercel preview URL
9. Merge to master once signed off
