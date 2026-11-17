# GitHub 리포 설정 체크리스트 (리드)

> 리포: `smart1642/aidlc_workshop` (Public → 무료 플랜에서도 브랜치 보호 사용 가능)

## 1. 브랜치 생성

```bash
git checkout main && git pull
git checkout -b develop
git push -u origin develop
```

- [ ] Settings → General → **Default branch** 를 `develop` 으로 변경 (PR 기본 대상이 develop 이 됨)

## 2. 협업자

- [ ] Settings → Collaborators → 팀원 3명 초대, 권한 **Write**
- [ ] `.github/CODEOWNERS` 의 `@smart1642` 가 리드 GitHub 계정이 맞는지 확인 (다르면 수정)

## 3. 머지 옵션 (Settings → General → Pull Requests)

- [ ] **Allow merge commits**: 켜기 (`develop` → `main` 용)
- [ ] **Allow squash merging**: 켜기 (작업 브랜치 → `develop` 용)
  - Default commit message: **Pull request title**
- [ ] **Allow rebase merging**: 끄기
- [ ] **Automatically delete head branches**: 켜기

## 4. 브랜치 보호 (Settings → Branches → Add branch protection rule)

`main`, `develop` 각각 동일하게 설정:

- [ ] Branch name pattern: `main` / `develop`
- [ ] **Require a pull request before merging**
  - [ ] Required approvals: **1**
  - [ ] **Require review from Code Owners** (→ 리드 승인 필수)
  - [ ] Dismiss stale pull request approvals when new commits are pushed
- [ ] **Require status checks to pass before merging**
  - [ ] Require branches to be up to date before merging
  - [ ] 체크 선택: `branch-rule`, `markdown-lint` (CI 가 한 번 돈 뒤에 목록에 나타남)
- [ ] **Require conversation resolution before merging**
- [ ] **Do not allow bypassing the above settings**: **끄기**
  - 이유: 리드 본인 PR 은 본인이 승인할 수 없음 → 팀원 리뷰 후 리드가 관리자 권한으로 머지
- [ ] Allow force pushes: 끄기 / Allow deletions: 끄기

## 5. Actions

- [ ] Settings → Actions → General → **Allow all actions** (또는 GitHub·검증된 작성자 액션 허용)
- [ ] 첫 PR 에서 `CI` 워크플로가 실행되는지 확인

## 6. 확인

- [ ] 팀원이 `develop` 에 직접 push → 거부되는지
- [ ] 리드 승인 없이 PR 머지 버튼이 비활성인지
- [ ] 잘못된 브랜치 이름(예: `test123`)으로 PR → `branch-rule` 실패하는지
