# English Drill

A [Claude Code](https://claude.com/claude-code) plugin that turns your English speaking practice into a closed feedback loop inside your [Obsidian](https://obsidian.md) vault.

You practice English → Claude **reviews** the conversation and pinpoints your weaknesses → Claude **drills** you on exactly those weaknesses until they stick → your next practice is sharper. Two skills, one loop:

```
 speaking practice
        │
        ▼
┌─────────────────────────────┐      saves weaknesses + scores
│ english-conversation-review │ ───────────────┐
└─────────────────────────────┘                │
        ▲                                       ▼
        │                          REVIEW_DIR/history.json (shared memory)
   sharper next time                            │
        │                                       ▼
┌─────────────────────────────┐   reads your recurring weaknesses
│   english-weakness-drill    │ ◀──────────────┘
└─────────────────────────────┘
```

---

## The two skills

### 1. `english-conversation-review` — turn a transcript into a review note

You finished a lesson with a tutor or an AI speaking app (e.g. [Speak](https://www.speak.com/)). Paste the conversation transcript and any stats screenshots, and the skill writes a structured Korean review note.

**You give it:** the conversation text + 1–2 stats screenshots.
**You get back** a note containing:
- **Vocab / 유용한 문장** — key words and reusable expressions, with examples
- **틀린 문장** — your mistakes as ❌ wrong → ✅ corrected → one-line grammar rule
- **잘한 것 / 아쉬운 것** — concrete strengths and weaknesses
- **시각화** — a bar chart scoring 6 skills (발음·유창성·문법·어휘·청취·발화 속도) on a 1–5 scale, **compared against your last session** with ▲/▼ deltas
- **실력 요약** — your current level + any mistake you keep repeating across sessions
- **statistics** — the numbers pulled from your screenshots

**Triggers:** "영어 회화 복습 노트", "영어 대화 분석", "회화 복습", or just paste a transcript and ask for a review.

### 2. `english-weakness-drill` — practice your weaknesses, reflexively

A Korean→English reflex drill that targets the exact weaknesses the review found. It shows you a Korean sentence; you say/type the English immediately.

**How it works:** loads your recurring weaknesses → drills them in short rounds → corrects only what you got wrong (❌/✅/👉) → re-asks the same trap in a new situation → you "graduate" a pattern after 2 correct in a row → saves an 오답노트 when done.

**Triggers:** "영어 약점 연습", "영어 드릴", "문법 훈련시켜줘", "영어 훈련".

> The skills are independent — you can run either alone — but they shine together: review feeds the drill, the drill closes the gaps.

---

## Requirements

- **Claude Code** (CLI, desktop, or IDE extension)
- An **Obsidian vault** to store your notes
- The Obsidian **[Charts](https://github.com/phibr0/obsidian-charts)** community plugin — needed to render the bar chart in review notes. (Without it the note still saves; only the chart block won't render.)

---

## Installation

### Option A — install from a local marketplace (recommended)

1. **Clone the repo** somewhere Claude Code can read:
   ```bash
   git clone https://github.com/jdh4601/english-drill.git ~/.claude/plugins/english-drill
   ```
2. **Register it** in your local marketplace file at `~/.claude/plugins/local/.claude-plugin/marketplace.json` (create the file if it doesn't exist):
   ```json
   {
     "id": "local",
     "name": "Local Custom Plugins",
     "plugins": [
       {
         "id": "english-drill",
         "name": "English Drill",
         "description": "영어 회화 복습 + 약점 반사 드릴 세트",
         "version": "1.0.0",
         "source": { "type": "directory", "path": "../english-drill" }
       }
     ]
   }
   ```
   (`path` is relative to the marketplace file — adjust it to wherever you cloned the repo.)
3. **Enable it** — run `/plugin` in Claude Code, find **English Drill**, and turn it on.
4. **Verify** — type `/` and you should see `english-conversation-review` and `english-weakness-drill` in the skill list.

### Option B — drop the skills in directly

If you don't use plugins, copy the two skill folders from `.claude-plugin/skills/` into your `~/.claude/skills/` directory. Each is a self-contained `SKILL.md` (the drill also has a `REFERENCE.md`).

---

## Configuration — set your vault folder (important)

The plugin stores everything under **one folder in your vault**, referenced in the skills as:

| Token | Meaning | Default |
| --- | --- | --- |
| `ENGLISH_DIR` | where finished review notes are saved | `English/` |
| `REVIEW_DIR` | `ENGLISH_DIR/Review/` — holds `history.json` (shared memory) + drill 오답노트 | `English/Review/` |

**To use your own layout**, open both `SKILL.md` files and change the defaults in the `## Configuration` block to your vault's folder — for example `06_English/`, `05_Personal/English/`, or whatever you use. Both skills must point at the **same** `REVIEW_DIR`, or the review→drill loop breaks.

There's nothing to configure on first run beyond this: if `REVIEW_DIR/history.json` doesn't exist yet, the review skill creates it with an empty `sessions: []`.

---

## The shared memory: `history.json`

Both skills read and write `REVIEW_DIR/history.json`. This is your personal progress log — it **lives in your vault, not in this repo** (it's `.gitignore`d). Each review appends one entry:

```json
{
  "sessions": [
    {
      "date": "2026-06-30",
      "topic": "culture & in-laws",
      "scores": { "발음": 3, "유창성": 3, "문법": 2.5, "어휘": 3.5, "청취": 3.5, "발화 속도": 2.5 },
      "wpm": 80,
      "mistakes": ["주어-동사 수일치", "시제 일관성", "관사/전치사"]
    }
  ]
}
```

- `scores` — the 6 skill dimensions (1–5), used for the week-over-week bar chart.
- `wpm` — raw speaking speed from your stats; `발화 속도` is derived from it.
- `mistakes` — short tags. When a tag shows up in **2+ sessions**, the review flags it as a recurring weakness, and the drill prioritizes it.

---

## A typical loop

1. Finish a speaking lesson. Paste the transcript + screenshots → "회화 복습 해줘".
   → Review note saved to `ENGLISH_DIR`, scores + weaknesses appended to `history.json`.
2. Later: "영어 드릴 시작" → drill pulls your recurring weaknesses from `history.json` and drills them until you graduate each one. → 오답노트 saved to `REVIEW_DIR`.
3. Next lesson, the review compares against today's scores and tells you what moved.

---

## Notes & language

- Review notes and feedback are written in **Korean** (vocab/examples in English) — the skills are tuned for Korean speakers learning English. Adapt the `SKILL.md` output-language sections if you want a different language.
- Notes use Obsidian-flavored Markdown (callouts, `[[wikilinks]]`, the Charts code block).

## License

Personal project by [@jdh4601](https://github.com/jdh4601). Use and adapt freely.
