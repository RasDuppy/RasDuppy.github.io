# StudyReview — Architecture Context Map
> Drop this in the project folder. Read at the start of every session for full context without loading source files.

## Last Updated
- **2026-04-19** — Phase 1 generalization complete. Renamed from ChemReview to StudyReview. Debranded all user-visible UI strings. Neutralized default icon palette. Generalized hub notice to count any vocab `type` dynamically. Added `image_url` rendering to QuestionEngine. Generic export filenames. RANDOMIZER_REGISTRY documented as chemistry-specific/optional. See TODO G-1 through G-7.
- **2026-04-19** — Added `chapter_icon` to questions CSV, vocab CSV, `questionsToCSV()`, `vocabToCSV()`, and `rebuildChapters()`. Both data types now share aligned metadata fields.
- **2026-04-18** — Planner topic selector redesigned: full Q/V cards replace chips. Ions/Elements toggles moved to global controls bar. Old bottom vocab picker removed.
- **2026-04-18** — Connections Board Builder added (admin tab). `chemq_conn_boards` localStorage key. `exportConnStandalone()` for offline HTML export.
- **2026-04-18** — Wiring complete: V-1 through V-5 all applied. `studyPlan.vocabSelections` added. `_getActiveVocabPool` / `_renderVocabSelector` added to `cr-planner.js`. All vocab games now route through `cr-vocab-engine.js` / `getVocabForChapter()`.

---

## Platform Philosophy (updated 2026-04-19)

StudyReview is a **subject-agnostic** study game platform. It was originally built for chemistry but all game logic, data structures, and UI are now generic. Content is entirely teacher-driven via CSV import — no code changes are needed to add a new subject.

**Any teacher can use this platform for any subject** by providing:
- `questions.csv` — for Jeopardy, Practice, and Millionaire
- `vocab.csv` — for Crossword, Flashcards, Word Search, Wheel/Hangman, Wordle, and Connections

The only chemistry-specific code remaining is the `RANDOMIZER_REGISTRY` in `cr-core.js`, which is clearly marked as optional and subject-specific. Teachers for non-STEM subjects simply leave the `randomizer_type` column blank.

---

## File Load Order (cr-shell.html)
```
cr-data.js → cr-core.js → cr-admin.js → cr-question-engine.js
→ cr-jeopardy.js → cr-vocab-engine.js → cr-games-questions.js
→ cr-games-vocab.js → cr-planner.js
```
**Rule:** Each file can only call functions defined in files loaded before it.
**Exception:** `cr-core.js` guards late-loaded functions with `typeof fn === 'function'` checks.
`cr-vocab-engine.js` loads BEFORE `cr-games-questions.js` and `cr-games-vocab.js`.

---

## File Responsibilities & Exports

