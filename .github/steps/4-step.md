## Step 4: 에이전트를 벽에 부딪히게 한다

벽을 세웠으니 확인할 차례입니다. 에이전트가 실제로 걸리는지 봅니다.

<img width="180" alt="Inspectocat" src="../images/inspectocat.png" />

### 📖 이론: 설정으로 표현 안 되는 규칙은 워크플로로 강제한다

룰셋과 CODEOWNERS 로 많은 것이 되지만 안 되는 것도 있습니다. 예를 들면 이런 규칙입니다.

> 승인은 **사람**이 한 것만 인정한다. 봇 승인은 세지 않는다.

이것은 설정 화면에 없습니다. 그래서 워크플로로 만듭니다.

```yaml
- name: Require at least one human approval
  uses: actions/github-script@v7
  with:
    script: |
      const { owner, repo } = context.repo;
      const pull_number = context.payload.pull_request.number;
      const reviews = await github.rest.pulls.listReviews({ owner, repo, pull_number });
      const approvedByHuman = reviews.data.some(r =>
        r.state === "APPROVED" && r.user && r.user.type === "User"
      );
      if (!approvedByHuman) {
        core.setFailed("사람의 승인이 필요합니다.");
      }
```

이것이 **방어 심층화(defense in depth)** 입니다.
룰셋도 걸고, CODEOWNERS 도 걸고, 그 위에 워크플로로 한 겹 더 얹습니다.

> [!WARNING]
> 에이전트 PR 작성자를 식별할 때 주의할 점이 있습니다.
> 실제 로그인은 `Copilot` 이고 타입은 `Bot`, id 는 `198982749` 입니다.
> 일부 자료에 `github-copilot[bot]` 으로 적혀 있지만 그런 계정은 **존재하지 않습니다.**
> 그대로 쓰면 조건이 절대 참이 되지 않아 가드레일이 작동하지 않습니다.

거버넌스 실패 패턴 다섯 가지도 함께 기억하세요.

| 안티패턴 | 모습 | 대응 |
| --- | --- | --- |
| unbounded autonomy | 프로덕션 배포에 승인이 없다 | 환경 + 필수 리뷰어 + 룰셋 |
| excess permissions | 토큰이 전부 쓰기 권한 | 최소 권한 + 잡 단위 승격 |
| missing audit trail | 콘솔 로그만 있고 아티팩트가 없다 | 아티팩트 업로드, 증거 우선 |
| bypass paths | main 직접 푸시, 체크 비활성화 | 룰셋으로 우회 경로 차단 |
| rubber-stamping | 승인이 "막힌 것 뚫는 클릭" 이 된다 | 증거 개선, CODEOWNERS, 작은 PR |

### ⌨️ 실습: 보호된 경로를 건드려 본다

1. Issues 탭에서 새 이슈를 만듭니다. 제목과 본문은 이렇게 씁니다.

    ```
    제목: docs 폴더에 운영 가이드 초안을 추가해 주세요

    본문:
    docs/operations.md 파일을 새로 만들고,
    이 리포지토리의 배포 절차와 롤백 방법을 정리해 주세요.
    docs/ 밖의 파일은 건드리지 마세요.
    ```

1. 이 이슈를 **Copilot 에게 할당**합니다.

1. 에이전트가 PR 을 열면 관찰합니다.

    - 브랜치 이름이 `copilot/` 으로 시작하는가
    - PR 이 **draft** 로 열렸는가
    - `docs/` 는 CODEOWNERS 대상이므로 **리뷰가 자동으로 요청**되는가
    - 승인 없이는 머지 버튼이 막혀 있는가

1. 승인 요청이 걸린 상태를 확인한 뒤 실습을 마칩니다.

    머지까지 해도 되고, 걸린 것만 확인하고 넘어가도 됩니다.
    이 실습의 목적은 **벽이 실제로 작동하는 것을 보는 것**입니다.

<details>
<summary>문제가 있나요? 🤷</summary><br/>

- **채점이 통과하지 않습니다**
  - CODEOWNERS 에 본인 계정이 `@` 와 함께 정확히 적혀 있어야 리뷰 요청이 걸립니다.
  - 룰셋에서 Require review from Code Owners 가 켜져 있는지 확인하세요.
  - 에이전트 작업에 몇 분 걸립니다. 기다려 주세요.

- **리뷰 요청이 안 걸립니다**
  - CODEOWNERS 규칙이 `docs/` 를 실제로 포함하는지 확인하세요.
  - 룰셋 상태가 Active 인지 확인하세요.

- **에이전트에게 할당할 수 없습니다**
  - Copilot 플랜과 조직 정책을 확인하세요. 실습 1의 문제 해결과 같습니다.

</details>

---
