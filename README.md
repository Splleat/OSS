# OSS
### getopt
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

#### getopt의 기본 형태
`getopt -o | --options <shortopts> [-l | --longoptions <longopts을 정의하는 문자>] [-n | --name progname] [--] parameters`
* **shortopts** : short 옵션을 정의하는 문자

* **longopts** : long 옵션을 정의하는 문자

* **progname** : 오류 발생 시 리포팅할 프로그램 명칭(현재 쉘 스크립트 파일명)으로, 잘 사용되지 않음

* **parameters** : 옵션에 해당하는 실제 명령 구문

##### 예시
1. `getopt -o a:bc` : 옵션은 a, b, c 3개로, 옵션 우측에 콜론(:)의 유무로 옵션 인수의 사용 여부를 구별할 수 있다.

2. `getopt -l help,path:,name:` : long 옵션의 경우, 옵션명은 ','로 구분한다.

3. `getopt -o a: -l help,path:,name: -- "$@"` : 명령의 마지막에는 하이픈(-) 두 개와 옵션에 해당하는 실제 파라미터를 입력한다. 보통 모든 파라미터를 나타내는 **"$@"** 를 자주 이용한다.
***
> **Example Code**
> ```
> # RGB.sh
> 
> #!/bin/bash
>
> opts=$(getopt -o rgb -l color: -- "$@")
> 
> [ $? -eq 0 ] || {
> 	echo "Incorrect options."
> 	exit 1
> }
> 
> eval set -- "$opts"
> 
> while true
> do
> 	case "$1" in
> 	-r)
> 		echo "Color is RED"
> 		;;
> 	-g)
> 		echo "Color is GREEN"
> 		;;
> 	-b)
> 		echo "Color is BLUE"
> 		;;
> 	--color)
> 		echo "Color is $2"
> 		;;
> 	--)
> 		shift
> 		break
> 		;;
> 	esac
> 	shift
> done
> ```
> **Input / Output**
> 
> `./RGB.sh -r` 
>> _Color is RED_
>
> `./RGB.sh -y`
>> _getopt: invalid option -- 'y'_
>>
>> _Incorrect options_
> 
> `./RGB.sh --color BLACK`
>> _Color is BLACK_  
***

### getopts
> **명령줄 인수(command-line arguments)를 분석하기 위해 기본 제공되는 Unix shell 명령어**로, getopts는 getopt의 C 인터페이스에 기반을 둔 POSIX 유틸리티 문법 가이드라인을 따르는 명령줄 인수를 처리하도록 설계되었다.

#### getopt vs getopts
##### getopt를 사용하지 않는 이유
1. 외부 유틸리티 프로그램이기 때문에
2. 기본 버전은 long 옵션을 다루지 않으며 또한 내장 공간을 다루지 못하고, 공백인 인수나 공백이 포함된 인수를 처리할 수 없으며 오류 메시지의 출력을 비활성화 할 수 없음
3. 루프를 완벽하게 작동하기 위해서 for 루프 자체와 동일한 순서로 값을 제공해야 하며, 이 값은 제어하기가 어려움
4. 매개변수를 표준화하는 대부분의 방법 때문에
> getopt 사용을 고려할 수 있는 유일한 경우는 이름이 긴 하나의 변수만을 사용할 때이다.
##### getopt 대신 getopts를 사용하는 이유
1. 모든 POSIX shell에서 작업하고 휴대할 수 있음
2. "-a -b" 및 "-ab" 사용에 관대함
3. 하이픈(-)을 옵션 종료자로 이해

#### getopts의 기본 형태
`getopts <option_string> varname`
* **option_string** : 옵션을 정의하는 문자로, getopt와 동일
* **varname** : 옵션 명을 받을 변수, OPTARG 변수에는 실제 옵션의 값이 세팅
* 

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

_getopt와 동일_
***

### sed
### awk
