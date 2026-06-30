---
name: english-conversation-review
description: Analyze an English conversation transcript (with a teacher/AI tutor) into a Korean review note covering vocab, mistakes, strengths, weaknesses, a skill summary, and a statistics table extracted from app screenshots. Use when the user finishes English speaking practice and provides the conversation text plus stats screenshots, or asks for "영어 회화 복습 노트", "영어 대화 분석", "회화 복습".
---

# English Conversation Review Note

Turn a raw English-practice transcript + app stat screenshots into a polished review note saved in your vault's **`ENGLISH_DIR`** (see Configuration).

## Configuration

This skill reads/writes inside one folder of your Obsidian vault. Two paths, both vault-relative:

- **`ENGLISH_DIR`** — where the finished review notes go. Default: `English/`.
- **`REVIEW_DIR`** — `ENGLISH_DIR/Review/`, holding the shared memory `history.json` (and the drill's 오답노트). Default: `English/Review/`.

If your vault uses a different layout, change these two defaults to match (e.g. `06_English/`, `05_Personal/English/`). Everything below refers to `ENGLISH_DIR` / `REVIEW_DIR` — adapt them to your vault, don't hardcode someone else's path.

**Resolving the path at runtime:** if the user hasn't set one explicitly, search the vault for an existing `*/Review/history.json` and use that folder (so prior sessions are never orphaned); only if none exists, fall back to creating `English/Review/`. The author's vault, for reference, resolves to `06_English/`.

## Inputs

1. **Transcript** — the text of the conversation with the English teacher/tutor.
2. **Screenshot(s)** — stats screens from the speaking app (e.g. Speak's Recap/Statistics). May be one or several images.

If either is missing, ask for it before proceeding.

## Output language

- **Explanations & feedback → Korean.** Vocabulary entries, example sentences, and corrected sentences → **English** (with a short Korean gloss).

## Formatting conventions

- Keep `*`/`**` emphasis to a minimum — only where it carries real meaning.
- **Vocab** is a table: `| 단어 | 뜻 | 예문 |`. In the 예문 column, italicize **only the target word/phrase** as it appears in the sentence — nothing else.
- **유용한 문장** is a table: `| 표현 | 해석 |`. Max **5** rows, chosen as the expressions most worth reusing.
- **틀린 문장**: write the student's wrong sentence on a line prefixed with `❌`, the corrected sentence on the next line prefixed with `✅`, then a one-line 한국어 rule explanation in a `>` blockquote.
- **잘한 것 / 아쉬운 것**: each point is its own `###` subheading (short title) followed by 1–2 sentences.

## Memory (능력치 기록)

Past scores and recurring mistakes are persisted in **`REVIEW_DIR/history.json`** (in your vault, not in this plugin — it's shared memory that the `english-weakness-drill` skill also reads). This is the "memory" that powers the week-over-week comparison and the 실력 요약. If the file doesn't exist yet, create it (and `REVIEW_DIR` if needed) with an empty `sessions: []`. Structure:

```json
{
  "sessions": [
    {
      "date": "2026-06-23",
      "topic": "직장 면접",
      "scores": { "발음": 3, "유창성": 3.5, "문법": 3, "어휘": 4, "청취": 3.5, "발화 속도": 4 },
      "wpm": 112,
      "mistakes": ["주어-동사 수일치", "관사 누락", "시제 일관성"]
    }
  ]
}
```

- `scores` — the 6 skill dimensions on the 1–5 scale (same as 시각화).
- `wpm` — the raw Speaking speed (WPM) from statistics, so 발화 속도's actual value is tracked alongside its 1–5 score. Leave `null` if no screenshot shows it.
- `mistakes` — short Korean tags for the errors made this session (derived from 틀린 문장 / 아쉬운 것). Keep them as reusable tags so repeats across sessions can be detected.

## Workflow

1. **Read the transcript.** Identify the main topic (1 short phrase, e.g. `여행 계획`, `직장 면접`).
2. **Read `REVIEW_DIR/history.json` first.** Take the **last session** as the comparison baseline (지난 세션). Scan recent sessions' `mistakes` arrays to find any tag that appears **2+ times** — these are the user's repeated, unresolved weaknesses, and must be called out in 실력 요약. If the file has no sessions yet, skip the comparison (single-series chart, no delta table) and say so.
3. **Read every screenshot** and pull each visible metric. Map app labels → table columns:
   - Speaking time → `Speaking time` · Vocabulary size → `Vocabulary size` · Speaking speed (WPM) → `Speaking speed`
   - `Total words`, `New words`, `Vocabulary variety` come from **other screenshots** — fill from whichever screen shows them.
   - Any column with no value in any screenshot → leave the cell **blank**. Never invent numbers.
4. **Fill the template** (below). For each section:
   - **Vocab** — words/phrases the user likely found hard or that the teacher introduced, as a table (단어/뜻/예문). Italicize only the target word inside the example.
   - **유용한 문장** — up to 5 reusable expressions/sentence-starters taught in the lesson, as a table (표현/해석).
   - **틀린 문장** — student's wrong sentence prefixed with `❌`, corrected sentence prefixed with `✅`, then a one-line 한국어 rule in a `>` blockquote.
   - **잘한 것** — specific strengths (grammar handled well, good word choice, fluency); each as a `###` subheading with an example from the transcript.
   - **아쉬운 것** — recurring errors or missed opportunities, concrete and actionable; each as a `###` subheading.
   - **시각화** — score 6 skill dimensions (발음, 유창성, 문법, 어휘, 청취, 발화 속도) on a 1–5 scale. 발음/유창성/문법/어휘/청취 come from the transcript + screenshots (be honest — reflect 잘한 것 / 아쉬운 것). **발화 속도** is derived from the Speaking speed (WPM) in statistics, mapped to 1–5: `<70→2`, `70–89→2.5`, `90–109→3`, `110–129→3.5`, `130–149→4`, `150–169→4.5`, `≥170→5`. If no screenshot shows WPM, leave 발화 속도 out of the chart for this session. Render as an Obsidian **Charts** `bar` block with **two series**: 지난 세션 (from `history.json`'s last entry) and 이번 세션. Then add a **변화 table** showing the per-dimension delta with an arrow (▲ 상승 / ▼ 하락 / = 동일); for the 발화 속도 row, note the raw WPM in parentheses. If there's no prior session, use a single series and omit the 변화 table.
   - **실력 요약** — 3–5 sentences in Korean. Cover: (a) current level (reference the app's Speaking level / Grammar score if shown); (b) **week-over-week 변화** — which skills rose/fell vs the last session and the net trend; (c) **반복 실수** — any mistake tag that recurs across `history.json` (2+ sessions), named explicitly as the thing to fix; (d) the single most impactful next action.
5. **Append this session to `REVIEW_DIR/history.json`.** Add an object with `date` (today), `topic`, the 6 `scores`, `wpm` (raw Speaking speed, or `null`), and a `mistakes` array of short Korean tags drawn from this session's 틀린 문장 / 아쉬운 것. Preserve all existing sessions — read, append, write the whole file back. Never overwrite or drop prior entries. These `mistakes` tags are what `english-weakness-drill` pulls in to build its drill rounds, so keep them concise and reusable.
6. **Save** the note to `ENGLISH_DIR` as `{YYYY-MM-DD} {대화 주제 간단 요약}.md` (today's date, then the topic phrase from step 1).
7. Tell the user the filename and give a 1-line verbal summary that includes the week-over-week trend.

## Note template

````md
## Vocab

| 단어 | 뜻 | 예문 |
| --- | --- | --- |
| hands-on experience | 직접 해보는 경험 | Showing the kids the dirty water was a *hands-on experience*. |

## 유용한 문장

| 표현 | 해석 |
| --- | --- |
| If I had to sum up ___ in a few words... | ~을 한마디로 요약하자면... |

## 틀린 문장

❌ Last weekend I met my family and having lunch.
✅ Last weekend I met my family and had lunch.
> 앞 동사 met(과거)에 시제를 맞춰야 하므로 having(분사)이 아니라 정동사 had를 써야 함.

## 잘한 것

### 복잡한 주제를 논리적으로 전개
주니어 개발자 채용난을 AI와 노동시장 두 축으로 나눠 근거를 들어 설명함.

## 아쉬운 것

### 주어-동사 수일치
"Communication skill is" → skills are 처럼 복수·동사 일치가 반복적으로 흔들림.

## 실력 요약


## 시각화

```chart
type: bar
labels: [발음, 유창성, 문법, 어휘, 청취, 발화 속도]
series:
  - title: 지난 세션
    data: [3, 3, 3, 3.5, 3, 3.5]
  - title: 이번 세션
    data: [3.5, 3.5, 3, 4, 3.5, 4]
yMin: 0
yMax: 5
```

| 능력 | 지난 세션 | 이번 세션 | 변화 |
| --- | --- | --- | --- |
| 발음 | 3 | 3.5 | ▲ +0.5 |
| 유창성 | 3 | 3.5 | ▲ +0.5 |
| 문법 | 3 | 3 | = |
| 어휘 | 3.5 | 4 | ▲ +0.5 |
| 청취 | 3 | 3.5 | ▲ +0.5 |
| 발화 속도 | 3.5 (112 WPM) | 4 (128 WPM) | ▲ +0.5 |


## statistics

| Total words | New words | Vocabulary size | Vocabulary variety | Speaking speed | Speaking time |
| ----------- | --------- | --------------- | ------------------ | -------------- | ------------- |
|             |           |                 |                    |                |               |
````

> Notes:
> - The `chart` block uses the Obsidian **Charts** plugin (must be installed). The `data` values and the 변화 table are placeholders — replace with the real per-dimension scores and deltas.
> - The chart is a `bar` with two series (지난 세션 / 이번 세션) so the week-over-week change is read directly off the bars; the 변화 table makes the ▲/▼ explicit. First-ever session: one series, no 변화 table.
> - `실력 요약` + `시각화` sit between `아쉬운 것` and `statistics`. If the user prefers a different order or wants a section omitted, follow their lead.

## Rules

- Use Obsidian `[[wikilinks]]` only if linking to an existing note makes sense; otherwise plain text.
- Don't fabricate stats — blank cell over a guess.
- Keep Vocab to the ~5–10 most valuable items; quality over exhaustiveness.
