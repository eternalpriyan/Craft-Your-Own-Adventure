# Craft Your Own Adventure

In my younger days, my bookshelves groaned under the weight of **Choose Your Own Adventure** books, the **Time Machine** series, and the **Lone Wolf** saga. Each volume was a gateway to infinite possibilities. Those dog-eared paperbacks taught me that the best stories aren't just read—they're *lived*.

This project is a love letter to that golden era of interactive storytelling, blended with a tribute to my favorite PKM tool, **Craft Docs**. Just as those classic adventures let you forge your own path through mysterious worlds, this challenge invites you to craft your own journey—one block, one choice, one adventure at a time.

> ## **[Play Online](https://eternalpriyan.github.io/Craft-Your-Own-Adventure)** or download `index.html` and run locally.

# What’s This?

A choose-your-own-adventure game that uses a [Craft Doc](https://www.craft.do/) as a persistent save file. AI generates the narrative, and every choice creates a sub-page in your Craft document — a living record of your adventure.

## Features

- **Branching narratives** — Each choice creates a subpage in Craft. Your story tree is preserved and explorable.
- **AI-generated stories** — Uses Google Gemini API (2.5 Flash free tier, or paid models).
- **Edit & refresh** — Don't like the narrative? Edit it directly or regenerate with guidance.
- **Inline option editing** — Rename, delete, or add new options on the fly.
- **Dark/Light mode** — Toggle between warm amber terminal and light parchment themes.
- **Single HTML file** — No build step. Just open, add your API keys and play.

+ ## Setup - Getting Your Craft API and Gemini API Key

    ### 1. Get Your Free Gemini API Key (optional)

        1. Go to [Google AI Studio](https://aistudio.google.com/apikey)
        2. Create a project and an API key.
        3. Free tier available with generous limits.
        4. **You can also leave the field blank** to use placeholder text instead of AI

### 2. Craft Imagine API (needed)

        1. Create an empty Craft document
        2. Go to **Imagine → New API Connection → Connect Selected Documents**
        3. Click into your newly made API and **Add Document** (the empty Craft Doc you created earlier)
        4. Permission Level: **Read & Write.**
        5. Access Mode: **Public.**
        6. Copy the full API URL (looks like `https://connect.craft.do/links/xxx/api/v1`)

+ ## Running the App
    1. Open the app (online or local `index.html`)
    2. Click **Enter** on the splash screen
    3. Paste your Craft URL (and optionally Gemini API key)
    4. Select an AI model and theme
    5. Click **Save.**

+ ## Technical Details
    - **Single file**: All HTML, CSS, and JS in one file
    - **No dependencies**: Uses vanilla JS, no frameworks
    - **Fonts**: VT323 (retro) + IBM Plex Mono
    - **Storage**: localStorage for credentials and current page. Your keys remain on your system.
    - **Craft API**: RESTful, block-based document structure
    - 

+ ## Credits

    Made by [Priyan Nithya](https://github.com/eternalpriyan) & Claude Code

    [GitHub](https://github.com/eternalpriyan/Craft-Your-Own-Adventure) · [Buy me a coffee](https://buymeacoffee.com/npsoy)