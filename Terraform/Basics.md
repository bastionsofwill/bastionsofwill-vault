# How it works
![[images/Pasted image 20250124172240.png]]
Terraform은 논리적으로 크게 2개의 파트로 구성된다.

## Terraform Core
- Plugin과 통신하여 인프라 자원을 관리하는, Go로 작성되어 정적으로 compile된 binary
- CLI형태로 Terraform의 진입점(Entrypoint) 역할을 수행
- 다양한 CSP, DB, 서비스 등등과 상호작용할 수 있는 공통 interface를 제공한다.
- [github.com/hashicorp/terraform](https://github.com/hashicorp/terraform)
### 주요 기능
- IaC: 설정 파일과 모듈의 조회 및 보간(interpolation)
- 자원 상태(State) 관리
- [Resource Graph](https://developer.hashicorp.com/terraform/internals/graph) 생성
- Plan 실행
- Plugin과 RPC 통신
## Terraform Plugins
- Core와 RPC interface로 통신하도록 Go로 작성된 executable binaries.
- Provider 와 
	- [providers](https://developer.hashicorp.com/terraform/language/providers). Each provider plugin exposes an implementation for a specific service or tool, such as the [AWS provider](https://registry.terraform.io/providers/hashicorp/aws/latest) or the [cloud-init provider](https://registry.terraform.io/providers/hashicorp/cloudinit/latest/docs).
-
# References
- https://developer.hashicorp.com/terraform/plugin/how-terraform-works