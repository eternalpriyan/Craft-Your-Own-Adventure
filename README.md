# Craft Your Own Adventure

A choose-your-own-adventure game that uses [Craft Docs](https://www.craft.do/) as a persistent save file. AI generates the narrative, and every choice creates a branch in your Craft document—a living record of your adventure.

**[Play Online](https://eternalpriyan.github.io/Craft-Your-Own-Adventure)** or download `index.html` and run locally.

![Warm amber terminal aesthetic](https://img.shields.io/badge/aesthetic-retro%20terminal-ffb000)

## Features

- **Branching narratives** — Each choice creates a subpage in Craft. Your story tree is preserved and explorable.
- **AI-generated stories** — Uses Google Gemini API (2.5 Flash free tier, or paid models).
- **Edit & refresh** — Don't like the narrative? Edit it directly or regenerate with guidance.
- **Inline option editing** — Rename, delete, or add new options on the fly.
- **Dark/Light mode** — Toggle between warm amber terminal and light parchment themes.
- **Optimistic UI** — Instant feedback; Craft syncs in the background.
- **Single HTML file** — No build step. Just open and play.

## How It Works

```
┌─────────────────────────────────────────────────────────────┐
│  Craft Document                                              │
│  └── Story Title                                             │
│      ├── "Go north" (empty = unexplored)                     │
│      ├── "Go south" ← has content + child options            │
│      │   ├── "Cross the river"                               │
│      │   └── "Follow the path"                               │
│      └── "Stay here" (empty = unexplored)                    │
└─────────────────────────────────────────────────────────────┘
```

1. **Click an option** → Navigate to that page
2. **Empty page?** → AI generates narrative + new options
3. **Has content?** → Display it with available choices
4. **Type custom action** → Creates new branch with your text

## Setup

### 1. Gemini API Key (optional)
1. Go to [Google AI Studio](https://aistudio.google.com/apikey)
2. Create an API key
3. Free tier available with generous limits
4. **Leave blank** to use placeholder text instead of AI

### 2. Craft Connection URL
1. Open your Craft document
2. Go to **Share → Create Connection**
3. Enable **Read & Write** access
4. Copy the full API URL (looks like `https://connect.craft.do/links/xxx/api/v1`)

### Running the App
1. Open the app (online or local `index.html`)
2. Click **Enter** on the splash screen
3. Paste your Craft URL (and optionally Gemini API key)
4. Select an AI model and theme
5. Click **Save & Continue**

## Controls

| Action | How |
|--------|-----|
| Choose an option | Click it |
| Custom action | Type in the input and press Enter |
| Edit narrative | Hover narrative → click ✎ Edit |
| Regenerate narrative | Hover narrative → click ↻ Refresh |
| Edit option | Hover option → click ✎ |
| Delete option | Hover option → click ✕ |
| Add option | Click "+ New option" |
| Go back | Click ← Back in header |
| Refresh from Craft | Click ↻ Refresh in header |
| Change settings | Click ⚙ in header |

## AI Models (via Google Gemini)

- **Gemini 2.5 Flash** — Default, free tier with generous limits
- **Gemini 2.0 Flash** — Fast, reliable
- **Gemini 2.5 Pro** — Most capable (paid)

Or leave API key blank for **Placeholder mode** (lorem ipsum text).

## Technical Details

- **Single file**: All HTML, CSS, and JS in one file (~2300 lines)
- **No dependencies**: Uses vanilla JS, no frameworks
- **Fonts**: VT323 (retro) + IBM Plex Mono
- **Storage**: localStorage for credentials and current page
- **Craft API**: RESTful, block-based document structure

## Credits

Made by [Priyan Nithya](https://github.com/eternalpriyan) & Claude Code

Inspired by [Infocom](https://en.wikipedia.org/wiki/Infocom) text adventures.

---

[GitHub](https://github.com/eternalpriyan/Craft-Your-Own-Adventure) · [Buy me a coffee](https://buymeacoffee.com/npsoy)