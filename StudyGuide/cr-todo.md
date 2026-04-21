# ChemReview — Feature Roadmap & TODO
> Last updated: v15-split  
> Dual lens: 👩‍🏫 Teacher (pedagogy) | 🎮 Student (engagement/fun)

---

## 🏗️ ARCHITECTURE WORK (NEW — v15-split)

### [ ] §TODO_QUESTION_ENGINE — Universal Question Engine
**Priority: HIGH** — unblocks all future question-based game modes

Currently Jeopardy, Millionaire, and Practice each contain ~150–200 lines of
duplicated question-display, grading, sig-fig-check, hint, solution, and
result-tracking logic. This creates maintenance debt and makes adding new
question-based games expensive.

**Proposed API (`QuestionEngine` class or module in `cr-question-engine.js`):**
```js
const qe = new QuestionEngine(pool, options);
qe.next()                    // → question object (weighted pick)
qe.render(containerEl)       // → renders MC/numeric card into containerEl
qe.grade(answer)             // → { correct, sigFigWarning, feedback }
qe.showHint()
qe.showSolution()
qe.regen()                   // → if randomizer_type, replaces current q
qe.on('result', callback)    // fires after each grade, calls spTrackResult
```

**Games that would become thin wrappers:**
- Jeopardy modal → call `qe.render()` inside the existing modal shell
- Millionaire → replace `milRenderQuestion` and `milSubmit`
- Practice → replace `pracRender` and `pracSubmit`
- Speed Round (future) → trivially add a timer wrapper
- True/False (future) → restrict to MC questions, show only 2 options
- Formula Builder (future) → custom render, shared grading

**Also needed:** extract the visual rendering (`renderQuestionVisual`) into the engine
so image_url and data_table display consistently in all games.

---

### [ ] §TODO_UNIFIED_SELECTOR — Planner ↔ Topic Selector Merge (IN PROGRESS)
The Study Planner now IS the topic selector. Per-game selector screens
(`#selector-screen`, `#mil-sel-screen`, `#prac-sel-screen`, etc.) are
legacy and should be removed. All games launch from `studyPlan.selections`
via `launchGame()` → `_dispatchGame()`.

**Remaining work:**
- [ ] Remove `#selector-screen`, `#mil-sel-screen`, `#prac-sel-screen`,
      `#cw-sel-screen`, `#fc-sel-screen`, `#wof-sel-screen`, `#ws-sel-screen`
      HTML blocks and their JS functions
- [ ] Remove `openMillionaireSelector()`, `openPracticeSelector()`,
      `openJeopardySelector()`, `openCrosswordSelector()`, etc.
- [ ] The planner Games tab should show which topics are selected and
      let the student launch any game without visiting a separate screen
- [ ] For vocab games (Crossword, Flashcards, WOF, Word Search) that
      currently need a single chapter key: derive chapter from
      `studyPlan.selections[0].chKey` — or show a compact inline
      chapter picker inside the planner Games tab

---

## SECTION 1 — QUESTION & CONTENT SYSTEM

- [ ] **XLSX direct import** — import Excel directly (SheetJS, already on CDN)
- [ ] **Question previewer in Admin** — see render-in-game before students
- [ ] **Bulk enable/disable by chapter/topic** — one click instead of one-by-one
- [ ] **Image support** — `image_url` column exists, not rendered in modal yet
- [ ] **Answer explanation field** — one-sentence "why" after wrong answers
- [ ] **Standard/LO alignment** — surface `standard` + `learning_objective` in Admin and reports
- [ ] **Distractor analysis** — track which wrong MC option gets picked most
- [ ] **Randomizer/variant questions** — `randomizer_type` column + engine ✅ BUILT; extend registry

---

## SECTION 2 — PROGRESS TRACKING & SPACED REPETITION

- [ ] **Spaced repetition scheduler** — 1→3→7→14→30 day intervals, "Due today" count on hub
- [ ] **Mastery badges per topic** — ⭐ when ≥80% correct across all questions in topic
- [ ] **Weekly streak calendar** — GitHub-style heatmap on Progress tab
- [ ] **XP & level system** — XP per correct answer, level bar on hub
- [ ] **Personal bests** — best score per game per topic, shown on end screens
- [ ] **Mistake notebook** — auto-collected wrong answers, exportable as PDF

