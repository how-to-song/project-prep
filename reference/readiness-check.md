# Step 7: Readiness check

Open the files and check them. Do not rely on your memory of creating them in earlier steps.

## Checklist

| Item | Check | If it fails |
|---|---|---|
| Design documents | Overview, requirements, and design documents exist and are not empty. | Step 3 |
| CLAUDE.md | Required sections exist and the 작업 분담 section is filled. | Steps 4, 6 |
| Plan | A plan exists in `docs/superpowers/plans/` with a first unit. | Step 5 |
| Plan values | Any value the plan set that is missing from the step 3 documents and changes behavior or external formats was confirmed by the user or is `(미정)`. | Step 5 |
| Agents | An implementer and a verifier exist, and their frontmatter has `name`, `description`, `model`, `effort`, and `tools`. The verifier's `tools` has no `Write` or `Edit`. | Step 6 |
| Unit markers | Every unit in the plan has an owner and an intensity. | Step 6 |
| API docs | If the user chose generation, CLAUDE.md has the command, and for JavaScript `package.json` has the `docs` script. | Step 4 |
| No implementation | This session created no implementation code. | Stop and report |

If something fails, return to that step. These two items only notify the user and do not block:

- List the remaining `(미정)` items. If the test command is `(미정)`, say it must be decided in the first unit.
- If `.gitignore` does not exclude `.claude/settings.local.json`, say so. Personal settings could reach the repository. If npm was used, check `node_modules/` too.

## Finish

When the checklist passes, show the following in order and end. Do not start implementation.

**1. Commands for the user**

The user runs these in the target folder. List only paths created in this run in `git add`.

```bash
git add README.md CLAUDE.md docs .claude/agents
```

```bash
git status
```

```bash
git commit -m "docs: 프로젝트 준비 문서 추가"
```

```bash
git push
```

**2. New-session guidance**

Say this in Korean:

> 구현은 **프로젝트 폴더를 연 새 세션**에서 시작하세요. 준비 대화를 이어 가면 구현의 모든 요청이 이 대화를 다시 읽어 비용이 커집니다. 상위 폴더에서 세션을 열면 `.claude/agents/`를 찾지 못합니다.

**3. Instruction to paste into the new session**

Fill in the real path and unit name.

```text
CLAUDE.md를 따른다. <계획 파일 경로>의 <첫 단위 이름>부터 구현한다.
단위마다 계획에 표시된 담당에게 맡기고 verifier로 검증한다.
superpowers:subagent-driven-development를 쓰되, 프로젝트 규칙과 다르면 프로젝트 규칙을 따른다.
```

If `superpowers:subagent-driven-development` is not in the session's skill list, drop the third line.

If the user chose API doc generation and the Stop hook is not registered yet, add this line:

```text
첫 소스 파일을 만든 뒤 `.claude/settings.json`에 `<문서 생성 명령>`을 Stop 훅으로 등록할지 사용자에게 묻는다. 기존 설정은 지우지 않고 합친다.
```
