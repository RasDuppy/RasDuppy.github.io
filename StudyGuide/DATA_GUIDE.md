# StudyReview — Data Authoring Guide

This guide covers everything you need to add content to StudyReview for **any subject** using a spreadsheet or a text editor. There are two CSV files: **questions.csv** and **vocab.csv**. Both are imported through the game's Question Manager (✎ Manage Questions).

StudyReview works for any school subject — chemistry, biology, history, literature, geography, economics, art history, foreign languages, and more. You do not need to change any code. You only need to provide the right CSV files.

> **Using AI to create your CSV files?** Jump to **Part 5 — AI Prompt Guide** at the end of this document. You can paste a study guide, practice test, or vocabulary list into Claude, ChatGPT, or any LLM and ask it to generate a ready-to-import CSV in minutes.

---

## How the Game Reads Your Data

When you import either CSV file the game scans every row and automatically builds the chapter and topic structure — no code changes needed. A new chapter appears in every game selector the moment its questions or vocab terms are imported.

**Question games** (Jeopardy, Millionaire, Practice) read from **questions.csv**.
**Vocab games** (Crossword, Flashcards, Word Search, Wheel/Hangman, Wordle, Connections) read from **vocab.csv**.

---

## Part 1 — questions.csv

### Quick Rules

- Save as **CSV UTF-8** (not CSV Windows, not Excel Workbook)
- Any cell containing a comma must be wrapped in double quotes — Excel handles this automatically when you Save As CSV
- Empty cells are fine — leave columns blank if they don't apply
- Column order must match the header exactly
- Re-importing a file replaces the entire question bank

---

### Column Reference

#### Identity & Organisation

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 1 | `id` | Yes | Unique identifier. Convention: `{chapter}-{topic-slug}-{pts}-{variant}` e.g. `ch3-civil-war-400-001`. Must be unique across the whole file. |
| 2 | `variant_group` | No | Groups questions that are the same problem with different numbers. Same as `id` minus the final `-001` suffix. The game picks one variant per board at random. |
| 3 | `variant_num` | No | Which variant this is: `1`, `2`, `3`, etc. |

#### Chapter & Topic

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 4 | `chapter` | Yes | Short machine key, e.g. `ch3`, `unit2`, `ww2`, `shakespeare`. Must be identical across every row in the same chapter. Lowercase, no spaces. |
| 5 | `chapter_label` | Yes | Display label shown in selectors, e.g. `Chapter 3`, `Unit 2`, `Week 7`. |
| 6 | `chapter_name` | Yes | Subject area name shown below the label, e.g. `The Civil War`, `Cell Biology`, `Hamlet`. |
| 7 | `chapter_icon` | No | A single emoji for the chapter card, e.g. `🏛️`, `🧬`, `📖`. Defaults to a neutral icon if blank. |
| 8 | `topic` | Yes | Short machine key for the topic, e.g. `causes`, `battles`, `aftermath`. Lowercase with hyphens. |
| 9 | `topic_label` | Yes | Human-readable topic name shown on selector cards, e.g. `Causes of the War`. |
| 10 | `topic_icon` | No | A single emoji for the topic card, e.g. `⚔️`, `🗺️`, `📜`. Defaults to `📌` if blank. |

#### Jeopardy Board

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 11 | `cat` | Yes | The Jeopardy board column this question belongs to. Each topic should have 5–8 categories. Example: for a Civil War topic, categories might be `Key Battles`, `Political Figures`, `Causes`, `Technology`, `Aftermath`. |
| 12 | `pts` | Yes | Point value: `200`, `400`, `600`, `800`, or `1000`. |

