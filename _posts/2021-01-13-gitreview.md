---
title : "20210113_GitHub 복습 및 명령어 정리" #제목
category : #카테고리
    - Git
tag : #태그
toc : true #옆에 목차
---

## 1. 리눅스 명령어 

- `pwd` : 현재 위치 경로
- `ls` : 현재 파일,디렉 확인
  - `ls -a` : 숨김 파일, 디렉 표시 (all)
  - `ls -l` : 상세정보 함께 표시 (list)
  - `ls -r` : 정렬 순서 거꾸로 표시 (reverse)
  - `ls -t` : 작성 시간순 정렬 (time)
  - `cd 파일이름` : 해당 디렉으로 이동

<br>

## 2. git 구조와 상태

- 작업트리(working directory) : 작업 공간
- 스테이지 (stage) : 버전으로 만들어질 파일 대기 공간
- 저장소(repotory) : 
  스테이지 , 저장소 영역이 .git 디렉임

- untracked files : 아직 한번도 버전관리 안한 파일
- unmodified : 한번 스테이징 되었던 파일
- nodified : 스테이징 경험이 있고 수정된 파일
- staged : 커밋 준비 단계엥 있는 파일
  커밋하면 다시 unmodified 상태로 감

<br>

## 3. git 명령어
### 1) git 환경설정 명령어

- `git config user.name "이름"` :  환경설정
- `git config user.email "이메일"` : 환경설정
- `git init` : 현재 위치에 지역 저장소 생성

### 2) git 기본 명령어
- `git status` : git 상태 확인
- `git add 파일 이름` : 스테이지에 올림
  - `git add . ` : 작업한거 모두 스테이지에 올림

<br>

- `git commit -m"내용"` : 스테이지에 올라간 파일 버전 만들기
  - `git commit -am"내용"`: 스테이지 + 커밋 모두 한번에(근데 파일에 커밋 경험이 있어야 가능)
  - `git commit --amend` : 방금 들어간 커밋 수정(편집기 실행시 `|`눌러서 수정)

<br>

- `git log` : 지금까지 커밋, 버전 확인
  - `git log --start` : 커밋 관련 파일 까지 함께 보기
  - `git log --oneline` : 한줄 커밋으로 보기 편함
  - `git log --branches` : 각 브랜치 커밋도 함께 보기
  - `git log --graph` : 관계 그림으로 보기
  - `git log master..브렌치 이름` : 브렌치 비교 가능(다른 커밋만 볼수 있음)

<br>

- `git diff` : 변경 사항 확인하기
- `git checkout -- 파일 이름` : 작업트리 변경 상태(stage엔 아직이고) 취소
- `git checkout 특정 브랜치 이름` : 해당 브랜치로 이동
  - `git checkout -b 브렌치 이름` : 해당브렌치 생성후 이동

<br>

- `git reset HEAD 파일이름` : 스테이징 까지 한경우 스테이징 취소
- `git reset HEAD^` : 커밋 까지 된경우 마지막 한줄 커밋 취소, 스테이지 까지 취소됨
- `git reset --soft HEAD^` : 최근 커밋을 하기전 상태로 작업 트리 되돌리기
- `git reset --mixed HEAD^` : 최근 커밋, 스테이징을 하기전 상태로 작업 트리 되돌리기
- `git reset --hard HEAD^` : 최근 커밋, 스테이징 파일 수정 하기전 상태로 작업 트리 되돌리기(되돌리면 복구 불가)
- `git reset --hard 커밋해시` : 특정 커밋 해시 상태로 감 
  (A 브렌치에서 B 브렌치 커밋해시를 사용하여 reset의 경우 A 브렌치 과거 커밋은 모두 삭제 되고 B브랜치 커밋 상태로 감 )

<br>

- `git revert 커밋 해시`

<br>

- `git branch 브랜치 이름` : 해당 이름브랜치 생성
  - `git branch` : 현재 브렌치 확인 
  - `git branch -d 브렌치 이름` : 해당 브랜치 삭제

<br>

