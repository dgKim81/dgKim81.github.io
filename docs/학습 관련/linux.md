# Linux 관련 내용..
리눅스 명령 방식 
구문 구분은 스페이스 바로 한다.
옵션 값 "-" 짧은 옵셥 "--" 긴옵션 2자리 이상인 것.
짧은 옵션 값은 싱글 하이픈 뒤에 나열할 수 있다.

***help*** : 리눅스 명령 나열

***man*** : 리눅스 메뉴얼 명령
ex) man [man options] [[section] page ...] ...
man 필수 값 [man options] 옵션널 값, [[section] page... ] 옵션널 값 여러개 가능 ... 여러 값

이 명령은 
```
man -k [command]
```

- 단축키 :
    e : 한줄 아래로
    y : 한줄 뒤로
    f : 한 화면 아래로
    b : 한화면 뒤로 
    /pattern : 전방 검색하기
    ?pattern : 후방 검색하기
    g : 첫번째 줄
    G : 마지막 줄

*. pattern : 
    ! : 매칭이 안돼는 라인 검색
    * : 멀티 파일 
    @ : 시작 문자 검색

***type*** : 리눅스 명령 유형 찾기.

## 파일 시스템 탐색
root 디렉토리 
최상위 디렉토리, 시스템의 시작 디렉토리 

home 디렉토리
/home에는 모든 사용자 정보가 들어가 있다.

root 디렉토리는 '/'로 나타나고, home 디렉토리는 '~'로 나타난다.

pwd : 현재 위치를 표현한다.
ls -la 디렉토리 내용을 보여준다.
l : 파일의 상세 정보를 보여준다.
a : 숨겨져있던 파일도 모두 보여준다.

cd : 이동하기 
cd .. : ..은 이전 디렉토리.

root에 있는 폴더 설명..
- bin : 바이너리 디렉터리. 프로그램 들이 정의되어 있다.
- etc : 대부분 초기화 스크립트, 설정 파일들이 들어 있다. nano도 들어 있다.
- media : use 드라이브나 sd 카드, dvd, cd 같은 것들 여기에 있다.
- var : 로깅과 관련된 일을 처리한다. 로그 파일들이 들어 있고, 캐시 파일이나 다른 프로그램이 만들어낸 파일들이 있다.
- var/logs : system 로그가 들어 있다.
- root : 슈퍼 사용자를 위한 홈디렉토리이다.
- usr : 실행 파일과 라이브러리 소프테위어 새로운 명령어를 설치하거나 하면 이쪽으로 들어온다.
    - bin : 실행명령들이 여기에 있다.

file : 파일을 테스트해서 어떤 유형인지 알려준다.
touch : 파일을 만들때 많이 쓴다. 살짝 건드려서 update타임을 변경한다.

공백 처리하기.
file my website -> file 'my website' -> file my\ website

문자 에디터 
nano이다...

리눅스 터미널에 단축키가 있다.
키보드에서 손을 떼지않고 진행할 수 있다.
ctrl + l 화면을 지운다.
ctrl + a line의 시작
ctrl + e line의 끝
ctrl + f 한글자 앞으로
ctrl + b 한글자 뒤로
alt + f 한단어 앞으로
alt + b 한단어 뒤로
ctrl + t 한단어를 변경한다. 앞과 뒤의 문자를 스왑한다. 
*. 이걸 어디에 쓰지???;;
ctrl + k 는 현재 커서 위치에서 오른쪽을 모두 삭제한다.
ctrl + u 는 현재 커서 위치에서 왼쪽을 모두 삭제한다.

ctrl + d 커서 기준 한글자를 지운다.
ctrl + w 커서 기준 왼쪽 한 단어를 지운다.
alt + d 커서 기준 오른쪽 한 단어를 지운다. 첫단어를 지우지 않는다.

ctrl + _ 방금한 작업을 취소 한다.
ctrl + y 방금 삭제된 kill-ring 존에 있는 것을 붙여 넣는다.

