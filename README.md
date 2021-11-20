# OSS
## getopt
> 유닉스/POSIX 스타일의 **명령줄 옵션(command-line options)을 분석**하는 데 사용되는 C 라이브러리 함수로, 쉘 스크립트에서 명령줄 인수를 구문 분석하기 위한 유닉스 프로그램의 이름이기도 하다.
>> **command-line options** : **프로그램에 매개 변수를 전달하는 데 사용되는 명령어**들로, 인터페이스에서 다양한 설정을 변경하거나 명령어들을 실행하기 위한 신호를 전달하는 역할을 수행
>>
>> **POSIX(Portable Operation Systems Interface with Unix)** : Unix의 호환성과 이식성을 위해서 국제 표준 기구(ISO)에 표준으로 선택된 규약 

#### option의 종류와 특징
* **short option**
1. `ls -a`, `ls -lh`와 같이 하나의 하이픈(-)으로 시작하면서 `$ command -a -b -c`의 형태를 가지고 있다.
2. `$ command -abc`, `$command -b -ac`와 같이 붙여서 사용하거나 순서가 바뀌어도 된다.
3. `$ command -a nnn -b -c mmm`와 같이 nnn, mmm부분과 같은 옵션 인수(argument)를 가질 수 있으며, `$ command -annn`처럼 옵션 인수를 옵션에 붙여 쓸 수 있다.
4. `$ command -a -b -- -c`와 같이 옵션구분자 '--'가 올 경우 우측에 있는 값은 옵션으로 해석할 수 없다.

* **long option**
1. `$ command --help`와 같이 두개의 하이픈(-)으로 시작한다.
2. short 옵션과는 달리 붙여 쓸 수 없다.