#### Difficulty & Classification

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 13 | `millionaire_val` | No | Reference only — not used by game logic. |
| 14 | `difficulty` | Yes | `1` (easiest) through `5` (hardest). Millionaire uses this to order questions on the ladder. Try to have questions at every level per chapter. |
| 15 | `bloom_level` | No | Bloom's Taxonomy level (1–6). Not used by game logic — for curriculum planning. |
| 16 | `type` | Yes | `mc` (multiple choice) or `numeric`. Must be lowercase. Use `numeric` for maths, science calculations, and statistics. Use `mc` for everything else. |
| 17 | `standard` | No | Curriculum standard code, e.g. `CCSS.ELA-LITERACY.RI.9-10.1`. Not used by game logic. |
| 18 | `learning_objective` | No | Full learning objective text. Not used by game logic. |
| 19 | `tags` | No | Pipe-separated labels for your own filtering, e.g. `primary-source\|analysis`. |
| 20 | `enabled` | No | Leave blank or `1` to include. Set `0` to hide without deleting. |

#### Stimulus / Context (shown above the question)

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 21 | `context_paragraph` | No | A block of text displayed above the question in a highlighted box. Use for: passage-based questions, document excerpts, scenario descriptions, lab setups. |
| 22 | `reaction_equation` | No | A formula or equation displayed in a styled monospace box. Originally for chemistry equations but works for any subject: maths formulas, physics equations, linguistic rules, etc. |
| 23 | `reaction_type` | No | Label for the equation type. Not displayed — for your reference. |
| 24 | `data_table` | No | A small data table displayed above the question. Format: `Header1\|Header2;R1C1\|R1C2;R2C1\|R2C2` — rows separated by `;`, cells within a row by `\|`. Useful for: statistics tables, economic data, periodic data, comparison charts. |

#### Visuals

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 25 | `visual_type` | No | Triggers a drawn diagram above the question. Currently chemistry-specific diagram types (see Part 6). Leave blank for other subjects — use `image_url` instead. |
| 26 | `visual_params` | No | Parameters for the drawn diagram. `key:value` pairs separated by `\|`. Only used with `visual_type`. |
| 27 | `visual_type_2` | No | A second diagram below the first. |
| 28 | `visual_params_2` | No | Parameters for the second diagram. |
| 29 | `image_url` | No | **URL of an image displayed above the question.** Works for any subject. Use for: historical photos, maps, artwork, diagrams, microscope images, portrait photos, book covers, etc. The image is hidden automatically if the URL fails to load. Must be a direct image URL (ending in `.jpg`, `.png`, `.gif`, `.webp`, etc.), not a web page link. Free options: Wikimedia Commons, your school's Google Drive (with public link), or any publicly accessible image host. |

#### Question Text

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 30 | `q` | Yes | The question text. Write complete sentences. Avoid using the answer word in the question. |

#### Multiple Choice Answers (type = mc)

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 31 | `option_a` | Yes (mc) | First answer choice. |
| 32 | `option_b` | Yes (mc) | Second answer choice. |
| 33 | `option_c` | Yes (mc) | Third answer choice. |
| 34 | `option_d` | Yes (mc) | Fourth answer choice. |
| 35 | `answer` | Yes (mc) | The correct answer. Must match one of `option_a`–`option_d` **exactly** — case-sensitive. |

For `mc` questions, leave numeric columns (36–44) blank.

#### Numeric Answer (type = numeric)

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 36 | `answer_raw` | Yes (num) | Exact numeric answer for grading, e.g. `1865`. |
| 37 | `answer_display` | No | How to display the answer, e.g. `1,865`. If blank, `answer_raw` is shown. |
| 38 | `answer_sig_figs` | No | Expected significant figures (science/maths). Leave blank to skip sig-fig checking. |
| 39 | `tolerance` | Yes (num) | Accepted margin of error. `\|student answer − answer_raw\| ≤ tolerance` is correct. Use `0` for exact integer answers (years, counts). Use `0.05` for decimal calculations. |
| 40 | `unit` | No | Unit label shown below the input box, e.g. `years`, `km`, `%`, `million USD`. |
| 41 | `answer_unit_type` | No | Reserved for future unit-conversion checking. Leave blank. |

