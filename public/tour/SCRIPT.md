# Quickstart Tour Script — v1 draft

**Total length:** ~3 minutes, ~2,400 characters
**Target voice:** TBD — samples to audition: [Rachel (warm pro), Adam (neutral), Jessica (friendly)](https://elevenlabs.io/voice-library)
**Narration speed:** ElevenLabs default (1.0× for first pass; can bump to 1.05× if it feels sluggish)
**Custom pronunciations to define in ElevenLabs:** `RDM`, `CAN bus`, `DBC`, `ECU`, `LVGL`

---

## Chapter 1 — Welcome (15s, ~220 chars)

**On-screen action:** Fresh empty canvas. Soft spotlight on the app logo, then widens to the full workspace.

> Welcome to RDM Studio. This is where you design the dashboard your RDM-7 display will show. In the next three minutes I'll walk you through adding a widget, binding it to live data, and watching it come to life.

---

## Chapter 2 — Add a widget (30s, ~380 chars)

**On-screen action:** Spotlight opens on the Widgets palette (desktop) or the Widgets button in the bottom action bar (mobile). Fake cursor moves to the RPM Bar item. An RPM bar appears on the canvas, pre-positioned near the top.

> This sidebar holds every widget you can add to your dash. On mobile it's behind the Widgets button at the bottom. Let's grab an RPM bar — the strip that flashes as the engine revs. I'll drop one onto the canvas now. Eight hundred pixels wide, right where the driver can see it.

---

## Chapter 3 — Move and resize (35s, ~440 chars)

**On-screen action:** Widget is selected (white border glows). Desktop: fake cursor drags a corner handle. Mobile: spotlight shifts to the selection toolbar that's slid up above the action bar — the nudge arrows and W/H steppers pulse.

> With a widget selected you can move and resize it. On desktop, drag the corner handles. On mobile, a toolbar pops up above your thumbs — the arrows nudge by ten pixels each tap, and the W and H steppers resize. Precision without typing. Let me stretch this one out and line it up nicely.

---

## Chapter 4 — Bind a signal (45s, ~580 chars)

**On-screen action:** Spotlight moves to the Properties panel — on mobile it's the Properties button in the bottom action bar; on desktop it's the right sidebar's Properties tab. The "Assign Data Source" button pulses. Tapping it opens the Data Source picker with a short list of signals (RPM highlighted). Selecting RPM closes the picker and the widget now shows "→ RPM" as a label on the canvas.

> A widget is just a shape until you connect it to data. With the widget selected, open Properties — on mobile it's the Properties button at the bottom, on desktop it's the sidebar on the right. Right there at the top you'll see "Assign Data Source". One tap opens a picker of every signal your dashboard can receive. I'll pick RPM. Done — the widget now shows the signal it's listening to.

---

## Chapter 5 — Hit Sim (35s, ~440 chars)

**On-screen action:** The mobile Sim button (or desktop Sim pill) turns green. Then spotlight moves to the Test slider on the selection toolbar. Fake slider drag animates the RPM bar filling up in real time.

> Before you flash this to your RDM-7, test it. Tap Sim to feed realistic values into every signal. Or for a single widget, drag the Test slider I just opened — watch the RPM bar react. This is how you sanity-check a layout before it ever hits your dash.

---

## Chapter 6 — Save and outro (20s, ~320 chars)

**On-screen action:** Save button pulses. A success toast appears. Fade to a clean outro card with three follow-up chips: "Explore Marketplace", "Browse Help", "Start designing".

> Happy with it? Hit Save. Your layout is safe and you can come back any time. That's the basics. When you're ready, check the Marketplace for community dashboards, or the Help menu for deep-dive topics. Thanks for watching — now go build something great.

---

## Summary

| Chapter | Duration | Chars |
|---|---|---|
| 1. Welcome | 15s | ~220 |
| 2. Add a widget | 30s | ~380 |
| 3. Move and resize | 35s | ~440 |
| 4. Bind a signal | 45s | ~580 |
| 5. Hit Sim | 35s | ~440 |
| 6. Save and outro | 20s | ~320 |
| **Total** | **~3:00** | **~2,380** |

Fits ElevenLabs free tier (10,000 chars/month) with room for ~3× revisions in a single month. Zero ongoing cost after generation.

---

## Open questions for review

1. Tone — is this warm/conversational enough, or too casual for your brand? ("your RDM-7", "now go build something great")
2. Technical depth — should Chapter 4 explain *what* a CAN signal is, or assume that's part of the audience's background?
3. Mobile-first phrasing — the script mentions mobile affordances explicitly ("on mobile it's behind the Widgets button"). Alternative: two separate audio tracks, one per platform, so each version only references the relevant UI.
4. Outro CTAs — Marketplace and Help are the two I picked. Any others to surface here?
5. Voice gender/accent — any preference before I generate samples?