| File | Owns | Key Exports |
|------|------|-------------|
| `cr-data.js` | Data layer, CSV parsers, vocab bank, chapter registry | `CHAPTERS`, `vocabBank`, `customBank`, `activeSelections`, `allQ()`, `boardQ()`, `boardCats()`, `chEntries()`, `activeVocab(ch)`, `allVocab(chKeyFilter?)`, `getWordlePool(mode)`, `rebuildChapters()`, `importCSV()`, `importVocabCSV()`, `exportCSV()`, `exportVocabCSV()`, `clearImported()`, `clearVocab()`, `reassignTopic()`, `esc()`, `parseCSVLine()` |
| `cr-core.js` | Navigation, shared chapter selector, randomizer engine | `showScreen(id)`, `showHub()`, `showAdmin()`, `openJeopardySelector()` (stub), `openCrosswordSelector()`, `openFlashcardSelector()`, `renderChapterSelector(containerId, cb, requireVocab)`, `generateVariant(templateQ)`, `RANDOMIZER_REGISTRY` *(chemistry-specific, optional)*, `toSigFigs()`, `randFloat()`, `randPick()` |
| `cr-admin.js` | Question manager UI, CSV import/export UI, vocab manager, add/edit form, visual renderers, Connections Board Builder | `showAdminTab(tab)`, `renderQList()`, `renderVocabList()`, `editQ(idx)`, `delQ(idx)`, `toggleQ(idx)`, `saveQ()`, `renderQuestionVisual(q, canvas, cap)`, `_renderDataTable(raw)`, `parseVP(str)`, `renderBoardsTab()`, `saveBoardToBank()`, `deleteBoardFromBank(id)`, `launchCustomBoard(board?)`, `exportConnStandalone(board)` |
| `cr-question-engine.js` | Universal question renderer — Jeopardy, Practice, Millionaire | `QuestionEngine` class, `createQuestionEngine(pool, opts)`, `qeCountSigFigs(str)` |
| `cr-jeopardy.js` | Jeopardy board, selector, question modal | `startJeopardyWith(chKey, topicKey)`, `startJeopardyMulti(selections)`, `openJeopardySelector()`, `buildBoard()`, `renderBoard()`, `openQ(key)`, `closeModal()`, `resetBoard()`, `randomizeBoard()` |
| `cr-vocab-engine.js` | Vocab routing layer, type filtering, pool normalization | `getVocabPool()`, `getVocabForChapter(chKey, gameHint?)`, `renderVocabSourcesSection(containerEl)` |
| `cr-games-questions.js` | Practice mode, Millionaire | `startPractice(chKey, topicKey)`, `startPracticeMulti(selections)`, `startMillionaire(chKey, topicKey)`, `startMillionaireMulti(selections)` |
| `cr-games-vocab.js` | Crossword, Flashcards, WoF/Hangman, Word Search, Wordle, Connections | `startCrossword(chKey)`, `startFlashcards(chKey)`, `startWof(chKey)`, `startWordSearch(chKey)`, `startWordle()`, `startConnections()` |
| `cr-planner.js` | Study planner, session tracking, hub widget, game launcher | `showPlanner(tab)`, `renderPlannerScreen()`, `renderPlannerGames()`, `spTrackResult(q, correct, game)`, `renderStudyPlanWidget()`, `launchGame(id)`, `_dispatchGame(id)`, `weightedPick(pool)`, `_getActiveVocabPool(filter?)` |

---

## Key Global State

| Variable | Defined in | Type | Purpose |
|----------|-----------|------|---------|
| `CHAPTERS` | `cr-data.js` | `{[chKey]: ChapterObj}` | Registry of all chapters + topics. Rebuilt on every import. |
| `customBank` | `cr-data.js` | `Question[]` | All imported questions (persisted: `chemq_custom`*) |
| `vocabBank` | `cr-data.js` | `VocabEntry[]` | All vocab entries — any type (persisted: `chemq_vocab`*) |
| `activeSelections` | `cr-data.js` | `[{chKey, topicKey}]` | Set by planner before launching any game |
| `activeChapter` | `cr-data.js` | `string` | **@deprecated** legacy single-chapter key |
| `activeTopic` | `cr-data.js` | `string` | **@deprecated** legacy single-topic key |
| `gameState` | `cr-data.js` | `{cells:{}, score:0}` | Jeopardy board state |
| `studyPlan` | `cr-planner.js` | see shape below | Full planner state (persisted: `chemq_study_plan`*) |
| `pracState` | `cr-games-questions.js` | object | Current practice session state |
| `wdlState` | `cr-games-vocab.js` | object | Current Wordle game state |
| `connState` | `cr-games-vocab.js` | object | Current Connections game state |
| `window._activeConnBoard` | `cr-admin.js` / `cr-games-vocab.js` | `ConnBoard\|null` | Set before `startConnections()` for custom boards |

*\* localStorage keys retain `chemq_` prefix for backwards compatibility. Future multi-subject
work (TODO G-9) will namespace these per subject.*

---

## Navigation Flow

