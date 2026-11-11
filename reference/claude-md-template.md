# Step 4: CLAUDE.md

Create `CLAUDE.md` at the target project root.
It is loaded in every session, so keep it to about 80 lines. Move long content to documents and write only the path.

## First: offer a session break

If this session just finished step 3, say the line below before writing CLAUDE.md, then end your turn.

> 설계가 끝났습니다. 대화가 길어졌다면 프로젝트 폴더에서 새 세션을 열고 `/project-prep`을 부르면 여기서 이어집니다. 이 세션에서 계속할까요?

The design conversation is re-read by every later request, so tell the user where they can cut it. This is not forced, and you ask only once.
If the user says to continue, go on below. If step 3 finished before this session, skip this section.

## How to fill the template

- Fill `{{...}}` only from the step 3 documents. Without a source, write `(미정)`. If no test command has been chosen, do not guess a common one.
- For constraints and design decisions, copy only what every session needs. Implementation details stay in the design documents.
- Delete lines that do not fit the project. Numbers are defaults, so use the design documents' values when they differ.
- The 기본 원칙 section and the 배운 점 line are mandatory. Copy them exactly as written. Never delete, shorten, or ask whether to include them.
- Ask the user about the automation scope once, and write the answer as given: "단위가 끝나 검증을 통과하면 커밋과 push를 어디까지 확인 없이 할까요? PR과 이슈는 어떻게 할까요?"
- Read `reference/api-docs.md` and ask its question in the same message. Its answer fills 문서 생성 and the API-doc lines of 문서화.
- Leave the 작업 분담 section as a heading and a placeholder line. Step 6 fills it.

## When CLAUDE.md already exists

Do not overwrite it. Compare it with the template, show only the missing sections, and append only the sections the user approves.
Exception: always append the 기본 원칙 section and the 배운 점 line if they are missing, without asking. Show what you appended.
Do not append a line that conflicts with an existing rule. Report the conflict instead.

## Template

````markdown
# {{프로젝트 이름}}

{{프로젝트를 설명하는 한두 문장}}

## 문서
- 개요: `{{개요 문서 경로}}`
- 요구사항: `{{요구사항 문서 경로}}`. 기능을 구현하기 전에 관련 절을 읽는다.
- 설계: `{{설계 문서 경로}}`. 구현 전에 해당 단계의 관련 절을 읽는다.
- 계획: `docs/superpowers/plans/`
- 배운 점: `docs/lessons.md`. 작업 전에 읽는다.

## 기본 원칙
- 정보 없이 코딩하지 않는다. 부족한 정보는 사용자에게 질문한다.
- 무조건 동의하지 않는다. 구현 전에 문제점과 대안을 검토해 알린다.
- 기본 테스트만으로 완료하지 않는다. 경계값, 잘못된 입력, 오류 경로까지 확인한다.
- 같은 실수를 반복하지 않는다.
  - 배운 점은 `docs/lessons.md`에 저장한다. 파일이 없으면 만든다.
  - 쌓이면 중복을 합쳐 줄인다.

## 명령어
- 테스트: {{테스트 명령}}
- 실행: {{실행 명령}}
- 그 밖의 검사: {{린트, 타입 검사 등}}
- 문서 생성: {{API 문서 생성 명령}}

## 제약
{{사용자가 정한 제약. 언어, 금지 도구, 과제 조건 등}}

## 설계 결정
{{매 세션 알아야 하는 결정}}

## 작업 방식
- 구현 전에 계획을 확인하고, 작은 단위로 구현한다.
- 단위마다 위 명령을 실행하고, 종료 코드가 0인 것을 확인한 뒤 완료로 보고한다.
- 요구사항에 없는 기능은 구현하지 않고 제안만 한다.
- 설계와 다르게 구현해야 하면 멈추고 보고한다.
- 자동화 범위: {{커밋, push, PR, 이슈를 어디까지 확인 없이 할지}}
- 요구사항 변경, 설계 변경, 새 의존성 추가는 사용자에게 묻는다.
- 그 밖에 메인 세션이 정한 판단은 이유와 틀렸을 때의 비용을 적어 마지막에 보고한다.
- 에이전트가 확인하지 않았다고 보고한 항목은 메인 세션이 직접 확인한다.

## 작업 분담
(6단계에서 채운다.)

## 문서화
- 내보내는 함수와 클래스에는 {{문서 주석 형식}}을 작성한다. 설명은 한국어로 쓰고, 태그와 타입, 인자 이름은 영어로 둔다.
- `{{API 문서 경로}}`는 자동 생성 파일이므로 직접 고치지 않는다.
- 구조나 설계가 바뀌면 설계 문서에 변경 내용과 이유를 적는다.

## 토큰과 속도
- 학습, 설계, 구현처럼 작업의 성격이 바뀌거나 구현 단계가 바뀌면 새 세션에서 시작한다. 이어 갈 내용은 문서와 커밋에 남긴다.
- 에이전트의 기본은 sonnet, 중간 강도이다. 틀렸을 때 비용이 큰 단위만 호출할 때 `model: opus`, `effort: high`를 지정한다.
- 단계가 끝나면 `final-reviewer`로 전체 리뷰를 한 번 돌린다. 이 리뷰는 낮추지 않는다.
- 고의 결함 검사는 `verifier`만 한다. 무작위 검사는 1,000건을 넘기지 않는다.
- 에이전트 보고는 문제와 결론만 20줄 이내로 받는다. 긴 근거는 꼭 필요할 때만 저장소 밖 단위별 파일에 이어 쓴다.
- Critical과 Important는 그 단위 안에서 고친다. Minor는 모아서 단계 끝에 한 번에 고친다.
- 큰 문서는 전체를 읽지 않는다. 필요한 절이나 값만 찾아 담당에게 넘긴다.
- 파일이 겹치지 않는 단위는 병렬로 돌린다.
- 검증은 테스트 개수가 아니라 명령의 종료 코드로 확인한다. 파이프로 종료 코드를 가리지 않고, `git add` 같은 명령의 오류를 숨기지 않는다.
````