tab 자동 완성 기능
ctrl + ins 복사
shift + ins 붙여넣기

삭제된 명령은 kill-ring 존으로 이동된다.

history 명령으로 실행했던 명령을 볼 수 있다.
history | less  f 다음 페이지, b 다음 페이지
!번호를 입력해서 해당 명령을 다시 실행할 수 있다. 

~/.bash_history 안에 존재한다.
ctrl + r 이전에 작업했던 것을 찾는다.
ctrl + r을 계속 입력하면 같은 단어로 계속 찾는다. 사이클!!

한계가 있다. 
echo $HISTFILESIZE  명령어를 저장하는데 파일 사이즈이다.
echo $HISTSIZE      메모리에 저장되는 HIST의 사이즈 이다.

명령어
cat : 파일을 연결한다는 뜻이다. OpenSSL에서 사용했었다.
    - cat 파일로 파일 내용을 볼수 있다.
less : 파일의 내용을 본다. f / b  앞페이지 뒷페이지, q로 나올수 있다. "/" 검색가능.
    *. 실행 중에도 옵션을 줄 수가 있다.
head : 파일의 첫부분부터 정해진 라인까지 출력한다.
tail : 파일의 끝부분부터 정해진 라인 전까지 출력한다.(출력순서는 뒤집어지지 않는다.)
    - 중요한 옵션 -f , --follow 실시간으로? 출력한다.
sort : 파일의 내용을 선서대로 나타낸다.
    - -n 숫자로 보고 정렬, -u 유일한 것만 나타낸다., -r 역순으로 나타낸다. -k 필드를 정할 수 있다.
wc : 단어의 갯수를 새어 준다.
    - 출력 %n %n %n %s 1이 라인수, 2가 단어수, 3이 바이트수 4가 파일명이다.
    - -l 라인수, -w 단어수, -c 바이트수, -m 문자수
fancy sort stuff :

tac : cat와 반대.. 역순이래 ''; 마지막 줄부터 출력한다.
rev : 문자를 표현하고 문자를 뒤집는다.

Redirection!
Standard Streams 
- Standard Input(stdin)
- Standard Output(stdout)
- Standard Error(stderr)
통신 체널이다.

stdin -> command -> stdout
                 -> stderr

- stdout의 default는 터미널 윈도우이다.
  하지만 프린터로 보내거나 다른 기기로 보낼 수 있다.
- stderr은 오류 정보를 전달한다. default 는 터미널 윈도우이다.
- stdin 은 명령이 입력되는 스트림으로 기본적으로 키보드 이다.
  파일이 될 수도 있다. 
  *. 다른 명령의 stdout이 stdin으로 제공 될 수 있다.

redirecting output
리다이렉트 output symbol (>) 이것이다.
- 파일로 리디렉션을 하면, 이전 내용을 완전히 날리고 새로 작성한다.

덮어쓰지않고, 추가하기는 (>>) 이렇게 쓴다. 

redirecting input
표준 입력으로 리다이렉트 할때는 (<) 이렇게 쓴다.
*. man 페이지에 stdin에 대한 언급이 있다.
cat < cat.txt

combo!에는 순서가 있다.
stdin이 먼저 되고, stdout이 된다.

stderr에 대해서 알아보자 
리다이렉트를 할 때 (2>) 를 쓴다.
추가하려면 2>> 로 쓴다.

각 스트림은 번호가 있다. stdin(0), stdout(1), stderr(2) 이다.
기본은 1> 이다.

출력 스트림과 오류 스트림을 같이 쓸 때는 출력 스트림을 먼저 쓴다.
같은 파일을 바라보게 할 때는 &1 로 쓸수 있다.
ls dogs > output.txt 2>&1

오~! 축약형으로 쓸수 있다.
ls dogs &> output.txt
ls dogs &>> output.txt

