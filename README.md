# VisemeVoice

A Python desktop application that animates lip-sync visemes (mouth shapes) in real time while speaking user-entered text aloud using Google Text-to-Speech.

---

## What It Does

1. You type any text into the input field and click **Speak**.
2. The app generates an MP3 audio file using Google TTS and plays it back.
3. Simultaneously, it animates a face graphic on screen, cycling through mouth-shape images (visemes) that correspond to each letter in the text — one frame every 100 ms.

---

## Project Structure

```
CPAD/
├── main.py          # All application logic (GUI, TTS, viseme animation)
├── Lips/            # 14 PNG mouth-shape assets (visemes + base face)
│   ├── face.png
│   ├── A_E_I.png
│   ├── B_M_P.png
│   ├── C_D_G_K_N_R_S_T_X_Y_Z.png
│   ├── CH_J_SH.png
│   ├── EE.png
│   ├── F_V.png
│   ├── L.png
│   ├── N.png
│   ├── O.png
│   ├── Q_W.png
│   ├── TH.png
│   └── U.png
└── Audio/           # Auto-created; generated MP3 files saved here
```

---

## Requirements

- Python 3.7+
- An internet connection (gTTS calls the Google TTS API)

### Install dependencies

```bash
pip install pillow gtts pygame
```

| Package  | Purpose                        |
|----------|--------------------------------|
| Pillow   | Image loading and compositing  |
| gTTS     | Google Text-to-Speech synthesis|
| pygame   | MP3 audio playback             |

> `tkinter` ships with standard Python on Windows and macOS. On Linux, install it with `sudo apt install python3-tk`.

---

## How to Run

### Step 1 — Clone the repository

```bash
git clone https://github.com/arpitha93045/CPAD.git
cd CPAD
```

### Step 2 — Check Python is installed

```bash
python --version
# Should print Python 3.7 or higher
```

If Python is not installed, download it from [python.org](https://www.python.org/downloads/).

On Linux, also install tkinter if it is missing:

```bash
sudo apt install python3-tk
```

### Step 3 — Install dependencies

```bash
pip install pillow gtts pygame
```

### Step 4 — Fix the image paths (one-time setup)

> **Skip this step if you are on Windows and cloned the repo to `C:/Assignment/VISEME/`.**

The `image_map` dictionary in `main.py` has hardcoded Windows paths. Open `main.py` and replace every path like:

```python
"A": "C:/Assignment/VISEME/Lips/A_E_I.png",
```

with a relative path:

```python
"A": "./Lips/A_E_I.png",
```

Do this for all 40+ entries including the `"face"` key at the bottom. A quick find-and-replace works:
- Find: `C:/Assignment/VISEME/Lips/`
- Replace with: `./Lips/`

### Step 5 — Run the application

```bash
python main.py
```

A 600×400 window titled **"Modern Image Viewer"** will open.

### Step 6 — Use the app

1. Click the **"Enter text:"** field and type any word or sentence.
2. Click the **Speak** button.
3. The app will:
   - Generate an MP3 file and save it to the `Audio/` folder (created automatically).
   - Animate mouth shapes on the face graphic — one shape per letter, updated every 100 ms.
   - Play the generated speech audio via pygame.

> **Note:** An internet connection is required — gTTS sends the text to the Google TTS API to generate audio.

---

## How It Works

### Viseme mapping

Each letter (A–Z, upper and lower case) maps to one of the 14 mouth-shape PNGs. Phonetically similar sounds share the same image — for example, B, M, and P all produce the same closed-lip shape (`B_M_P.png`), while vowels A, E, I share the wide-mouth shape (`A_E_I.png`).

### Animation pipeline

```
User types text
      │
      ▼
speech_update()  ──►  gTTS generates MP3  ──►  saved to Audio/
      │
      ▼
VISEME(text)  ──►  filters text to mapped letters
      │
      ▼
display_images()  ──►  loads face.png + overlays viseme image
                        at position (113, 178) using PIL paste
                        shows frame, schedules next via app.after(100ms)
      │
      ▼
play_speech()  ──►  pygame plays latest MP3
```

---

## Known Issues

- **Hardcoded Windows paths** — the `image_map` paths must be updated to relative paths before the app works cross-platform (see setup step above).
- **Final frame error** — `display_images()` tries to open an empty string path after the last letter, which will throw an exception. The animation still works; only the final "rest" frame is broken.
- **No requirements.txt** — dependencies must be installed manually (listed above).
- **Audio accumulation** — each "Speak" click saves a new MP3; the `Audio/` folder grows indefinitely.

---

## Technologies Used

| Technology | Role |
|---|---|
| Python 3 | Core language |
| tkinter / ttk | Desktop GUI framework |
| Pillow (PIL) | Image loading, resizing, compositing |
| gTTS | Text-to-speech via Google API |
| pygame | Audio playback |
