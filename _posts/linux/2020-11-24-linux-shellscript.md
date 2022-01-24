---
title: Linux Shell Script
author: dejavuhyo
date: 2020-11-24 10:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-shellscript, shellscript, 리눅스-쉘스크립트, 쉘스크립트]
---

## 1. 변수

* 쉘변수는 처음 사용될 때 만들어진다. 즉 미리 선언할 필요가 없다.
* 쉘변수는 유닉스 명령과 마찬가지로 대소문자에 구별이 있다.
* 쉘변수는 기본적으로 데이터를 문자열로 저장한다. 수치를 대입해도 실제 수치가 아닌 문자열이 저장된다. 계산이 필요할 경우는 자동으로 수치로 변환하여 계산후 다시 문자열로 저장된다.
* 쉘변수의 값을 사용할 때는 변수명앞에 "$" 를 붙여서 사용한다.
* 쉘변수에 값을 대입할때는 "$" 를 사용하지 않는다.
* 쉘변수는 타입이 없다. 즉 아무 값이나 다 넣을 수 있다.

### 1) 환경변수
쉘을 기동하고나면 기본적으로 셋팅되어있는 변수들이다. 유닉스/리눅스에는 많은 환경변수들이 있고 필요한경우 이 변수들을 마치 일반변수처럼 값을 얻어오거나 셋팅할 수 있다. 여기서는 쉘과 직접적인 관련이 있는것만 설명한다.

* $0: 실행된 쉘 스크립트 이름
* $#: 스크립트에 넘겨진 인자의 갯수
* $$: 쉘 스크립트의 프로세스 ID

### 2) 인자변수
쉘스크립트에 인자를 넘겨줄때 그 인자들에 대한 정보를 가지고 있는 변수들.

* $1~ $nnn: 넘겨진 인자들
* $*: 스크립트에 전달된 인자들을 모아놓은 문자열. 하나의 변수에 저장되며 IFS 환경변수의 첫번째 문자로 구분된다.
* $@: $*과 같다. 다만 구분자가 IFS변수의 영향을 받지 않는다.

## 2. 일반변수
일반변수에 특별한 제약은 없다. 단 대소문자 구분만 정확하게 해주면 된다.

* 예

```shell
#!/bin/sh
echo "This Script Executable File : $0"
echo "Argument Count : $#"
echo "Process ID : $$"
echo "Argument List \$* : $*"
echo "Argument List \$@ : $@"
echo "Argument 1 : $1"
echo "Argument 2 : $2"
echo "Argument 3 : $3"
echo "Argument 4 : $4"  
```

* 실행

```shell
$chmod 755 test1
$./test1 a1 a2 a3 a4
This Script Executable File : ./test1
Argument Count : 4
Process ID : 905
Argument List $* : a1 a2 a3 a4
Argument List $@ : a1 a2 a3 a4
Argument 1 : a1
Argument 2 : a2
Argument 3 : a3
Argument 4 : a4
```

### 1) 연산
변수의 산술 연산은 생각하는 것처럼 쉽지 않다. 위에서 언급했듯이 변수에는 모든 것이 문자열로 저장되기 때문에 연산이 불가능하다. 연산을 위해서는 좀 복잡한 절차를 거쳐야 한다.

```text
변수 = $((산술식))
```

이것이 가장 단순한 연산 규칙이다. 산술식내에는 변수($1, $a 와 같은) 도 들어갈 수 있다. 산술식 내에 숫자가 아닌 문자열, 또는 문자열이 담겨있는 변수가 들어가면 그것들은 계산에서 제외된다. (정확히 말하면 0 으로 간주되어 연산이 이루어 지지 않는다.)

### 2) 매개변수 확장  
매개변수 확장이란 변수의 값을 문자열 등으로 대체하는 것을 말한다. 단순한 대체뿐 아니라 변수내의 문자열을 조작하여 원하는 문자열만을 추출할 수도 있다.

