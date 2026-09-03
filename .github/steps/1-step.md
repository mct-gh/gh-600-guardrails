## Step 1: 무엇을 얼마나 맡길지 먼저 정한다

가드레일을 세우기 전에 답해야 할 질문이 있습니다.
**이 에이전트에게 무엇까지 허용할 것인가.**

<img width="180" alt="Inspectocat" src="../images/inspectocat.png" />

### 📖 이론: 자율성은 위험에 맞춰 정한다

모든 작업의 위험이 같지 않습니다. README 를 고치는 것과 `.github/workflows/` 를
고치는 것은 완전히 다른 일입니다. 그런데 같은 규칙을 적용하면 둘 중 하나가 됩니다.
느려서 못 쓰거나, 위험해서 못 씁니다.

**자율성 4단계**

| 단계 | 할 수 있는 것 |
| --- | --- |
| read-only | 검사, 요약, 분류, 권고. 아무것도 수정 못 함 |
| propose-only | 브랜치와 PR 생성. 머지와 배포는 못 함 |
| execute with guardrails | 사전 승인된 워크플로를 정해진 한계 안에서 실행 |
| human-authorized | 사람이 승인해야 고영향 작업 수행 |

**위험 4등급과 통제**

| 위험 | 예 | 통제 |
| --- | --- | --- |
| Low | docs, 포매팅 | 전자동 허용 |
| Medium | src, 의존성 업데이트 | PR + 필수 체크 |
| High | infra, `.github/workflows/` | CODEOWNERS + 복수 승인 |
| Critical | 프로덕션 배포, 프로덕션 시크릿 | 환경 게이트 + 지정 리뷰어 + 감사 증거 |

위험은 행동 자체만으로 정해지지 않습니다. **어디에** 적용되는지, **얼마나 쉽게 되돌리는지**,
**얼마나 빨리 사용자에게 닿는지**가 함께 결정합니다.

> [!IMPORTANT]
> `.github/workflows/`, `infra/`, `security/` 는 기본적으로 High 로 둡니다.
> **작은 diff, 큰 결과** 인 영역입니다.

자율성은 한 번에 다 주지 않고 단계적으로 넓힙니다.
PR 만 → 저위험 머지 → 스테이징 자동 배포 → 프로덕션은 환경 승인.

### ⌨️ 실습: 자율성 등급표를 만든다

1. 아래 경로에 파일을 만듭니다.

    ```
    docs/autonomy-levels.md
    ```

1. 아래 틀을 채웁니다. 영어 표기는 **그대로** 두세요. 채점이 찾습니다.

    ```markdown
    # 자율성 등급표

    ## 자율성 단계
    - read-only: 검사와 권고만. 수정 불가
    - propose-only: 브랜치와 PR 생성 가능. 머지 불가
    - execute with guardrails: 사전 승인된 워크플로만 실행
    - human-authorized: 사람 승인 후에만 고영향 작업 수행

    ## 이 리포지토리의 경로별 위험 등급
    | 경로 | 위험 | 자율성 | 통제 |
    | --- | --- | --- | --- |
    | docs/ | Low | execute with guardrails | 자동 |
    | app/ | Medium | propose-only | PR + 필수 체크 |
    | .github/workflows/ | High | propose-only | CODEOWNERS + 복수 승인 |
    | 프로덕션 배포 | Critical | human-authorized | 환경 승인 + 감사 증거 |

    ## 점진적 확대 계획
    1. PR 만 허용
    2. 저위험 PR 자동 머지
    3. 스테이징 자동 배포
    4. 프로덕션은 환경 승인 유지
    ```

1. **Commit changes** 로 `main` 에 커밋합니다.

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- **채점이 통과하지 않습니다**
  - `docs/autonomy-levels.md` 경로를 확인하세요.
  - `read-only`, `propose-only`, `human-authorized`, `.github/workflows/` 네 표현이
    파일 안에 있어야 합니다.

- **왜 영어로 쓰나요**
  - 시험 문항이 영어 용어로 나옵니다. 지금 눈에 익혀 두는 편이 낫습니다.

</details>

---
