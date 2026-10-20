# 지시서: SRS 영어 번역 세션

`[ ]`를 채워 작업 세션에 그대로 준다. 기준은 srs-kit STANDARD.md 7절이다.

## 목표
[페이지 목록]의 한국어 페이지(`<경로>.mdx`)를 영어 페이지(`<경로>.en.mdx`)로 옮긴다. 한국어가 원문이다.

## 입력
- 한국어 페이지: [경로 목록] (판이면 기준 커밋 [SHA])
- 용어집: `docs/srs/glossary.mdx`, `docs/srs/glossary.en.mdx`

## 할 일
1. 표, 행, 순서를 한국어와 같게 둔다. 규칙 ID, 예시 ID, 확인 요청 번호와 행 수, 표시 개수가 같아야 한다.
2. 용어는 용어집의 영어만 쓴다. 용어집에 없는 말이 나오면 번역하지 말고 용어집 추가안으로 보고한다.
3. 화면 문구(메뉴, 버튼, 상태 이름, 안내 문구)는 `"한국어" (English)` 꼴로 쓴다. 예: `"고객센터로 문의해 주세요" (please contact customer support)`.
4. 화면 이름이 바뀌었으면 영어 페이지에 남은 옛 이름을 모두 찾아 고친다.
5. 표시와 절 이름은 고정 번역을 쓴다.

| 한국어 | 영어 |
| --- | --- |
| `(제안)` | `(proposed)` |
| `(준비 필요)` | `(client to prepare)` |
| `결론: …` | `Settled: …` |
| 이 페이지에는 확인할 질문이 없습니다. | This page has no open questions. |
| 목적 · 포함 · 이번 출시에 없는 것 | Purpose · In scope · Not in this release |
| 규칙 · 예시 · 화면 · 적용 기능 | Rules · Examples · Screens · Applies to |
| 확인 요청 · 확인 기록 · 개발 근거 | Open questions · Confirmation record · Developer notes |

6. 코드 식별자, 경로, 상품 코드는 번역하지 않는다. 본문에 `{`, `}`를 그대로 쓰지 않는다.
7. 한 페이지씩 끝나면: 판이면 `srs check --base [기준]`, 한 페이지 수정이면 `srs bump <페이지> --lang en`.

## 완료 조건
- `srs check`에 두 언어 짝(ID, 행, 표시 개수) 오류와 "번역 뒤처짐" 경고가 없다.
- 용어집 추가안이 보고서에 있다(없으면 "없음").

## 금지
- 한국어 원문 수정. 한국어가 이상하면 보고서에 적는다.
- 커밋·push, 고객사·팀 채널 발송.

## 산출물
- `*.en.mdx` 변경(미커밋), 보고서 [경로]: 옮긴 페이지, 용어집 추가안, 한국어에서 이상한 곳.
