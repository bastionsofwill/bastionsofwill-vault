# 그립의 클라우드 거버넌스와 로그 분석 혁신
# 비즈니스 성장과 다중 계정 관리 중요성
## AWS Control Tower
- 다중 계정 환경 설정 자동화
- LZ 기반의 계정 관리 및 자동 생성
- 보안 가드레일 사용하여 규정 준수 제어 가능
- 중앙 집중화된 관리
- 실시간 규정 준수 상태 모니터링 대시보드

### Management Account
 - Service Catalog
 - AWS SSO
 - AWS Organizations
 - Management Accoun

## OpenSearch
- Centralized logging 및 시각화

# ISMS 도전 과제 해결 방안
- 매출 100억 이상 시 ISMS-P 인증 대상
	- 인증 및 권한 관리
	- 접근 통제
	- 암호화 적용
	- 정보시스템 도입 및 개발 보안
	- 시스템 및 서비스 운영 관리
- 거버넌스 및 모니터링 체계 구성

# 거버넌스
## 계정 분리
- Security OU
	- log archive account: log 수집하여 필요한 담당자만 접근 허용
	- audit account: 유사 시 다른 계정에 cross-account 접근
- Shared Service OU
	- shared service: 비용분석, short link 등의 공통 service
	- network: Transit gateway, route53
	- security: IPS, quarify(?, DB/server 접근)
- Dev OU
	- 상대적으로 헐겁게
- Prod OU
	- 상대적으로 빡빡하게
- Sandbox OU
	- 신사업 용도
	- 추후 계정 분리될 경우
## IAM Identity Center
- 용도별 IAM identity center group 생성
-  EKS의 경우, R/W 권한을 별도로 관리해야 하므로 이를 별개 group으로 구성
- Built-in 그룹과 관리형 정책 set: 주로 보안팀에서 사용 중

## Centralized 네트워크 관리 체계
- Transit Gateway 라우팅 테이블 통해서 모든 VPC 관리(?) 

## Guardrail
- CfCT 활용한 K-ISMS 팩 적용(AWS Config)
- AWS Control tower로 계정을 만들면, CloudWatch event 발생하여 이를 Lambda에서 받아서 S3 파일 기반 프로비저닝 가능

# 보안 모니터링
다중 계정 체계 적용에 대한 계정 별 불편함

Centralized logging pipeline 구성: solutions library 활용
