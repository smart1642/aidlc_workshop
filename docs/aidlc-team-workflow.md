# AI-DLC 팀 진행 절차

> AI-DLC v1 은 **1인 1세션** 기준으로 설계됨 (산출물 경로 `aidlc-docs/` 고정, 상태 파일 1개).
> 4명이 함께 쓰기 위해 아래 규칙으로 **"개인 Inception → 통합 → Unit 병렬 Construction"** 으로 진행.

## 전체 흐름

```text
[0 준비]  리드: 리포 설정 + 규칙 적용 / 전원: 공통 입력 작성
   │
[1 개인 Inception]   inception/<이름>  ×4   → proposals/<이름>/ 로 PR
   │
[2 Inception 통합]   inception/merge (리드 + 전원 리뷰) → aidlc-docs/ 확정
   │
[3 Unit 배정]        unit-of-work.md 기준, 1인 1 Unit
   │
[4 병렬 Construction] feature/<unit>  ×N  → develop (Squash)
   │
[5 Build and Test]   리드, develop 에서 전체 통합 검증
   │
[6 릴리즈·배포]       develop → main (Merge commit) → AWS 수동 배포
```

## 0. 준비

| 누가 | 할 일 |
| --- | --- |
| 리드 | [github-settings.md](github-settings.md) 설정, [aidlc-setup.md](aidlc-setup.md) 규칙 적용 |
| 전원 | `docs/inputs/vision.md`, `docs/inputs/technical-environment.md` **함께 작성 후 develop 반영** |

- 4명이 **같은 입력**으로 Inception 해야 결과를 비교·통합할 수 있음

## 1. 개인 Inception

```bash
git checkout develop && git pull
git checkout -b inception/<개인이름>        # 예: inception/dongryul
```

1. Kiro 에 요청:

   ```text
   Using AI-DLC, docs/inputs/vision.md 와 docs/inputs/technical-environment.md 를 읽고 새 프로젝트를 시작해줘.
   이번에는 INCEPTION 단계(Units Generation)까지만 진행하고 CONSTRUCTION 은 시작하지 마.
   ```

2. 질문 파일 답변 → 단계별 승인 → 단계마다 커밋 (`aidlc(inception): <단계> 승인`)
3. Units Generation 승인까지 완료되면 결과를 **본인 폴더로 이동**:

   ```bash
   mkdir -p proposals
   git mv aidlc-docs proposals/<개인이름>
   git commit -m "aidlc(inception): <개인이름> Inception 제안"
   git push -u origin inception/<개인이름>
   ```

4. PR → `develop` (제목 `aidlc(inception): <개인이름> Inception 제안`)
   - 각자 다른 폴더라 **충돌 없음**

> 루트의 `aidlc-docs/` 를 그대로 PR 하면 4명이 같은 파일을 덮어써서 충돌함 → 반드시 `proposals/<이름>/` 로 옮긴 뒤 PR.

## 2. Inception 통합 (리드 주도, 전원 참여)

```bash
git checkout develop && git pull            # proposals/ 4개가 모두 머지된 상태
git checkout -b inception/merge
```

1. **전원 함께** 4개 제안 비교 (요구사항, 스토리, 설계, Unit 분할)
   - 쟁점·채택안 메모: `proposals/merge-notes.md`
2. Kiro 에 요청:

   ```text
   Using AI-DLC, 새 프로젝트를 시작해줘.
   입력: docs/inputs/ 의 문서 + proposals/ 아래 4개 Inception 결과 + proposals/merge-notes.md 의 결정사항.
   4개 제안을 비교·통합해서 INCEPTION 을 Units Generation 까지 진행해줘. CONSTRUCTION 은 시작하지 마.
   ```

3. 질문 파일 답변은 **전원 합의**로 작성 → 단계별 승인·커밋
4. PR → `develop` → 머지되면 루트 `aidlc-docs/` 가 **팀 공식 산출물**
5. `proposals/` 는 기록용으로 유지 (이후 수정 금지)

### Unit 분할 시 요청할 것 (병렬화 위해)

- Unit 간 **겹치는 파일이 없도록** 소스 폴더를 Unit 별로 분리 (예: `services/<unit-name>/`)
- 공통 코드(공통 라이브러리, 공용 인프라)는 **별도 Unit** 으로 분리
- Unit 의존 관계를 `unit-of-work-dependency.md` 에 명시

## 3. Unit 배정

`aidlc-docs/inception/application-design/unit-of-work.md` 확정 후 아래 표를 채워 `develop` 에 반영.

| Unit | 담당 | 의존 Unit | 소스 폴더 | 브랜치 | 상태 |
| --- | --- | --- | --- | --- | --- |
| (예) common | 동률 | - | `services/common/` | `feature/common` | 대기 |
| | | | | | |

