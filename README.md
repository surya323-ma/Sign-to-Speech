# Sign to Speech — 

Real-time sign language recognition that converts hand gestures into natural spoken sentences, with live facial emotion detection overlaid on the camera feed.

## Features

- **Hand gesture recognition** using MediaPipe Hands — tracks finger positions and classifies signs in real time
- **Sentence generation** via Claude (Anthropic API) — turns a sequence of signed words into a natural English sentence, with an offline fallback if no API key is set
- **Text-to-speech output** — speaks each locked word and the final sentence aloud
- **Facial emotion detection** using DeepFace — shows live emotion scores (Happy, Sad, Neutral, Angry, Questioning, Skeptical)
- **Live camera overlay UI** — gesture pill tray, hold-to-lock progress bar, sentence banner, and status panel, all rendered on the video feed

## Supported signs

| Gesture | Word |
|---|---|
| Open hand | TECHNOLOGY |
| Peace / V sign | USE |
| Point index finger | FOR |
| Thumbs up | HELP |
| Index + pinky ("rock") | NOT |
| Closed fist | WAR |
| Both hands open | HELLO |

Additional signs (LIFE, IMPROVE, WORLD, LOVE, GOOD, LEARN, CARE, VOICE, AI, HUMAN, SMART, THANK) are also mapped — see `GTABLE` in the script.

## Requirements

- Python 3.9–3.12 recommended (some dependencies may not have prebuilt wheels for very new Python versions)
- A working webcam
- Windows, macOS, or Linux

## Setup

1. Clone or download this repo
2. Run the script directly — dependencies install automatically on first run:

```bash
python sign_to_speech.py
```

On the first run it installs `opencv-python`, `mediapipe`, `numpy`, `pyttsx3`, `requests`, and `deepface`, then restarts itself automatically. This can take a minute or two.

### Optional: enable AI sentence generation

By default, sentences are built from a small offline fallback table. To use Claude for more natural sentence generation, set your API key before running:

**macOS / Linux**
```bash
export ANTHROPIC_API_KEY="your-key-here"
python sign_to_speech.py
```

**Windows (cmd)**
```powershell
set ANTHROPIC_API_KEY=your-key-here
python sign_to_speech.py
```

## Usage

1. Launch the script — a camera window titled **Tuba's Glasses** opens
2. Hold a sign steady for **1.5 seconds** to lock it in — it's added to the word list and spoken aloud
3. Lower your hand briefly before making the next sign
4. Hold **both hands open** for **2 seconds** to trigger the finish gesture — the full sentence is generated and spoken
5. Click **Clear** (top-right) or press `C` to reset
6. Press `Q` or `Esc` to quit

## How it works

- A background thread runs MediaPipe hand and face tracking on downscaled frames to keep the UI responsive
- A separate thread runs DeepFace emotion analysis periodically (every ~20 frames) on a small snapshot
- Finger states are converted into a 5-bit pattern (thumb, index, middle, ring, pinky) and matched against a gesture table
- A `Signs` state machine handles the hold-to-lock and gap-to-reset timing
- Locked words are sent to the LLM layer, which asynchronously requests a sentence from Claude (or falls back to a local template) and speaks it via `pyttsx3`

## Troubleshooting

- **`no webcam`** — close other apps using the camera (Zoom, Teams, etc.)
- **`ERROR: No matching distribution found for mediapipe==...`** — your Python version doesn't have a wheel for that pinned version; the script now installs the latest compatible `mediapipe` automatically
- **`can't open file ... No such file or directory`** — make sure `sign_to_speech.py` is actually saved in the folder you're running the command from
- First run is slow — this is expected while dependencies install

## License

Add your preferred license here (e.g. MIT).
