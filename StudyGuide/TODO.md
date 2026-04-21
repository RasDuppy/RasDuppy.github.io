# StudyReview — Prioritized TODO & Improvement Backlog

> Updated 2026-04-19: Phase 1 debranding complete. Added Phase 2 (subject isolation)
> and Phase 3 (multi-subject planner) generalization tracks. Chemistry vocab engine
> wiring (V-series) marked complete per CONTEXT.md 2026-04-18 entry.

---

## ✅ Recently Completed

| Task | Completed |
|------|-----------|
| V-1 through V-5 — vocab engine wiring | 2026-04-18 |
| Connections Board Builder (admin tab) | 2026-04-18 |
| Planner topic selector redesign (Q/V cards) | 2026-04-18 |
| `chapter_icon` / `topic_icon` added to both CSVs | 2026-04-19 |
| **G-1 through G-7 — Phase 1 debranding** | **2026-04-19** |

---

## 🟣 P0 — Blocking: Subject Generalization Phase 2

These must be done before the platform can cleanly support multiple subjects
loaded simultaneously. Until then, importing a second subject's CSV replaces
the first — students must import one subject at a time.

---

### G-1 ✅ Debrand UI strings — cr-shell.html
**Status:** ✅ Done 2026-04-19
- Title, hub logo, game card descriptions, Jeopardy subtitle all genericized
- Wordle mode buttons renamed from "Ions / Elements" to "Type A / Type B"
  (these labels should eventually be driven by data, not hardcoded)

### G-2 ✅ Neutral default icon palette — cr-data.js
**Status:** ✅ Done 2026-04-19
- `CH_ICONS` palette replaced with subject-neutral emojis
- Default topic icon fallback changed from `⚗️` to `📌`

### G-3 ✅ Generic hub notice — cr-core.js
**Status:** ✅ Done 2026-04-19
- Hub now counts vocab entries by type dynamically instead of hardcoding
  `ions` / `elements` labels. Any `type` value in vocab.csv appears correctly.

### G-4 ✅ Render `image_url` in QuestionEngine — cr-question-engine.js
**Status:** ✅ Done 2026-04-19
- `image_url` column was already in the CSV schema but never rendered
- Now displays an `<img>` above the question text, with `onerror` hide fallback
- Works for any subject: maps, artwork, diagrams, photos, etc.

### G-5 ✅ Generic export filenames — cr-data.js
**Status:** ✅ Done 2026-04-19
- `chemreview-questions.csv` → `studyreview-questions.csv`
- `chemreview-vocab.csv` → `studyreview-vocab.csv`

### G-6 ✅ Generic vocab import alert — cr-data.js
**Status:** ✅ Done 2026-04-19
- Success alert now shows dynamic type counts instead of hardcoded ion/element counts
- Error alert now mentions that `type` can be any subject-specific value

### G-7 ✅ Neutralize RANDOMIZER_REGISTRY comment — cr-core.js
**Status:** ✅ Done 2026-04-19
- Added clear comment that built-in randomizers are chemistry-specific and optional
- Documented how to add randomizers for other subjects

---

### G-8 Subject Layer in Data Model — cr-data.js, cr-core.js
**Status:** ❌ Not done
**Priority:** P0 for true multi-subject support
**Fix:** Add a `subject` layer above `chapter` in the data hierarchy.

CSV columns to add to both questions.csv and vocab.csv:
```
subject_id     — machine key, e.g. ap_biology, us_history
subject_name   — display name, e.g. "AP Biology"
subject_icon   — emoji, e.g. 🧬
subject_color  — hex color for subject card, e.g. #27ae60
```

Data model change in cr-data.js:
```javascript
// New top-level registry above CHAPTERS:
let SUBJECTS = {};
// Shape: { [subjectId]: { name, icon, color, chapters: [chKey, ...] } }
```

`rebuildChapters()` needs a `rebuildSubjects()` pass that groups chapters by `subject_id`.