- `git merge 특정 브랜치 이름` : 현재 브렌치에 특정 브랜치 병합
  - `git merge origin/master` : 페치한 뒤 원격 master 브랜치 커밋인 경우
  - `git merge origin/브렌치 이름` : 페치한 뒤 원격 다른 브랜치 커밋인 경우
  - `git merge FETCH_HEAD` : 지역저장소에 반영하지 않은 최신 커밋 병합

<br>

- `git stash` : 수정중이 파일 감추기, 되돌리기 (커밋 시키고 싶지 않을 때)
  - `git stash list` : 감춘 파일들 목록 확인(최근:0 ~ 나중일수록 숫자가 높음)-> 선입 후출 stack 형
  - `git stash pop` : stash 목록에서 가장 최근 항목 되돌리기-> 예전 수정된 상태로 되돌아감
  - `git stash apply` : 또 사용될수 있을지 모르는 경우, 목록에서 최근 항목을 되돌리지만 목록 내용은 안제거 됨
  - `git stash drop` : stash 목록 가장 최근 항목 제거

<br>

## 4. HEAD의 이해
HEAD : 현재 작업 트리가 어떤 버전을 기반으로 작업중인지 가리키는 포인터
- `HEAD` -> `master` -> `commit_1` 
(commit_1 작성 상황에서 sub 브랜치 생성시)
- `sub branch` -> `commit_1`
(master - commit_2 작성) 
- `HEAD` -> `master` -> `commit_2`
(sub branch로 이동)
- `HEAD` -> `sub branch` -> `commit_1`

<br>

## 5. 깃허브 사이트 둘러보기
오픈 소스의 성지
깃허브 - explorer 메뉴 - explorer github

<br>

## 6. git 원격 저장소 관련 명령어
- `http://github.com/아이디/저장소명` : 인터넷 주소창으로 접근가능

- `git remote add origin 주소` : 원격 저장소 연결
- `git remote -v` : 원격 저장소 연결 확인
- `git push -u origin master` : -u 옵션 = 원격 저장소 첫 연결시 파일 올리기
- `git push` : 한번 연결되고 파일이 올라갔던 저장소에 파일 올릴때
- `git pull origin master` : 원격과 지역 저장소를 같이 만들 경우
- `git pull`
- `git cloen 주소 폴더이름` : 원격 저장소 복제 ,(폴더 없어도 상관X) , 원격과 지역 저장소 자동 연결
- 1개의 원격저장소에서 2개의 기기로 작업하면 push와 pull 작업을 무조건 해주자
- `git fetch` : 저장소 커밋 정보만 가져와서 훑어보는 식 그리고 합치고 싶으면 pull이나 merge로 같게 만들면 됨(단, FETCH_HEAD라는 브랜치로 가져옴)

<br>

## 7. SSH(Secure Shell) 원격 접속
- 보안이 강화된 안전한 방법으로 정보 교환 방식
- 계정으로 인증하는게 아니라, 현재 사용하고 있는 기기를 인증
- 기기를 등록해 두면 언제든지 터미널에서 아이디 비밀번호를 쳐줄 필요가 없음 (다른 컴퓨터에 접속할 수 있는 비밀번호 생성)

- `ssh-keygen` : ssh키가 저장되는 경로가 확인(/.ssh)하고 엔터
- `엔터 두번` : 비밀번호 생성 , id_rsa 파일 : 프라이빗 키, id_rsa.pub 파일 : 퍼블릭 키 
- 해당 경로의 파일 확인(없으면 cat명령 사용해서 파일 열기)하면 키 파일 만들어진거 확인 가능
- 깃허브 서버에 퍼블릭 키 전송
  -  파일 열어서 ssh-rsa 부터 문자열 전체 복사
  -  깃허브 사이트에 로그인하고 setting 메뉴 들어감
  -  SSH and gpg keys 누르고 New SSH key 누르기
  -  알아볼수 있는 이름으로 넣고, key값 붙여넣기(빈칸 빈줄 없애서)
  -  add SSH key 버튼 눌러서 추가
- SSH 주소로 원격 저장소 연결하기
  - 레포 만들고 SSH 버튼 눌러서 주소 복사
  - `git init connect-ssh`
  - `cd connect-ssh`
  - `git remote add origin 복사한 주소`
  - `git remote -v` : 연결 확인


<br>

## 8. Question?
- CRLF 문자
- 병합 자동화 프로그램 : P4Merge