---

## SECTION 3 — NEW GAME MODES

- [✅] **Connections** — built in v12
- [✅] **Wordle** — built in v12
- [ ] **Speed Round / Blitz Mode** — 30q, 20s each, score = correct × time bonus
- [ ] **True/False Rapid Fire** — swipe/click T/F on MC questions
- [ ] **Matching Pairs (Memory)** — flip cards: term ↔ definition
- [ ] **Equation Balancer** — drag coefficients onto unbalanced equation
- [ ] **Periodic Table Quiz** — click element asked for
- [ ] **Fill in the Blank** — type missing vocab term from definition sentence
- [ ] **Unit Conversion Trainer** — dimensional analysis scaffold
- [ ] **Formula Puzzle / Formula Builder** — drag-and-drop ionic formula building

---

## SECTION 4 — UI / UX

- [ ] **Sound effects toggle** — chime/buzz, off by default
- [ ] **Confetti** — on perfect score / achievement unlock
- [ ] **Dark/light mode toggle**
- [ ] **Mobile swipe gestures** — swipe flashcards
- [ ] **Keyboard shortcuts** — 1-4 for MC, Enter/Space/arrows
- [ ] **Jeopardy: show topic label on answered cells** (not just ✓/✗)
- [ ] **Timer option** — optional countdown per question in Jeopardy/Practice
- [ ] **Print/export study sheet** — printable PDF of questions + answer key

---

## SECTION 5 — TEACHER TOOLS

- [ ] **Class progress dashboard** — aggregate wrong-answer rate per question (requires backend)
- [ ] **Question difficulty auto-calibration** — adjust difficulty tag from actual wrong rate
- [ ] **Assignment mode** — shareable link/code that locks students to a question set
- [ ] **AI question generator** — Claude API generates 5 draft questions from a topic (API already integrated)

---

## SECTION 6 — CSV FORMAT ADDITIONS

New columns to add to `questions.csv`:
```
xp_value         — integer XP per correct answer (default = pts/100)
time_limit       — seconds for timed mode (0 = no limit)
wordle_eligible  — boolean (already on vocab.csv, not questions.csv)
true_false_stmt  — T/F statement version of this question
distractor_note  — why each wrong answer is wrong (teacher note)
image_url        — already exists; ensure all games render it  ← TODO
explanation      — one-sentence "why" after wrong answer       ← TODO
```

---

## SECTION 7 — BIG IDEAS

- [ ] **Live Multiplayer Jeopardy** — WebSockets / Firebase (room code like Kahoot)
- [ ] **Battle Mode (1v1 Async)** — same 10q, compare scores via shareable link
- [ ] **Chemistry Escape Room** — narrative 5-question sequence
- [ ] **AI Tutor Chat** — "why?" after wrong answer → Socratic Claude dialogue (API ready)
- [ ] **Adaptive Learning Path** — auto-generate daily study sequence from performance data
- [ ] **Export to Anki/Quizlet**
- [ ] **Chemistry Simulation Lab** — titration/gas law sliders (Visual Lab canvas ready)
- [ ] **Voice Input Mode** — speech-to-text for formula names

---

## FILE MAP (after split)

| File | Contents | Lines |
|------|----------|-------|
| `cr-data.js` | Data layer, CSV, vocab accessors | ~666 |
| `cr-core.js` | Nav, Jeopardy board/modal, selector, randomizer | ~1107 |
| `cr-admin.js` | Admin panel (list, reassign, vocab, form) | ~582 |
| `cr-games-questions.js` | Millionaire + Practice | ~752 |
| `cr-games-vocab.js` | Crossword, Flashcards, WOF, Word Search, Wordle, Connections | ~2161 |
| `cr-planner.js` | Study Planner + Launcher + Session tracking | ~997 |
| `cr-todo.md` | This file |  |

**Load order in HTML shell:**
```html
<script src="cr-data.js"></script>
<script src="cr-core.js"></script>
<script src="cr-admin.js"></script>
<script src="cr-games-questions.js"></script>
<script src="cr-games-vocab.js"></script>
<script src="cr-planner.js"></script>
```
