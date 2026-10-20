# srs-kit

고객사와 합의하는 SRS(요구사항 명세서)를 세팅·작성·검수하는 키트다. 작성 기준은 `STANDARD.md`, 운영 순서는 `PROCESS.md`, 명령 도구는 `bin/srs`, Claude Code 스킬은 `skill/SKILL.md`다.

## 클론한 뒤 처음 열었을 때 (자동 온보딩)

세션 시작 훅(`.claude/settings.json`)이 `node bin/srs doctor --hook`으로 설치 상태를 알려 준다. 훅 안내가 없으면(훅이 꺼져 있거나 Node.js가 없을 때) 첫 메시지 전에 `node bin/srs doctor`를 직접 실행해 같은 판단을 한다.

스킬이 "연결됨"이 아니면, 사용자의 첫 메시지가 무엇이든 먼저 온보딩을 시작한다. 키트 자체를 고치러 온 것이 분명하면 한 번 묻고 건너뛴다.

1. 한 줄로 인사하고 `node bin/srs install`을 실행한다. 스킬(`~/.claude/skills/srs`)과 `srs` 명령을 연결한다. 이미 다른 srs 스킬이 있다는 오류면 바꿀지 묻고, 동의하면 `--force`.
2. "SRS를 세팅할 프로젝트 폴더 경로를 알려 주세요(없으면 새로 만들 폴더)."를 묻는다.
3. 그 폴더에서 `skill/SKILL.md`의 "온보딩" 절차를 따른다. 기본 정보 → 근거 문서 링크·파일을 하나씩 받기 → 읽기 → 보고. 명령은 `node bin/srs … --root <그 폴더>`(init·onboard는 폴더를 첫 인자로).

설치가 끝나 있으면 먼저 꺼내지 않는다. 사용자가 SRS 세팅을 원할 때 위 2~3단계를 한다.

## 키트를 고칠 때

- 이 저장소는 공개다. 고객사·회사·사람 이름, 비공개 문서 주소, 비밀값을 넣지 않는다. 예시는 일반적인 것으로 쓴다.
- `bin/srs`는 Node.js 18 이상, 외부 패키지 없이 유지한다. 고친 뒤 `node --check bin/srs`, 빈 폴더에서 `node bin/srs init . --example && node bin/srs check`(오류 0)로 확인한다.
- 작성 기준을 바꾸면 `STANDARD.md` 맨 끝 변경 이력에 한 줄 넣고, `README.md`, `skill/SKILL.md`, 템플릿을 함께 맞춘다.
