# 협업 규칙

## 1. 브랜치

### 장기 브랜치

| 브랜치 | 용도 | 직접 Push |
| --- | --- | --- |
| `main` | 배포 기준. AWS 배포는 여기서만 | 금지 (PR 만) |
| `develop` | 통합. 모든 작업 브랜치의 출발점·도착점 | 금지 (PR 만) |

### 작업 브랜치

- 이름: **영문 소문자 + kebab-case**, `<종류>/<이름>`
- 항상 `develop` 에서 생성 (`hotfix/*` 만 `main` 에서 생성)

| 패턴 | 용도 | 예시 | 생성 기준 | PR 대상 |
| --- | --- | --- | --- | --- |
| `inception/<개인이름>` | 개인 Inception | `inception/dongryul` | `develop` | `develop` |
| `inception/merge` | Inception 통합 (리드) | `inception/merge` | `develop` | `develop` |
| `feature/<unit-name>` | Unit Construction | `feature/order-service` | `develop` | `develop` |
| `feature/<설명>` | Unit 외 기능 | `feature/common-logger` | `develop` | `develop` |
| `fix/<설명>` | 버그 수정 | `fix/login-timeout` | `develop` | `develop` |
| `docs/<설명>` | 문서 | `docs/update-rules` | `develop` | `develop` |
| `chore/<설명>` | 설정·빌드·기타 | `chore/ci-node-cache` | `develop` | `develop` |
| `hotfix/<설명>` | 배포 후 긴급 수정 | `hotfix/api-500` | `main` | `main` → 이후 `develop` 에 반영 |

> CI 의 `branch-rule` 체크가 위 규칙(브랜치 이름, PR 대상)을 검사한다.

## 2. 커밋

### 형식 (Conventional Commits)

```text
<type>(<scope>): <요약>

<본문 - 선택, 무엇을/왜>
```

- 요약: 한글 가능, 50자 이내, 마침표 없음
- `scope`: Unit 이름 또는 영역 (`order-service`, `inception`, `ci` 등)

| type | 용도 |
| --- | --- |
| `feat` | 기능 추가 |
| `fix` | 버그 수정 |
| `aidlc` | `aidlc-docs/`, `proposals/` 산출물 변경 (AI-DLC 단계 진행·승인) |
| `docs` | 그 외 문서 |
| `refactor` | 동작 변경 없는 코드 개선 |
| `test` | 테스트 |
| `chore` | 설정, 의존성, 기타 |
| `ci` | CI 설정 |

예시:

```text
aidlc(inception): requirements analysis 승인
aidlc(order-service): functional design 승인
feat(order-service): 주문 생성 API 구현
```

### 커밋 시점

- **AI-DLC 단계 하나를 승인할 때마다 1커밋** (산출물 + 해당 단계 코드)
- 한 커밋에 여러 Unit 섞지 않기

## 3. Push

- `main`, `develop` 직접 Push 금지 (보호 규칙으로 차단)
- 공유 브랜치 force push 금지
- 내 작업 브랜치에서는 `git push --force-with-lease` 만 허용 (`--force` 금지)
- 최소 **AI-DLC 단계 승인마다 / 하루 작업 끝**에 Push (작업 유실 방지)
- 비밀정보 Push 금지 (아래 6번)

## 4. PR

### 올리기 전

1. 최신 `develop` 반영: `git fetch origin && git merge origin/develop`
2. 충돌은 로컬에서 해결 (공용 파일은 [aidlc-team-workflow.md 5장](docs/aidlc-team-workflow.md#5-공용-파일-충돌-처리))
3. 내 Unit 범위 밖 파일이 바뀌지 않았는지 확인

### 규칙

- **PR 제목 = 커밋 형식** (Squash 시 PR 제목이 그대로 `develop` 커밋 메시지가 됨)
- 템플릿(`.github/pull_request_template.md`) 항목 채우기
- 승인: **리드(동률) 승인 필수** (CODEOWNERS)
- 리드 본인 PR: 팀원 1명 리뷰 후 리드가 관리자 권한으로 머지
- CI 통과 + 리뷰 코멘트 해결 후 머지
- PR 은 작게: 1 PR = 1 Unit 또는 1 주제

## 5. 머지

| From → To | 방식 | 이유 |
| --- | --- | --- |
| 작업 브랜치 → `develop` | **Squash** | `develop` 히스토리를 PR 단위 1커밋으로 깔끔하게 |
| `develop` → `main` | **Merge commit** | Squash 하면 두 브랜치 히스토리가 갈라져 매번 충돌 |
| `hotfix/*` → `main` | **Merge commit** | 동일 |
| `main` → `develop` (hotfix 반영) | **Merge commit** | 동일 |

- 머지 버튼 방식은 사람이 선택해야 하므로 **머지하는 사람(리드)이 확인**
- 머지 후 작업 브랜치는 자동 삭제

### 릴리즈 (`develop` → `main`)

- 시점: 리드가 결정 (Build and Test 완료 후, 배포 직전)
- PR 제목: `release: <날짜 또는 버전>` (예: `release: v0.1.0`)
- 머지 후 태그(선택): `git tag v0.1.0 && git push origin v0.1.0`

## 6. 비밀정보

- `.env`, AWS 키, 토큰은 **절대 커밋 금지** (`.gitignore` 에 등록됨)
- AWS 인증은 각자 로컬의 AWS CLI 프로파일/SSO 로만 사용
- 실수로 커밋했으면 즉시 리드에게 알리고 해당 키 폐기(재발급)

## 7. CI

`.github/workflows/ci.yml` — `develop`, `main` 대상 PR 과 Push 에서 실행.

| Job | 내용 | 비고 |
| --- | --- | --- |
| `branch-rule` | 브랜치 이름 / PR 대상 규칙 검사 | PR 에서만 |
| `markdown-lint` | 팀 문서(`README`, `CONTRIBUTING`, `docs/`) Markdown 검사 | `aidlc-docs/`, `proposals/` 제외 |
| `build-test` | 빌드·테스트 | **기술 스택 확정 후 추가** (현재 자리만) |

## 8. AWS 배포 (수동)

- 배포 대상: 워크샵용 AWS 계정
- **`main` 기준으로만 배포** (`git checkout main && git pull` 후)
- 배포 담당: 리드 또는 리드가 지정한 사람 1명 (동시 배포 금지)
- 배포 절차·명령: AI-DLC Construction 의 Infrastructure Design / Build and Test 산출물 기준으로 확정 후 이 문서에 추가
- 배포 후 팀 채널에 공유: 커밋 해시, 배포 시각, 결과
