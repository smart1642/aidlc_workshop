# aidlc_workshop

AI-DLC(AI-Driven Development Life Cycle) **v1** 방법론으로 4명이 함께 개발하는 워크샵 리포지토리.

## 한눈에 보기

| 항목 | 결정 |
| --- | --- |
| 방법론 | AI-DLC v1.0.1 (문서 기반 규칙, Kiro steering) |
| 도구 | Kiro (Vibe 모드) |
| 브랜치 | `main`(배포), `develop`(통합) — release 브랜치 없음 |
| 머지 | `feature/*` → `develop`: **Squash** / `develop` → `main`: **Merge commit** |
| PR 승인 | 통합 리드(동률) 승인 필수 |
| AI-DLC 확장 | 전부 사용 안 함 |
| 기술 스택 | AWS 기반, 상세는 AI-DLC Inception 에서 결정 |
| 배포 | 워크샵용 AWS 계정, **사람이 직접** 배포 |

## 문서

| 문서 | 내용 | 대상 |
| --- | --- | --- |
| [CONTRIBUTING.md](CONTRIBUTING.md) | 브랜치·커밋·Push·PR·머지·CI·배포 규칙 | 전원 |
| [docs/aidlc-setup.md](docs/aidlc-setup.md) | AI-DLC v1 규칙을 폴더에 적용하는 방법, 폴더 구조 | 전원 (적용은 리드 1회) |
| [docs/aidlc-team-workflow.md](docs/aidlc-team-workflow.md) | 개인 Inception → 통합 → Unit 병렬 Construction 절차 | 전원 |
| [docs/github-settings.md](docs/github-settings.md) | GitHub 리포 설정 체크리스트 | 리드 |
| [docs/inputs/](docs/inputs/) | AI-DLC 에 넣을 공통 입력 문서 (비전, 기술 환경) | 전원 |

## 시작 순서

1. 리드: [docs/github-settings.md](docs/github-settings.md) 대로 리포 설정
2. 리드: [docs/aidlc-setup.md](docs/aidlc-setup.md) 대로 AI-DLC 규칙 적용 후 `develop` 에 반영
3. 전원: 리포 clone → `develop` checkout → Kiro 로 열고 규칙 로드 확인
4. 전원: [docs/aidlc-team-workflow.md](docs/aidlc-team-workflow.md) 순서대로 진행
