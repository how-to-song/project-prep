# Steps 1-2: Repository and clone

The output of these steps is commands for the user to copy and a README draft.
Do not run the commands. Move on only after the user says they ran them.

## Step 1: Repository and README

Ask only for the project name, a one-line description, and visibility. Do not ask again for anything already in the invocation argument.
If the user does not mention visibility, suggest private as the default.

Show the command:

```bash
gh repo create <이름> --private --description "<한 줄 설명>" --add-readme
```

For a public repository, replace `--private` with `--public`.

Show the README draft with it. Include only what the user said.

```markdown
# <이름>

<한 줄 설명>

## 상태
준비 중이다.

## 시작하기
설계 뒤 채운다.
```

### Without gh
If `gh --version` fails, tell the user to enter the same values at https://github.com/new and turn on "Add a README file".

## Step 2: Clone

Ask which folder to clone under, then show the command:

```bash
gh repo clone <계정>/<이름> "<경로>"
```

Without `gh`, show `git clone https://github.com/<계정>/<이름>.git "<경로>"`.

After the user says they ran it:

1. Confirm the repository with the exit code of `git -C "<경로>" rev-parse --show-toplevel`.
2. Write all later files using this folder's absolute path, because the session's current folder may differ.
3. If the README has only GitHub's default content (a title and description), ask once whether to replace it with the draft. Otherwise leave it alone.

## 기존 폴더

If the session starts in a folder that has files but is not a git repository, ask whether to use it as the target.
If yes, show the commands below instead of clone, and go to step 3 after the user says they ran them.

```bash
git init
gh repo create <이름> --private --description "<한 줄 설명>" --source=. --remote=origin
```

If the user wants local use without a remote, show only `git init`.