* 형식
  - ${parm:-default}: parm이 존재하지 않으면 default로 대체된다.
  - ${#parm}: parm의 길이를 참조한다.(가져온다)
  - ${parm%word}: 끝에서부터 word와 일치하는 parm의 최소부분(첫번째 일치)을 제거하고 나머지를 반환한다.
  - ${parm%%word}: 끝에서부터 word와 일치하는 parm의 최대부분(마지막 일치)을 제거하고 나머지를 반환한다.
  - ${parm#word}: 처음부터 word와 맞는 parm의 최소부분(첫번째 일치)을 제거하고 나머지 부분을 반환한다.
  - ${parm##word}: 처음부터 word와 맞는 parm의 최대부분(마지막 일치)을 제거하고 나머지를 반환한다.

* 예

```shell
#!/bin/sh

p="/usr/X11R6/bin/startx"

unset p
a=${p:-"Variable p Not found"}
echo $a

p="/usr/X11R6/bin/startx"
a=${p:-"Variable parm Not found"}
echo $a

a=${#p}
echo $a

a=${p%/*}
echo $a

a=${p%%/*}
echo $a

a=${p#*/}
echo $a

a=${p##*/}
echo $a
```

* 결과

```text
Variable p Not found
/usr/X11R6/bin/startx
21
/usr/X11R6/bin

usr/X11R6/bin/startx
startx
```

> 6행: 변수 p 가 제거 되었으므로 "Variable p Not found" 가 a에 들어간다.
>
> 10행: 변수 p 가 있으므로 그대로 a에 들어간다.
>
> 13행: a에는 변수 p의 길이가 들어간다.
>
> 16행: p 에서 가장 오른쪽의 "/"부터 끝까지 지우고 나머지를 a에 넣는다.
>
> 19행: p 에서 가장 왼쪽의 "/" 부터 끝까지 지우고 나머지를 a에 넣는다. (아무것도 없다)
>
> 22행: p 의 처음부터 가장왼쪽의 "/" 까지 지우고 나머지를 a에 넣는다.
>
> 25행: p 의 처음부터 가장 오른쪽의 "/"까지 지우고 나머지를 a에 넣는다.

## 3. 조건 판단
쉘 스크립트에서 조건판단은 if 와 test 명령을 혼합하여 사용한다. 일반적인 예는 다음과 같다.

```text
if test -f test1
then
...
fi
```

-f 는 주어진 인자가 일반 파일일 때 참이 된다.

test 명령은 [] 로 대체될 수 있다.

```text
if [ -f test1 ]
then
...
fi

if [ -f test1 ]; then
...
fi
```

### 1) test 명령
test 명령의 조건은 다음과 같이 세 부류로 나누어진다.

* 문자열 비교
  - string: string이 빈 문자열이 아니라면 참
  - string1 = string2: 두 문자열이 같다면 참
  - string1 != string2: 두 문자열이 다르면 참
  - -n string: 문자열이 null(빈 문자열) 이 아니라면 참
  - -z string: 문자열이 null(빈 문자열) 이라면 참

* 산술 비교
  - expr1 -eq expr2: 두 표현식 값이 같다면 참 ('EQual')
  - expr1 -ne expr2: 두 표현식 값이 같지 않다면 참 ('Not Equal')
  - expr1 -gt expr2: expr1 > expr2 이면 참 ('Greater Than')
  - expr1 -ge expr2: expr1 >= expr2 이면 참 ('Greater Equal')
  - expr1 -lt expr2: expr1 < expr2 이면 참 ('Less Than')
  - expr1 -le expr2: expr1 <= expr2 이면 참 ('Less Equal')
  - ! expr - expr 이 참이면 거짓, 거짓이면 참
  - expr1 -a expr2: expr1 AND expr2 의 결과 (둘다 참이면 참, 'And')
  - expr1 -o expr2: expr1 OR expr2 의 결과 (둘중 하나만 참이면 참, 'Or')

* 파일 조건
  - -b FILE: FILE 이 블럭 디바이스 이면 참
  - -c FILE: FILE 이 문자 디바이스 이면 참.
  - -d FILE: FILE 이 디렉토리이면 참
  - -e FILE: FILE 이 존재하면 참
  - -f FILE: FILE 이 존재하고 정규파일이면 참
  - -g FILE: FILE 이 set-group-id 파일이면 참
  - -h FILE: FILE 이 심볼릭 링크이면 참
  - -L FILE: FILE 이 심볼릭 링크이면 참
  - -k FILE: FILE 이 Sticky bit 가 셋팅되어 있으면 참
  - -p FILE: True if file is a named pipe.
  - -r FILE: 현재 사용자가 읽을 수 있는 파일이면 참
  - -s FILE: 파일이 비어있지 않으면 참
  - -S FILE: 소켓 디바이스이면 참
  - -t FD: FD 가 열려진 터미널이면 참
  - -u FILE: FILE 이 set-user-id 파일이면 참
  - -w FILE: 현재 사용자가 쓸 수 있는 파일(writable file) 이면 참
  - -x FILE: 현재사용자가 실행할 수 있는 파일(Executable file) 이면 참
  - -O FILE: FILE 의 소유자가 현재 사용자이면 참
  - -G FILE: FILE 의 그룹이 현재 사용자의 그룹과 같으면 참
  - FILE1 -nt FILE2 -: FILE1이 FILE2 보다 새로운 파일이면 ( 최근파일이면 ) 참
  - FILE1 -ot FILE2 -: FILE1이 FILE2 보다 오래된 파일이면 참
  - FILE1 -ef FILE2 -: FILE1 이 FILE2의 하드링크 파일이면 참

### 2) if 구문
if 문은 조건을 판단하여 주어진 문장을 수행한다.

* 단일 if 문

```text
if [ 조건 ]
then
    문장1
    문장2
fi
```

* if-else 문

```text
if [ 조건 ]
then
    문장3
    문장4
fi
```


* if-elif 문

```text
if [ 조건 ]
then
    문장1
    문장2
elif
    문장3
    문장4
else
    문장5
    문장6
fi
```

### 3) case 구문
'패턴'에는 * 문자, 즉 와일드카드를 사용할 수 있다.

```text
case 변수 in
패턴 [ | 패턴 ] ... ) 문장 ;;
패턴 [ | 패턴 ] ... ) 문장 ;;
....
* ) 문장 ;;
esac
```

### 4) 목록
여러 명령을 실행할때 앞의 명령의 결과에 의해서 다음행동이 결정되어야 할 경우가 있다. 이런경우에 AND나 OR조건을 사용해서 한번에 처리할 수 있다. 이것은 쉘 스크립트 뿐 아니라 명령행에서도 사용 가능하다. 물론 if 문을 이용해서 반환값을 검사하여 처리할 수 있지만 문장이 길어지고 복잡해진다.

* AND 목록

```text
statment1 && statment2 && statmentN && .....
```

위의 명령들은 각 명령이 거짓이 될 때 까지 명령을 수행해 나간다. 수행 도중 결과가 거짓이 되면 그이후의 명령은 수행되지 않는다.

* OR 목록

```text
statment1 || statment2 || statmentN || .....
```

위의 명령들은 각 명령이 거짓이 나오는 동안 계속된다. 즉 참이 나오면 실행을 멈춘다.

* AND와 OR목록은 혼용이 가능하다.

```text
[ 조건 ] && 문장1 || 문장2
```

위의 예는 조건이 참이면 문장1을 수행하고 거짓이면 문장2를 수행한다. 또한 위의 문장1이나 문장2에서 여러개의 문장을 수행하고 싶을 때는 {}를 사용하면 된다.

```text
[조건] && {
    문장1
    문장2
    문장3
} || {
    문장4
    문장5
    문장6
}
```

## 4. 제어문

### 1) for
for 문은 지정된 범위안에서 루프를 수행한다. 범위는 어떤 집합도 가능하다.

* 형식

```text
for 변수 in 값1, 값2, ...
do
    문장
done
```

매 루프를 돌때마다 변수의 값은 in 이후의 값으로 대체된다.

* 예

```text
for str in "test1", "test2", "test3", "test4"
do
    echo @str
done
```

* 출력

```text
test1
test2
test3
test4
```

값에는 와일드 카드 확장을 사용할 수 있다.

```text
for file in $(ls -a | grep "^\.")
do
    echo "$file is Hidden File"
done
```

위 예의 출력 결과는 현재 디렉토리에서 처음이 "." 으로시작하는 파일(히든파일)만을 출력한다.

```text
for file in $(ls chap[345].txt); do
    echo "--- $file ---" >> Books.txt
    cat $file >> Books.txt
done
```

위의 예는 chap3.txt, chap4.txt, chap5.txt 파일을 Books.txt 라는 파일에 붙여 넣는다.

### 2) while
for 명령의 경우는 횟수를 지정해서 루프를 수행하는 데는 문제가 있다. while 문은 실행 횟수가 지정되지 않았을 때 편리하다.

* 형식

```text
while 조건문
do
    문장
done
```

예를 보자. 패스워드를 입력받고 맞는지 확인하는 프로그램이다.

```text
echo "Enter Password : "
read password1

echo "Retype Password : "
read password2

while [ "$password1" != "$password2" ]
do
    echo "Password mismatch Try again "

    echo "Retype Password : "
    read password2
done

echo "OK Password Match complete"
```

### 3) until
until은 while문과 동일한 효과를 내지만 조건이 반대이다. 즉, while문은 조건이 참일동안 루프를 수행하지만 until은 조건이 거짓일 동안 루프를 수행한다.

* 형식

```text
until 조건문
do
    문장
done
```

다음 예를 보자. 이 예는 지정한 유저가 로그인하면 알려준다.

```shell
#!/bin/sh

until who | grep "$1" > /dev/null
do
    sleep 10
done

echo "User $1 just logged in ^_^"
```

### 4) select
select문은 원하는 리스트를 출력하고 그 중 선택된 것을 돌려주는 구문이다. 주의할 점은 select의 루프 내에서는 자동적으로 루프를 벗어날 수 없다. 반드시 break문을 사용해서 루프를 벗어나야 한다.

* 예

```shell
#!/bin/sh

echo "다음중 스크립트언어 프로그래밍에 속하는 것은 ?"
select var in "쉘 프로그래밍" "C 프로그래밍" "자바 프로그래밍" "Exit"
do
    if [ "$var" = "쉘 프로그래밍" ]
    then
        echo "정답입니다."
        exit 0
    elif [ "$var" = "Exit" ]
    then
        echo "종료합니다."
        exit 1
    else
        echo "$var 을 선택하셨습니다. 오답입니다."
        echo "다음중 스크립트언어 프로그래밍에 속하는 것은 ?"
    fi
done
```

## 5. 함수
쉘 스크립트 내부에 또는 다른 스크립트파일에 함수를 정의해 놓고 사용할 수 있다. 함수를 사용하면 코드를 최적화 할 수 있고, 코딩이 간결해지며,재사용이 가능하다. 그러나 다른 스크립트 파일을 호출해서 함수를 실행할 경우, 가능은 하지만 스크립트의 실행시간이 길어지고, 함수의 결과를 전달하는 것이 까다롭기 때문에 가급적이면 외부파일의 함수는 안쓰는 것이 좋다.

* 형식

```text
함수명 ()
{
	문장
	return 값
}
```

* 사용

```text
함수명 인자1, 인자2, ...
```

함수는 독립적으로 $#, $*, $0 등의 인자 변수를 사용한다. 즉 함수내의 $#과 본체의 $#은 다를 수 있다는 것이다.

* 예

```shell
#!/bin/sh
		
func()
{
    echo ------ this is func --------
    echo "This Script Executable File : $0"
    echo "Argument Count : $#"
    echo "Process ID : $$"
    echo "Argument List \$* : $*"
    echo "Argument List \$@ : $@"
    echo "Argument 1 : $1"
    echo "Argument 2 : $2"
    echo "Argument 3 : $3"
}

echo ------ this is main --------
echo "This Script Executable File : $0"
echo "Argument Count : $#"
echo "Process ID : $$"
echo "Argument List \$* : $*"
echo "Argument List \$@ : $@"
echo "Argument 1 : $1"
echo "Argument 2 : $2"
echo "Argument 3 : $3"
echo "Argument 4 : $4"
func aa bb cc
```

본체와 함수에서 동일한 변수를 보여주지만 값은 틀린다는 것을 알 수 있다.

함수에서 값을 반환하기 - 함수에서 반환값은 반드시 정수값만을 반환할 수 있다. 이 값을 if 등으로 조건을 판단해서 사용할 수 있다. 반환값 중 0은 참으로 나머지 숫자는 거짓으로 판별된다.

## 6. 명령어
쉘에서 쓸 수 있는 명령어는 두가지로 나누어진다. 명령 프롬프트 상에서 실행 시킬 수 있는 외부 명령어와 쉘 내부 명령이다. 내부 명령은 보통 쉘 내부나 쉘 구문상에서 쓰인다. 외부명령은 쉘에 관계없이 사용이 가능하다.

### 1) break
제어문이나 조건문의 루프를 빠져나갈때 사용한다.

* 예

```text
while [ $a -eq 10 ]
do
    if [ $a -eq 5 ]; then
        break
    fi
done
```

### 2) continue
제어문이나 조건문의 처음으로 돌아가서 다시수행한다.

* 예

```text
while [ $a -eq 10 ]
do
    if [ $a -eq 5 ]; then
        continue
    fi
done
```

### 3) : 명령
의미없는 명령. 논리값 true를 대신해 쓰기도 한다.

### 4) . 명령
. 명령을 사용하면 현재 쉘에서 명령을 실행시킨다 그러므로 실행된 명령의 결과를 본 프로그램에서 사용할 수 있다.

예를 들면 A 라는 스크립트에서 B라는 스크립트를 그냥 실행할 경우 B에서의 변화(환경변수 등)는 A에게 아무런 영향도 미치지 않는다. 그러나 . 명령을 사용해서 실행하면 B에서의 변화가 A에도 영향을 미친다.

### 5) echo
문장을 출력한다. 자동으로 개행문자가 삽입된다. (다음 줄로 넘어간다)

### 6) eval
인자의 실제 값을 구하는데 사용한다.

```text
foo=10
x=foo
y='$'$x
echo $y
```

이 예를 실행해 보면 $foo가 출력된다

```text
foo=10
x=foo
eval y='$'$x
echo $y
```

이 예에서는 $foo의 값 즉 10 이 출력된다. eval명령은 원하는 문자열들을 조합해서 변수를 액세스 할 수 있다.

### 7) exec
현재 쉘을 다른 프로그램으로 대체한다.

* 예

```text
exec csh
```

### 8) exit n
현재 쉘을 종료한다. 종료시 n 값을 리턴한다.

### 9) export
해당 쉘에서 파생된 자식 프로세스에서 export한 환경변수는 본래 쉘에서 관리한다.

### 10) expr
표현식의 값을 구한다.

```text
x=`expr 1 + 2`
```

요즘은 expr보다는 $((계산식)) 구문을 많이 사용한다.

### 11) printf
C 언어의 printf명령과 흡사하다.

* 형식

```text
printf "Format String" arg1 arg2 arg3 ...
```

### 12) return
쉘 함수에서 값을 반환 할 때 쓰인다. 0은 성공을 1~125까지는 쉘 에러코드를 나타낸다.

### 13) set
쉘 내부에서 매개 인자를 설정한다. set의 인자로 쓰인 문자열은 공백에 의해 $1 부터 차례대로 대입된다.

* 예

```shell
#!/bin/sh

echo $#
set $(ls)
echo $#
```

* 결과

```text
0
22
```

(22는 필자의 ls 결과의 갯수이다.) 첫번째 0는 이 스크립트에 인수가 없으므로 0이고 set $(ls) 에 의해서 인수의 갯수가 22개로 늘었다.

### 14) shift
쉘의 인자를 한자리씩 아래로(n -> 1 로) 이동시킨다.

* 예

```shell
#!/bin/sh

echo $1
shift
echo $1
shift 5
echo $1
```

* 실행

```text
#./myscript 1 2 3 4 5 6 7 8 9 0
1
2
7
```

### 15) trap
쉘의 실행도중 시그널을 처리하는 시그널 처리기를 만드는 역할을 한다.

* 형식

```text
trap command signal
```

쉘 스크립트는 위에서 아래로 실행되므로 보호하려는 부분 이전에 trap 명령을 사용해야 한다. trap 조건을 기본으로 사용하려면 명령에 - 를 넣으면 된다. 신호를 무시하려면 '' 빈 문자열을 준다.

### 16) unset
변수나 함수를 제거한다.

## 7. 명령 실행
외부 명령의 실행 결과를 변수에 집어넣어 변수의 값으로 사용할 수 있다.

* 형식

```text
x = $(명령)
```

이렇게 변수에 결과를 넣은 후에는 이 변수를 일반문자열로 생각하고 원하는 가공을 해서 결과를 얻어낼 수 있다. 위에서 보았던 매개변수 확장이나 set명령을 이용해서 원하는 부분을 추출해 내면 그만이다.

## 8. 쉘 스크립트 내부에서 명령에 입력 전달(Here Documents)
이 기능은 쉘 내부에서 명령어에 입력을 전달하는 방법이다. 전달된 입력은 마치 키보드에서 눌려진 것처럼 반응한다.

* 형식

```text
명령 << 종료문자열
입력값.....
종료문자열
```

* 예

```shell
#!/bin/sh

mail $1 << myscript
This is Header
This is Body
.

myscript
```

## 9. 디버깅
쉘 프로그래밍 시 간단하게 디버깅하는 방법

### 1) 쉘 옵션

* sh -n 스크립트: 문법 에러만을 검사, 명령을 실행하지 않음
* sh -v 스크립트: 명령을 실행하기 전에 에코
* sh -x 스크립트: 명령줄에서 처리한 다음 에코

### 2) set 옵션
위의 쉘 옵션은 아래와 같이 set 옵션으로도 설정할 수 있다.

* set -o noexec 또는 set -n: 문법 에러만을 검사, 명령을 실행하지 않음
* set -o verbose 또는 set -v: 명령을 실행하기 전에 에코
* set -o xtrace 또는 set -x: 명령줄에서 처리한 다음 에코
* set -o nounset 또는 set -u: 정의되지 않은 변수가 사용되면 에러 메시지를 제공한다.

아래와 같이 set -x를 이용하여 손쉽게 실행과정을 추적할 수 있다. (참고로 set 옵션을 취소하려면 set +x를 입력하면 된다. 다른 옵션도 마찬가지)

```text
set -x
for str in "test1" "test2" "test3" "test4"
do
    echo $str
done
```

* 결과

```text
+ for str in '"test1"' '"test2"' '"test3"' '"test4"'
+ echo @str
@str
+ for str in '"test1"' '"test2"' '"test3"' '"test4"'
+ echo @str
@str
+ for str in '"test1"' '"test2"' '"test3"' '"test4"'
+ echo @str
@str
+ for str in '"test1"' '"test2"' '"test3"' '"test4"'
+ echo @str
@str
```

## [출처 및 참고]
* <https://wiki.kldp.org/wiki.php/ShellProgrammingTutorial>