```
showHub()  <──────────────────────────────────────────┐
    │                                                  │
    ├─ Hub buttons → openJeopardySelector()            │
    │                showScreen('prac-sel-screen')     │
    │                openCrosswordSelector() etc.      │
    │                                                  │
    ├─ Study Plan widget → showPlanner(tab)            │
    │    └─ plannerTab('topics'|'games'|'progress')    │
    │         └─ renderPlannerGames()                  │
    │              └─ launchGame(id)                   │
    │                   └─ _dispatchGame(id) ──────────┤
    │                        ├─ startJeopardyMulti()   │
    │                        ├─ startPracticeMulti()   │
    │                        ├─ startMillionaireMulti()│
    │                        ├─ startCrossword()       │
    │                        ├─ startFlashcards()      │
    │                        ├─ startWordSearch()      │
    │                        ├─ startWof()             │
    │                        ├─ startWordle()          │
    │                        └─ startConnections()     │
    │                                                  │
    └─ Session bar → showPlanner('games') / openSessionReport()
```

---

## Vocab Type System (generalized 2026-04-19)

The `type` field on VocabEntry was originally `vocab | ion | element` (chemistry).
It is now fully open — any string is valid. Built-in behaviour by type:

| type | Wordle hint panel | Connections grouping | Extra columns used |
|------|------------------|---------------------|-------------------|
| `vocab` | definition only | `connections_category` col | none |
| `ion` | formula + charge | `category` col | `formula`, `charge`, `charge_display`, `mnemonic`, `common_compounds` |
| `element` | symbol + period + charge | `family` col | `symbol`, `atomic_num`, `mass`, `family`, `period`, `group`, `common_charge` |
| *any other* | definition only | `connections_category` col | none |

**For non-chemistry subjects:** use `type=vocab` for all entries, or define your own
type names (e.g. `person`, `event`, `term`, `structure`). Custom types fall back to
the `vocab` display behaviour. The hub notice will show the count per type automatically.

---

## Game Entry Points & Data Sources

| Game | Entry functions | Needs chapter? | Data source |
|------|----------------|----------------|-------------|
| Jeopardy | `startJeopardyMulti(selections)` | yes | questions |
| Practice | `startPracticeMulti(selections)` | yes | questions |
| Millionaire | `startMillionaireMulti(selections)` | yes | questions |
| Crossword | `startCrossword(chKey)` | yes | `getVocabForChapter(chKey, 'crossword')` |
| Flashcards | `startFlashcards(chKey)` | yes | `getVocabForChapter(chKey)` |
| Word Search | `startWordSearch(chKey)` | yes | `getVocabForChapter(chKey)` |
| WoF/Hangman | `startWof(chKey)` | yes | `getVocabForChapter(chKey)` |
| Wordle | `startWordle()` | no | `_getActiveVocabPool('wordle')` or full `vocabBank` by type |
| Connections | `startConnections()` | no | `window._activeConnBoard` if set, else `_getActiveVocabPool()` |

---

## Planner GAMES Array (cr-planner.js → renderPlannerGames)

```javascript
const GAMES = [
  {id:'jeopardy',    icon:'🎯', name:'Jeopardy',      needsQ:true,  needsV:false},
  {id:'practice',    icon:'🔁', name:'Practice',       needsQ:true,  needsV:false},
  {id:'millionaire', icon:'💎', name:'Millionaire',    needsQ:true,  needsV:false},
  {id:'flashcards',  icon:'📇', name:'Flashcards',     needsQ:false, needsV:true},
  {id:'crossword',   icon:'✏️', name:'Crossword',      needsQ:false, needsV:true},
  {id:'wordsearch',  icon:'🔍', name:'Word Search',    needsQ:false, needsV:true},
  {id:'wof',         icon:'🎡', name:'Wheel/Hangman',  needsQ:false, needsV:true},
  {id:'wordle',      icon:'🟩', name:'Wordle',         needsQ:false, needsV:true, special:true},
  {id:'connections', icon:'🔗', name:'Connections',    needsQ:false, needsV:true, special:true},
];

const hasVocab = typeof getVocabPool === 'function' && getVocabPool().length > 0;
const canPlay  = (!g.needsQ || totalQ > 0) && (!g.needsV || hasVocab);
```