---

### G-9 Per-Subject localStorage Keys — cr-data.js
**Status:** ❌ Not done
**Depends on:** G-8
**Fix:** Namespace storage keys by subject so multiple subjects can coexist:
```javascript
// Instead of single 'chemq_custom':
localStorage.setItem(`srq_q_${subjectId}`, JSON.stringify(questions));
localStorage.setItem(`srq_v_${subjectId}`, JSON.stringify(vocab));
```
Keep `chemq_custom` / `chemq_vocab` read on first load as migration path, then
re-save under new keys and remove old ones.

---

### G-10 Subject Hub Screen — cr-shell.html, cr-core.js
**Status:** ❌ Not done
**Depends on:** G-8, G-9
**Fix:** Add a subject selection screen that appears before the main hub when
more than one subject is loaded:

```
[Subject Hub]
  📗 AP Biology (147 questions, 89 vocab)     → Study Now
  📘 US History (203 questions, 45 vocab)     → Study Now
  🧪 Chemistry (312 questions, 156 vocab)     → Study Now
  [+ Import New Subject]
```

New screen ID: `subject-screen`. `showHub()` routes through it when
`Object.keys(SUBJECTS).length > 1`.

---

### G-11 Wordle Mode Buttons Driven by Data — cr-shell.html + vocab engine
**Status:** ❌ Not done
**Fix:** The Wordle mode buttons currently hardcode "Type A / Vocab / Type B"
(formerly Ions / Vocab / Elements). They should instead be generated from the
distinct `type` values present in the loaded vocabBank:
```javascript
// In wdlRenderModes():
const types = [...new Set(vocabBank.map(v => v.type || 'vocab'))];
// Render one button per type, label = type, mode = type
```
This makes Wordle work correctly for history (people, events, terms),
biology (structures, processes, organisms), etc.

---

## 🔴 P1 — Critical: Stability & Bug Risk

### 1. Eliminate Implicit Global State
**Files:** `cr-data.js`, `cr-planner.js`, `cr-games-questions.js`, `cr-jeopardy.js`, `cr-games-vocab.js`
**Problem:** All key state variables are bare globals on `window`.
**Fix:** Wrap each module's state in a plain object namespace.

### 2. Remove the `activeChapter` / `activeTopic` Legacy Fallback
**Files:** `cr-data.js` (`boardQ()`)
**Fix:** Remove fallback. If `activeSelections` is empty, `boardQ()` returns `[]`.
Mark `activeChapter` / `activeTopic` as `@deprecated`.

### 3. Guard `weightedPick` Dependency Across File Boundary
**Files:** `cr-question-engine.js`, `cr-planner.js`
**Fix:**
```javascript
const pick = (typeof weightedPick === 'function') ? weightedPick : randPick;
return pick(pool);
```

### 4. Always Call `rebuildChapters()` After Mutation
**Files:** `cr-data.js`
**Fix:** Move `rebuildChapters()` to the end of `importCSV()`, `importVocabCSV()`,
`clearImported()`, `clearVocab()`, and `reassignTopic()` so it fires automatically.

### 5. Validate `connections_group` and `wordle_eligible` at Parse Time
**Files:** `cr-data.js`
**Fix:** After parsing vocab CSV, log counts and show UI warning if zero.

---

## 🟠 P2 — High: Architecture & Maintainability

### 6. Centralize the GAMES Registry — cr-planner.js
Co-locate dispatch function with each GAMES entry.

### 7. Introduce a Typed `GameResult` Factory
`makeResult(q, correct, game, earnings)` to ensure consistent shape.

### 8. Replace `visual_params` String Parsing with JSON — cr-admin.js, cr-data.js

### 9. Extract Screen/Route Constants — cr-core.js
```javascript
const SCREENS = { HUB: 'hub-screen', ADMIN: 'admin-screen', ... };
```

