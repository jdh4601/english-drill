# 오답노트 작성 규칙

드릴이 끝나면 이 spec 그대로 노트를 만든다. 매번 같은 결과물이 재현되어야 한다.

## 저장 위치 / 파일명

- 경로: `/Users/jayden/Obsidian-vault/06_English/Review/`
- 파일명: `YYYY-MM-DD {약점주제}.md`
  - 예: `2026-06-25 동사충돌·불가산명사·한국어직역·불필요한전치사.md`
  - {약점주제}는 이번 세션에서 다룬 약점들을 `·`로 연결.
- frontmatter (슬림 버전 고정):

```
---
date: YYYY-MM-DD
type: english-review
tags:
  - english/오답노트
---
```

## 섹션 순서 (고정)

1. `# 영어 오답노트 (YYYY-MM-DD)`
2. `## 오늘 뭐했는지` — 2~3줄. 몇 라운드 / 몇 문제, 어떤 약점을 점검했는지.
3. `## 틀린 문장들 (문법 오류)` — 명백한 비문만.
4. `## 어색한 문장들 (틀리진 않지만 부자연)` — 통하지만 부자연스러운 것.
5. `## 잘한 것 (약점인데 정확히 피함)` — 한 줄씩, 피한 함정을 괄호로.
6. (선택) `## 레퍼런스` — 관련 이전 노트 `[[wikilink]]`.

## 각 오답 항목 형식 (❌/✅/👉 3줄 고정)

```
❌ {사용자가 실제로 말한 틀린 문장}
✅ {교정문}
👉 {머릿속 규칙 한 줄}
```

- ❌ 줄은 반드시 사용자가 실제로 말한 원문 그대로. 각색·미화 금지. 이게 오답노트의 핵심 가치다.
- 👉 줄은 규칙 한 줄로 짧게. `(❌ 틀린형 → ✅ 맞는형)` 표기 허용.
- 한 문장에 오류가 여러 개면 항목을 나누거나 👉에 모두 적는다.

## 문체 규칙

- `**볼드**` 강조 금지.
- 콜아웃(`> [!danger]`), 점수표, 치트시트 등 군더더기 금지. 짧게.
- 항목 사이는 빈 줄로 띄워 가독성 확보.

## 분류 기준 (틀린 vs 어색한)

- 문법적으로 비문 → "틀린 문장". 예: `a training`, `many homeworks`, `that skills`, 현재완료+과거시점(`have gotten ... last year`).
- 문법은 맞지만 원어민이 안 쓰는 표현 → "어색한 문장". 예: `was kept going for`, `high relationship`, `gave me advice for me`.
- 경계가 애매하면 "틀린 문장"으로 보낸다.

## 출력 예시

```markdown
---
date: 2026-06-25
type: english-review
tags:
  - english/오답노트
---

# 영어 오답노트 (2026-06-25)

## 오늘 뭐했는지
한국어 문장을 보고 영어로 즉시 말하는 반사 드릴 4라운드(총 23문제). 동사 충돌 · 불가산 명사 · 한국어 직역 · 불필요한 전치사 4대 약점을 점검하고, 틀린 패턴만 변형 재출제하는 방식으로 반복했다.

## 틀린 문장들 (문법 오류)

❌ I had a training last month.
✅ I had safety training last month.
👉 training은 불가산 명사 → 관사 a 못 붙임.

❌ I have so many homeworks.
✅ I have so much homework.
👉 불가산 명사는 복수 -s 금지, many 금지 → much.

## 어색한 문장들 (틀리진 않지만 부자연)

❌ The meeting was kept going for ten minutes.
✅ The meeting only lasted ten minutes.
👉 ~동안 지속됐다는 last 동사 하나로. 수동태로 풀지 말 것.

## 잘한 것 (약점인데 정확히 피함)
- I helped my sister finish her homework. (help + 목적어 + 동사원형)
- She's having trouble getting a new job. (trouble 뒤에 with 안 넣음)
```