---

## Vocab Engine (cr-vocab-engine.js)

Routing/normalization layer between raw `vocabBank` and game consumers.

```javascript
getVocabPool()                        // all enabled entries from vocabBank
getVocabForChapter(chKey, gameHint?)  // chapter-scoped, normalized {word, def, definition, ...}
renderVocabSourcesSection(containerEl) // vocab type/source picker UI
```

**Normalization:** always returns entries with both `def` and `definition` set.
**Connections fallback:** if `connections_group` is blank, auto-fills from `topic_label`.
**Any vocab type** passes through correctly — the engine does not filter by type name.

---

## Vocab Pool Helper (cr-planner.js → _getActiveVocabPool)

```javascript
_getActiveVocabPool(filter?)
  // filter = 'wordle' → strips multiword + wordle_eligible===false
  // reads studyPlan.vocabSelections: { chapters:[], ions:true, elements:false }
```

> **Note for generalization:** the `ions` and `elements` boolean flags in
> `vocabSelections` are chemistry-specific. TODO G-11 will replace these with
> a dynamic set of type toggles driven by whatever types exist in vocabBank.

---

## QuestionEngine — image_url support (added 2026-04-19)

`cr-question-engine.js` render() now renders `q.image_url` as an `<img>` element
above the question text, after any `visual_type` canvas graphic. Supported use cases:

- History: photos of documents, maps, paintings, portraits
- Biology: diagrams, microscope images, anatomical figures
- Literature: book cover images, author portraits
- Art: artwork for identification questions
- Any subject: any direct-link image URL

The image is hidden automatically if the URL fails to load (`onerror` handler).
`image_url` is already present in the questions.csv export column list.

---

## Randomizer Registry — chemistry-specific (cr-core.js)

The built-in randomizers are **chemistry-only** and fully optional.
Teachers for other subjects leave `randomizer_type` blank.

| Key | Subject | Type |
|-----|---------|------|
| `boyles_law` | Chemistry | Gas Laws |
| `charles_law` | Chemistry | Gas Laws |
| `gay_lussac_law` | Chemistry | Gas Laws |
| `combined_gas_law` | Chemistry | Gas Laws |
| `ideal_gas_law` | Chemistry | Gas Laws |
| `dilution` | Chemistry | Solutions |
| `titration_monoprotic` | Chemistry | Acid-Base |
| `titration_diprotic` | Chemistry | Acid-Base |
| `stoichiometry_mole` | Chemistry | Stoichiometry |
| `ph_calc` | Chemistry | Acid-Base |
| `percent_composition` | Chemistry | Stoichiometry |

To add randomizers for another subject, add a key to `RANDOMIZER_REGISTRY` in
`cr-core.js`. The key must match the `randomizer_type` CSV column value exactly.

---

## Data Shapes

### CHAPTERS registry entry
```javascript
CHAPTERS[chKey] = {
  label:  'Chapter 9',       // e.g. "Unit 3", "Module A", "Week 7"
  name:   'Gas Laws',        // topic area name
  icon:   '📚',              // from chapter_icon CSV col; falls back to CH_ICONS palette
  color:  '#3d8bff',
  order:  9,
  topics: { [topicKey]: { label, icon, cats:[] } },
  vocab:  [{word, def, enabled, type}]
}
```

### Question object (customBank / allQ())
```javascript
{
  id, variant_group, variant_num,
  chapter, chapter_label, chapter_name, chapter_icon,
  topic, topic_label, topic_icon,
  cat, pts, millionaire_val, difficulty, bloom_level,
  type: 'mc' | 'numeric',
  q,
  option_a, option_b, option_c, option_d,
  options: [],
  answer,
  answer_raw, answer_display, answer_sig_figs, tolerance, unit,
  given_keys, given_vals_display,
  hint, sol_1, sol_2, sol_3, sol_4,
  randomizer_type,
  visual_type, visual_params,       // chemistry visual graphics (optional)
  visual_type_2, visual_params_2,
  image_url,                        // ← now rendered above question text
  context_paragraph,
  reaction_equation,                // generic: any "stimulus equation/formula" text
  data_table,
  enabled,
  _is_generated,
}
```