#### Given Values Box

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 42 | `given_keys` | No | Pipe-separated variable labels shown in a "Given" box, e.g. `Start year\|End year`. |
| 43 | `given_vals_display` | No | Pipe-separated values matching the keys, e.g. `1861\|1865`. Must have the same number of entries as `given_keys`. Both columns must be filled or both left blank. |

#### Randomizer

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 44 | `randomizer_type` | No | **Chemistry-specific.** Generates a fresh question with random numbers each time. Available only for built-in chemistry types: `boyles_law`, `charles_law`, `gay_lussac_law`, `combined_gas_law`, `ideal_gas_law`, `dilution`, `titration_monoprotic`, `titration_diprotic`, `stoichiometry_mole`, `ph_calc`, `percent_composition`. Leave blank for all other subjects. |

#### Hints & Solutions

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 45 | `hint` | No | One sentence shown when the student clicks 💡 Hint. Nudge without giving away the answer. |
| 46 | `sol_1` | No | Step 1 of the worked solution shown after answering. |
| 47 | `sol_2` | No | Step 2. |
| 48 | `sol_3` | No | Step 3. |
| 49 | `sol_4` | No | Step 4. Leave blank if fewer steps needed. |
| 50 | `notes` | No | Internal notes never shown to students. Good for source citations, authoring comments, or difficulty rationale. |

---

### Full Header Row (copy-paste ready)

```
id,variant_group,variant_num,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,millionaire_val,difficulty,bloom_level,type,standard,learning_objective,tags,enabled,context_paragraph,reaction_equation,reaction_type,data_table,visual_type,visual_params,visual_type_2,visual_params_2,q,option_a,option_b,option_c,option_d,answer,answer_raw,answer_display,answer_sig_figs,tolerance,unit,answer_unit_type,given_keys,given_vals_display,randomizer_type,hint,sol_1,sol_2,sol_3,sol_4,notes,image_url
```

---

## Part 2 — vocab.csv

Vocabulary is used by **Crossword**, **Flashcards**, **Word Search**, **Wheel/Hangman**, **Wordle**, and **Connections**. One file handles all word types — regular vocabulary, and any subject-specific enriched types (chemistry ions/elements are built in; other subjects can use any type string).

### Quick Rules

- `word` must be a **single word, uppercase letters only** (A–Z, no spaces, hyphens, or numbers). Multi-word terms are stored here but automatically excluded from Crossword, Wordle, and Hangman — they still appear in Flashcards and Connections.
- `definition` is the primary display text — used as the crossword clue, flashcard back, and Wordle hint.
- Aim for **20–30 words per chapter** for good crossword generation. Below 8 words the crossword may not build a connected grid.
- `type` is open — use `vocab` for any subject. Use `ion` or `element` only for chemistry to unlock the richer Wordle hint panels for those types.

---

### Column Reference

#### Organisation (all types)

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 1 | `chapter` | Yes | Must exactly match the `chapter` key used in questions.csv, e.g. `ch3`. |
| 2 | `chapter_label` | Yes | Same as in questions.csv, e.g. `Chapter 3`. |
| 3 | `chapter_name` | Yes | Same as in questions.csv, e.g. `The Civil War`. |
| 4 | `chapter_icon` | No | Emoji for the chapter card. |
| 5 | `topic` | No | Topic key for organisation, e.g. `key-figures`, `battles`. Vocab games currently use all words for the chapter. |
| 6 | `topic_label` | No | Human-readable topic name. Also used as the default Connections group if `connections_category` is blank. |
| 7 | `topic_icon` | No | Emoji for the topic card. |
| 8 | `type` | No | Entry type. Use `vocab` (default) for any subject. Use `ion` or `element` for chemistry to unlock extra hint panels. Any other string is accepted and will be counted in the hub notice. |
| 9 | `enabled` | No | Leave blank or `1` to include. Set `0` to exclude without deleting. |

#### Word & Definition (all types)

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 10 | `word` | Yes | **Single uppercase word, letters only.** E.g. `RECONSTRUCTION`, `PHOTOSYNTHESIS`, `HAMLET`. Multi-word terms are stored but excluded from Crossword, Wordle, and Hangman. |
| 11 | `definition` | Yes | The clue or definition shown to students. Write as a complete sentence. Avoid using the word itself. |

