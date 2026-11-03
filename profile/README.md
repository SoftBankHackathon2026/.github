# Team Ghost 👻 — SoftBank Hackathon 2026

온프렘 ↔ 클라우드 사이에서 DB와 앱을 옮기면서, 보안·정책 검사와 AI 수정 제안을 한 흐름으로 묶는 플랫폼을 만든다.

## 저장소 역할

```text
Hackathon        코드      이전 도구(migration/)·정책(policies/)·에이전트. CI가 Docker 이미지를 GHCR에 올린다
      │ 이미지
      ▼
gitops           선언      Kubernetes 매니페스트와 Argo CD Application. 클러스터 상태의 유일한 원본
      │ Argo CD sync
      ▼
k3s(온프렘) · AWS          실제 실행 환경
      ▲
oneaction-infra  AWS 땅    Terraform. VPC·RDS·라우팅 등 클러스터 바깥 리소스

sample-app       데모 앱   이전 대상이 되는 샘플 웹앱. CI가 이미지를 올리고 gitops 태그를 갱신
```

| 저장소 | 무엇이 들어가나 | 누가 쓰나 |
|---|---|---|
| [Hackathon](https://github.com/SoftBankHackathon2026/Hackathon) | 애플리케이션 코드. `migration/`(seed·INSERT 워커·Debezium CDC·전환·검증), `policies/`(OPA Rego) | gitops가 이미지 태그로 참조 |
| [gitops](https://github.com/SoftBankHackathon2026/gitops) | Kustomize 매니페스트(`apps/*`), Argo CD Application(`argocd/*`). 클러스터는 손으로 고치지 않고 여기서만 바꾼다 | Argo CD가 `main`을 보고 자동 적용 |
| [oneaction-infra](https://github.com/SoftBankHackathon2026/oneaction-infra) | AWS Terraform 스택. 상태는 S3 + DynamoDB 잠금 | `terraform apply` |
| [sample-app](https://github.com/SoftBankHackathon2026/sample-app) | 어느 환경이 응답했는지 보여주는 Express 앱. 이후 DB 읽기/쓰기 API 추가 | gitops `apps/sample-app` |

**흐름:** 코드 → 이미지(GHCR) → 선언(gitops) → 적용(Argo CD → 클러스터). AWS 리소스는 oneaction-infra가 따로 만들고, gitops 설정에 그 주소를 넣으면 연결된다.

## 작업 규칙

- 모든 변경은 feature 브랜치 → PR. PR 본문은 각 저장소의 템플릿(`feature` / `infra` / `design` …)을 따르고 작업 카드·Notion 링크를 붙인다.
- 클러스터·클라우드를 직접 수정하지 않는다. gitops / oneaction-infra 를 고치고 적용한다.
- 비밀번호·토큰·kubeconfig는 저장소에 넣지 않는다.

기획·회의록·작업 보드는 Notion `final_team_ghost` 에 있다.
