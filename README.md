# Git 명령어 정리
![img.png](images/img.png)
## 1. 기본 개념

- **작업 폴더(Working Directory)**  
  실제로 파일을 수정하고 개발하는 공간

- **스테이징 영역(Staging Area, Index)**  
  변경된 내용을 임시로 올려서 "커밋할 준비"를 하는 공간  
  (`git add`로 올리고, `git commit`으로 저장소에 반영)

- **로컬 저장소(Local Repository)**  
  내 PC에 있는 Git 저장소 (.git 폴더)

- **원격 저장소(Remote Repository)**  
  GitHub, GitLab, Bitbucket 같은 서버에 있는 저장소  
  협업 시 이 원격 저장소를 사용해 코드 공유

---

## 2. 초기 설정

```bash
git init                   # 현재 디렉토리를 Git 저장소로 초기화
git clone [url]            # 원격 저장소를 복제해서 가져오기
```

---

## 3. 변경 사항 관리 (Stage & Commit)

```
git status                 # 현재 상태 확인
git add [파일명]            # 수정된 파일을 스테이징에 추가
git reset [파일명]          # 스테이징에서 내리기 (수정은 남김)
git diff                   # 수정됐지만 스테이징 안 된 내용 확인
git diff --staged          # 스테이징된 변경 내용 확인
git commit -m "메시지"      # 커밋 생성
git restore --staged .      # 잘못 스테이징한 것 되돌리기 (특정 파일만 이전 커밋/브랜치 상태로 롤백)
```

---

## 4. 브랜치 & 병합

```
git branch                 # 브랜치 목록 확인
git branch [이름]           # 새 브랜치 생성
git checkout [이름]         # 브랜치 이동
git checkout -b [이름]      # 브랜치 생성 후 이동
git merge [브랜치명]        # 브랜치 병합
git cherry-pick [커밋ID]    # 특정 커밋만 현재 브랜치에 반영
git branch -d [이름]        # 병합된 브랜치 삭제
git branch -D [이름]        # 강제 브랜치 삭제
git push origin --delete 브랜치명   # 원격 브랜치 삭제
```
---

## 5. 기록 & 비교

```
git log                          # 커밋 로그 확인
git log --oneline --graph --all  # 한 줄 로그 + 브랜치 그래프 보기
git log branchB..branchA         # A에는 있지만 B에는 없는 커밋 보기
git log --follow [파일명]         # 파일의 변경 이력 확인
git show [커밋ID]                # 특정 커밋 상세 보기
git blame [파일명]               # 파일별 줄 단위 작성자/커밋 확인
```
---

## 6. 원격 저장소와 동기화

```
git remote add origin [url]    # 원격 저장소 추가
git fetch origin               # 원격 저장소 최신 이력 가져오기 (병합은 안 함)
git merge origin/[브랜치]      # 원격 브랜치를 현재 브랜치에 병합
git pull                       # 원격 변경 내용을 가져와 병합
git push origin [브랜치]       # 로컬 커밋을 원격에 업로드
```
---

## 7. 파일 & 경로 관리

```
git rm [파일명]               # 파일 삭제
git rm -r [폴더명]            # 폴더 삭제 (-r는 recursive하게 폴더 내부의 내용을 다 삭제하라는 뜻)
git mv [이전경로] [새경로]     # 파일 이동 또는 이름 변경 후 스테이징
git log --stat -M             # 파일 이동/이름 변경 포함 로그 보기
```
---

## 8. 기록 수정 & 되돌리기

```
git reset --soft [커밋 ID]     # 커밋은 취소하지만, 스테이징 및 working directory 상태는 그대로 남겨두기 => “방금 커밋을 했는데 메시지만 바꾸고 싶어!”
git reset --mixed [커밋 ID]     # 커밋도 취소하고, 스테이징도 풀어버리지만 파일 수정 내용은 working directory에 존재 => “커밋도 취소하고 add도 잘못했네. 하지만 내용은 살려야지.”
git reset --hard [커밋ID]     # 커밋 취소 + 스테이징 취소 + working directory의 수정한 파일까지 전부 없애고 특정 시점으로 완전히 돌아가기 => “진짜 다 꼬였어. 그냥 어제 상태로 완전히 되돌려!”
# 주의: 되돌린 내용은 reflog 없으면 못 찾음 → 반드시 신중히

git revert [커밋ID]           # 이미 원격에 올린(공유된) 커밋을 안전하게 되돌릴 때 => push 후에 사용해도 안전!
git rebase [브랜치]           # 현재 브랜치를 지정한 브랜치 위로 재배치 => 히스토리를 깔끔하게 직선으로 유지하고 싶을 때
```

#### git rebase [브랜치] 추가 예시
- 상황: feature/login 작업 중인데, 팀이 main에 여러 커밋을 더 넣음. 내 브랜치를 최신 main 뒤에 “옮겨 적어” 충돌을 지금 해결하고, 나중 머지 충돌을 줄이고 싶다.
```
git checkout feature/login
git fetch origin
git rebase origin/main
# 충돌 나면 파일 수정 → git add <파일들> → git rebase --continue 
# 중단하려면 git rebase --abort
git push --force-with-lease origin feature/login   # rebase 후엔 보통 필요
```
- 상황: 협업 중 “pull할 때마다 merge 커밋”이 생기는 게 싫다.
```
git config pull.rebase true
# 또는 매번:
git pull --rebase
```
---

## 9. 임시 저장 (stash)

```
git stash                     # 변경 사항 임시 저장
git stash list                # stash 목록 확인
git stash pop                 # 가장 최근 stash 적용 + 제거
git stash drop                # stash에서 제거
```
---

## 10. 실무에서 유용한 추가 명령어

```
git commit --amend            # 가장 최근 커밋 메시지 수정 (push 전만 사용)
git push -f origin [브랜치]   # 강제로 push (주의!)
git fetch -p                  # 원격에서 삭제된 브랜치 로컬에서도 정리
git clean -fd                 # 추적되지 않은 파일/폴더 삭제
git reflog                    # HEAD 이동 기록 보기 (reset, checkout 포함) => 예를 들어 커밋을 실수로 날려도 복구 가능
git shortlog -sn              # 커밋한 사람별 통계 확인
```
---

## 자주 쓰는 명령어 TOP 10 (실무 기준)

```
git status

git add .

git commit -m "메시지"

git log --oneline --graph --all

git checkout -b feature/기능명

git merge 브랜치명

git pull

git push origin 브랜치명

git stash / git stash pop

git reset --hard [커밋ID] (push 전 실수 정정)
```
---
