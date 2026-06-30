# English Drill

영어 학습 피드백 루프를 하나로 묶은 Claude Code 플러그인. 두 스킬이 한 세트로 동작한다.

```
회화 연습  →  [english-conversation-review]  →  약점 추출 (Review/history.json)
                                                        │
                                                        ▼
   다음 회화  ←  실력 향상  ←  [english-weakness-drill]  ←  약점 훈련
```

## 스킬

### `english-conversation-review`
영어 회화록(튜터/AI와의 대화) + Speak 앱 통계 스크린샷을 한국어 복습 노트로 변환한다.

- **Vocab / 유용한 문장 / 틀린 문장 / 잘한 것 / 아쉬운 것 / 실력 요약** 섹션 자동 작성
- **능력치 막대그래프** — 발음·유창성·문법·어휘·청취·발화 속도 6개 차원을 1~5로 점수화, **지난 세션 대비 ▲/▼ 변화** 표시 (Obsidian Charts 플러그인)
- **반복 실수 감지** — 과거 세션에서 2회 이상 반복된 실수를 실력 요약에서 콕 집어줌
- 저장 위치: `06_English/{날짜} {주제}.md`

호출: "영어 회화 복습 노트", "영어 대화 분석", "회화 복습"

### `english-weakness-drill`
한국어 문장을 보고 영어로 즉시 말하는 반사 드릴로 약점을 적응형 훈련한다.

- review가 남긴 약점(`Review/history.json`의 반복 실수 태그 + 최신 오답노트)을 읽어 출제
- 틀린 패턴만 변형 재출제, 2연속 정답이면 졸업
- 끝나면 오답노트를 `06_English/Review/`에 자동 저장

호출: "영어 약점 연습", "영어 드릴", "문법 훈련시켜줘"

## 공유 메모리

두 스킬은 `06_English/Review/history.json`을 공유 메모리로 쓴다. 이 파일은 **vault에 저장되고 이 repo에는 포함되지 않는다** (개인 학습 데이터). 구조:

```json
{
  "sessions": [
    {
      "date": "2026-06-30",
      "topic": "문화 차이와 인척(in-laws)",
      "scores": { "발음": 3, "유창성": 3, "문법": 2.5, "어휘": 3.5, "청취": 3.5, "발화 속도": 2.5 },
      "wpm": 80,
      "mistakes": ["주어-동사 수일치", "시제 일관성", "관사/전치사"]
    }
  ]
}
```

## 설치

로컬 마켓플레이스(`~/.claude/plugins/local/`)에 등록되어 있다. 다른 환경에서는:

```bash
git clone https://github.com/jdh4601/english-drill.git
```

후 Claude Code의 plugin 디렉터리에 두고 마켓플레이스에 등록한다.

## 의존성

- `06_English/{날짜} {주제}.md` 복습 노트의 차트는 Obsidian **Charts** 플러그인 필요.
- 노트 작성 시 Obsidian-flavored markdown 사용.