- 원칙: **1인 1 Unit**
- Unit 이 4개보다 많으면: 의존 순서대로 다음 Unit 을 이어서 담당
- Unit 이 4개보다 적으면: 남는 인원은 리뷰·테스트·인프라 담당 또는 페어
- **의존 Unit 이 있으면 그 Unit 이 develop 에 머지된 뒤 시작** (또는 인터페이스만 먼저 합의)

## 4. 병렬 Construction

```bash
git checkout develop && git pull
git checkout -b feature/<unit-name>
```

1. Kiro 에 요청:

   ```text
   Using AI-DLC, aidlc-docs 를 이어서 진행해줘.
   CONSTRUCTION 은 <unit-name> Unit 만 진행하고, Code Generation 승인까지만 해줘.
   다른 Unit 의 산출물과 소스는 수정하지 마.
   ```

2. Unit 내 단계: Functional Design → NFR Requirements → NFR Design → Infrastructure Design → Code Generation
   - 단계는 AI-DLC 가 필요에 따라 생략할 수 있음
3. 단계마다 승인·커밋 (`aidlc(<unit-name>): <단계> 승인`, `feat(<unit-name>): ...`)
4. PR 전 `git merge origin/develop` → 충돌 처리(5장) → PR → `develop` (Squash)

### 수정 가능 범위

| 수정 가능 | 수정 금지 |
| --- | --- |
| `aidlc-docs/construction/<내-unit>/` | 다른 Unit 의 `aidlc-docs/construction/<unit>/` |
| `aidlc-docs/construction/plans/<내-unit>-*.md` | `aidlc-docs/inception/` (변경 필요 시 리드와 협의 → 별도 PR) |
| 내 Unit 소스 폴더 | 다른 Unit 소스 폴더, 공통 Unit 소스 |
| `aidlc-docs/aidlc-state.md`, `audit.md` (AI-DLC 가 자동 갱신) | `.kiro/` 규칙 파일 |

- 공통 코드·Inception 변경이 필요하면: **혼자 고치지 말고** 리드에게 공유 → 별도 PR

## 5. 공용 파일 충돌 처리

| 파일 | 충돌 이유 | 처리 |
| --- | --- | --- |
| `aidlc-docs/audit.md` | 모두가 끝에 기록 추가 | `.gitattributes` 의 `merge=union` 으로 **로컬 merge 시 자동 병합** (양쪽 내용 모두 유지) |
| `aidlc-docs/aidlc-state.md` | 모두가 진행 상태 갱신 | **수동 해결**: develop 쪽 내용 유지 + 내 Unit 진행 줄만 반영 |
| `aidlc-docs/construction/shared-infrastructure.md` (생기면) | 공용 인프라 | 리드 소유. 변경은 리드와 협의 |

`aidlc-state.md` 충돌 해결 예:

```bash
git fetch origin
git merge origin/develop
# 충돌 시: aidlc-state.md 에서 다른 Unit 의 완료 표시는 모두 살리고, 내 Unit 줄만 내 브랜치 내용으로
git add aidlc-docs/aidlc-state.md
git commit
```

- `merge=union` 은 **로컬에서 git merge 할 때** 적용됨 → 그래서 PR 전에 반드시 로컬에서 `develop` 을 머지
- 해결이 애매하면 리드에게 요청

## 6. Build and Test (리드)

- AI-DLC v1 의 Build and Test 는 **모든 Unit 의 Code Generation 이후** 1회 실행
- 모든 `feature/<unit>` 머지 후:

  ```bash
  git checkout develop && git pull
  git checkout -b feature/build-and-test
  ```

  ```text
  Using AI-DLC, aidlc-docs 를 이어서 진행해줘. 모든 Unit 의 Code Generation 이 끝났으니 Build and Test 를 진행해줘.
  ```

- 결과 `aidlc-docs/construction/build-and-test/` → PR → `develop`
- 이후 CI 의 `build-test` job 을 확정된 스택에 맞게 추가 (`chore/ci-build-test`)

## 7. 릴리즈·배포

1. `develop` → `main` PR (`release: v0.1.0`), **Merge commit**
2. `main` 기준으로 AWS 워크샵 계정에 **수동 배포** ([CONTRIBUTING 8장](../CONTRIBUTING.md#8-aws-배포-수동))
3. 배포 후 문제 → `hotfix/*` (main 에서 생성) → `main` PR → `main` → `develop` PR

## 체크리스트 요약

- [ ] 같은 입력 문서로 개인 Inception
- [ ] 개인 결과는 `proposals/<이름>/` 로 옮긴 뒤 PR
- [ ] 통합 Inception 은 전원 합의로 답변
- [ ] Unit 별 소스 폴더 분리, 1인 1 Unit
- [ ] 내 Unit 범위만 수정
- [ ] PR 전 로컬에서 `develop` 머지
- [ ] 단계 승인마다 커밋