### VocabEntry (vocabBank)
```javascript
{
  type: 'vocab' | 'ion' | 'element' | string,  // open — any string valid
  word,              // always uppercase
  definition,
  chapter, chapter_label, chapter_name, chapter_icon,
  topic, topic_label, topic_icon,
  enabled,
  wordle_eligible,
  is_multiword,
  connections_group,
  // ion extras (chemistry-specific, optional):
  formula, charge, charge_display, category, mnemonic, common_compounds, name_upper,
  // element extras (chemistry-specific, optional):
  symbol, atomic_num, mass, family, period, group, common_charge,
}
```

### studyPlan (localStorage: 'chemq_study_plan')
```javascript
{
  selections:      [{chKey, topicKey}],
  vocabSelections: {
    chapters: [chKey, ...],
    ions:     true,      // legacy flag — will become dynamic type toggles (TODO G-11)
    elements: false,     // legacy flag
  },
  results:      [{id, chapter, topic, cat, correct, ts, game}],
  performance:  {[id]: {correct, total}},
  earnings:     {[game]: number},
  achievements: [string],
}
```

---

## CSV Column Reference

### questions.csv — full header
```
id,variant_group,variant_num,chapter,chapter_label,chapter_name,chapter_icon,
topic,topic_label,topic_icon,cat,pts,millionaire_val,difficulty,bloom_level,
type,standard,learning_objective,tags,enabled,
context_paragraph,reaction_equation,reaction_type,data_table,
visual_type,visual_params,visual_type_2,visual_params_2,
q,option_a,option_b,option_c,option_d,answer,
answer_raw,answer_display,answer_sig_figs,tolerance,unit,answer_unit_type,
given_keys,given_vals_display,randomizer_type,
hint,sol_1,sol_2,sol_3,sol_4,notes,image_url
```

**`image_url`** — renders an image above the question text (added rendering 2026-04-19).
**`chapter_icon`** — optional emoji; falls back to neutral palette if blank.
**`randomizer_type`** — chemistry-only built-ins; leave blank for other subjects.

### vocab.csv — full header
```
chapter,chapter_label,chapter_name,chapter_icon,
topic,topic_label,topic_icon,
type,enabled,word,definition,wordle_eligible,connections_category,
formula,charge,charge_display,category,mnemonic,common_compounds,
symbol,atomic_num,mass,family,period,group,common_charge,hint
```

**Minimum columns for any subject:**
`chapter, chapter_label, chapter_name, word, definition`
Everything else has defaults: `type='vocab'`, `enabled=true`, `wordle_eligible` auto-computed.

**`type`** is open — use `vocab` for any subject. Use `ion` / `element` only for chemistry
to unlock the extra Wordle hint panels for those types.

---

## Connections Board Builder (cr-admin.js)

**Admin tab:** `tab-boards` / `admin-boards`
**localStorage key:** `chemq_conn_boards`

Board shape:
```javascript
{
  id: 'board_' + Date.now(),
  name: string,
  created: string,
  groups: [{ label, words: [string,string,string,string], colorIdx: 0|1|2|3 }]  // 4 groups
}
```

`exportConnStandalone(board)` — downloads fully self-contained offline HTML.
Works for any subject — no chemistry dependencies.

---

## HTML Screens

| Screen ID | Game/View |
|-----------|-----------|
| `hub-screen` | Main hub |
| `admin-screen` | Question manager |
| `selector-screen` | Jeopardy selector |
| `jeop-screen` | Jeopardy board |
| `prac-sel-screen` | Practice selector |
| `prac-screen` | Practice mode |
| `mil-screen` | Millionaire |
| `cw-sel-screen` | Crossword selector |
| `cw-screen` | Crossword |
| `fc-sel-screen` | Flashcard selector |
| `fc-screen` | Flashcards |
| `ws-sel-screen` | Word Search selector |
| `ws-screen` | Word Search |
| `wof-screen` | Wheel of Fortune / Hangman |
| `wordle-screen` | Wordle |
| `conn-screen` | Connections |
| `planner-screen` | Study Planner |