#### Vocab Games Control

| # | Column | Required | Description |
|---|--------|----------|-------------|
| 12 | `wordle_eligible` | No | Set to `FALSE` to exclude from Wordle even if it meets length requirements (4–12 letters). Leave blank to let the game decide automatically. Useful for obscure or confusingly-spelled terms. |
| 13 | `connections_category` | No | Pipe-separated category tags. The **first segment** becomes the Connections group label. You need at least 4 words sharing the same first segment. Example: `reconstruction-era\|post-war`. If blank, the game uses `topic_label` as the group automatically — so a plain teacher vocab list with good topics will work in Connections with no extra column needed. |

#### Chemistry-Specific Columns: Ion (leave blank for other subjects)

| # | Column | Description |
|---|--------|-------------|
| 14 | `formula` | Display formula, e.g. `SO₄²⁻`. Shown in Wordle hint panel. |
| 15 | `charge` | Charge as a number, e.g. `+1`, `-2`. |
| 16 | `charge_display` | Formatted charge for display, e.g. `2−`. |
| 17 | `category` | Ion category, e.g. `polyatomic`, `halide`. Used as Connections group for ions. |
| 18 | `mnemonic` | Memory device shown after 4 wrong Wordle guesses. |
| 19 | `common_compounds` | Pipe-separated common compounds, e.g. `NaCl\|HCl`. Shown on Wordle result. |

#### Chemistry-Specific Columns: Element (leave blank for other subjects)

| # | Column | Description |
|---|--------|-------------|
| 20 | `symbol` | Element symbol, e.g. `Na`. Shown in Wordle hint panel. |
| 21 | `atomic_num` | Atomic number, e.g. `11`. |
| 22 | `mass` | Atomic mass, e.g. `22.990`. |
| 23 | `family` | Family name, e.g. `Alkali Metal`. Used as Connections group for elements. |
| 24 | `period` | Period number (1–7). Shown in Wordle hint panel. |
| 25 | `group` | Group number (1–18). |
| 26 | `common_charge` | Typical ion charge, e.g. `+1`. Shown in Wordle hint panel. |
| 27 | `hint` | One-sentence Wordle clue shown before the student starts guessing. |

---

### Full Header Row (copy-paste ready)

```
chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,type,enabled,word,definition,wordle_eligible,connections_category,formula,charge,charge_display,category,mnemonic,common_compounds,symbol,atomic_num,mass,family,period,group,common_charge,hint
```

---

### Minimum Viable vocab.csv (any subject)

You only need four columns to get started:

```
chapter,chapter_label,chapter_name,word,definition
ch7,Chapter 7,The Civil War,CONFEDERACY,"The alliance of eleven Southern states that seceded from the United States in 1861"
ch7,Chapter 7,The Civil War,SECESSION,"The act of formally withdrawing from a political union or alliance"
ch7,Chapter 7,The Civil War,ABOLITIONIST,"A person who campaigned to end the practice of slavery"
```

Everything else defaults: `type=vocab`, `enabled=true`, `wordle_eligible` computed automatically from word length.

---

## Part 3 — Subject Examples

### Example A — US History

**questions.csv excerpt:**
```
id,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,difficulty,type,q,option_a,option_b,option_c,option_d,answer
ch7-001,ch7,Chapter 7,The Civil War,⚔️,causes,Causes of the War,🔥,Political Causes,200,1,mc,"Which 1854 act effectively repealed the Missouri Compromise by allowing new territories to decide the slavery question themselves?",Kansas-Nebraska Act,Compromise of 1850,Missouri Compromise,Fugitive Slave Act,Kansas-Nebraska Act
```

