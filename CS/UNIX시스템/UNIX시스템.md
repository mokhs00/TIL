# UNIX 시스템

- [UNIX 시스템](#unix-시스템)
  - [shell commands](#shell-commands)
    - [특수문자 처리](#특수문자-처리)
    - [command 연결과 확장](#command-연결과-확장)
    - [쉘 변수와 환경변수](#쉘-변수와-환경변수)
    - [chsh](#chsh)
    - [alias](#alias)
    - [type](#type)
    - [which](#which)
    - [man](#man)
    - [history](#history)
  - [File System](#file-system)
    - [파일, 디렉토리 관련 command](#파일-디렉토리-관련-command)
    - [파일 접근권한](#파일-접근권한)
    - [chmod](#chmod)
    - [umask](#umask)
    - [chown](#chown)
    - [ln](#ln)
    - [파일 내용 확인 command](#파일-내용-확인-command)

## shell commands

### 특수문자 처리

- 백슬래시 `\`
  - 명령 행을 분리할 때 또는 특수 문자의 기능을 제거하는 이스케이프(escape)문자로 사용
- Tilde `~`
  - `~`또는 `~ username`은 사용자의 홈 디렉토리를 의미
- `.`
  - 현재 위치 또는 명령 행의 맨 앞에서쉘 스크립트를 실행하는 `source` 명령
- `..`
  - 현재 위치의 부모 디렉토리
- `#`
  - 주석처리
- `$`
  - `$변수`는 변수의 값을 추출
- `&`
  - `명령&`는 명령을 백그라운드로 실행
- `*`
  - 파일 이름에서 사용할 때 0개 이상의 임의이 문자열과 대응됨
- `?`
  - 파일 이름에서 사용할 때 1개 문자와 대응됨
- pipe s`|`
  - 앞 명령의 출력을 다음 명령의 입력으로 연결시킴
  - e.g. `cat /etc/passwd | sort | more`
- `<`, `>`
  - 입출력 리다이렉션. 즉 파일로부터 입력받을 때 또는 파일로 출력할 때 사용
  - `sort < /etc/passwd`
  - `grep typedef /usr/include/* > typedef.out`
  - `ls 2>> error.txt` : 명령 수행 중 표준 오류 출력을 파일에 덧붙임
- `>>`
  - 표준 출력을 파일의 끝에 덧붙일(overwrit e) 때 사용
- `!`
  - 명령 히스토리 기능을 이용할 때 사용

### command 연결과 확장

- `파이프`
  - 앞 명령의 출력을 다음 명령의 입력으로 연결시킴
  - e.g. `cat /etc/passwd | sort | more`
- `입출력 리다이렉션`
  - 입출력 리다이렉션. 즉 파일로부터 입력받을 때 또는 파일로 출력할 때 사용
  - `sort < /etc/passwd`
  - `grep typedef /usr/include/* > typedef.out`
  - `ls 2>> error.txt` : 명령 수행 중 표준 오류 출력을 파일에 덧붙임
- `명령 치환`
  - 명령을 수행할 때, 명령의 인수로서 다른 명령의 결과를 사용
  - `$(command)` or `command`
  - e.g. `ls -l $(which passwd)`
- `인용 부호`
  - 빈칸을 포함하는 문자열을 1개의 인수로 사용할 때 인용 부호가 필요함
  - 작은따옴표(`''`)는 특수 문자의 의미를 제거함
  - 큰따옴표(`""`)는 $, ``, \, !를 해석하여 확장
  - e.g `echo "$(date)"`, `echo "\$(date)"`, `echo 'Today is $(date)'`
- `수식과 변수의 확장`
  - 수식의 결과를 명령 수행 전에 전달
  - `$[수식]` or `$((수식))`
  - e.g. `echo "$[10-5]"`

### 쉘 변수와 환경변수

- 모든 변수와 값을 출력하기 위해 `set` 명령을 사용 `unset`은 제거
- 쉘 변수
  - 현재 쉘에서만 사용 가능하고 서브 쉘로는 전달되지 않음(지역변수)
- 환경 변수
  - global하게 적용됨(전역변수)
  - 보통 변수 이름으로 대문자 사용
  - 모든 환경 변수와 값을 출력하기 위해 `printenv` 명령 사용
  - `printenv 변수`, ``echo $변수`
- `변수=값`: 쉘 변수 선언
- `export 변수` : 환경 변수로 선언
- `로그인과 쉘의 환경 설정 파일`
  - 로그인할 때 또는 쉘을 시작할 때 자동으로 실행되는 명령을 저장한 파일
  - 시스템 환경 설정 파일과 사용자 환경 설정 파일이 있음
  - 쉘의 종류에 따라 다른 이름의 파일을 사용
  - bash 쉘의 경우 다음과 같음
    - `/etc/profile`: 모든 사용자에게 적용, 로그인할 때 가장 먼저 수행됨. 전역 환경 변수가 설정됨
    - `~/.bash_profile`: 로그인할 때 수행됨. 개별 사용자에게 적용. 보통 내부에서 `~/.bashrc`를 실행
    - `~/.bashrc`: 쉘을 시작할 때마다 수행됟ㅁ 개별 사용자에게 적용. 보통 내부에서 `/etc/bashrc`를 수행
    - `/etc/bashrc`: 모든 사용자에게 적용, 쉘을 시작할 때마다 수행. PS1, alias config, umask를 사용한 접근권한의 초기값, 함수 등을 설정
    - `~/.bash_logout`: 로그아웃을 수행할 때 실행되는 스크립트 파일

### chsh

- 기본 쉘을 변경
- `chsh [options] [username]`
  
### alias

- create command alias
- `alias rm='rm -l'`
- 계속 유지하려면 쉘의 환경 설정 파일에 기록해두어야함

### type

- command가 어떻게 해석되는지 알려주는 명령
- `type cd`, `type -a cd`

### which

- 실행 프로그램을 환경변수 PATH를 기반으로 찾아 경로를 출력
- `which rm`

### man

- 해당 command의 메뉴얼 페이지를 출력(help page)
- `man ls`

### history

- 수행했던 command 히스토리 출력
- `history`, `history 10`

## File System

- 운영체제가 디스크(파티션) 상에 파일들을 구성하는 방식
- 파일과 디렉토리의 집합을 구조적으로 관리하는 체계
- 다양한 형식의 파일 시스템이 존재
- 리눅스는 전체 파일 시스템을 1개의 트리 구조로 관리
  - 1개의 루트(/) 디렉토리만 있음

### 파일, 디렉토리 관련 command

- 처음 보는 것만 상세하게 기록
- ls, cd, pwd, mkdir, rmdir, cp, mv, rm
- `file`: 파일의 종류를 알려줌

### 파일 접근권한

- 사용자 부류에 따라 읽기/쓰기/실행 권한을 각각 설정 가능
- 사용자 부류
  - 사용자(u), 그룹(g), 기타(o)
- 권한
  - 읽기(r)
  - 쓰기(w)
  - 실행(x)
- `ls -l` or `ll` 커맨드로 현재 디렉토리에 있는 파일들의 권한 확인 가능
- e.g. -rw-rw-r-- -> 사용자(rw), 그룹(rw), 기타(r)의 권한을 허용

### chmod

- 파일 소유자가 파일의 접근권한을 변경하는 command
- -R 옵션을 디렉토리에 적용하면 모든 파일과 서브 디렉토리까지 권한 변경
- 8진수 모드
  - 읽기(r)는 4, 쓰기(w)는 2, 실행(x)은 1
  - e.g. rwxr-xr-x -> 111101101 -> 755
  - chmod 775 dir1
- 기호 모드
  - 형식은 [ugoa][+-=][rwx]
  - ugoa는 각각 user, group, other, all을 의미 default: all
  - +-=는 각각 권한의 추가, 제거, 지정을 의미
  - chmod u+x file1
  - chmod u=rwx,g=rx file

### umask

- 접근권한의 기본값을 출력하거나 설정하는 command
  - 보통 /etc/bashrc에 설정되어 있음
- e.g. umask 002

### chown

- root 사용자가 파일이나 디렉토리의 소유자를 변경하는 명령
- e.g. chown [options] newowner files
  - newowner에 `소유자:그룹` 또는 `소유자.그룹`의 형태로 지정 가능
  - -R옵션으로 디렉토리에 재귀적으로 적용 가능

### ln

- 파일을 다른 이름으로 link하는 command
- 기본적으로 하드 링크를 만들며 -s옵션을 이용하면 심볼릭 링크로 생성
- `하드 링크(hard link)`
  - 하나의 파일에 다른 이름을 부여하는 것
  - 원본 파일의 링크 카운트가 증가함
  - 다른 파일 시스템에는 링크할 수 없음
- `심볼릭 링크(symbolic link)`
  - 윈도우의 **바로 가기**와 같은 역할의 독립적 파일
  - 다른 파일 시스템에 링크를 생성할 수 있음
  - 원본을 삭제하면 심볼릭 링크는 의미가 없어짐
  - **디렉토리의 경우, 심볼릭 링크만 생성 가능**

### 파일 내용 확인 command

- `more`
  - 파일의 내용을 화면 단위로 출력하는 command
  - SpaceBar누르면 다음 화면으로 이동
  - Enter 누르면 한 라인씩 이동
  - q누르면 종료
  - e.g. more -20 +10 /etc/services
    - 10번째 라인부터 20 라인 단위로 보여줌

- `less`
  - more 명령의 개선된 버전
  - 위 또는 아래로 스크롤할 수 있음
    - PageUp, PageDown, ↑, ↓
  - 라인/화면 단위 이동, 라인/퍼센트로 위치 지정, 검색 등 가능

- `head`
  - 파일 맨 앞 부분을 출력
- `tail`
  - 파일의 마지막 부분을 출력
  - -f 옵션을 사용하면 변화하는 파일의 내용을 계속 watch 가능 -> 실시간 로그 확인용으로 활용 가능
  - e.g. tail -f /var/log/messages
- `cat`
  - 하나의 파일 또는 여러 파일을 연결(concatenate)시켜 화면에 출력
  - cat [options] [files]
  - 파일을 지정하지 않으면 표준 입력으로부터 읽음
  - -n 옵션으로 라인에 번호를 붙일 수 있음
  - e.g.
    - cat > cat1.txt
    - cat cat*.txt > total.txt
