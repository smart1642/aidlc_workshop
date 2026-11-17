# AI-DLC v1 적용 가이드 (Kiro)

> 기준 버전: **AI-DLC v1.0.1** (`awslabs/aidlc-workflows` 태그 `v1.0.1`)
> 방식: Kiro **steering 파일** 기반 규칙 (CLI·엔진 없음)

## 1. 누가 하나

- **리드가 1회 적용 → `develop` 에 커밋** → 팀원은 pull 만 하면 됨
- 이유: 4명이 같은 규칙 버전을 쓰게 고정 (각자 설치하면 버전이 달라질 수 있음)

## 2. 최종 폴더 구조

```text
aidlc_workshop/
├── .kiro/
│   ├── steering/
│   │   └── aws-aidlc-rules/        ← 핵심 규칙 (Kiro 가 자동 로드)  [커밋]
│   │       └── core-workflow.md
│   └── aws-aidlc-rule-details/     ← 단계별 상세 규칙 (필요 시 참조) [커밋]
│       ├── common/
│       ├── inception/
│       ├── construction/
│       └── operations/
│       (extensions/ 는 넣지 않음 → 확장 전부 꺼짐)
│
├── aidlc-docs/                     ← AI-DLC 가 생성하는 산출물 (통합본) [커밋]
│   ├── aidlc-state.md              ← 진행 상태 (공용 파일)
│   ├── audit.md                    ← 대화·승인 기록 (공용 파일)
│   ├── inception/
│   └── construction/
│       ├── plans/
│       ├── <unit-name>/
│       └── build-and-test/
│
├── proposals/                      ← 개인 Inception 결과 보관 [커밋]
│   └── <개인이름>/                 ← 각자의 aidlc-docs/ 를 옮겨 둔 것
│
├── docs/inputs/                    ← AI-DLC 에 넣는 공통 입력 [커밋]
└── (애플리케이션 소스)              ← 실제 코드는 aidlc-docs/ 밖, 리포 루트 쪽에 생성됨
```

| 폴더 | 만드는 주체 | 커밋 | 수정 규칙 |
| --- | --- | --- | --- |
| `.kiro/steering/aws-aidlc-rules/` | 리드 (복사) | O | 수정 금지 (버전 업 시에만 리드가 교체) |
| `.kiro/aws-aidlc-rule-details/` | 리드 (복사) | O | 수정 금지 |
| `aidlc-docs/` | AI-DLC (Kiro) | O | 사람이 직접 편집 최소화, `[Answer]:` 답변만 |
| `proposals/<이름>/` | 각자 | O | 본인 폴더만 |
| `docs/inputs/` | 팀 | O | 합의 후 수정 |

## 3. 적용 절차 (리드)

### macOS / Linux / WSL

```bash
# 1) develop 에서 작업 브랜치 생성
git checkout develop && git pull
git checkout -b chore/setup-aidlc-v1

# 2) v1.0.1 규칙 받기 (리포 밖 임시 폴더)
git clone --depth 1 --branch v1.0.1 https://github.com/awslabs/aidlc-workflows /tmp/aidlc-v1

# 3) Kiro 폴더에 복사
mkdir -p .kiro/steering
cp -R /tmp/aidlc-v1/aidlc-rules/aws-aidlc-rules .kiro/steering/
cp -R /tmp/aidlc-v1/aidlc-rules/aws-aidlc-rule-details .kiro/

# 4) 확장 끄기: extensions 폴더 제거
rm -rf .kiro/aws-aidlc-rule-details/extensions

# 5) 커밋 → PR (develop)
git add .kiro
git commit -m "chore(aidlc): AI-DLC v1.0.1 규칙 적용 (확장 제외)"
git push -u origin chore/setup-aidlc-v1
```

### Windows (PowerShell)

```powershell
git checkout develop; git pull
git checkout -b chore/setup-aidlc-v1

git clone --depth 1 --branch v1.0.1 https://github.com/awslabs/aidlc-workflows $env:TEMP\aidlc-v1

New-Item -ItemType Directory -Force -Path ".kiro\steering"
Copy-Item -Recurse "$env:TEMP\aidlc-v1\aidlc-rules\aws-aidlc-rules" ".kiro\steering\"
Copy-Item -Recurse "$env:TEMP\aidlc-v1\aidlc-rules\aws-aidlc-rule-details" ".kiro\"

Remove-Item -Recurse -Force ".kiro\aws-aidlc-rule-details\extensions"

git add .kiro
git commit -m "chore(aidlc): AI-DLC v1.0.1 규칙 적용 (확장 제외)"
git push -u origin chore/setup-aidlc-v1
```

> 대안: [Releases](https://github.com/awslabs/aidlc-workflows/releases) 에서 v1 의 `ai-dlc-rules-v<버전>.zip` 을 받아 압축 해제 후 같은 위치에 복사해도 됨.

## 4. 확장(Extensions) 끄기

v1 에는 선택형 확장 3개가 있음. **우리 팀은 전부 사용 안 함.**

| 확장 | 내용 |
| --- | --- |
| Security Baseline | 보안 규칙을 차단 조건으로 강제 |
| Property-Based Testing | 속성 기반 테스트 규칙 강제 |
| Resiliency Baseline | 복원력(장애 대응) 규칙 강제 |

- 끄는 방법: 위 절차 4) 처럼 `extensions/` 폴더를 **복사하지 않음** → 질문 자체가 안 나옴
- 혹시 Requirements 질문지에 확장 질문이 나오면: **`B) No`** 로 답변
  - 주의: 답을 비워두면 규칙상 **기본값이 "적용"** 임
- 나중에 켜고 싶으면: 해당 확장 폴더만 `.kiro/aws-aidlc-rule-details/extensions/` 에 복사 (팀 합의 후)

## 5. 적용 확인 (전원)

1. `develop` pull 후 Kiro IDE 로 리포 폴더 열기
2. Kiro 패널의 **Steering** 목록 → `Workspace` 아래 **`core-workflow`** 보이면 성공
   - Kiro CLI: `kiro-cli` 실행 → `/context show` → `.kiro/steering/aws-aidlc-rules` 확인
3. 채팅 모드는 **Vibe** 사용
   - Kiro 가 Spec 모드로 바꾸라고 제안하면 **No**

## 6. 사용 방법 (기본)

- 시작: Kiro 채팅에 `Using AI-DLC, <하고 싶은 것>` 형태로 요청
  - 예: `Using AI-DLC, docs/inputs/vision.md 를 읽고 요구사항 분석부터 시작해줘`
- 질문: AI-DLC 는 질문을 채팅이 아닌 **`.md` 파일**로 만듦 → 파일의 `[Answer]:` 뒤에 답 작성 후 채팅에 "답변 완료"
- 승인: 각 단계 끝에 승인 요청 → 검토 후 승인 → **커밋** (CONTRIBUTING 2장)
- 재개: 세션이 끊겨도 `aidlc-docs/aidlc-state.md` 를 읽고 이어서 진행

## 7. 버전 변경 규칙

- 규칙 버전 변경은 **리드만**, 별도 PR (`chore/upgrade-aidlc-<버전>`)
- 진행 중(Inception·Construction 도중)에는 버전 변경 금지
- v2(`aidlc` CLI 방식)로의 전환은 구조가 완전히 다르므로 별도 논의