**vocab.csv excerpt:**
```
chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,word,definition,connections_category
ch7,Chapter 7,The Civil War,⚔️,key-figures,Key Figures,LINCOLN,"16th President of the United States who led the nation through the Civil War and issued the Emancipation Proclamation",union-leaders
ch7,Chapter 7,The Civil War,⚔️,key-figures,Key Figures,GRANT,"Union general who became the 18th President; accepted Lee's surrender at Appomattox",union-leaders
ch7,Chapter 7,The Civil War,⚔️,key-figures,Key Figures,SHERMAN,"Union general famous for his destructive March to the Sea through Georgia",union-leaders
ch7,Chapter 7,The Civil War,⚔️,key-figures,Key Figures,DOUGLASS,"Abolitionist and orator who escaped slavery and became a leading voice for emancipation",abolitionists
```

---

### Example B — AP Biology

**questions.csv excerpt (with image_url):**
```
id,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,difficulty,type,q,option_a,option_b,option_c,option_d,answer,image_url
bio3-001,bio3,Unit 3,Cell Structure,🦠,organelles,Organelles,🔬,Mitochondria,400,2,mc,"What is the primary function of the mitochondrion?",Protein synthesis,ATP production,DNA replication,Lipid storage,ATP production,https://upload.wikimedia.org/wikipedia/commons/thumb/8/8e/Mitochondria_structure.svg/500px-Mitochondria_structure.svg.png
```

**vocab.csv excerpt:**
```
chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,word,definition
bio3,Unit 3,Cell Structure,🦠,organelles,Organelles,MITOCHONDRIA,"Double-membraned organelle that produces ATP through cellular respiration; known as the powerhouse of the cell"
bio3,Unit 3,Cell Structure,🦠,organelles,Organelles,RIBOSOME,"Small organelle responsible for protein synthesis; found free in cytoplasm or attached to rough ER"
bio3,Unit 3,Cell Structure,🦠,membrane,Cell Membrane,OSMOSIS,"Diffusion of water molecules through a selectively permeable membrane from an area of high water concentration to low"
```

---

### Example C — Art History

**questions.csv excerpt (with image_url):**
```
id,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,difficulty,type,q,option_a,option_b,option_c,option_d,answer,image_url
art2-001,art2,Unit 2,The Renaissance,🎨,painters,Renaissance Painters,🖼️,Identification,600,3,mc,"Who painted this work?",Leonardo da Vinci,Raphael,Michelangelo,Botticelli,Botticelli,https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/Sandro_Botticelli_-_La_nascita_di_Venere_-_Google_Art_Project.jpg/800px-Sandro_Botticelli_-_La_nascita_di_Venere_-_Google_Art_Project.jpg
```

---

### Example D — Spanish Vocabulary

**vocab.csv excerpt:**
```
chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,word,definition,connections_category
sp2,Chapter 2,La Familia,👨‍👩‍👧,family,Family Members,MADRE,"Mother; the female parent in a family",familia-nuclear
sp2,Chapter 2,La Familia,👨‍👩‍👧,family,Family Members,PADRE,"Father; the male parent in a family",familia-nuclear
sp2,Chapter 2,La Familia,👨‍👩‍👧,family,Family Members,HERMANO,"Brother; a male sibling",familia-nuclear
sp2,Chapter 2,La Familia,👨‍👩‍👧,family,Family Members,HERMANA,"Sister; a female sibling",familia-nuclear
sp2,Chapter 2,La Familia,👨‍👩‍👧,extended,Extended Family,ABUELO,"Grandfather; the father of one's parent",familia-extendida
```

---

## Part 4 — Building a New Chapter: Step-by-Step

### Step 1 — Plan your chapter key and topics

Pick a short machine key. Use lowercase, no spaces: `ch7`, `unit3`, `ww2`, `shakespeare`, `ecosystems`.

Then list your topics (aim for 5–8 per chapter):

| topic key | topic_label |
|---|---|
| `causes` | Causes of the War |
| `battles` | Key Battles |
| `figures` | Important Figures |
| `aftermath` | Aftermath & Reconstruction |

### Step 2 — Plan your Jeopardy categories