### 10. Decouple `QuestionEngine` from `cr-planner.js`'s `weightedPick`
Accept `pickFn` as constructor option.

---

## 🟡 P3 — Medium: Developer Experience

### 11. JSDoc Type Annotations on key data shapes
### 12. localStorage Read/Write Error Handling — wrap all in try/catch
### 13. Dev-Mode Integrity Check — `window.__srCheck()`
### 14. Standardize Dual Entry-Point Pattern (Single vs. Multi)
### 15. Document `_is_generated` Flag Lifecycle

---

## 🔵 P4 — Low: Polish & Future-Proofing

### 16. Consistent `wof` Naming — pick one canonical name
### 17. Add `version` field to `studyPlan` localStorage schema
### 18. Consider Splitting `cr-games-vocab.js` by game
### 19. Clarify `skip` column in data guide
### 20. Per-topic vocab selection (see CONTEXT.md Upcoming Work)
### 21. Auto-load CSV from directory on page load (see CONTEXT.md Upcoming Work)

---

## Summary Table

| # | Priority | Effort | Area | Status |
|---|----------|--------|------|--------|
| G-1 | 🟣 P0 | Tiny | Debrand UI strings | ✅ |
| G-2 | 🟣 P0 | Tiny | Neutral icon palette | ✅ |
| G-3 | 🟣 P0 | Tiny | Generic hub notice | ✅ |
| G-4 | 🟣 P0 | Small | Render image_url | ✅ |
| G-5 | 🟣 P0 | Tiny | Generic export filenames | ✅ |
| G-6 | 🟣 P0 | Tiny | Generic vocab import alert | ✅ |
| G-7 | 🟣 P0 | Tiny | Neutralize randomizer comment | ✅ |
| G-8 | 🟣 P0 | Medium | Subject layer in data model | ❌ |
| G-9 | 🟣 P0 | Small | Per-subject localStorage keys | ❌ |
| G-10 | 🟣 P0 | Medium | Subject hub screen | ❌ |
| G-11 | 🟣 P0 | Small | Wordle modes driven by data | ❌ |
| 1 | 🔴 P1 | Medium | Global state encapsulation | ❌ |
| 2 | 🔴 P1 | Small | Remove legacy boardQ() fallback | ❌ |
| 3 | 🔴 P1 | Tiny | Guard weightedPick dependency | ❌ |
| 4 | 🔴 P1 | Small | Auto-call rebuildChapters() | ❌ |
| 5 | 🔴 P1 | Small | Validate vocab pool at parse time | ❌ |
| 6 | 🟠 P2 | Small | Centralize GAMES registry | ❌ |
| 7 | 🟠 P2 | Small | Typed GameResult factory | ❌ |
| 8 | 🟠 P2 | Medium | JSON-based visual_params | ❌ |
| 9 | 🟠 P2 | Small | SCREENS constants object | ❌ |
| 10 | 🟠 P2 | Small | Decouple QE from planner | ❌ |
| 11 | 🟡 P3 | Medium | JSDoc type annotations | ❌ |
| 12 | 🟡 P3 | Tiny | localStorage error handling | ❌ |
| 13 | 🟡 P3 | Small | Dev-mode integrity check | ❌ |
| 14 | 🟡 P3 | Small | Unify single vs. multi entry points | ❌ |
| 15 | 🟡 P3 | Tiny | Document _is_generated lifecycle | ❌ |
| 16 | 🔵 P4 | Tiny | Consistent wof naming | ❌ |
| 17 | 🔵 P4 | Tiny | version field in studyPlan | ❌ |
| 18 | 🔵 P4 | Large | Split cr-games-vocab.js | ❌ |
| 19 | 🔵 P4 | Tiny | Clarify skip column | ❌ |
| 20 | 🔵 P4 | Medium | Per-topic vocab selection | ❌ |
| 21 | 🔵 P4 | Small | Auto-load CSV on page load | ❌ |