![image](https://user-images.githubusercontent.com/94677012/142723700-566004fb-d5d8-420e-a28b-dccd0f9ed743.png)

#### getopt의 Syntax
`getopt -o | --options <shortopts> [-l | --longoptions <longopts을 정의하는 문자>] [-n | --name progname] [--] parameters`
* **shortopts** : short 옵션을 정의하는 문자

* **longopts** : long 옵션을 정의하는 문자

* **progname** : 오류 발생 시 리포팅할 프로그램 명칭(현재 쉘 스크립트 파일명)으로, 잘 사용되지 않는다.

* **parameters** : 옵션에 해당하는 실제 명령 구문

##### 예시
1. `getopt -o a:bc` : 옵션은 a, b, c 3개로, 옵션 우측에 콜론(:)의 유무로 옵션 인수의 사용 여부를 구별할 수 있다.

2. `getopt -l help,path:,name:` : long 옵션의 경우, 옵션명은 ','로 구분한다.

3. `getopt -o a: -l help,path:,name: -- "$@"` : 명령의 마지막에는 하이픈(-) 두 개와 옵션에 해당하는 실제 파라미터를 입력한다. 보통 모든 파라미터를 나타내는 **"$@"** 를 자주 이용한다.
***
**Example Code**
```
# RGB.sh

#!/bin/bash

opts=$(getopt -o rgb -l color: -- "$@")
 
[ $? -eq 0 ] || {
	echo "Incorrect options."
	exit 1
}

eval set -- "$opts"
 
while true
do
	case "$1" in
	-r)
		echo "Color is RED"
		;;
	-g)
		echo "Color is GREEN"
		;;
	-b)
		echo "Color is BLUE"
		;;
	--color)
		echo "Color is $2"
		;;
	--)
		shift
 		break
		;;
	esac
	shift
done
```
**Input / Output**

`./RGB.sh -r` 
```
Color is RED
```
 ***
 `./RGB.sh -y`
``` 
getopt: invalid option -- 'y'
Incorrect options
```
***
`./RGB.sh --color BLACK`
```
Color is BLACK
```
***

## getopts
> **명령줄 인수(command-line arguments)를 분석하기 위해 기본 제공되는 Unix shell 명령어**로, getopts는 getopt의 C 인터페이스에 기반을 둔 POSIX 유틸리티 문법 가이드라인을 따르는 명령줄 인수를 처리하도록 설계되었다.

#### getopt vs getopts
##### getopt를 사용하지 않는 이유
1. 외부 유틸리티 프로그램이기 때문에
2. 기본 버전은 **long 옵션을 다루지 않으며** 또한 **내장 공간을 다루지 못하고**, **공백인 인수나 공백이 포함된 인수를 처리할 수 없으며** **오류 메시지의 출력을 비활성화 할 수 없음**
3. 루프를 완벽하게 작동하기 위해서 for 루프 자체와 동일한 순서로 값을 제공해야 하며, 이 값은 제어하기가 어려움
4. 매개변수를 표준화하는 대부분의 방법 때문에
> getopt 사용을 고려할 수 있는 **유일한 경우**는 **이름이 긴 하나의 변수만을 사용할 때**이다.
##### getopt 대신 getopts를 사용하는 이유
1. 모든 POSIX shell에서 작업하고 휴대할 수 있음
2. "-a -b" 및 "-ab" 사용에 관대함
3. 하이픈(-)을 옵션 종료자로 이해

#### getopts의 Syntax
`getopts <option_string> varname`
* **option_string** : 옵션을 정의하는 문자로, getopt와 동일
* **varname** : 옵션 명을 받을 변수, OPTARG 변수에는 실제 옵션의 값이 세팅

##### 예시
`getopt a:b:c:d opt`
***
**Example Code**
```
# RGB.sh

#!/bin/bash

while getopts rgbi: opt
do
case $opt in
	r)
		echo "Color is RED"
		;;
	g)
		echo "Color is GREEN"
		;;
	b)
		echo "Color is BLUE"
		;;
	i)
		echo "Color is $2"
		;;
	*)
		echo "Incorrect options"
		exit 1
		;;
	esac
done
```
**Input / Output**

`./RGB.sh -r` 
```
Color is RED
```
 ***
 `./RGB.sh -y`
``` 
getopt: invalid option -- 'y'
Incorrect options
```
***
`./RGB.sh -i BLACK`
```
Color is BLACK
```
***

#### Error handling
getopts는 오류 메시지의 출력에 대해 다음의 두 가지 모드를 제공한다.
* Vervose mode
* Silent mode
> 간단히 설명하면 **Vervose** 모드는 **오류 메시지를 출력**하는 모드이고, **Silent mode**는 **오류 메시지를 출력하지 않는** 모드이다.

우리는 위의 예시 코드에서 존재하지 않는 옵션인 `-y`를 입력했을 때 `getopt: invalid option -- 'y'`라는 오류 메시지를 볼 수 있었다.

이를 통해 getopts의 디폴트 모드는 Vervose라는 것을 알 수 있다. 그렇다면 Silent 모드로 전환하기 위해서는 어떻게 해야 할까?

##### Silent mode
getopts에서 Silent 모드를 이용하려면 option_string의 맨 앞부분에 ':'를 추가하면 된다.

`while getopts rgbi: opt` -> `while getopts :rgbi: opt`

이렇게 코드를 수정한 뒤, 다시 `./RGB.sh -y`를 입력한다면, 
```
Invalid options
```

이렇게 오류 메시지가 출력되지 않는 것을 알 수 있다.

## sed
> sed는 Stream Editor의 약자로, 단순하고 명확한 프로그래밍 언어를 사용하여 **텍스트**를 **분석**하고 **변환**하는 Unix utility이다.
>> 일반 텍스트의 문자열 조작과 스트림 편집을 위한 대표적인 대체 툴로는 **AWK**와 **Perl**이 있다.

#### sed의 특징
1. 비 대화형 편집기로, vi편집기처럼 직접 파일을 열어 고치지 않고도 원하는 부분만 변경할 수 있다.
2. 쉘 리다이렉션을 이용하여 편집 결과를 저장하기 전까지는 원본 파일이 변경되지 않는다.
3. 쉘 스크립트에서 파이프('|')와 같이 사용될 수 있다.
4. 패턴 버퍼(Pattern Buffer)와 홀드 버퍼(Hold Buffer)라는 두 개의 임시 저장 공간을 사용한다.

#### sed의 Syntax
`sed [option] command fileName
**option**
* -e : sed 사용 시 출력되는 값을 보여줌. 기본값으로 설정되어 있으나, 다중 명령어 사용 시 반드시 사용 필요
* -n : 특정 값이 있는 줄만 출력. 주로 서브 명령어 p와 사용
* -f : 스크립트를 파일로부터 읽어들이며 명령어를 지정 
* -i : 변경되는 값을 출력 없이 원본 파일에 바로 적용

#### sed의 subcommand : p, d, s가 가장 자주 사용
|커맨드|설명|
|:---:|:----------:|
|**p**|**출력**|
|**d**|**삭제**|
|**s**|**치환**|
|,|범위 지정|
|r|사용자가 지정한 줄만 읽음|
|w|사용자가 지정한 줄을 파일에 저장|
|a|내용 추가|
|i|내용 삽입|
|c|내용 변경|
|y|변환|
|e|다중 편집|
|q|종료|

#### 예시
```
# Student_Data.txt

Kim	20200001	3.26
Park	20200002	2.84
Jeong	20200003	4.32
Nam	20200004	3.6
Lee	20200005	4.23
Kang	20200006	1.97
```
***

`sed -n '/Kim/p' Student_Data.txt` : "Kim"이 포함된 줄을 출력한다.
```
Kim     20200001        3.26
```
***
`sed -n '/Kim/,/Jeong/p' Student_Data.txt` : "Kim"과 "Jeong" 사이의 모든 줄을 출력한다.
```
Kim     20200001        3.26
Park    20200002        2.84
Jeong   20200003        4.32
```
***

`sed '$d' Student_Data.txt` : 마지막 줄을 제외한 나머지를 출력한다.
```
Kim     20200001        3.26
Park    20200002        2.84
Jeong   20200003        4.32
Nam     20200004        3.6
Lee     20200005        4.23
```
***

`sed 's/2020/2021/' Student_Data.txt` : 2020을 2021로 치환한다.
```
Kim     20210001        3.26
Park    20210002        2.84
Jeong   20210003        4.32
Nam     20210004        3.6
Lee     20210005        4.23
Kang    20210006        1.97
```
***

## AWK
> AWK(Aho Weinberger Kernighan)는 개발자들의 성의 앞글자를 따서 붙여진 이름으로, 유닉스에서 처음 개발된 **텍스트를 처리하기 위한 일반 스크립트 언어**이다.
>> 스크립트 언어 : 응용 소프트웨어를 제어하는 컴퓨터 프로그래밍 언어
>
> ![image](https://user-images.githubusercontent.com/94677012/142735232-8a96e9fc-d880-4c2c-b04e-ae884f617c86.png)
>
> AWK는 모든 자료를 **레코드(Record)** 와 **필드(Field)** 로 구분하는데, 위 표의 행($0)이 레코드에 해당하고 열($1, $2, $3)이 필드에 해당한다.
>> abc를 포함하는 1행이 첫번째, def를 포함하는 2행이 두번째, ghi를 포함하는 3행이 세번째 레코드이다.
>> 
>> adg를 포함하는 1열이 첫번째, beh를 포함하는 2열이 두번째, cfi를 포함하는 3열이 세번째 필드이다.

#### AWK의 option
* -F : 필드 구분 문자 지정
* -f : AWK program 파일 경로 지정
* -v : AWK program에서 사용될 특정 변수 값 지정

#### AWK의 내장 함수
|함수|설명|
|--------------------|-------------------------------|
|sub(a, b)|a를 b로 치환|
|gsub(a, b)|모든 a를 b로 치환|
|index(str, substr)|str에 대해 substr의 index 반환|
|length(str)|str의 길이 반환|
|substr(str, start, [length])|str에 대해 start부터 length까지의 길이 반환|

이 외에도 match, split, printf, systime, mktime 등 존재

#### AWK의 Syntax
`awk '<pattern> {action} ./fileName`
* pattern과 action을 정의하여 fileName의 데이터를 가공하여 출력
* pattern과 action 중 하나만 정의해도 됨

##### 예시
```
# Student_Data.txt

Kim	20200001	3.26
Park	20200002	2.84
Jeong	20200003	4.32
Nam	20200004	3.6
Lee	20200005	4.23
Kang	20200006	1.97
```
***
`awk '{ print $1 }' ./Student_Data.txt` : 첫번째 열을 출력한다.
```
Kim
Park
Jeong
Nam
Lee
Kang
```
***
`awk '/Kang/' ./Student_Data.txt` : "Kang"이 포함되어 있는 레코드를 출력한다.
```
Kang	20200006	1.97
```
***
`awk '{ print ("name : " $1, ", ", "student_ID : " $2) }' ./Student_Data.txt` : 이름과 학번을 명시적으로 나타낸다.
```
name : Kim ,  student_ID : 20200001
name : Park ,  student_ID : 20200002
name : Jeong ,  student_ID : 20200003
name : Nam ,  student_ID : 20200004
name : Lee ,  student_ID : 20200005
name : Kang ,  student_ID : 20200006
```
***
`awk '{ if ( $3 >= 3 ) print ($0) }' ./Student_Data.txt` : 학점이 3이 넘는 레코드만 출력한다.
```
Kim     20200001        3.26
Jeong   20200003        4.32
Nam     20200004        3.6
Lee     20200005        4.23
```
***
#### BEGIN / END 패턴
* BEGIN : AWK가 입력 파일의 라인들을 처리하기 이전에 실행, AWK의 {action} 블록 앞에 놓인다.
* END : 입력의 모든 라인이 처리되고 난 후에 처리된다.

##### 예시
```
# Student_Data.txt

Kim	20200001	3.26
Park	20200002	2.84
Jeong	20200003	4.32
Nam	20200004	3.6
Lee	20200005	4.23
Kang	20200006	1.97
```
***
`awk 'BEGIN{OFS=" | "}{print $1, $2, $3}' ./Student_Data.txt` : "|"로 이름과 학번, 학점을 구분한다.
```
Kim | 20200001 | 3.26
Park | 20200002 | 2.84
Jeong | 20200003 | 4.32
Nam | 20200004 | 3.6
Lee | 20200005 | 4.23
Kang | 20200006 | 1.97
```
***
`awk 'BEGIN{s=0}{s=s+$3}END{print s/6}' ./Student_Data.txt` : 학점의 평균값을 출력한다.
```
3.37
```
***