Each topic needs **5–8 categories** (board columns). Each category needs questions at 5 point values ($200–$1000). A topic with 6 categories × 5 point values = 30 questions for a complete Jeopardy board.

### Step 3 — Build questions.csv rows

```
id:             ch7-causes-200-001
variant_group:  ch7-causes-200
variant_num:    1
chapter:        ch7
chapter_label:  Chapter 7
chapter_name:   The Civil War
chapter_icon:   ⚔️
topic:          causes
topic_label:    Causes of the War
topic_icon:     🔥
cat:            Political Causes
pts:            200
difficulty:     1
type:           mc
```

### Step 4 — Build vocab.csv rows

```
chapter:               ch7
chapter_label:         Chapter 7
chapter_name:          The Civil War
topic:                 key-figures
topic_label:           Key Figures
word:                  CONFEDERACY
definition:            The alliance of eleven Southern states that seceded from the Union in 1861
connections_category:  political-terms
```

### Step 5 — Import

Open StudyReview → ✎ Manage Questions → **⬆ Questions CSV** → select your questions file. Then **⬆ Vocab CSV** → select your vocab file. The chapter will appear immediately in all game selectors.

### Quick Checklist

**questions.csv**
- [ ] Chapter key is consistent across all rows
- [ ] `chapter_label` and `chapter_name` filled in on every row
- [ ] 5–8 topics per chapter, each with a unique `topic` key
- [ ] Each topic has 5–8 Jeopardy categories
- [ ] Each category has questions at all 5 point values ($200–$1000)
- [ ] Each chapter has MC questions at difficulty 1–5 for Millionaire
- [ ] `answer` matches `option_a/b/c/d` **exactly** for MC questions
- [ ] `tolerance` set for every numeric question
- [ ] Saved as **CSV UTF-8**

**vocab.csv**
- [ ] 20–30 words per chapter for Crossword
- [ ] All `word` values are single uppercase words, letters only
- [ ] Connections groups have at least 4 words per `connections_category` first segment
  (or topics have at least 4 words each — the game auto-groups by topic_label)
- [ ] Saved as **CSV UTF-8**

---

## Part 5 — AI Prompt Guide

You can use any LLM (Claude, ChatGPT, Gemini, etc.) to generate StudyReview CSV files from your existing materials. This section gives you copy-paste-ready prompts.

---

### Prompt A — Generate questions.csv from a study guide or textbook chapter

```
I need you to create a questions.csv file for a study game called StudyReview.
The game uses Jeopardy, Millionaire, and Practice modes.

Here is the full CSV header (do not change column order):
id,variant_group,variant_num,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,millionaire_val,difficulty,bloom_level,type,standard,learning_objective,tags,enabled,context_paragraph,reaction_equation,reaction_type,data_table,visual_type,visual_params,visual_type_2,visual_params_2,q,option_a,option_b,option_c,option_d,answer,answer_raw,answer_display,answer_sig_figs,tolerance,unit,answer_unit_type,given_keys,given_vals_display,randomizer_type,hint,sol_1,sol_2,sol_3,sol_4,notes,image_url

Rules:
- chapter key: [e.g. ch7]
- chapter_label: [e.g. Chapter 7]
- chapter_name: [e.g. The Civil War]
- chapter_icon: [e.g. ⚔️]
- Create 4–6 topics with descriptive topic_label values
- For each topic create 5–6 Jeopardy categories (cat column)
- For each category create questions at pts: 200, 400, 600, 800, 1000
- difficulty: 1=easiest, 5=hardest (200pt → difficulty 1, 1000pt → difficulty 5)
- type: mc for all questions (4 plausible options, one correct answer)
- answer must exactly match one of option_a through option_d
- Leave these columns blank: variant_group, variant_num, millionaire_val, bloom_level,
  standard, learning_objective, tags, context_paragraph, reaction_equation, reaction_type,
  data_table, visual_type, visual_params, visual_type_2, visual_params_2, answer_raw,
  answer_display, answer_sig_figs, tolerance, unit, answer_unit_type, given_keys,
  given_vals_display, randomizer_type, image_url
- enabled: 1 for all rows
- Write a short hint for each question (1 sentence, nudge not giveaway)
- Write sol_1 as a one-sentence explanation of the correct answer
- id format: ch7-{topic-slug}-{pts}-{three-digit-number} e.g. ch7-causes-200-001

Here is the study material to convert:
[PASTE YOUR STUDY GUIDE, NOTES, OR TEXTBOOK EXCERPT HERE]

Output only the CSV with header row and data rows. No explanation, no markdown fences.
```

