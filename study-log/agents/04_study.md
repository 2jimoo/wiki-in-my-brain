# Claude Code Agent 설계 스터디

주제: Agent File 기반 역할 정의와 Multi-Agent 협업 설계

핵심 개념 · Agent File 구조 · Frontmatter · 협업 프로토콜 · 설계 예시

## 1. 핵심 개념: Agent File은 역할 계약서다

Claude Code의 Agent File은 에이전트가 무엇을 수행하고, 어떤 권한을 가지며, 다른 에이전트와 어떻게 협업할지 정의하는 설정 파일이다.

핵심은 Agent Definition과 Agent Instance를 구분하는 것이다.

Agent File (역할 정의)

Role · Model · Tools · Instructions · Protocol

Session / Orchestrator

Agent 선택 및 실행 요청

Instance A

독립 실행 문맥

Instance B

독립 실행 문맥

Instance C

독립 실행 문맥

동일한 Agent File을 기반으로 여러 실행 인스턴스를 생성할 수 있다. 각 인스턴스는 역할 정의를 공유하지만 실행 문맥과 작업 결과는 구분된다.

## 2. Agent File의 구성

| 구성 요소       | 역할          | 주요 내용                     |
| ----------- | ----------- | ------------------------- |
| Frontmatter | 호출자 관점의 계약  | 이름, 설명, 모델, 도구, 실행 옵션     |
| Body        | 에이전트 관점의 계약 | 역할, 판단 기준, 작업 절차, 협업 및 검증 |

Frontmatter는 어떤 에이전트를 언제 호출할 것인가에 초점을 두고, Body는 호출된 에이전트가 어떻게 행동해야 하는가에 초점을 둔다.

## 3. Agent File의 위치와 우선순위

같은 `name`의 Agent Definition이 여러 위치에 존재하면 우선순위가 높은 정의가 선택된다.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://t.co\&sz=32)

Claude Code Docs



| 우선순위    | 위치                  | 적용 범위      |
| ------- | ------------------- | ---------- |
| 1 (최상위) | Managed Settings    | 조직 전체      |
| 2       | `--agents` CLI flag | 현재 세션      |
| 3       | `.claude/agents/`   | 프로젝트       |
| 4       | `~/.claude/agents/` | 사용자 전역     |
| 5       | Plugin `agents/`    | 플러그인 활성 범위 |

예를 들어 사용자 전역과 프로젝트에 모두 `code-reviewer`가 존재한다면 프로젝트의 정의가 우선 적용된다.

주의: 정확한 프로젝트 디렉터리는 `.claude/agents/`이며, `name`은 파일명과 반드시 같을 필요는 없다. 실제 식별자는 Frontmatter의 `name`이다.

## 4. Frontmatter: 호출과 실행 환경 정의

YAML Frontmatter는 파일 최상단의 `---` 구분자로 작성한다.

```

---
name: code-reviewer
description: >
  Review code changes for bugs, security issues,
  and maintainability. Use after implementation.
model: sonnet
tools:
  - Read
  - Grep
  - Glob
disallowedTools:
  - Edit
  - Write
maxTurns: 12
---

```

### 주요 필드

| 필드                | 역할        | 설계 기준             |
| ----------------- | --------- | ----------------- |
| `name`            | 에이전트 식별자  | 역할이 명확한 이름        |
| `description`     | 호출 시점과 목적 | 언제 호출해야 하는지 구체적으로 |
| `model`           | 실행 모델     | 작업 복잡도에 따라 선택     |
| `tools`           | 허용 도구     | 최소 권한 원칙          |
| `disallowedTools` | 금지 도구     | 부작용이 큰 작업 제한      |
| `maxTurns`        | 최대 실행 턴   | 무한 탐색 방지          |

`name`, `description`은 필수이며, 나머지는 선택 사항이다.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://webfiddle.net\&sz=32)

Claude Code Docs



### Model 선택

Haiku

탐색·분류

Sonnet

구현·리뷰

Opus

복잡한 추론·설계

개념적 작업 복잡도에 따른 선택 가이드이며, 실제 모델 성능이나 비용을 정량 비교한 그래프는 아님.

기본적으로 부모 대화의 모델을 상속할 수 있지만, 명시적 모델 설정이나 호출별 모델 지정이 우선할 수 있다.

### Tools: 권한은 곧 부작용 범위

- 읽기 도구: 탐색 및 분석
- 쓰기 도구: 파일 수정 및 생성
- 실행 도구: 명령 실행 및 외부 시스템 변경 가능성

따라서 코드 리뷰 전용 에이전트에는 파일 수정 권한을 부여하지 않는 편이 안전하다. `tools`를 생략하면 사용 가능한 도구를 상속하므로, 최소 권한을 보장하려면 명시적으로 제한해야 한다.

## 5. Body: 행동 규칙과 협업 계약

Body는 Markdown으로 작성되며 에이전트의 지침으로 사용된다.

