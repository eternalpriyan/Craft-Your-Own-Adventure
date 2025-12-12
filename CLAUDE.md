# Craft Choose Your Own Adventure

## Concept
HTML app: AI narrator (Gemini 2.5 Flash) + Craft doc as persistent save file. Player chats story → AI streams narrative → writes to Craft. Branches = nested `<page>` blocks. Tree view for navigation. Warm amber terminal aesthetic (Infocom tribute).

## Craft API (Multi-Document Block-Based)
Base: `https://connect.craft.do/links/{LINK_ID}/api/v1`

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

## Gemini API
```
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:streamGenerateContent?alt=sse&key={KEY}
Body: {contents: [{role, parts: [{text}]}], generationConfig: {temperature: 0.9, maxOutputTokens: 1024}}
```
Streaming: SSE format, parse `data: {candidates: [{content: {parts: [{text}]}}]}`

## App Flow
1. Splash → Settings (Gemini key, Craft URL, optional doc ID)
2. Load: `GET /documents` → `GET /blocks?id={docId}` → build tree
3. Play: User input → Gemini streams → display + `POST /blocks` to Craft
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
- `craft-cyoa.html` — single-file app (HTML + CSS + JS)
- Credentials stored in localStorage: `cyoa_gemini_key`, `cyoa_craft_token`, `cyoa_craft_doc_id`

## Current Status
- UI complete (splash, settings, chat, tree panel)
- Gemini streaming working
- Craft API integration written but untested (CORS blocked in Claude artifact sandbox)
- Needs testing with actual Craft API from local browser

## Style
- Warm amber terminal: `#ffb000` on `#0a0a0f`
- VT323 + IBM Plex Mono fonts
- Subtle scanlines + flicker
- Infocom ASCII splash screen