---

### Prompt B — Generate vocab.csv from a vocabulary list

```
I need you to create a vocab.csv file for a study game called StudyReview.
The vocab file feeds Crossword, Flashcards, Word Search, Wordle, and Connections games.

Here is the full CSV header:
chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,type,enabled,word,definition,wordle_eligible,connections_category,formula,charge,charge_display,category,mnemonic,common_compounds,symbol,atomic_num,mass,family,period,group,common_charge,hint

Rules:
- chapter: [e.g. ch7]
- chapter_label: [e.g. Chapter 7]
- chapter_name: [e.g. The Civil War]
- chapter_icon: [e.g. ⚔️]
- type: vocab (for all entries)
- enabled: 1 for all rows
- word: SINGLE WORD, ALL CAPS, letters only (A-Z, no spaces/hyphens/numbers).
  For multi-word terms, use the most distinctive single word (e.g. RECONSTRUCTION not
  "RECONSTRUCTION ERA"). Multi-word terms can be stored but won't appear in Crossword or Wordle.
- definition: Complete sentence. Do not use the word itself in the definition. Min 10 words.
- Assign each word to a topic (topic key = lowercase-hyphenated, topic_label = title case)
- Group words into Connections categories using connections_category.
  Each category needs AT LEAST 4 words sharing the same first segment.
  Format: category-name (e.g. union-leaders, confederate-leaders, battles, political-terms)
- Leave blank: wordle_eligible, formula, charge, charge_display, category, mnemonic,
  common_compounds, symbol, atomic_num, mass, family, period, group, common_charge, hint

Here are the vocabulary terms to convert:
[PASTE YOUR VOCAB LIST HERE — can be a simple list, glossary, or paragraph form]

Output only the CSV with header row and data rows. No explanation, no markdown fences.
```

---

### Prompt C — Generate both files from a practice test or exam

```
I have a practice test I want to convert into a StudyReview study game.
Please generate BOTH a questions.csv and a vocab.csv file.

QUESTIONS.CSV HEADER:
id,variant_group,variant_num,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,millionaire_val,difficulty,bloom_level,type,standard,learning_objective,tags,enabled,context_paragraph,reaction_equation,reaction_type,data_table,visual_type,visual_params,visual_type_2,visual_params_2,q,option_a,option_b,option_c,option_d,answer,answer_raw,answer_display,answer_sig_figs,tolerance,unit,answer_unit_type,given_keys,given_vals_display,randomizer_type,hint,sol_1,sol_2,sol_3,sol_4,notes,image_url

VOCAB.CSV HEADER:
chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,type,enabled,word,definition,wordle_eligible,connections_category,formula,charge,charge_display,category,mnemonic,common_compounds,symbol,atomic_num,mass,family,period,group,common_charge,hint

For questions.csv:
- Convert each test question into a multiple choice question with 4 options
- If the original was short answer, write 3 plausible wrong answers
- Assign pts based on difficulty: easy=200, medium=400-600, hard=800-1000
- Group questions into logical topics and Jeopardy categories
- chapter: [FILL IN], chapter_label: [FILL IN], chapter_name: [FILL IN]

For vocab.csv:
- Extract all key terms, people, places, concepts, and events from the test
- Write a clear definition for each (complete sentence, do not use the word)
- Group into Connections categories of at least 4 words each

Output format:
--- QUESTIONS.CSV ---
[header + data rows]

--- VOCAB.CSV ---
[header + data rows]

Here is the practice test:
[PASTE YOUR TEST HERE]
```

