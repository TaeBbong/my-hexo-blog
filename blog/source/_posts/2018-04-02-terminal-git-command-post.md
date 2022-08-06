---
layout: post
title: "[개발TIP&기록] Terminal, Git Command 완벽 정리"
date: 2018-04-02
excerpt: "Terminal, Git을 사용하며 쓰는 Command의 정리본"
category:
  - 개발
  - 개발 Tip
tag:
  - terminal
  - bash
  - git
  - command

comments: true
---

# Useful Terminal / GitHub Command Key

## Terminal Command Key

- 디렉토리 이동 \* cd directory
- 파일 이름 변경 \* mv file1 file2
- 파일 복사 \* cp /directory1/file /directory2/file
- 파일 삭제 \* rm file
- 파일 목록 조회 \* ls
- 폴더 생성 \* mkdir folder
- 파일 생성
  _ touch file
  _ vim file (edit now)

## GitHub Command Key

- Basic Process
  _ git init
  _ git add file (or add --all)
  _ git commit -m "Message"
  _ git remote add origin https://github.com/user/repo.git \* git push origin master
- Get Clone \* git clone https://github.com/user/repo.git
- Pull \* git pull
- Branch & Merge
  _ git checkout -b new_branch_name (Make and Go to new branch)
  _ git checkout master (Back to master branch)
  _ git push origin new_branch_name
  _ git merge new_branch_name
- Force Push \* git push -f origin master
- Cancel Push
  _ git reset --hard commit_id
  _ git push --force

## How to Make a Pull Request

1. Fork
   - Fork project on github website to my repo
2. Clone, Remote Setting
   - git clone https://github.com/user/repo.git
   - git remote add project_name https://github.com/project/repo.git
3. Branch
   - git checkout -b branch_name
   - git branch
4. Add, Commit, Push
   - git add files
   - git commit -m "message"
   - git push origin branch_name
5. Pull Request
   - Press Compare & pull request button on my repo
6. Synchronize & Remove Branch
   - git pull project_name
   - git branch -d develop
7. If we have to repeat
   - git pull project_name
   - Repeat 3~6
