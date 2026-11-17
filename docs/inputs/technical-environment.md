# 기술 환경 (Technical Environment)

> AI-DLC 개인 Inception 에 **4명 공통으로** 넣는 입력. 확정된 것만 적고, 나머지는 "AI-DLC 에서 결정" 으로 둠.

## 확정

- 클라우드: **AWS** (워크샵용 계정)
- 배포 방식: **사람이 직접 배포** (CI/CD 자동 배포 없음)
- 소스 관리: GitHub, `main` / `develop`
- AI-DLC 확장(Security / Property-Based Testing / Resiliency): **사용 안 함**

## AI-DLC 에서 결정

- 언어 / 버전:
- 프레임워크:
- 테스트 도구:
- AWS 서비스 (컴퓨팅, DB, 스토리지 등):
- IaC 도구 (CDK / CloudFormation / Terraform / SAM 등):

## 금지 / 주의

- 비밀정보(키, 토큰)를 코드나 문서에 넣지 않음
- Unit 별 소스 폴더를 분리 (병렬 개발 충돌 방지)
