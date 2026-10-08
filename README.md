# srs-kit

외주(수탁) 프로젝트에서 고객사와 합의하는 SRS(요구사항 명세서)를 같은 방법으로 세팅하고 관리하기 위한 키트다.

SRS는 **고객사와 합의한 범위 기준선**이다. 고객사가 기능별로 확인한 페이지 버전이 기준이 되고, QA에서는 "확인된 SRS와 다르면 버그, 없으면 변경 요청"으로 판정한다.

## 들어 있는 것

| 경로 | 내용 |
| --- | --- |
| [STANDARD.md](STANDARD.md) | 작성 기준: 원칙, 출처 우선순위, 문서 구성, 위험 기반 상세 수준, 기능 페이지 양식, 언어, 버전, 고객사 확인·변경 요청, 체크리스트 |
| [PROCESS.md](PROCESS.md) | 세팅·운영 순서: 세팅 → 출처 전수 확인 → 초안 → 구현 대조 → 고객사 확인 → 기준선 → 변경 관리, 기존 SRS 도입, 세션 나누기 |
| [templates/docs/srs/](templates/docs/srs/) | 새 프로젝트에 복사하는 `docs/srs` 뼈대(한국어·영어 페이지, `meta.json`) |
| [templates/pages/](templates/pages/) | 기능·정책 페이지 틀(`srs new`가 쓴다) |
| [templates/examples/](templates/examples/) | 예시: 기능 페이지 "주문 취소와 환불", 그 출처 목록·추적표·용어집 |
| [templates/messages/](templates/messages/) | 고객사에 올리는 글: 확인 요청, 변경 요청 답변 |
| [templates/briefs/](templates/briefs/) | 작업 세션 지시서: 내용 작성, 구현 대조, 문서 사이트 |
| [web/](web/README.md) | 문서 사이트 코드(Next.js + fumadocs): 언어 전환, 페이지별 버전 드롭다운 |
| [bin/srs](bin/srs) | 명령줄 도구: `init`, `new`, `bump`, `check`, `status` |
| [skill/SKILL.md](skill/SKILL.md) | Claude Code 스킬 원본 |

## 빠른 시작

Node.js 18 이상만 있으면 된다(외부 패키지 없음).

```bash
# 1. 새 프로젝트에 뼈대를 만든다(있는 파일은 덮어쓰지 않는다)
~/Workspace/srs-kit/bin/srs init ~/work/my-project --name "내 프로젝트"
#    예시 페이지까지: --example / 문서 사이트 코드까지: --web apps/web

# 2. 기능 페이지를 만든다
cd ~/work/my-project
~/Workspace/srs-kit/bin/srs new feature order-cancellation --title "주문 취소와 환불" --code CAN

# 3. 페이지를 고치기 전에 버전을 올린다(지금 내용은 docs/srs-versions/<지금 version>/에 남는다)
~/Workspace/srs-kit/bin/srs bump features/order-cancellation --note "취소 기한 변경"

# 4. 검사하고 현황을 본다
~/Workspace/srs-kit/bin/srs check
~/Workspace/srs-kit/bin/srs status
```

## 명령

| 명령 | 하는 일 |
| --- | --- |
| `srs init <저장소> [--name] [--date] [--example] [--web <앱 폴더>] [--force]` | `docs/srs` 뼈대와 `srs.config.json`을 만든다. `--web`은 문서 사이트 코드를 앱 폴더에 복사한다. 있는 파일은 건너뛴다 |
| `srs new <feature\|policy> <slug> --title --code [--description] [--en-title]` | 기능·정책 페이지를 한국어·영어로 만들고 `meta.json`에 넣는다. 같은 `code`가 있으면 멈춘다 |
| `srs bump <페이지> [--note] [--date] [--lang ko\|en\|all] [--from]` | 한국어 파일을 지금 version 폴더에, 영어 파일을 영어 version 폴더에 남기고(이미 같은 내용이 있으면 건너뜀) 한국어 version을 올린다. 확인됨 페이지는 `draft`로 돌린다. `--lang en`은 번역을 마친 뒤 영어 version을 한국어 버전에 맞춘다. `--from`은 version이 없는 기존 페이지의 지금 날짜 |
| `srs check [--json] [--strict] [--verbose] [--limit N]` | 검사. 오류가 있으면 종료 코드 1(`--strict`면 경고도 1) |
| `srs status [--all]` | 기능·정책 페이지의 버전, 번역, 상태, `(제안)` 수, 마지막 확인 버전, 규칙 수를 마크다운 표로 출력한다(`baseline/confirmations`에 붙여 넣는다) |

