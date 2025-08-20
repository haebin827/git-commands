# 실무 GIT 사용 팁

### 1) merge conflict 발생 시 해결

```bash
git pull origin develop                 # 최신 develop 가져옴(충돌 예상)
# 파일에서 충돌 해결 후
git add <충돌해결-파일들>
git commit                              # 자동 메시지 또는 직접 작성
# 필요하면 병합 완료를 push
git push origin <현재브랜치>
```

### 2) 작업 중 변경사항을 다른 브랜치로 옮겨야 할 때
- 대안: 변경이 커밋돼있다면 git cherry-pick <커밋ID> 사용.
```bash
git stash                               # 현재 변경 임시 보관
git checkout target-branch
git stash pop                           # 보관한 변경 적용
# 충돌 해결 후 add/commit
```

### 3) 특정 커밋만 골라서 현재 브랜치에 적용 (cherry-pick)

```bash
git checkout feature/a
git cherry-pick <commitA> <commitB>     # 여러 개도 가능
# 충돌 시 해결 → add → cherry-pick 계속
```

### 4) push 이후 잘못된 커밋 되돌리기(공개 이력, 안전하게)
- 주의: 이미 공유된 이력은 reset --hard 대신 revert가 안전.
```bash
git log --oneline
git revert <bad-commit>                  # 되돌리는 "새 커밋" 생성
git push origin <branch>
```

### 5) push 전 실수 커밋 고치기(로컬 전용)
- --force-with-lease는 팀원의 동시 작업을 덮어쓰는 리스크를 줄임.
```bash
git commit --amend                      # 메시지/내용 수정
git push origin <branch>                # (처음 푸시인 경우)
# 이미 원격에 같은 브랜치가 있고 이력이 달랐다면:
git push --force-with-lease origin <branch>
```

### 6) 원격의 최신 변경과 내 변경을 깔끔히 합치기(rebase)
- 팀 정책에 따라 rebase vs merge 선택. 공개 브랜치에 무분별한 rebase 금지.
```bash
git fetch origin
git rebase origin/develop               # 내 커밋들을 최신 develop 위로 재배치
# 충돌 해결 → add → `git rebase --continue`
git push --force-with-lease origin <branch>
```

### 7) 원격에 삭제된 브랜치를 로컬에서도 정리

```bash
git fetch -p                            # prune
git branch -vv                          # 추적 상태 확인
```

### 8) 실수로 브랜치/커밋을 날렸을 때 복구(reflog)

```bash
git reflog                              # 과거 HEAD 이동 이력 확인
git checkout -b rescue <잃은지점-커밋ID>  # 복구 브랜치 생성
```

### 9) 특정 파일만 다른 브랜치의 버전으로 되돌리기

```bash
git checkout develop -- path/to/file     # Git<2.23
# 또는
git restore -s develop -- path/to/file   # Git>=2.23
git add path/to/file && git commit -m "Restore file from develop"
```

### 10) 추적되지 않은 찌꺼기/빌드 산출물 일괄 제거

```bash
git clean -fd                            # 폴더 포함 강제 삭제
git clean -fdx                           # .gitignore 무시하고 싹 삭제(주의)
```

### 11) 로컬만의 WIP를 잠깐 치워두고 다른 이슈 처리(stash)
- 스테이징된 변경만 stash: git stash -k 또는 --staged.
```bash
git stash push -m "wip: cart coupon"
# 다른 이슈 처리 후
git stash list
git stash pop                            # 충돌 시 해결 → add/commit
```

### 12) 릴리스 태깅과 배포용 커밋 만들기

```bash
git checkout main
git pull --ff-only origin main
git tag -a v1.4.0 -m "Release 1.4.0"
git push origin v1.4.0
```

### 13) 릴리스 브랜치에서 버그핫픽스 가져오기(cherry-pick)
- 동일 수정이 develop에도 필요하면 develop에도 cherry-pick 또는 머지.
```bash
git checkout release/1.4
git cherry-pick <hotfix-commit>
git push origin release/1.4
```

### 14) 커밋 히스토리 정리(squash) 후 깔끔하게 올리기
- 팀 정책상 금지일 수 있으니 브랜치 정책 확인.
```bash
git rebase -i origin/develop
# 에디터에서 squash(s)로 묶고 메시지 정리
git push --force-with-lease origin <branch>
```

### 15) .gitignore를 뒤늦게 추가했는데 이미 추적 중인 파일 제외하기

```bash
# .gitignore 업데이트
git rm -r --cached .
git add .
git commit -m "Apply .gitignore"
git push origin <branch>
```

### 16) 특정 커밋에서 어떤 커밋이 문제를 일으켰는지 범인 찾기(bisect)

```bash
git bisect start
git bisect bad                          # 현재가 문제 버전
git bisect good <known-good-commit>
# Git이 체크아웃한 각 지점에서 테스트/빌드 후
git bisect good | git bisect bad        # 반복
git bisect reset
```

---

# 상황별 단축 스니펫

### A) “내 브랜치 최신 develop으로 정리하고 푸시”

```bash
git fetch origin
git rebase origin/develop
git push --force-with-lease origin $(git branch --show-current)
```

### B) “merge 대신 최신만 fast-forward”

```bash
git pull --ff-only
```

### C) “원격-로컬 불일치로 push 실패 시 표준 루틴”

```bash
git fetch origin
git rebase origin/$(git branch --show-current)
git push --force-with-lease
```

### D) “직전 커밋에 빠진 파일 추가”

```bash
git add missed/file
git commit --amend --no-edit
```

### E) “원격 브랜치 삭제 + 로컬 추적 제거”

```bash
git push origin --delete old/feature
git fetch -p
```

---

## 실무 팁

- 공유 브랜치의 공개 이력은 가능하면 revert로 되돌리고, force push는 최소화.
- 강제 푸시가 필요하다면 항상 --force-with-lease를 사용하고, 팀에 사전 공지하기.
- 리베이스 중단: git rebase --abort, 체리픽 중단: git cherry-pick --abort.
- 머지 충돌이 잦다면, CI에서 자동 머지 가능 여부(e.g., --ff-only)를 미리 검사하는 훅/워크플로를 운영하기.