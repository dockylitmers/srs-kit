---
name: srs
description: 고객사와 합의하는 SRS(요구사항 명세서)를 srs-kit 기준으로 세팅·작성·갱신·검수한다. 새 프로젝트에 SRS를 세팅할 때, 기능·정책 페이지를 쓰거나 고칠 때, 고객사 확인 요청·확인 기록·변경 요청을 다룰 때, SRS를 구현과 대조하거나 검수할 때 사용. Use when setting up, writing, updating, versioning, translating or reviewing a client-agreed SRS (docs/srs, srs.config.json, srs bump/check).
---

# SRS (srs-kit)

고객사와 합의한 범위 기준선으로 SRS를 쓰고 관리한다. 판정 규칙은 하나다: **확인된 SRS와 다르면 버그, 없으면 변경 요청.**

- 키트 위치: `$SRS_KIT` (없으면 `~/Workspace/srs-kit`). 도구: `$SRS_KIT/bin/srs` (Node.js 18+, 의존성 없음).
- 기준: `$SRS_KIT/STANDARD.md`(쓰는 법), `$SRS_KIT/PROCESS.md`(순서). 작업 전에 해당 절을 읽는다.
- 템플릿: `$SRS_KIT/templates/` (페이지, 예시 기능, 고객사 메시지, 작업 세션 지시서). 문서 사이트: `$SRS_KIT/web/README.md`.

## 언제 쓰나

| 상황 | 할 일 |
| --- | --- |
| 새 프로젝트에 SRS를 세팅 | 아래 "세팅" |
| 기존 SRS가 있는 프로젝트에 도입 | PROCESS.md "기존 SRS가 있는 프로젝트에 들일 때" |
| 기능·정책 페이지를 쓰거나 고침 | 아래 "갱신" |
| 고객사 답, 확인, 변경 요청을 반영 | STANDARD.md 9절 |
| SRS와 구현 대조 | PROCESS.md 3단계, `templates/briefs/impl-check.md` |
| 검수 요청 | 아래 "검수" |

## 세팅 (PROCESS.md 0~2단계)

1. `srs init <저장소> --name "<프로젝트>"` → `docs/srs` 뼈대와 `srs.config.json`. 사이트까지: `--web <앱 폴더>` 후 `web/README.md`의 "합칠 것".
2. 고객사와 합의 방식(확인 요청 위치, 확인 담당, 회신 기한, QA 판정 규칙, 변경 요청 절차)을 정해 `overview`의 "합의 방식"에 적는다. 고객사 발송은 사람이 승인한다.
3. 출처 전수 확인: 고객 소통 도구의 페이지 트리 전체와 이슈 트래커에 걸린 고객 소통 도구 링크를 모두 모아 `baseline/sources`에 한 행씩. 묶음별 전체 수 = 넣은 수.
4. 기능마다 `srs new feature <slug> --title "..." --code ABC`, 여러 기능에 걸친 규칙은 `srs new policy ...`.
5. `srs check` 오류 0.

## 갱신 (페이지를 고칠 때마다)

1. `srs bump <페이지> --note "바뀐 것 한 줄"` — 고치기 **전에**. 같은 날 두 번째 수정이면 생략.
2. 고친다. 출처를 새로 반영했으면 출처 목록의 반영 위치를 채운다. 새 규칙 ID는 추적표에 넣는다.
3. 영어: 번역을 고친 뒤 `srs bump <페이지> --lang en`(영어 version = 옮긴 한국어 버전). 두 언어를 함께 고치면 처음부터 `--lang all`.
4. `srs check` → 오류 0, 새 경고는 고치거나 이유를 남긴다.
5. 고객사 확인이 필요하면 `status: in-review`, `templates/messages/confirmation-request.md`로 요청 초안. 답을 받으면 확인 기록에 행을 넣고, `(제안)`이 없고 현재 버전 확인 행이 있으면 `status: confirmed`.
6. 기준선 이후 고객사가 확인된 내용과 다른 것을 원하면 `baseline/change-requests`에 CR로 등록하고 결정 후에만 bump한다.

## 쓰는 규칙 (요약, 자세히는 STANDARD.md 5~7절)

- 깊이는 위험 기반: 돈·날짜·상태·권한·개인정보는 규칙 표 + 예시 표(경계값). 화면은 캡처. 내부 구현은 "개발 근거"에만.
- 규칙 하나 = 표 한 행 = ID 하나(`<code>-NN`), 출처 ID(`SRC-NNN`)를 단다.
- 숫자가 있으면 예시, "~하면"에는 "아니면", 형용사 금지, "확인 필요" 대신 `(제안)` + 확인 요청.
- 고객사가 읽는 부분: 이슈 번호·진행 상태 열·코드 이름 금지, 쉬운 말(앱 푸시, 문자, 이메일).
- 본문에 `# 제목`·"최종 수정일" 줄 없음. 링크는 상대 경로. 최상위 이름 `en`, `v` 금지. `docs/srs` 안에 작성 안내 문서를 두지 않는다(메모는 MDX 주석).
- 비밀값·개인정보는 옮기지 않는다. 예전 버전 파일(`docs/srs-versions/**`)은 고치지 않는다.

## 검수

1. `srs check` (CI나 커밋 전에는 `srs check --strict`). 결과를 오류·경고 유형별 개수로 보고한다.
2. `srs status`로 페이지별 버전·번역·상태·`(제안)` 수·마지막 확인 버전을 본다.
3. 사람이 보는 것(STANDARD.md 10절):
   - 고객사 담당자가 이 페이지만 읽고 규칙마다 맞다/틀리다로 답할 수 있다.
   - "이번 출시에 없는 것"이 고객사가 기대할 만한 것을 다룬다.
   - 예시 값이 실제 운영 값이다(목업·테스트 값 아님).
   - 캡처가 최신 빌드다.
   - 이슈·회의에서 정한 것이 모두 반영됐다.
   - 영어가 용어집을 따른다.

## 하지 않는 것

- 승인 없이 고객사·팀 채널에 보내거나 이슈 트래커에 쓰지 않는다. 초안까지만 만든다.
- 코드가 SRS와 다르다고 SRS를 코드에 맞춰 몰래 고치지 않는다. 차이는 구현 상태에 적고 고객사 확인이나 버그로 정리한다.
- 확인된 페이지를 bump 없이 고치지 않는다(오타 제외).