공통 옵션: `--root <저장소>`(기본: `srs.config.json`이 있는 가장 가까운 상위 폴더), `--config <파일>`.

### `srs check`가 보는 것

| 분류 | 검사 |
| --- | --- |
| 버전 | `version` 있음·형식, 영어 `version`이 한국어보다 늦지 않음(같으면 최신, 이르면 "번역 뒤처짐"), 예전 버전 폴더 이름·날짜, 현재와 같은 날짜의 예전 파일이 현재와 다름(bump 없이 고침) |
| 파일 구조 | 영어 짝, `meta.json`의 페이지 목록, `meta.en.json`, 최상위 예약어 `en`·`v`, 깨진 상대 링크, 문서 사이 절대 링크 |
| 본문 | `# 제목`·"최종 수정일" 줄, 쓰지 않는 말(STANDARD 5절, 설정, 용어집의 "쓰지 않는 말"), 템플릿 자리 표시, 비밀값으로 보이는 문자열 |
| 고객사가 읽는 부분 | 코드 경로, 코드 이름, 이슈 번호, 진행 상태 열, 쉬운 말(문자, 앱 푸시, 이메일) |
| 규칙 | ID 중복, 페이지 `code`로 시작, 출처 열·출처 ID가 출처 목록에 있음, 숫자 규칙의 예시, "~하면"의 "아니면", 추적표와 ID 일치 |
| 기능·정책 양식 | 필수 절, `status`·`code`, 확인됨 페이지에 `(제안)` 없음, 현재 버전의 확인 기록 |
| 출처 목록 | ID 열, 모든 행의 반영 위치(링크 또는 "반영 없음: 이유"), ID 중복 |

### `srs.config.json`

저장소 루트에 둔다. 기본값과 다른 것만 적어도 된다.

| 키 | 기본값 | 뜻 |
| --- | --- | --- |
| `docsDir` / `versionsDir` | `docs/srs` / `docs/srs-versions` | 현재 문서와 예전 버전 폴더 |
| `primaryLanguage` / `translations` | `ko` / `["en"]` | 원문 언어와 번역 언어(`<경로>.<언어>.mdx`) |
| `requireTranslations` | `true` | 번역이 없으면 오류(false면 경고) |
| `legacyVersion` | `null` | `version`이 없는 페이지를 이 날짜로 본다(기존 SRS 도입 때). 사이트의 `SRS_BASELINE_VERSION`과 같게 |
| `pages` | `sources: baseline/sources`, `traceability: baseline/traceability`, `glossary: glossary` | 특별한 페이지의 경로 |
| `internalPages` | `["baseline/"]` | 팀이 관리하는 페이지(경로 또는 `폴더/`). 고객사가 읽는 부분 검사를 하지 않고, 규칙 ID를 정의하지 않는다 |
| `formDirs` | `{ "features": "feature", "policies": "policy" }` | 기능·정책 양식으로 검사할 폴더 |
| `ruleIdPattern` / `sourceIdPattern` | `[A-Z][A-Z0-9]*(?:[-.][A-Z0-9]+)*-\d{2,4}` / `SRC-\d{3,4}` | 규칙 ID와 출처 ID 형식(정규식) |
| `docBasePaths` | `["/docs/srs"]` | 문서 사이트 주소(이 주소로 시작하는 절대 링크를 알린다) |
| `terms` / `allowTerms` | `{ko: [], en: []}` / `[]` | 프로젝트에서 더 금지할 말, 허용할 말 |
| `assets` | `urlPrefix: /srs-assets/`, `dir: null` | `dir`을 주면 그 주소로 시작하는 이미지 파일이 있는지 본다 |

## 스킬 설치

설치는 각자 한다(키트는 설치하지 않는다).

```bash
ln -s ~/Workspace/srs-kit/skill ~/.claude/skills/srs
# 키트를 다른 곳에 두었으면 셸 설정에: export SRS_KIT=<키트 경로>
```

## 기존 SRS가 있는 프로젝트

경로를 바꾸지 않고 덧입힌다. `srs.config.json`의 `pages`, `formDirs`, `internalPages`에 기존 경로를 적고, 기존 전체를 첫 예전 버전으로 남긴 뒤 `legacyVersion`을 그 날짜로 둔다. 자세한 순서는 [PROCESS.md](PROCESS.md) "기존 SRS가 있는 프로젝트에 들일 때". 첫 `srs check`는 실패가 많은 것이 정상이다. 페이지를 고칠 때마다 그 페이지의 오류를 없앤다.
