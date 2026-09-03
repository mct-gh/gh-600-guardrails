## Step 3: 위험에 따라 경로를 나눈다

모든 변경을 사람이 승인하면 아무도 에이전트를 쓰지 않습니다.
저위험은 흘려보내고 고위험만 잡아야 합니다. 그 판단을 기계가 하게 만듭니다.

<img width="180" alt="Jetpacktocat" src="../images/jetpacktocat.png" />

### 📖 이론: 기계가 읽는 위험 신호

계획서에 "이건 좀 위험합니다" 라고 적어 두는 것으로는 아무것도 라우팅되지 않습니다.
Learn 의 표현을 옮기면 이렇습니다.

> 위험을 서술로 설명하지 말고, 기계가 읽는 신호로 만들어 강제하라.

그래서 계획을 `plan.json` 같은 구조화된 파일로 만들고,
그 안의 `risk` 값으로 실행 경로를 나눕니다.

- `low` 면 자동으로 적용
- 그 밖이면 환경 승인으로 보냄

**환경(environment)** 은 승인 게이트를 만드는 가장 강한 장치입니다.
required reviewers 가 설정된 환경을 대상으로 하는 잡은 승인 전까지 멈춰 있습니다.
프로덕션 시크릿도 그 환경에만 두면 승인 없이는 접근할 수 없습니다.

프로덕션 배포는 겹쳐 돌면 안 되므로 직렬화합니다.

```yaml
concurrency:
  group: production
  cancel-in-progress: true
```

> [!TIP]
> 사람은 **결정 지점**에 둡니다. 머지, 배포, 시크릿 접근입니다.
> 분석이나 포매팅 같은 모든 단계에 사람을 두면 거버넌스 피로가 생기고,
> 승인이 "막힌 것 뚫는 클릭" 으로 전락합니다.

### ⌨️ 실습: 환경 승인과 위험 라우팅

1. **Settings → Environments → New environment** 로 갑니다.

1. 이름을 `approval-required` 로 만듭니다.

1. **Required reviewers** 를 켜고 본인 계정을 추가한 뒤 **Save protection rules** 를 누릅니다.

1. 아래 경로에 워크플로 파일을 만듭니다.

    ```
    .github/workflows/risk-routing.yml
    ```

1. 아래 내용을 붙여 넣습니다.

    ```yaml
    name: Risk routing

    on:
      workflow_dispatch:
        inputs:
          risk:
            description: "위험 등급 (low / medium / high / critical)"
            required: true
            default: "low"

    permissions:
      contents: read

    jobs:
      plan:
        runs-on: ubuntu-latest
        outputs:
          risk: ${{ steps.read.outputs.risk }}
        steps:
          - name: Build a machine-readable plan
            run: |
              mkdir -p out
              echo "{\"risk\":\"${{ inputs.risk }}\"}" > out/plan.json
              cat out/plan.json
          - id: read
            name: Read risk from plan.json
            run: |
              echo "risk=$(jq -r .risk out/plan.json)" >> "$GITHUB_OUTPUT"

      apply_auto:
        needs: plan
        if: ${{ needs.plan.outputs.risk == 'low' }}
        runs-on: ubuntu-latest
        steps:
          - run: echo "저위험. 승인 없이 적용합니다."

      apply_gated:
        needs: plan
        if: ${{ needs.plan.outputs.risk != 'low' }}
        runs-on: ubuntu-latest
        environment:
          name: approval-required
        concurrency:
          group: production
          cancel-in-progress: true
        steps:
          - run: echo "고위험. 사람이 승인해서 여기까지 왔습니다."
    ```

1. **Commit changes** 로 `main` 에 커밋합니다.

1. **Actions → Risk routing → Run workflow** 로 두 번 실행해 보세요.

    - `risk` 를 `low` 로 두면 그냥 통과합니다
    - `high` 로 두면 승인 대기에 걸립니다. **Review deployments** 버튼이 나타납니다

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- **채점이 통과하지 않습니다**
  - 환경 이름이 `approval-required` 인지 확인하세요.
  - 워크플로 파일에 `environment` 와 `jq` 가 들어 있어야 합니다.

- **승인 대기에 안 걸립니다**
  - 환경에 Required reviewers 가 실제로 저장됐는지 확인하세요.
  - `risk` 입력을 `low` 가 아닌 값으로 넣었는지 확인하세요.

- **환경 메뉴가 안 보입니다**
  - 비공개 리포지토리는 플랜에 따라 환경 보호 규칙이 제한됩니다. 공개로 만드세요.

</details>

---
