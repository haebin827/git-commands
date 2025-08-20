# Git 심화 개념 & 팀 운영 팁 (Cheatsheet)

## 필수 심화 개념

- **객체 모델**
    - `blob` / `tree` / `commit` / `tag`, `refs`
    - `loose object` vs `packfile` (리포 최적화 시 `repack`, `gc`)

- **리베이스 전략**
    - 로컬 *feature* 브랜치에서는 **rebase OK** (선형 히스토리)
    - **공유 브랜치에서는 rebase 금지** (공유 이력 변경 금지)

- **머지 전략**
    - **fast-forward**: 분기 없이 포인터만 이동
    - **no-ff**: 머지 커밋 생성(기록 보존)
    - **ours / theirs**: 충돌 시 전략적 선택(주의해서 사용)
    - `--squash`: 여러 커밋을 한 커밋으로 합쳐 단순화

- **충돌 해결 숙련**
    - `git mergetool` 사용법 숙지
    - `git rerere`(충돌 해결 캐시)로 반복 충돌 자동 해결
    - 3-way merge 뷰 이해 & 수동 편집 능력

- **기록 복구**
    - `git reflog` + `git fsck` 로 잃어버린 커밋 탐색/복구

- **CI/CD와 태깅**
    - 릴리스는 **annotated tag** + (선택) GPG 서명
    - `CHANGELOG.md` 자동화(예: Conventional Commits)
    - **Semantic Versioning** 준수: `MAJOR.MINOR.PATCH`

- **크로스플랫폼 줄바꿈(EOL)**
    - `.gitattributes`로 `text eol=lf` 정책 고정
    - Windows 개발자는 `core.autocrlf=input` 권장

- **대용량/바이너리**
    - Git LFS 사용법 숙지(`git lfs track`)
    - 큰 리포 최적화: `git gc`, `git repack`

- **보안**
    - **submodule** vs **subtree** 차이 이해(의존성 관리)
    - 커밋/Git 태그 **GPG 서명**(`commit.gpgsign`)
    - 비밀키 유출 방지 및 `git filter-repo`로 이력에서 제거

---

## 팀 운영 팁

- **보호 브랜치**
    - `main`/`develop`에 **force-push 금지**
    - **필수 코드 리뷰** & **status check(CI) 통과** 요구

- **PR 가이드**
    - 변경은 **작은 단위**로 나누기
    - **의미 있는 커밋 메시지**: 제목 ≤ 50자, 본문 72자 래핑, *왜(의도)* 명시

- **브랜치 네이밍**
    - `feat/<ticket-id>-...`, `fix/...`, `chore/...` 등 규칙화(티켓번호 포함)

- **pull 전략 통일**
    - 팀 합의로 `pull.rebase=true` 또는 `false` 고정

- **훅(Hooks) 활용**
    - `pre-commit`: lint/test 자동 실행
    - `commit-msg`: 메시지 포맷 검사(예: Conventional Commits)
    - `pre-push`: 단위 테스트 실행

---

## 유용한 설정/스니펫

```bash
# 커밋 서명(선택)
git config --global commit.gpgsign true

# pull 전략 통일 (팀 규칙에 따라 true/false)
git config --global pull.rebase true

# EOL 정책 (리포지토리 루트)
echo "* text=auto eol=lf" >> .gitattributes
git config --global core.autocrlf input

# 반복 충돌 자동 학습
git config --global rerere.enabled true

# LFS 예시
git lfs install
git lfs track "*.psd"