---

### Prompt D — Add more questions to an existing chapter

```
I have an existing StudyReview questions.csv and I want to add more questions
to chapter [CHAPTER_KEY] on the topic of [TOPIC_LABEL].

The chapter is already set up with:
- chapter: [CHAPTER_KEY]
- chapter_label: [CHAPTER_LABEL]
- chapter_name: [CHAPTER_NAME]
- The topic key should be: [TOPIC_KEY]
- The Jeopardy categories I want to fill are: [LIST CATEGORIES]

Use this CSV format (same header as existing file):
id,variant_group,variant_num,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,...

Generate 5 questions per category at pts 200, 400, 600, 800, 1000.
Number new IDs starting from [CHAPTER_KEY]-[TOPIC_KEY]-200-[NEXT_NUMBER].

Study material for these questions:
[PASTE RELEVANT NOTES OR TEXT]

Output only the new data rows (no header row, I'll append to my existing file).
```

---

### Prompt E — Student prompt: generate themed practice questions to share

```
I'm studying for my [SUBJECT] final exam and I want to create extra practice
questions for StudyReview to share with my class.

Please generate a questions.csv covering [TOPIC]. Make the questions fun and
use pop culture references or real-world examples where possible to make them
memorable.

The chapter setup should be:
- chapter: final-[subject-slug]
- chapter_label: Final Review
- chapter_name: [Subject name]
- chapter_icon: [appropriate emoji]

Focus on these specific areas: [LIST TOPICS OR CONCEPTS]

Use this header:
id,variant_group,variant_num,chapter,chapter_label,chapter_name,chapter_icon,topic,topic_label,topic_icon,cat,pts,millionaire_val,difficulty,bloom_level,type,standard,learning_objective,tags,enabled,context_paragraph,reaction_equation,reaction_type,data_table,visual_type,visual_params,visual_type_2,visual_params_2,q,option_a,option_b,option_c,option_d,answer,answer_raw,answer_display,answer_sig_figs,tolerance,unit,answer_unit_type,given_keys,given_vals_display,randomizer_type,hint,sol_1,sol_2,sol_3,sol_4,notes,image_url

Create 30 multiple choice questions across 5 categories. Include a good hint for each.
Output only the CSV — no explanation or markdown.
```

---

## Part 6 — Visual Diagrams (Chemistry only)

The `visual_type` column triggers drawn diagrams above the question. These are currently chemistry-specific. For other subjects, use `image_url` instead.

**Available visual_type values:**
`container_gas`, `piston`, `beaker_solution`, `molecular_geometry`, `electron_config`,
`orbital_diagram`, `phase_diagram`, `energy_diagram`, `titration_curve`,
`kinetics_graph`, `maxwell_boltzmann`, `galvanic_cell`, `heating_curve`, `mass_spectrum`

Each visual type has specific `visual_params` keys documented separately in the
chemistry teacher reference. For non-chemistry subjects, ignore these columns entirely.

---

## Part 7 — Connections Game Setup

The Connections game groups 16 terms into 4 hidden categories. Students guess which group each term belongs to. You get 4 mistakes.

**Automatic grouping (easiest):** If you leave `connections_category` blank, the game automatically groups vocab words by their `topic_label`. As long as each topic has at least 4 words, Connections will work with no extra column needed.

**Manual grouping (more control):** Fill in `connections_category` with a group name. All words sharing the same first segment become one category. You need exactly or more than 4 words per category — the game will pick 4 if you have more.

```
connections_category examples:
  union-leaders       → 4+ Union leaders grouped together
  confederate-leaders → 4+ Confederate leaders grouped together
  battles             → 4+ battle names grouped together
  political-terms     → 4+ political/legal terms grouped together
```

**Custom board builder:** You can also build a hand-crafted Connections board in the Admin panel (🔗 Conn Boards tab) without needing a CSV at all.
