# Notable updates
Tranium 2
EVS(Elastic VMware Service)
EKS Hybrid Nodes(On-prem, Cloud 노드 함께 운영)
Oracle DB@AWS in preview
Nova(새로운 생성형 AI 파운데이션 모델)

Amazon S3 Tables - Fully managed Apache Iceberg
Amazon S3 Metadata - preview

# re:Invent 2024 Keynote summary
## Peter DeSantis
### 컴퓨팅 혁신
- Graviton 4: 최근 2년간 새로 추가된 CPU 중 절반이 Graviton
- Nitro(가상화 기술, 보안/네트워크 관련 분리가 핵심): 유연성
### 스토리지
- Nitro 기반 분산 스토리지
### AI
- Tranium2(Trn2)
- AI 전용 네트워크: Trn2 UltraServers
- Amazon Bedrock latency-optimized inference
### Anthropic
- Claude 협업, 최적화, Project Rainier(차세대 모델 프로젝트) 과시
- Claude 3.5 Haiku - Trn2 적용으로 60% 속도 향상

## Matt Garman
- 올해 AWS CEO 취임
- 신규 서비스 런칭 위주
- Trn2 GA, Trn3, P6(Blackwell) 인스턴스
- Compute, Storage, Network, **Inference** 4개의 building block을 제시
- Amazon Nova 발표: Speech to Speech(내년 1Q), Any to Any(내년)
- Amazon.com의 AWS 사용 사례
### Apple
- 10년 이상 협력했으나 공식적으로는 올해 처음 언급 및 협력관계 홍보
- 머신러닝 쪽 학습에서 AWS 인프라 사용 언급(Trn2, Graviton, Inf2(Inferentia2))

## Dr. Swami Sivasubramanian
- 신규 서비스 런칭 위주(Sagemaker, Bedrock, Q, ...)

## Dr. Werner Vogels
- 아키텍처 패턴 설계 지침 위주
Complexity를 다루기 위한 6가지 교훈 - Simplexity
1. Evolvability를 요구사항으로 설정
2. 복잡성을 작은 단위로 분해
3. 조직을 아키텍처에 맞추기
4. Cell 기반 아키텍처
5. 예측 가능한 시스템 설계
6. 복잡성의 자동화

# New services
## Storage
정형 데이터와 비정형 데이터의 관리
### S3 Tables
- 정형 데이터: 관리형 Apache Iceberg table
- S3 장점과 Warehouse같은 느낌을 잘 내주는 무언가...?
- AWS 관련 데이터들을 통합해서 query할 수 있게 해줌
- lake formation이라는 도구로 권한 정보를 해야함
### S3 Metadata
- 데이터가 워낙 많이 들어가있으니 찾기가 힘듦
- 21가지 metadata 및 custom 메타데이터를 함께 저장해서 각각 또는 다른 데이터와 join해서 query가 가능

## Security
### VPC Block Public Access
- Internet 통신을 제한할 필요가 있을 때 유용
### Amazon GuardDuty Extended Threat Detection
 - AI/ML 기반 인사이트 제공: 상관관계 분석을 통한 자동화된 triage
 - MITRE ATT&CK과 연동
### Centrally manage AWS IAM root access
Organization에 속한 계정의 root access를 원천 차단하는 기능
### Security Incident Response
모니터링 자동화 및 일부 조치 자동화도 가능
AWS Security expert team도 대응 가능
 
## Containers
### Amazon EKS Hybrid Nodes
on-premise 노드와 Cloud 노드를 함께 관리 가능(Control plane의 클라우드 상 운영)
노드와 Control plane 간 네트워크 연결: Direct Connect 또는 VPN
### Amazon EKS Auto Mode
컴퓨팅, 네트워킹 관련 설정 자동으로 최적화

## Database
### Amazon Aurora DSQL
분산형, 서버리스 DB
### DynamoDB multi-Region strong consistency
아무튼 빠르고 가용성좋고
### MemoryDb multi-Region
아무튼 빠르고 가용성좋고
### DMS Schema Conversion with genAI
복잡한 스키마 변환의 용이화

## Network & CDN
### AWS PrivateLink
- 다른 VPC 안에 있는 리소스에 접근 가능(원래 RDS, EC2 안됐음)
- Cross-Region Connectivity: 기존에는 Cross-region peering이 필요했음
### CDN
- gRPC Support
- Media Quality Aware resiliency: CloudFront가 전달하는 Media quality의 점수를 매겨서 품질이 좋은 source를 제공하는 edge를 우선으로 연결하는...?

# M&E focused update
## AWS AI stack
- Bedrock: 학습된 모델을 사용하는 애플리케이션의 개발 및 배포
- Sagemaker: 모델의 생성 및 학습

## Amazon Nova
### Understanding Model
Nova Micro, Lite, Pro Premier로 구분: Micro는 텍스트만, Lite/Pro 는 이미지, 비디오까지 
### Creative Content Generation Model
Nova Canvas: 텍스트/이미지 인풋, 이미지 아웃풋
Nova Reel: 비디오 아웃풋



## Amazon SageMaker
- Main Goal: End-to-end ML/AI development platform
- Key Features:
  - Build, train, and deploy ML models
  - Notebook environments for development
  - Built-in algorithms and frameworks
  - Model monitoring and management
  - MLOps capabilities
  - Custom model development
  - Data labeling and preparation

## Amazon Bedrock
- Main Goal: Managed service for using foundation models
- Key Features:
  - Access to pre-trained foundation models
  - API access to models like Claude, Llama 2, Titan
  - Model customization through fine-tuning
  - Built-in safety controls and guardrails
  - Serverless deployment
  - Integration with enterprise data

## AWS Neuron:
- Main Goal: SDK for ML acceleration on AWS hardware
- Key Features:
  - Optimizes ML workloads for AWS Inferentia and Trainium chips
  - Performance optimization for inference and training
  - Framework support (PyTorch, TensorFlow)
  - Hardware-specific optimizations
  - Cost-effective ML deployment

## Amazon Q:
- Main Goal: AI-powered assistant for developers and business users
- Key Features:
  - Code assistance and generation
  - Documentation help
  - AWS service integration
  - Business data analysis
  - Security-aware responses
  - Natural language interface


# Use Case Selection:
1. Choose SageMaker when:
   - Building custom ML models
   - Need complete ML lifecycle management
   - Want MLOps capabilities
   - Require extensive model training

2. Choose Bedrock when:
   - Need quick access to foundation models
   - Want to use pre-trained models
   - Require minimal ML expertise
   - Focus on application development

3. Use Neuron when:
   - Optimizing ML performance on AWS hardware
   - Need cost-effective ML deployment
   - Working with specific AWS ML accelerators

4. Use Amazon Q when:
   - Need AI assistance for development
   - Want conversational access to AWS services
   - Require business data analysis
   - Looking for code help and documentation