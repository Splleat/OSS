# OSS
### getopt
> 
> 유닉스/POSIX 스타일의 **명령줄 옵션(command-line options)을 분석**하는 데 사용되는 C 라이브러리 함수로, 쉘 스크립트에서 명령줄 인수를 구문 분석하기 위한 유닉스 프로그램의 이름이기도 하다.
> 
>> **command-line options** : **프로그램에 매개 변수를 전달하는 데 사용되는 명령어**들로, 인터페이스에서 다양한 설정을 변경하거나 명령어들을 실행하기 위한 신호를 전달하는 역할을 수행한다.

#### option의 종류와 특징
* **short option**
> 1. `ls -a`, `ls -lh`와 같이 하나의 하이픈(-)으로 시작하면서 `$ command -a -b -c`의 형태를 가지고 있다.
> 
> 2. `$ command -abc`, `$command -b -ac`와 같이 붙여서 사용하거나 순서가 바뀌어도 된다.
>
> 3. `$ command -a nnn -b -c mmm`와 같이 옵션인수를 가질 수 있으며, `$ command -annn`처럼 옵션인수를 옵션에 붙여 쓸 수 있다.
>
> 4. `$ command -a -b -- -c`와 같이 옵션구분자 '--'가 올 경우 우측에 있는 값은 옵션으로 해석할 수 없다.
>
* **long option**
> 1. `$ command --help`와 같이 두개의 하이픈(-)으로 시작한다.
>
> 2. short 옵션과는 달리 붙여 쓸 수 없다.

#### getopt의 기본 형태
`getopt -o | --options <shortopts> [-l | --longoptions <longopts을 정의하는 문자>] [-n | --name progname] [--] parameters`
* **shortopts** : short option을 정의하는 문자

* **longopts** : long option을 정의하는 문자

* **progname** : 오류 발생 시 리포팅할 프로그램 명칭(현재 쉘 스크립트 파일명)으로, 잘 사용되지 않음

* **parameters** : option에 해당하는 실제 명령 구문

##### 예시
> `getopt -o a:` : 옵션 우측에 콜론(:)의 유무로 옵션 인수의 사용 여부를 구별할 수 있다.
>
> `getopt -l help,path:,name:` : 옵션명은 ','로 구분한다.
>
> `getopt -o a: -l help,path:,name: -- "$@"` : 명령의 마지막에는 하이픈(-) 두 개와 옵션에 해당하는 실제 파라미터를 입력한다. 보통 모든 파라미터를 나타내는 **"$@"** 를 자주 이용한다.
> ***
> **Code**
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
> ***

### getopts
### sed
### awk