---

## Common Bug Checklist

1. **New game missing from planner** → add to `GAMES` array AND `_dispatchGame()` — both in `cr-planner.js`
2. **Whole file crashes on load** → orphaned statements at module scope
3. **Function not found at runtime** → load order violation — check script order in `cr-shell.html`
4. **Vocab game empty** → `vocabBank` entries missing, `enabled===false`, or `getVocabForChapter()` returning empty
5. **Wordle no words** → `wordle_eligible` false, word multiword/too short/too long, or no chapters selected in `vocabSelections`
6. **Connections no groups** → `connections_group` not populated AND `topic_label` blank
7. **QuestionEngine crash** → `weightedPick` called before `cr-planner.js` loads
8. **Randomizer wrong values** → key mismatch between CSV `randomizer_type` and `RANDOMIZER_REGISTRY`
9. **`rebuildChapters()` not called** → always call after any import/clear/reassign
10. **Vocab games empty despite vocab loaded** → check `studyPlan.vocabSelections` chapters selected
11. **Icons wrong or cycling** → check `chapter_icon` / `topic_icon` columns in CSV; blank = palette fallback
12. **Vocab pool preview shows 0** → `_getActiveVocabPool()` empty — check `vocabSelections.chapters` matches `vocabBank` chapter keys exactly
13. **image_url not showing** → check URL is a direct image link (not a page); check browser CORS if self-hosted
14. **Board Builder broken** → check `_cbRenderGroups()` called, `cb-groups` div in DOM
15. **Export empty (Board Builder)** → board must have 4 groups of 4 words before `exportConnStandalone()`

---

## Upcoming / Planned Work

### Phase 2 — Subject isolation (TODO G-8, G-9, G-10)
Add `subject_id / subject_name / subject_icon / subject_color` columns to both CSVs.
Per-subject localStorage keys. Subject hub screen for multi-subject navigation.

### Phase 3 — Dynamic Wordle type buttons (TODO G-11)
Replace hardcoded "Type A / Vocab / Type B" Wordle mode buttons with buttons
generated from the distinct `type` values actually present in the loaded `vocabBank`.

### Per-topic vocab selection
Expand V cards in planner to per-topic sub-selections. Requires `vocabSelections.topics`
array alongside `chapters`. See data prerequisite note — `topic_icon` now exists on VocabEntry.

### Auto-load CSV from directory
On page load, `fetch('./questions.csv')` and `fetch('./vocab.csv')` — auto-import if found.
Only if localStorage empty or `srq_auto_csv=true` flag set. `cr-data.js` only.

---

## Files to Upload for Common Issues

| Symptom | Upload these |
|---------|-------------|
| Planner UI bug / game not launching | `cr-planner.js` |
| Vocab game empty / wrong data | `cr-vocab-engine.js` + `cr-games-vocab.js` |
| Wordle / Connections crash | `cr-vocab-engine.js` + `cr-games-vocab.js` + `cr-planner.js` |
| image_url not rendering | `cr-question-engine.js` |
| Practice crash | `cr-games-questions.js` |
| Millionaire crash | `cr-games-questions.js` |
| Jeopardy board/modal crash | `cr-jeopardy.js` |
| Question rendering wrong | `cr-question-engine.js` |
| Data not loading / CSV broken | `cr-data.js` |
| Admin UI broken | `cr-admin.js` |
| Navigation broken | `cr-core.js` |
| Randomizer wrong answers | `cr-core.js` |
| Screen missing / layout broken | `cr-shell.html` |
| Board Builder broken | `cr-admin.js` + `cr-shell.html` |
