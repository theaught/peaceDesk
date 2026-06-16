# Velma2 Demo — User Guide

## What Is This?

This is a single-file browser app that streams live microphone audio to Modulate's Velma2 API and displays the results in real time. It's styled as a fictional call center platform ("PeaceDesk") to simulate a realistic agent workspace.

---

## Opening the App

Open `velma2-demo.html` in a web browser. Chrome or Edge are recommended. Do **not** double-click the file to open it directly — mic access requires either:

- A local web server: run `python3 -m http.server` in the same folder, then visit `http://localhost:8000/velma2-demo.html`
- Any HTTPS-hosted URL (e.g., dropped onto a static hosting service)

If the mic button does nothing, this is almost certainly why.

---

## Layout Overview

The app has two panes:

- **Left — PeaceDesk**: the simulated call center view. Shows the live transcript, a Velma Alerts panel, and floating notification popups.
- **Right — Velma2 API Controls**: everything you configure before and during a session.

---

## Getting Started

### 1. Enter Your API Key
Paste your Velma2 API key into the **API Key** field in the top bar. The field is pre-filled with a default key — replace it with your own if needed.

### 2. Choose a Config
In the **Analysis Config** section on the right, one config is always available:

- **FraudConfig (default)** — a full-featured configuration with 20 behaviors, 16 conversation types, 9 participant roles, topic detection, and summary enabled.

You can also upload your own config:
- Click the **+ Add .json config** area (or drag and drop a `.json` file onto it)
- Uploaded configs appear as additional rows with a toggle
- Only one config can be active at a time — toggling one on turns the others off
- Toggling all configs off switches to **presets-only mode** (minimal config, only selected presets are sent)

### 3. Configure STT Signals
Below the config section, four signal toggles control what the speech engine enriches each transcript clip with:

| Signal | Default | Effect |
|---|---|---|
| Speaker Diarization | On | Labels each clip by speaker |
| Emotion Signal | Off | Adds emotion tag per clip |
| Accent Signal | Off | Detects speaker accent |
| Deepfake Signal | Off | Scores likelihood of synthetic voice |

> **Tip:** Disabling Speaker Diarization reduces transcription latency the most. Only enable the signals you actually need for a given session.

### 4. Select Behavior Presets (Optional)
Presets are additional behavior detectors loaded from the API. They appear as checkboxes below the STT Signals section. Check any you want appended to the active config's behavior list. Click **↻** to refresh the preset list if it fails to load.

---

## Running a Session

### Starting
Click the **microphone button** in the Live Microphone section. The browser will ask for mic permission on first use. Once granted:

- The call timer starts in the top bar of the left pane
- The status dot turns green and shows "Streaming microphone to Velma2…"
- The mic button turns red — click it again to stop

### During the Call
- **Transcript** (left, center): each clip appears as a chat bubble as it comes back from the API. Speaker 1 appears on the left, Speaker 2 on the right. If diarization is on, clips are labeled by speaker number; if participant roles are detected, the label updates to the role name (e.g., "Customer", "Support Specialist").
- **Velma Alerts** (left, right panel): insights accumulate here as the API returns them — behaviors detected, conversation type, participant roles, topics, sentiments, and a summary at the end.
- **Floating popups** (top-right of left pane): brief notifications appear for each event and auto-dismiss after a few seconds.
- **Event Log** (bottom of right pane): timestamped log of every event received, useful for debugging.

### Ending
Click the **End Call** button (appears in the call header once a session starts). This sends an end-of-stream signal to the API and waits for any final results (summary, remaining clips) before closing the connection.

Alternatively, click the mic button again to stop recording — the app will send the EOF signal and wait for final results the same way.

---

## Clearing the View

The **Clear** button (call header, top of left pane) resets the transcript, Velma Alerts panel, and popups without ending an active session. Useful between test runs.

---

## Reading the Results

### Transcript Clips
Each bubble shows:
- Speaker label or role name
- Timestamp (offset from call start)
- Emotion tag (if Emotion Signal is on and detected)
- Deepfake score badge (if Deepfake Signal is on and score is returned)

### Velma Alerts Panel
Cards are color-coded by type:

| Color | Type |
|---|---|
| Pink | Conversation Type |
| Amber | Behavior detected |
| Green | Topic |
| Blue | Sentiment |
| Purple | Summary |
| Cyan | Emotion |
| Red | High-risk behavior or deepfake alert |

High-risk behaviors (Vishing, Account Impersonation, AI Agent Manipulation, etc.) are highlighted in red with a ⚠ prefix.

Confidence scores are shown as a percentage with a small bar where available.

---

## Sharing the App

The file is fully self-contained — send the `.html` file directly. Recipients need:

1. Their own API key (they can type it into the API Key field)
2. Chrome or Edge (recommended)
3. To open it via a local server or HTTPS, not by double-clicking the file

---

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| Mic button does nothing | File opened as `file://` — use a local server instead |
| Presets show "Failed to load" | API key is missing or invalid |
| Transcript is slow | Normal — the API waits for utterance boundaries. Disabling Speaker Diarization helps. |
| Conversation type shows as "Type 59f3f656…" | The UUID returned by the API isn't in the active config's conversation_types list (common in presets-only mode) |
| No results after stopping | Check the Event Log for WebSocket errors |
