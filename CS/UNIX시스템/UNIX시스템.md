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