| 구성       | 핵심 질문                 | 예시             |
| -------- | --------------------- | -------------- |
| 핵심 역할    | 무엇을 책임지는가?            | 코드 변경사항의 결함 탐지 |
| 작업 원칙    | 충돌 시 무엇을 우선하는가?       | 보안 → 정확성 → 가독성 |
| 입출력 프로토콜 | 결과를 어디에 어떤 형식으로 남기는가? | JSON 보고서       |
| 팀 통신     | 누구에게 무엇을 전달하는가?       | 구현 담당자에게 수정 요청 |
| 에러 핸들링   | 실패 시 어떻게 대응하는가?       | 재시도 2회 후 실패 보고 |
| 협업       | 누구와 어떤 순서로 연결되는가?     | 구현 → 리뷰 → 테스트  |
| 품질 검증    | 반환 전에 무엇을 확인하는가?      | 근거 위치 및 재현 가능성 |

중요한 점은 단순히 역할을 설명하는 것이 아니라 실행 가능한 규칙과 검증 가능한 종료 조건을 작성하는 것이다.

## 6. Subagent와 Agent Team의 차이

Subagent

Parent Agent

Subagent

위임 → 작업 → 결과 반환

Agent Team

Team Lead

Agent A

Agent B

동료 간 직접 메시지 교환

Subagent는 일반적으로 부모에게 결과를 반환하는 위임 모델이다. 반면 Agent Team은 팀원들이 서로 메시지를 주고받으며 협업할 수 있다.

따라서 `SendMessage` 기반 팀원 간 통신 규칙은 Agent Team으로 실행할 때 의미가 있다. 같은 Agent File을 Subagent로 사용할 때 그 통신 프로토콜이 그대로 작동한다고 가정해서는 안 된다.

## 7. 실습: Code Reviewer Agent 설계

실습 목표는 코드를 수정하지 않고 결함을 분석해 구현 담당자에게 전달하는 에이전트를 정의하는 것이다.

파일 위치: `.claude/agents/code-reviewer.md`

```

---
name: code-reviewer
description: >
  Review code changes after implementation.
  Identify correctness, security, and maintainability
  issues. Do not modify source code.
model: sonnet
tools:
  - Read
  - Grep
  - Glob
maxTurns: 12
---

# Role
You are a code review specialist.
Review changed code and report actionable issues.
Never modify source files.

# Working Principles
Prioritize:
1. Security vulnerabilities
2. Functional correctness
3. Maintainability

Only report issues supported by code evidence.
Do not invent missing context.

# Input / Output Protocol
Input:
- Changed file paths
- Relevant requirements

Output:
- Severity: critical / major / minor
- File and line reference
- Evidence
- Recommended action

# Team Communication
When running as an Agent Team teammate:
- Send actionable findings to the implementation agent.
- Include evidence and the required correction.
- Notify the team lead when review is complete.

# Error Handling
- If a file cannot be read, retry once.
- If required context is missing, report the blocker.
- Do not mark the review as passed without evidence.

# Quality Verification
Before returning:
- Verify every finding has evidence.
- Remove duplicate findings.
- Distinguish confirmed defects from potential risks.
- Confirm no source files were modified.

```

### 실습 점검표

Agent Definition 검토

0/7 완료

Frontmatter에 name과 description이 있는가?

역할의 책임 범위가 명확한가?

불필요한 도구 권한을 제거했는가?

입출력 형식과 산출물 위치를 정의했는가?

실패 및 재시도 조건을 정의했는가?

협업 메시지에 수신자와 요청 행동이 포함되는가?

완료 전 품질 검증 기준이 있는가?

초기화

## 8. 스터디 토론 질문

1. Role vs Capability: Agent를 역할별로 분리하는 것과 도구·능력별로 분리하는 것 중 어떤 기준이 더 적절할까?
2. Protocol vs Autonomy: 협업 프로토콜을 엄격하게 정의하면 안정성은 높아지지만 에이전트의 자율성이 제한되지 않을까?
3. Self-Verification: 자체 검증 체크리스트만으로 품질을 보장할 수 있을까? 별도의 Reviewer Agent가 필요한 조건은 무엇일까?
4. Subagent vs Agent Team: 작업을 독립적으로 분할할 수 있는 경우와 팀원 간 상호작용이 필요한 경우를 어떻게 구분할까?

## 9. 핵심 정리

Agent File은 단순한 페르소나 프롬프트가 아니라 역할, 실행 권한, 작업 절차, 협업 인터페이스, 실패 대응 및 품질 기준을 명시하는 실행 계약이다.

설계의 핵심은 세 가지다.

- Role Clarity: 에이전트의 책임과 경계를 명확히 한다.
- Least Privilege: 역할에 필요한 최소한의 도구와 권한만 제공한다.
- Verifiable Contract: 입출력과 완료 조건을 다른 에이전트가 확인할 수 있도록 정의한다.

참고 문서: [Claude Code — Custom Subagents](https://code.claude.com/docs/en/sub-agents?utm_source=chatgpt.com) · [Claude Code — Agent Teams](https://code.claude.com/docs/en/agent-teams?utm_source=chatgpt.com)
