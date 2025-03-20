---
title: 리눅스 쉘 스크립트 변수
description: >-
  리눅스 쉘 스크립트에 변수 대해서 조금더 자세하게 알아보기
author: dgkim
date: 2025-03-20 15:43:00 +0900
categories: [학습]
tags: [Linux, Ubuntu]
pin: false
media_subpath: '/posts/20250120'
comments: true
---
# 변수
[쉡스크립트 사이트 참조](https://www.shellscript.sh/)

쉘에서 기본적으로 설정된 변수가 있으며, 대부분은 직접 값을 할당할 수 없습니다.
이 변수들은 실행 환경에 대한 유용한 정보를 포함하고 있으며, 이를 활용하면 스크립트가 실행되는 환경을 더 잘 파악할 수 있습니다.

## 기본적으로 제공되는 변수들

먼저 살펴볼 변수들은 `$0`부터 `$9`까지, 그리고 `$#`입니다.

- ***$0*** : 현재 실행 중인 스크립트의 파일명 (basename)
- ***$1 ~ $9*** : 스크립트를 실행할 때 전달한 첫 번째부터 아홉 번째까지의 인자(Arguments)
- ***$@*** : $1부터 모든 인자를 포함
- ***`$*`*** : $@와 비슷하지만, 공백 및 따옴표를 보존하지 않음
    - 예: "File with spaces"는 $@에서는 그대로 "File with spaces"로 전달되지만, $*에서는 "File" "with" "spaces"로 나뉘어 전달됨
    - 일반적으로 $*보다는 $@를 사용하는 것이 좋음
- ***$#*** : 스크립트 실행 시 전달된 인자의 개수

### 예제: 변수 활용 (var3.sh)
``` bash
#!/bin/sh
echo "I was called with $# parameters"
echo "My name is $0"
echo "My first parameter is $1"
echo "My second parameter is $2"
echo "All parameters are $@"
```
### 실행 예시
1. 인자 없이 실행

``` bash
$ /home/dong-dong/var3.sh
I was called with 0 parameters
My name is /home/steve/var3.sh
My first parameter is 
My second parameter is 
All parameters are 
```
2. 인자와 함께 실행

``` bash
$ ./var3.sh hello world earth
I was called with 3 parameters
My name is ./var3.sh
My first parameter is hello
My second parameter is world
All parameters are hello world earth
```

> 주의 사항!
> > $0의 값은 실행 방법에 따라 달라질 수 있음
> > > /home/dong-dong/var3.sh로 실행하면 절대 경로
> > > ./var3.sh로 실행하면 상대 경로
> > 이를 정리하려면 basename 명령어를 사용할 수 있음

``` bash
echo "My name is `basename $0`"
```

## 9개 이상의 인자 처리 (shift 활용)
쉘에서는 $1 ~ $9까지만 직접 사용할 수 있지만, shift 명령어를 사용하면 9개 이상도 처리할 수 있음.

### 예제: shift 사용 (var4.sh)

``` bash
#!/bin/sh
while [ "$#" -gt "0" ]
do
  echo "\$1 is $1"
  shift
done              
```

### 실행 예시

``` bash
$ ./var4.sh apple banana cherry date
$1 is apple
$1 is banana
$1 is cherry
$1 is date
``` 

### 동작 원리

1. shift를 실행하면 $2가 $1이 되고, $3이 $2가 되는 방식으로 한 칸씩 이동
2. $#이 0이 될 때까지 반복 실행됨

## 명령어 실행 결과를 변수에 저장 ($?)
특수 변수 $?는 마지막으로 실행된 명령어의 종료 상태(exit status) 를 저장함.
일반적으로 0은 성공, 0이 아닌 값은 실패를 의미함.

### 예제: 명령어 실행 결과 확인

``` bash
#!/bin/sh
/usr/local/bin/my-command
if [ "$?" -ne "0" ]; then
  echo "Sorry, we had a problem there!"
fi
```
- /usr/local/bin/my-command 실행
- 실행 결과가 **0(성공)**이 아니면 "Sorry, we had a problem there!" 출력

**잘 설계된 프로그램은 성공 시 0을 반환해야 함.**

> 로마 제국의 멸망 원인 중 하나는, 그들이 0이라는 개념을 가지지 못해 C 프로그램이 정상 종료되었는지 알 수 없었기 때문이다.
> – Robert Firth

## 프로세스 관련 특수 변수 ($$와 $!)
쉘은 프로세스 관련된 두 개의 중요한 변수를 자동으로 설정함.

- ***$$*** : 현재 실행 중인 스크립트의 프로세스 ID(PID)
- ***$!*** : 마지막으로 실행된 백그라운드 프로세스의 PID

### 예제: PID 활용

``` bash
#!/bin/sh
echo "This script's PID is $$"
sleep 30 &
echo "Background process PID is $!"
```

### 실행 결과

``` bash
This script's PID is 12345
Background process PID is 12346
```

**활용**:
- $$를 이용해 고유한 임시 파일 생성
- $!를 활용해 특정 백그라운드 프로세스가 완료될 때까지 기다리는 로직 구현 가능

## IFS (Internal Field Separator) 변수
IFS 변수는 쉘이 입력을 분리할 때 사용하는 기준 문자를 정의함.
기본값: 공백(SPACE), 탭(TAB), 개행(NEWLINE)

### IFS를 활용한 데이터 분리 (var5.sh)

``` bash
#!/bin/sh
old_IFS="$IFS"
IFS=:
echo "Please input some data separated by colons ..."
read x y z
IFS=$old_IFS
echo "x is $x y is $y z is $z"

```

### 실행 예시

``` bash
$ ./var5.sh
Please input some data separated by colons ...
hello:how are you:today
x is hello y is how are you z is today
```

**추가 설명**
- 기본적으로 read는 공백을 기준으로 데이터를 나눔
- IFS=: 설정으로 :을 구분자로 변경
- 실행 후 원래 IFS 값으로 복원 (IFS=$old_IFS)