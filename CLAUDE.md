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

## Craft Document Structure

**Everything is a `<page>`**. Options are child pages, not text.

```
Document (root)
└── <page> Story Title (user's first message)
    content: "Narrative only - no options as text"
    │
    ├── <page> Option 1: Go north     ← empty = unexplored
    │       content: ""
    │
    ├── <page> Option 2: Go south     ← has content = explored
    │       content: "You went south and found a river..."
    │       ├── <page> Option A       ← empty
    │       └── <page> Option B       ← empty
    │
    └── <page> Option 3: Stay here    ← empty = unexplored
            content: ""
```

**Key rules:**
- **Page title** = user's choice or typed message
- **Page content** = AI-generated narrative (no options in text)
- **Child pages** = available options (created empty when parent is generated)
- **Empty page** = unexplored option
- **Page with content** = explored option

## App Flow

### Step 0: LOAD PAGE (central function - everything funnels here)
```
loadPage(pageId):
  → Fetch page from Craft
  → Check if page has content

  IF EMPTY:
    → Generate narrative (API call 1)
    → Write narrative to page content
    → Generate options (API call 2 - returns JSON array)
    → Create empty child pages for each option

  THEN (always):
    → Display content
    → Display child pages as clickable options
    → Show text input for custom option
```

### Step 1: USER CLICKS OPTION
```
→ Navigate to that child page
→ Go to step 0 (loadPage)
```

### Step 2: USER TYPES CUSTOM MESSAGE
```
→ Create new empty child page with that title
→ Navigate to it
→ Go to step 0 (loadPage triggers generation since empty)
```

### Step 3: TREE NAVIGATION (sidebar)
```
→ Navigate to clicked page
→ Go to step 0 (loadPage)
```

## AI API Calls (Two Separate Calls)

**Call 1 - Narrative:**
```
Prompt: "You are a narrator. Continue this adventure based on the user's choice: '{choice}'.
Write 2-3 paragraphs of narrative. Do NOT include options or choices."
→ Returns: freeform narrative text
```

**Call 2 - Options:**
```
Prompt: "Based on this story so far, suggest exactly 3 possible actions.
Return ONLY a JSON array with 3 short action phrases.
Example: ["Search the room", "Open the door", "Call for help"]"
→ Returns: JSON array of 3 strings
```

**Why separate calls:**
- Narrative can be freeform/creative
- Options must be deterministic/parseable
- No reliance on AI formatting options correctly in narrative

## Tree Structure (in-memory)
```js
{id, title, content, children: [...childPages]}
```
- `content`: narrative text (empty if unexplored)
- `children`: child pages (the options)

## Files
- `index.html` — single-file app (HTML + CSS + JS)
- Credentials stored in localStorage: `cyoa_openrouter_key`, `cyoa_craft_token`, `cyoa_craft_doc_id`

## Current Status

**WORKING** - Core architecture complete.

### Implemented:
- Central `loadPage()` function (Step 0) - all navigation funnels here
- Options stored as empty child pages in Craft (not text)
- Consolidated code paths (useSuggestion just navigates, handleSend creates page then navigates)
- Tree panel shows all branches, click to navigate
- Markdown parsing (bold, italic, dialogue quotes, blockquotes)
- Paragraph breaks with proper styling

### AI Currently Mocked:
- `generateNarrative()` returns placeholder lorem ipsum
- `generateOptions()` returns fixed 3 options
- Ready to swap in real OpenRouter calls

### Key Functions:
- `loadPage(pageId)` - Central entry point, generates content if empty
- `displayPage(node)` - Renders content + child pages as options
- `createEmptyChildPage(parentId, title)` - Creates option page in Craft
- `refreshNode(pageId)` - Syncs local tree with Craft
- `buildTreeFromBlocks(block)` - Parses Craft response into tree

## Known Issues Fixed
- **ASCII art spacing**: Fixed inconsistent ASCII characters in splash screen
- **ADVENTURE spelling**: Corrected and centered ASCII art text
- **Story branching**: Fixed to properly create subpages in Craft Docs
- **Craft doc loading**: Fixed undefined error with optional chaining
- **Duplicate branches**: Now navigates to existing pages instead of creating duplicates
- **Title display**: Fixed - Craft stores title in `markdown` field directly (not wrapped in `<page>` tags)

## Style
- Warm amber terminal: `#ffb000` on `#0a0a0f`
- VT323 + IBM Plex Mono fonts
- Subtle scanlines + flicker
- Infocom ASCII splash screen
