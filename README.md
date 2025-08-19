# GIT COMMANDS

This repository is a playground to test git commands

![img.png](images/img.png)

1. 작업 폴더(Working Directory)
   - 작업이 일어나는 폴더

2. Staging Area(Index)
   - 작업 폴더에서 작업한 변경 내용을 기록 하는 곳 (git 저장소에 commit하기 전에 올려두는 공간)
   - stage에서 commit을 하게 되면 git 저장소로 변경 내용이 저장됨

3. 로컬 저장소(local repository)
   - 내 PC에서 관리하는 git 저장소
   - 로컬 git 저장소를 만들어주려는 디렉토리로 이동해서 아래 명령어를 실행하면 .git 폴더 생성

4. 원격 저장소(remote repository)
   - 로컬 저장소를 업로드 하는 곳. ex) GitHub, Bitbucket, GitLab 등
   - clone 명령어로 기존 원격 저장소를 로컬에 받을 수 있음

---

### Setup & Init

#### git init
- Initialize an existing directory as a Git repository

#### git clone [url]
- Retrieve an entire repository from a hosted location via URL

---

### Stage & Snapshot

#### git status
- Show modified files in working directory, staged for your next commit
  - 커밋된 파일 & 스테이지에 있는 파일 : tracked
  - 그 외 : untracked

#### git add [file]
- Add a file as it looks now to your next commit (stage)

#### git reset [file]
- Unstage a file while retaining the changes in working directory

#### git diff
- Diff of what is changed but not staged

#### git diff -- staged
- Diff of what is staged but not yet committed

#### git commit -m "[descriptive message]"
- Commit your staged content as a new commit snapshot

#### git restore --staged [file-name / .]
- 실수로 전체 커밋해버린 경우 되돌리기
---

### Branch & Merge

#### git branch
- List your branches a will appear next to the currently active branch

#### git branch [branch-name]
- Create a new branch at the current commit

#### git checkout
- Switch to another branch and check it out into your working directory

#### git checkout -M [curent-branch] [new-branch]
- 무조건 덮어쓰기

#### git checkout -b [new-branch]
- 새 브랜치 만들고 즉시 체크아웃

#### git cherry-pick [commit-name]
- 커밋하지 않고 선택하여 합치기

#### git cherry-pick -n [commit-name]
- 브랜치의 이력을 다른 브랜치에 합치기

#### git branch -d [branch-name]
- 삭제할 브랜치가 현재 브랜치에 합쳐졌을 경우에만 삭제

#### git branch -D [branch-name]
- 삭제할 브랜치가 현재 브랜치에 합쳐지지 않았어도

#### git push origin --delete <wrong-branch>
- 원격 브랜치 삭제

#### git merge [branch]
- Merge the specified branch's history into the current one

#### git log
- Show all commits in the current branch's history

---

### Inspect & Compare

#### git log
- Show the commit history for the currently active branch

#### git log branchB..branchA
- Show the commits on branchA that are not on branchB

#### git log --follow [file]
- Show the commits that changed file, even across renames

#### git show [SHA]
- Show any object in Git in human-readable format

---
로컬에 commit만 하는 경우에는 쉽게 reset 기능으로 처리할 수 있지만, 원격 저장소까지 push가 된 경우엔 revert를 사용한다.
### Share & Update

#### git remote add [alias] [url]
- Add a git URL as an alias
- alias에는 주로 origin이 옮

#### git fetch [alias]
- Fetch down all the branches from that Git remote

#### git merge [alias]/[branch]
- merge a remote branch into your current branch to bring it up to date

#### git push [alias] [branch]
- Transmit local branch commits to the remote repository branch

#### git pull
- Fetch and merge any commits from the tracking remote branch

---

### Tracking Path Changes

#### git rm [file]
- Delete the file from project and stage the removal for commit

#### git mv [existing-path] [new-path]
- Change an existing file path and stage the move

#### git log --stat -M
- Show all commit logs with indication of any paths that moved

---

### Rewrite History

#### git rebase [branch]
- Apply any commits of current branch ahead of specified one

#### git reset --hard [commit]
- Clear staging area, rewrite working tree from specified commit

---

### Temporary Commits

#### git stash
- Save modified and staged changes

#### git stash list
- list stack-order of stashed file changes

#### git stash pop
- Write working from top of stash stack

#### git stash drop
- Discard the changes from top of stash stack