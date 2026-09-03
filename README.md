# 실습 6 — 위험 기반 자율성과 가드레일

_에이전트가 뚫지 못하는 벽을 세우고, 실제로 걸리는 것을 눈으로 확인합니다._

## 이 실습에 대하여

- **대상**: 에이전트에게 무엇을 얼마나 맡길지 정하고 그것을 강제해야 하는 분
- **배우는 것**: 자율성 4단계와 위험 4등급, CODEOWNERS 와 룰셋,
  환경 승인 게이트, 기계가 읽는 위험 신호로 실행 경로 나누기
- **만드는 것**: 자율성 등급표, CODEOWNERS, 룰셋, 승인 환경, 위험 라우팅 워크플로
- **필요한 것**:
  - GitHub 계정과 리포지토리 관리자 권한
  - **Copilot 유료 플랜** (4단계에서 사용)
  - **공개 리포지토리** — 환경 보호 규칙이 비공개에서는 제한될 수 있습니다
- **소요 시간**: 25분

진행 순서

1. 자율성 등급표를 세운다
2. CODEOWNERS 와 룰셋으로 소유자와 규칙을 강제한다
3. 환경 승인과 위험 라우팅으로 저위험과 고위험 경로를 나눈다
4. 에이전트에게 보호된 경로를 건드리게 해서 벽이 작동하는지 본다

## 시작하는 법

아래 버튼으로 이 실습을 여러분 계정에 복사한 뒤, **20초 정도** 기다렸다가
페이지를 **새로고침** 하세요.

[![](https://img.shields.io/badge/Copy%20Exercise-%E2%86%92-1f883d?style=for-the-badge&logo=github&labelColor=197935)](https://github.com/new?template_owner=mct-gh&template_name=gh600-guardrails&owner=%40me&name=gh600-guardrails&description=GH-600+Lab+D+-+Risk-based+autonomy+and+guardrails&visibility=public)

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- 반드시 **공개(public)** 로 만드세요. 환경 보호 규칙과 룰셋이 비공개 리포에서는
  플랜에 따라 제한됩니다.
- 소유자는 여러분이 **관리자 권한을 가진** 계정이어야 합니다. 룰셋과 환경을 직접 만듭니다.

20초 뒤에도 준비되지 않으면 [Actions](../../actions) 탭을 확인하세요.

</details>

> [!IMPORTANT]
> **Fork 하지 마세요.** 위 Copy Exercise 버튼을 눌러야 채점이 동작합니다.
>
> 룰셋을 만들 때 **Require signed commits 는 켜지 마세요.**
> 에이전트는 커밋에 서명할 수 없어 `copilot/` 브랜치 푸시가 막힙니다.

## 시험 대응

**영역 6 — 가드레일 및 책임 구현 (10~15%)**

## 관련 학습 자료

- [Governance, guardrails, and operations](https://learn.microsoft.com/ko-kr/training/modules/governance-guardrails-operations/)
- [Copilot cloud agent 위험과 완화](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations)
- [실습 모음으로 돌아가기](https://github.com/mct-gh/gh-600-labs)
