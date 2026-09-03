## Step 2: 소유자를 정하고 규칙으로 강제한다

등급표는 문서입니다. 문서는 지켜지지 않습니다.
이제 플랫폼이 강제하도록 옮깁니다.

<img width="180" alt="Inflatocat" src="../images/inflatocat.png" />

### 📖 이론: CODEOWNERS 와 룰셋

`CODEOWNERS` 는 경로별로 리뷰어를 자동 지정합니다.
멀티 에이전트 환경에서는 이것이 **중재 장치**가 됩니다.
어느 에이전트가 만든 변경이든 민감한 경로를 건드리면 담당자에게 자동으로 갑니다.

```
/security/           @security-team
/.github/workflows/  @platform-team
/infra/              @platform-team
*                    @core-team
```

> [!WARNING]
> 룰셋에서 **require CODEOWNERS review** 를 켜지 않으면
> `CODEOWNERS` 는 권고에 그칩니다. 리뷰어가 자동 지정되기만 하고 머지는 막지 못합니다.
> 파일을 만드는 것과 강제하는 것은 별개입니다.

**룰셋**으로 보호 브랜치에 걸 것들입니다.

- 머지하려면 PR 이 필요하다
- 필수 체크가 통과해야 한다
- 승인 리뷰가 필요하다 (위험에 따라 수를 정한다)
- 민감한 경로는 CODEOWNERS 리뷰가 필요하다
- 직접 푸시를 막는다
- force push 와 브랜치 삭제를 막는다

> [!NOTE]
> 룰셋이 **서명 커밋**을 요구하면 `copilot/` 브랜치 푸시가 막힙니다.
> 에이전트는 커밋에 서명할 수 없기 때문입니다. 필요하면 룰셋의 bypass actor 에
> GitHub Copilot 을 추가합니다. 실습 리포에는 서명 커밋 규칙을 걸지 마세요.

### ⌨️ 실습: CODEOWNERS 와 룰셋

1. 아래 경로에 파일을 만듭니다. 리포지토리 루트입니다.

    ```
    CODEOWNERS
    ```

1. 아래 내용을 붙여 넣되, `@여러분계정` 을 실제 본인 계정으로 바꿉니다.

    ```
    # 민감한 영역은 담당자가 반드시 검토한다
    /.github/workflows/   @여러분계정
    /docs/                @여러분계정
    *                     @여러분계정
    ```

1. **Commit changes** 로 `main` 에 커밋합니다.

1. **Settings → Rules → Rulesets → New ruleset → New branch ruleset** 으로 갑니다.

1. 이렇게 설정합니다.

    - Ruleset Name: `main-protection`
    - Enforcement status: **Active**
    - Target branches: **Include default branch**
    - 체크할 항목
      - Require a pull request before merging
      - Required approvals: `1`
      - Require review from Code Owners
      - Block force pushes

    > 서명 커밋(Require signed commits)은 **켜지 마세요.** 에이전트 푸시가 막힙니다.

1. **Create** 를 눌러 저장합니다.

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- **채점이 통과하지 않습니다**
  - `CODEOWNERS` 파일이 리포지토리 루트에 있어야 합니다. (`.github/CODEOWNERS` 도 유효하지만
    이 실습은 루트를 확인합니다.)
  - 룰셋이 **Active** 상태인지 확인하세요. Evaluate 상태는 강제하지 않습니다.

- **내 계정이 코드 소유자로 인정되지 않습니다**
  - `@` 를 빼먹지 않았는지, 계정명 철자가 맞는지 확인하세요.
  - 개인 리포에서는 본인이 유일한 소유자라 자기 PR 을 승인하지 못할 수 있습니다.
    다음 단계에서 이 점을 이용합니다.

- **룰셋 메뉴가 안 보입니다**
  - Settings 왼쪽 사이드바에서 Rules 아래에 있습니다.

</details>

---
