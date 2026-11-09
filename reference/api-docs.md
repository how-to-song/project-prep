# Step 4 add-on: API documentation from doc comments

Function and class descriptions live in doc comments and are generated into `docs/api.md`. Design reasons stay in the design documents.
Generating the file keeps it in sync with the code without anyone editing it by hand.

## Ask

Ask in the same message as the automation-scope question:

> 함수와 클래스의 설명을 문서 주석에서 `docs/api.md`로 자동 생성할까요?

- JavaScript project: suggest `jsdoc-to-markdown` with JSDoc.
- Other languages: suggest the language's common tool as a proposal. Use it only if the user picks it, and follow the same order below with that tool's commands.
- Not decided, or TypeScript without a chosen tool: write `(미정)` in CLAUDE.md and skip the rest.
- The user declines: delete the API-doc lines from the CLAUDE.md template.

## JavaScript with jsdoc-to-markdown

1. Show the commands for the user to run in the target folder. Do not run them. Skip `npm init -y` if `package.json` exists.

   ```bash
   npm init -y
   ```

   ```bash
   npm install --save-dev jsdoc-to-markdown
   ```

2. After the user says they ran them, confirm with the exit code of `npx --no-install jsdoc2md --version`. If it fails, report it and write `(미정)` for the command.
3. Take the source folder from the design documents. If it is not there, ask. Show the script and add it to `scripts` in `package.json` after approval. If a `docs` script already exists, do not replace it; show the difference.

   ```json
   "docs": "jsdoc2md \"<소스 폴더>/**/*.js\" > docs/api.md"
   ```

4. Fill the CLAUDE.md template: `npm run docs` for 문서 생성, JSDoc for the doc-comment format, and `docs/api.md` for the generated path.

## Stop hook

A Stop hook runs the generation command at the end of every turn, so the file never goes stale and nobody has to remember it.
The generator fails when no source file matches, so do not register the hook during prep.

- **No source files yet (new project):** do not register. Step 7 adds a line to the new-session instruction instead.
- **Source files exist (existing project):** propose registering it now. After approval, merge into `.claude/settings.json` without removing existing settings:

  ```json
  {
    "hooks": {
      "Stop": [
        { "hooks": [{ "type": "command", "command": "npm run docs" }] }
      ]
    }
  }
  ```

  Then run `npm run docs` once and check its exit code.
