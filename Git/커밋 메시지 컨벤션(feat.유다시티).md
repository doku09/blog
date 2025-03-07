# Udacity Git 커밋 메시지 스타일 가이드

### 소개

이 스타일 가이드는 프로젝트에서 따라야 할 공식적인 가이드입니다. Udacity 평가자는 이 가이드를 기준으로 프로젝트를 평가합니다.  
소프트웨어 개발에서는 "이상적인" 스타일에 대한 다양한 의견이 존재합니다. 따라서, 학생들이 프로젝트를 진행하는 동안 혼란을 줄이기 위해 이 스타일 가이드를 따를 것을 권장합니다.

## 커밋 메시지 작성법

### 메시지 구조

커밋 메시지는 세 가지 요소로 구성되며, 각 요소는 빈 줄로 구분됩니다.

```
type: Subject

body

footer
```

#### 타이틀(제목)

- type(유형)과 subject(제목)으로 구성됩니다.

#### 바디(본문)

- 선택 사항이며, 필요할 경우 추가적인 설명을 작성할 수 있습니다.

#### 푸터(추가 정보)

- 선택 사항이며, 주로 이슈 트래커 ID 등을 참조할 때 사용됩니다.

## 타입(Type)

타입은 제목에 포함되며, 다음 중 하나를 선택할 수 있습니다.

- feat: 새로운 기능 추가
- fix: 버그 수정
- docs: 문서 수정
- style: 코드 포맷 변경 (세미콜론 추가, 들여쓰기 등), 기능 변경 없음
- refactor: 프로덕션 코드 리팩토링 (동작은 유지하면서 구조 개선)
- test: 테스트 코드 추가 및 수정 (프로덕션 코드 변경 없음)
- chore: 빌드 작업, 패키지 매니저 설정 변경 등 (프로덕션 코드 변경 없음)

## 제목(Subject) 작성법

- 제목은 50자 이내로 작성해야 합니다.
- 첫 글자는 대문자로 시작합니다.
- 마침표(.)를 사용하지 않습니다.
- 명령형(imperative tone)으로 작성합니다.
  - ✅ Change 버튼 스타일
  - ❌ Changed 버튼 스타일
  - ❌ Changes 버튼 스타일

## 본문(Body) 작성법

모든 커밋에 본문이 필요한 것은 아니지만, 추가적인 설명이 필요할 경우 작성합니다.

- 무엇을 변경했는지(What)와 왜 변경했는지(Why)를 설명해야 합니다.
- 어떻게(How) 변경했는지는 설명하지 않습니다.
- 제목과 본문 사이에는 반드시 빈 줄을 추가해야 합니다.
- 본문의 각 줄은 72자 이하로 작성하는 것이 좋습니다.

## 푸터(Footer) 작성법

푸터는 선택 사항이며, 이슈 트래커 ID 등을 명시할 때 사용합니다.

예시 커밋 메시지

```
feat: Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

> 참고: https://udacity.github.io/git-styleguide/
