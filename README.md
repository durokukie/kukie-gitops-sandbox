# Kukie GitOps 시험용 매니페스트 레포

Kukie GitOps 기능(채팅으로 이 레포에 PR을 올려 ArgoCD 관리 리소스를 바꾸기)을 시험하는 작은 앱 셋이다. 시험 클러스터 `kukie-gitops-test`의 ArgoCD가 main을 읽는다. Application 정의와 설치 절차는 [kukie-gitops-test-infra](https://github.com/durokukie/kukie-gitops-test-infra)의 `kubernetes/argocd/`와 `runbooks/argocd.md`에 있다.

| 경로 | 형식 | Application | 바꿀 곳 | sync |
| --- | --- | --- | --- | --- |
| `apps/plain` | plain YAML (Deployment `web`, Service) | `sandbox-plain` → ns `sandbox-plain` | YAML 문서 그대로 | 자동 + selfHeal |
| `apps/kustomize` | Kustomize `base` + `overlays/prod` (Deployment `api` 2개) | `sandbox-kustomize` → ns `sandbox-kustomize` (`overlays/prod`) | `overlays/prod`만. `replicas:`·`images:`·`env.yaml` patch | 자동 + selfHeal |
| `apps/helm` | 로컬 Helm 차트 `worker` + `values-prod.yaml` | `sandbox-helm` → ns `sandbox-helm` (`helm.valueFiles`) | `values-prod.yaml` | 자동만(selfHeal 끔) |

이미지는 Docker Hub 제한을 피해 ECR Public의 `nginx`를 쓴다. 시험용이라 실제 서비스나 비밀 값은 넣지 않는다.

렌더 확인:

```sh
kustomize build apps/kustomize/overlays/prod
helm template worker apps/helm -f apps/helm/values-prod.yaml
```
