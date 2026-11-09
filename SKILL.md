---
name: project-prep
description: 새 프로젝트를 구현 직전까지 준비한다. 저장소 명령 안내, 설계 문서, CLAUDE.md, 작업 계획, 에이전트 설정을 단계별로 진행하고 이미 끝난 단계는 건너뛴다. /project-prep으로 직접 호출한다.
disable-model-invocation: true
argument-hint: "[프로젝트 이름이나 한 줄 설명]"
---

# project-prep

This file is a router. Find the target folder and the current step, then follow that step. Read a `reference/` file only when you reach its step.
Paths are relative to this skill's folder. All outputs go in the target project folder.

## Language

These instructions are in English, but everything the user sees is Korean: messages, tables, questions, and every file you write into the project.
Quoted lines and fenced templates in `reference/` are output. Copy them in Korean as written.
`(미정)` is the literal marker for "undecided". Keep it as is.

## Rules

- **Show repository commands; never run them.** Do not run `gh repo create`, `git clone`, `git commit`, or `git push`. They leave traces on the user's account and remote, so the user runs them.
- **Do not overwrite existing files.** If a file exists, show the difference and add only what the user approves.
- **Do not invent requirements or design.** Write only what the user said or what the documents state. When unknown, ask or write `(미정)`. Every later step treats invented content as fact.
- **Do not start implementation.** If the user asks for it in this session, point them to the new-session guidance in step 7. Carrying the prep conversation into implementation makes every later request more expensive.
- **Judge commands by exit code.** Do not report success from output alone, and do not hide errors.

## Start: target folder and current step

1. Run `git rev-parse --show-toplevel`.
   - On success, confirm once that this folder is the project. It may be a notes vault or a parent repository.
   - On failure, ask the user. For a new start, go to step 1. For a folder that already has files, follow "기존 폴더" in `reference/repo-setup.md`.
2. Check the table below from the top and find the first step whose completion condition is not met.
3. Show the status of every step once, as a table, and continue from that step.

There is no separate state file. The outputs are the state, so the skill can resume from any session.

| Step | Complete when | Instructions |
|---|---|---|
| 1. Repository and README | The user said they created the repository, or the target is a git repository with a remote. | `reference/repo-setup.md` |
| 2. Clone | The target folder has `.git`. | `reference/repo-setup.md` |
| 3. Design | Overview, requirements, and design documents exist and are approved. | "Step 3" below |
| 4. CLAUDE.md | It exists at the target root, and each required section is filled or marked `(미정)`. | `reference/claude-md-template.md` |
| 5. Work units | A plan exists in `docs/superpowers/plans/`. | "Step 5" below |
| 6. Agents | `.claude/agents/` has an implementer and a verifier, and the plan's units show their owners. | `reference/agent-templates.md` |
| 7. Readiness | The checklist passes. | `reference/readiness-check.md` |

When judging a step:

- On a later invocation there is no approval record, so if step 3 documents exist, ask once whether they are approved.
- If documents exist under other names (`docs/superpowers/specs/`, `SPEC.md`, and so on), ask whether to use them as that step's output. Do not move or merge them, and use those paths in later steps.
- A file that exists but is empty does not complete a step.

## Step 3: Design

Invoke `superpowers:brainstorming` and pass the text below. The user ran `project-prep`, so this is the user's instruction and takes precedence over brainstorming's defaults.

```text
Project: <the name and description the user gave>
Save to: docs/overview.md (overview), docs/requirements.md (requirements), docs/architecture.md (design).
         If implementation has two or more phases, split the design into docs/architecture-<phase>.md.
Commits: do not commit the documents.
After approval: do not invoke writing-plans. Return to step 4 of project-prep.
Language: speak to the user in Korean and write the documents in Korean.
```

After approval, read `reference/claude-md-template.md` and follow it from its first section.

## Step 5: Work units

Invoke `superpowers:writing-plans` when all three conditions hold:

- It is in the session's skill list.
- The step 3 documents are approved.
- The work divides into two or more units.

Pass the text below.

```text
Source documents: <step 3 document paths>
If implementation has two or more phases, detail only the first. Write the next phase's plan when that phase starts.
Do not repeat rules that are already in CLAUDE.md.
Write the plan in Korean.
Do not set values that the source documents lack. Mark them (미정), or, if you set one, collect them in a single section of the plan.
After saving the plan, do not ask about execution. Return to project-prep.
```

If the conditions do not hold, say why. With only one unit, write a short list of the unit name, files, and completion condition in `docs/superpowers/plans/`.

When the plan returns, look for values missing from the source documents before going to step 6. This means values that change user-visible behavior or external formats, such as API formats, status codes, and ports. Internal details such as file names do not count.
Show the values as a list and wait for the user's answer. Ask whether confirmed values should go into the step 3 documents. Change unanswered values to `(미정)` everywhere they appear, including commands in the plan's steps. Writing a value in the plan does not mean the user confirmed it.

## Dependencies

| Dependency | When missing |
|---|---|
| `superpowers:brainstorming` | Stop at step 3. Do not write the design yourself. Guide the user to install the plugin, or accept documents the user wrote and continue. |
| `superpowers:writing-plans` | Write the short list above, one entry per unit. |
| `gh` | Use the web instructions in `reference/repo-setup.md`. |
