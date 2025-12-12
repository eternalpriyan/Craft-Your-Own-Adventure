# Craft Choose Your Own Adventure

## Concept
HTML app: AI narrator (via OpenRouter) + Craft doc as persistent save file. Player chats story → AI streams narrative → writes to Craft. Branches = nested `<page>` blocks. Tree view for navigation. Warm amber terminal aesthetic (Infocom tribute).

## Craft API (Multi-Document Block-Based)
Base: `https://connect.craft.do/links/{LINK_ID}/api/v1`

> **Full API Reference:** See [CRAFT_API.md](./CRAFT_API.md) for complete endpoint documentation, parameters, and examples.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/documents` | GET | List docs `{items: [{id, title, isDeleted}]}` |
| `/blocks?id={id}&maxDepth=-1` | GET | Fetch doc tree. Returns `{id, type, markdown, content: [...children]}` |
| `/blocks` | POST | Insert block. Body: `{markdown: "...", position: {position: "end", pageId: "..."}}` |
| `/blocks` | PUT | Update. Body: `{blocks: [{id, markdown}]}` |

### Key Structure
- Documents are flat containers
- `<page>Title</page>` blocks = subpages (our branches)
- Content blocks have `type: "text"`, `markdown: "..."`
- Page blocks have `type: "page"`, nested `content: [...]`

## OpenRouter API
```
POST https://openrouter.ai/api/v1/chat/completions
Headers: Authorization: Bearer {KEY}, Content-Type: application/json
Body: {model: "google/gemma-2-9b-it:free", messages: [{role, content}], stream: true, temperature: 0.9, max_tokens: 1024}
```
Streaming: SSE format, parse `data: {choices: [{delta: {content}}]}`

## App Flow
1. Splash → Settings (OpenRouter key, Craft URL, optional doc ID)
2. Load: `GET /documents` → `GET /blocks?id={docId}` → build tree
3. Play: User input → OpenRouter streams → display + `POST /blocks` to Craft
4. Branch: AI outputs `[BRANCH: title]` → `POST /blocks` with `<page>title</page>` → continue in new branch
5. Navigate: Tree panel shows all branches, click to load any

## Tree Structure (in-memory)
```js
{id, title, markdown, blocks: [...contentBlocks], children: [...branchNodes]}
```
- `blocks`: non-page content for display
- `children`: nested page blocks (branches)

## Content Format in Craft
```markdown
> player input here

Narrative paragraph from AI...

---
**What will you do?**
• Suggestion 1
• Suggestion 2
```

## Files
- `index.html` — single-file app (HTML + CSS + JS)
- Credentials stored in localStorage: `cyoa_openrouter_key`, `cyoa_craft_token`, `cyoa_craft_doc_id`

## Current Status
- UI complete (splash, settings, chat, tree panel)
- OpenRouter streaming working
- Craft API integration working
- Story branching creates subpages in Craft Docs
- Every response creates a subpage under the current page (builds nested tree)
- Suggestion format is hard-coded for consistency (extracts from various AI formats)

## Known Issues Fixed
- **ASCII art spacing**: Fixed inconsistent ASCII characters in splash screen
- **ADVENTURE spelling**: Corrected and centered ASCII art text
- **Story branching**: Fixed to properly create subpages in Craft Docs
- **Craft doc loading**: Fixed "Cannot read properties of undefined (reading 'trim')" error when loading docs with mixed content segments (user inputs + suggestions). Added optional chaining to `parseContent()` at lines 1059 and 1070

## Style
- Warm amber terminal: `#ffb000` on `#0a0a0f`
- VT323 + IBM Plex Mono fonts
- Subtle scanlines + flicker
- Infocom ASCII splash screen
