# Git: 실무형 버전 관리 가이드

## 1. Git이란?

Git은 **분산 버전 관리 시스템(DVCS)** 으로, 로컬에서 스냅샷(커밋)을 만들고 언제든 원하는 시점으로 되돌리거나 공유 저장소(원격)로 동기화할 수 있다. **GitHub/GitLab은 원격 호스팅 서비스**일 뿐, Git 자체와 동일시하면 안 된다.

---

## 2. 핵심 저장 위치(States)

### Working Directory
- 현재 체크아웃된 스냅샷의 실제 파일들이 존재.
- 편집/삭제는 파일시스템에서 변하지만, **커밋 전까지 히스토리에는 반영되지 않는다**.
- 복구: 이전 커밋 기준으로 `git restore <file>` 등으로 되돌릴 수 있다.

### Staging Area(Index)
- `git add` 로 **커밋 후보를 모으는 공간**. `.git/index` 파일에 반영된다.
- 다음 커밋에 **정확히 무엇을 담을지** 여기서 결정한다.

### Local Repository(.git)
- 객체 DB(`objects`), 인덱스(`index`), 참조(`refs`), 설정(`config`), `HEAD` 등 **전체 구조**를 포함.
- `git commit`으로 스냅샷이 **커밋 객체**로 축적된다.
- **브랜치/태그는 커밋을 가리키는 참조**다.

### Remote Repository
- GitHub/GitLab 등 서버에 있는 저장소.
- `git push`로 로컬 커밋을 업로드하고, `git fetch`/`git pull`로 변경을 가져온다.

---

## 3. 브랜치와 그래프 모델

- Git의 히스토리는 **DAG**.
- **브랜치**는 마지막 커밋을 가리키는 **포인터**이며, 체크아웃할 때 `HEAD`가 그 브랜치를 가리킨다.

### 통합 방법
- **Merge**: 머지 커밋으로 병합(기록 보존).
- **Rebase**: 대상 브랜치 끝으로 커밋을 재적용(히스토리 선형화, 해시 변경).
- **Squash**: 여러 커밋을 하나로 합쳐 기록 단순화(머지 `--squash` 또는 `rebase -i`).

---

## 4. 필수 명령어 요약 (면접 포인트 ★)

- `git init` ★: 새 저장소 시작(.git 생성).
- `git clone <url>` ★: **원격 기본 브랜치 자동 체크아웃**하여 워킹트리 생성.
- `git status` ★: WD/Index/HEAD 차이 확인(면접에서 “세 영역 설명” 자주 출제).
- `git add [-p]` ★: 다음 커밋에 포함할 변경 선택(부분 스테이징).
- `git restore [--staged] <path>`: 파일/스테이징 되돌림(역할 분리 포인트 설명 가능).
- `git commit -m "…" | -S`: 스냅샷 생성, 필요 시 GPG 서명.
- `git log --oneline --graph --decorate --all` ★: 히스토리 가독성.
- `git show <commit|tag>`: 특정 객체 상세.
- `git branch [-d|-D] <name>`: 브랜치 관리(안전/강제 삭제 차이).
- `git switch -c <name>` / `git checkout -b <name>`: 새 브랜치 생성 후 전환.
- `git merge <branch>`: 병합. `--no-ff`, `--squash` 전략 이해.
- `git rebase [--onto]` ★: 선형화. **공유 브랜치 rebase 금지** 원칙 설명.
- `git cherry-pick <commit>`: 특정 커밋만 선택 이식.
- `git rm [--cached] <path>`: 파일 삭제 또는 추적만 해제.
- `git tag [-a] vX.Y.Z`: 릴리스 태깅(lightweight vs annotated).
- `git fetch [-p]` ★: **원격 추적 브랜치만 갱신**, `-p`로 **삭제된 원격 브랜치 정리**.
- `git pull` = fetch+merge(또는 rebase). 팀 규칙에 따라 `pull.rebase` 설정.
- `git reset [--soft|--mixed|--hard] <rev>` ★: 현재 브랜치 포인터/인덱스/워킹트리를 어떻게 맞출지 선택. (기본 `--mixed`).
- `git revert <rev>` ★: 기록 보존형 되돌리기(새 커밋 추가).
- `git reflog` ★: `HEAD` 이동 이력. “잃어버린 커밋 복구” 필살기.
- `git stash [push] [-k|-p]`, `pop`/`apply`: 임시 저장. `pull --rebase --autostash` 응용.

---

## 5. 안전한 히스토리 관리

- 이미 **공유된 브랜치**는 `rebase`/`reset --hard` 지양, 필요 시 `revert` 사용.
- 실수 복구: `git reflog` → 해당 커밋 해시로 `git switch -d <new-branch> <hash>` 등으로 보호.
- 태그와 릴리스: **annotated tag** + 릴리스 노트/체인질로그 자동화.

---

## 6. 협업 규칙(권장)

- **브랜치 보호** 및 **필수 리뷰**, **CI 통과** 필수.
- **브랜치 네이밍/커밋 메시지 컨벤션** 수립.
- 줄바꿈/인코딩 **`.gitattributes` 통일**.
- 대용량 아티팩트는 **Git LFS**.
- 크리덴셜/토큰은 깃에 올리지 말고 **비밀 관리**(유출 시 `git filter-repo`로 제거).

---

## 7. 면접에서 자주 묻는 포인트(요약)

- WD/Index/HEAD/refs 차이 **명확히 설명**할 수 있는가 ★
- **merge vs rebase** 장단/사용 시점 ★
- **reset 3종**과 **revert** 차이, 원격 이력과의 관계 ★
- **fetch/pull** 동작, `--prune`의 의미 ★
- 충돌 해결 절차와 도구(`rerere`, `mergetool`) 사용 경험 ★
- 태그, 릴리스, LFS, `.gitattributes` **실전 사례**
