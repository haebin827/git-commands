# Git 안전 모드 옵션 모음집

## 1. --dry-run (예행연습 모드)
- **실행은 안 하고, 실행될 내용을 미리 보여줌**
- **언제 쓰나?**
    - 삭제 전: `git clean -n` → 삭제될 untracked 파일 목록만 출력
    - 푸시 전: `git push --dry-run origin main` → 푸시될 브랜치/커밋 미리 확인
    - 가져오기 전: `git fetch --dry-run`, `git pull --dry-run` → 가져올 변경 미리 확인

---

## 2. --force-with-lease (안전한 강제 푸시)
- **내 로컬 이력으로 원격을 덮어쓰되, 팀원이 그 사이에 올린 변경은 덮어쓰지 않음**
- `git push --force-with-lease origin branch`
- **언제 쓰나?**
    - rebase 후 푸시할 때 (보통 필요한 경우)
    - 팀원 커밋을 날리는 사고 방지
- 비교:
    - `-f` / `--force` = 무조건 덮어씀 (위험)
    - `--force-with-lease` = 내가 알고 있는 상태랑 원격이 일치할 때만 덮어씀 (안전)

---

## 3. --ff-only (Fast-Forward만 허용)
- **머지 시 새로운 merge commit을 만들지 않고, fast-forward만 허용**
- `git merge --ff-only origin/main`
- **언제 쓰나?**
    - 이력을 직선(linear)으로 유지하고 싶을 때
    - merge commit이 불필요하게 생기는 것을 방지
- 실패하면: “Fast-forward 불가” 메시지 출력 → rebase 등 다른 전략 필요

---

## 4. -n / --dry-run (미리보기 단축)
- **특히 위험 명령어 전 확인**
    - `git clean -n` (파일 삭제 전 미리보기)
    - `git push --dry-run` (푸시 대상 미리보기)
    - `git pull --dry-run` (병합 전 미리보기)

---

## 5. --no-commit / --no-ff
- **머지 동작 제어**
    - `git merge --no-commit` : 자동 커밋하지 않고 멈춤 (내가 직접 확인 후 커밋)
    - `git merge --no-ff` : fast-forward 가능해도 강제로 merge commit 생성 (브랜치 흔적 보존)

---

## 6. --autostash / git stash 안전 모드
- `git pull --rebase --autostash`
- **작업 중이던 변경을 자동으로 stash 했다가 pull/rebase 끝나면 복원**
- 언제 쓰나?
    - 작업 중인데 원격 동기화해야 할 때 → “작업 날림” 방지

---

## 7. --rebase-merges
- `git rebase --rebase-merges main`
- **rebase 시 merge 구조도 보존**
- 언제 쓰나?
    - 여러 하위 브랜치가 합쳐진 기록을 rebase할 때
    - 기존 merge 관계를 잃지 않고 이력만 깔끔히 정리

---

## 8. --abort / --continue (실수 취소/계속)
- 여러 명령에서 제공되는 **중단/계속 옵션**
- 예시:
    - `git rebase --abort` / `git rebase --continue`
    - `git cherry-pick --abort` / `git cherry-pick --continue`
    - `git merge --abort`
- 언제 쓰나?
    - 충돌 처리 도중 더 이상 진행하지 않고 원래 상태로 돌아가고 싶을 때
    - 충돌 해결 후 다시 진행할 때

---

# 정리
- **삭제/덮어쓰기 전에 → `--dry-run`**
- **강제 푸시 시 → `--force-with-lease` (절대 그냥 `-f` 쓰지 말 것!)**
- **이력 깔끔 유지 → `--ff-only`**
- **머지 세부 제어 → `--no-commit`, `--no-ff`**
- **충돌 도중 → `--abort`, `--continue`**
- **작업 중 안전 동기화 → `--autostash`**