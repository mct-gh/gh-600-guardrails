## 정리

자율성 등급을 정하고, 소유자와 룰셋으로 강제하고, 위험에 따라 실행 경로를 나누고,
에이전트가 실제로 벽에 걸리는 것을 확인했습니다.

여기까지가 GH-600 하루 과정의 마지막 실습입니다.

### 꼭 기억할 것

- 자율성 4단계 — read-only / propose-only / execute with guardrails / human-authorized
- 위험 4등급 — Low 전자동 / Medium PR+체크 / High CODEOWNERS+복수승인 / Critical 환경게이트
- `.github/workflows/`, `infra/`, `security/` 는 기본 High. 작은 diff 큰 결과
- 위험을 서술로 설명하지 말고 기계가 읽는 신호로 만들어 라우팅한다
- require CODEOWNERS review 를 켜지 않으면 CODEOWNERS 는 권고에 그친다
- 사람은 결정 지점에 둔다. 머지, 배포, 시크릿 접근. 모든 단계가 아니다
- 설정으로 표현 안 되는 규칙은 워크플로로 강제한다 (방어 심층화)
- 에이전트 PR 작성자 로그인은 **`Copilot`** 이다. `github-copilot[bot]` 은 존재하지 않는다
- 룰셋이 서명 커밋을 요구하면 `copilot/` 푸시가 막힌다. bypass actor 로 Copilot 추가
- 플랫폼이 강제할 수 없으면 선택사항으로 취급하라

### 최소 권한

권한이 에이전트의 진짜 능력 경계입니다. 지시문이 아니라 권한이 결정합니다.

```yaml
permissions:
  contents: read      # 기본은 읽기만

jobs:
  update_artifacts:
    permissions:
      contents: write        # 써야 하는 잡만 승격
      pull-requests: write   # 이건 상승된 권한으로 취급한다
```

### 거버넌스 점검 주기

거버넌스는 한 번 만들고 끝나지 않습니다. 시간이 지나면 드리프트가 생깁니다.
체크 이름이 바뀌고, 권한이 넓어지고, CODEOWNERS 가 낡습니다.

| 주기 | 볼 것 |
| --- | --- |
| 주간 | 실패한 실행과 흔한 정책 위반 |
| 월간 | 워크플로 권한과 시크릿 범위 |
| 분기 | 룰셋, CODEOWNERS, 환경 리뷰어, 보존 정책 |

### 시험에서는

**영역 6 — 가드레일 및 책임 구현 (10~15%)**

관련 문항 유형
- 운영·보안·규정 위험으로 에이전트 행동을 분류해 개입 수준을 맞춘다
- 사람의 판단이 필요한 행동의 부분집합을 식별한다
- 보안·규정·책임 있는 AI 정책을 위반하는 행동을 차단한다
- 최소 권한으로 권한과 실행 컨텍스트를 좁힌다
- 되돌릴 수 없는 변경에 명시적 승인 경로를 요구한다
- 위험을 실질적으로 줄이지 않는 승인은 줄여 속도를 지킨다

### 하루 전체 정리

| 실습 | 배운 것 | 영역 |
| --- | --- | --- |
| 1 | 에이전트 수명 주기, 기여자 모델 | 1 |
| 2 | 계획 게이트, 도구 경계, MCP | 1, 2 |
| 3 | 에이전틱 워크플로 | 2 |
| 4 | 멀티 에이전트 조정 | 5 |
| 5 | 팬인, 아티팩트, 실패 분류 | 3, 4, 5 |
| 6 | 자율성 등급과 가드레일 | 6 |

### 더 볼 것

- [Governance, guardrails, and operations](https://learn.microsoft.com/ko-kr/training/modules/governance-guardrails-operations/)
- [Copilot cloud agent 위험과 완화](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations)
- [GH-600 시험 학습 가이드](https://learn.microsoft.com/ko-kr/credentials/certifications/resources/study-guides/gh-600)
- [실습 모음으로 돌아가기](https://github.com/mct-gh/gh-600-labs)

수고하셨습니다.
