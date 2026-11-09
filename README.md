# project-prep

새 프로젝트를 구현 직전까지 준비하는 Claude Code 스킬이다.
저장소 명령 안내, 설계 문서, `CLAUDE.md`, 작업 계획, 에이전트 설정을 단계별로 진행하고, 이미 끝난 단계는 건너뛴다.
특정 언어나 프레임워크를 가정하지 않는다.

이 파일은 사람이 읽는 문서이고, Claude는 `SKILL.md`와 `reference/`만 따른다.

## 하는 일

| 단계 | 내용 |
|---|---|
| 1. 저장소와 README | `gh repo create` 명령과 README 초안을 보여 준다. |
| 2. clone | clone 명령을 보여 주고, 실행한 뒤 이어 간다. |
| 3. 설계 | `superpowers:brainstorming`으로 개요, 요구사항, 설계 문서를 만든다. |
| 4. CLAUDE.md | 설계 문서에서만 채운다. 근거가 없으면 `(미정)`으로 둔다. JS 프로젝트는 API 문서 자동 생성(jsdoc-to-markdown)도 묻는다. |
| 5. 작업 단위 | `superpowers:writing-plans`로 계획을 만들고, 계획이 정한 값을 사용자에게 확인받는다. |
| 6. 에이전트 | `.claude/agents/`에 `builder`, `verifier`, `final-reviewer`를 만든다. |
| 7. 준비 확인 | 점검표로 빠진 것을 찾고, 구현은 새 세션에서 시작하도록 안내한다. |

## 원칙
- 저장소 생성, clone, commit, push, 패키지 설치는 실행하지 않고 명령만 보여 준다.
- 기존 파일을 덮어쓰지 않는다. 빠진 부분만 보여 주고 승인받은 내용만 덧붙인다.
- 요구사항과 설계를 지어내지 않는다.
- 같은 세션에서 구현을 시작하지 않는다.

생성되는 에이전트와 CLAUDE.md에는 비용을 줄이는 규칙이 들어 있다.
- 기본은 중간 모델과 중간 강도이고, 틀렸을 때 비용이 큰 단위만 올린다.
- 구현과 검증을 나누고, 단계 끝의 최종 리뷰는 강한 모델로 돌린다.
- 에이전트 보고는 짧게 받고, 검증은 명령의 종료 코드로 확인한다.

## 필요한 것
- Claude Code
- [superpowers](https://github.com/obra/superpowers) 플러그인: `brainstorming`, `writing-plans`
- `gh`: 없으면 웹 화면 안내로 대신한다.

## 설치

개인 스킬 폴더에 바로 clone한다.

macOS, Linux, Git Bash:

```bash
git clone https://github.com/how-to-song/project-prep.git ~/.claude/skills/project-prep
```

Windows PowerShell:

```powershell
git clone https://github.com/how-to-song/project-prep.git "$env:USERPROFILE\.claude\skills\project-prep"
```

새 세션부터 `/project-prep`으로 쓸 수 있다.

### 다른 폴더에 두고 링크하기
원본을 노트 보관소처럼 다른 곳에 두고 고치려면, 스킬 폴더에는 링크만 만든다.

macOS, Linux:

```bash
ln -s "<원본 경로>" ~/.claude/skills/project-prep
```

Windows PowerShell(관리자 권한이 필요 없는 junction):

```powershell
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\project-prep" -Target "<원본 경로>"
```

## 호출

```text
/project-prep
/project-prep 가계부 CSV를 요약하는 CLI
```

자동으로 실행되지 않고 직접 호출로만 실행된다.
기존 프로젝트에서 부르면 끝난 단계를 건너뛰고, 중간에 멈춘 프로젝트에서 부르면 이어 간다.

## 수정

| 바꿀 내용 | 파일 |
|---|---|
| 단계 순서, 공통 규칙 | `SKILL.md` |
| 저장소 명령, README 초안 | `reference/repo-setup.md` |
| CLAUDE.md 항목 | `reference/claude-md-template.md` |
| API 문서 자동 생성(jsdoc-to-markdown, Stop 훅) | `reference/api-docs.md` |
| 에이전트, 모델, 강도 | `reference/agent-templates.md` |
| 점검표, 새 세션 지시문 | `reference/readiness-check.md` |

지시문은 영어이고, 프로젝트에 복사되는 템플릿과 사용자에게 보이는 문구는 한국어이다.
더 강한 모델이 나오면 `reference/agent-templates.md`의 `final-reviewer`와 `reference/claude-md-template.md`의 기본 모델 줄을 고친다.

## 삭제

clone으로 설치했다면 폴더를 지운다.

```bash
rm -rf ~/.claude/skills/project-prep
```

**링크로 설치했다면 링크만 해제한다.** 링크 안의 내용을 지우면 원본이 함께 지워질 수 있다.

macOS, Linux:

```bash
rm ~/.claude/skills/project-prep
```

Windows:

```powershell
cmd /c rmdir "$env:USERPROFILE\.claude\skills\project-prep"
```

링크에는 `rm -rf`, `Remove-Item -Recurse`를 쓰지 않고, 탐색기에서 링크 폴더를 열어 안의 파일을 지우지 않는다.

## 검증 상태
2026-11-09 기준이다.

- Python CLI, Go 웹 API, JavaScript CLI 예시 프로젝트로 시험했다. 서브에이전트가 `SKILL.md`를 읽고 진행하고, 사람 역할은 메인 세션이 맡았다.
- 시험에서 저장소 명령과 패키지 설치를 실행한 기록은 없었고, 기존 파일은 승인받은 내용만 덧붙었다.
- 실제 `/project-prep` 슬래시 호출로 처음부터 끝까지 진행한 시험은 아직 없다. 스킬 목록에 나타나는 것까지만 확인했다.
- 소스 파일이 이미 있는 JS 프로젝트에서 Stop 훅을 바로 등록하는 경로와, JS가 아닌 언어의 API 문서 도구 경로는 시험하지 않았다.


## 라이선스
[MIT](LICENSE)
