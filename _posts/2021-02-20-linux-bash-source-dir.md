---
layout: article
title: LINUX - Bash 스크립트에서 자기 자신의 파일명 찾기
aside:
 toc: true
tags: linux
---

## Problem
bash 스크립트 내에서 스크립트가 저장된 위치(절대 경로)를 찾고 싶다.  

# Take Away  
스크립트의 절대 경로는 다음과 같은 명령어로 출력할 수 있다. 

```sh
cd $(dirname -- "${BASH_SOURCE[0]}") && pwd
```

`cd $(dirname -- "${BASH_SOURCE[0]}")` 는 스크립트가 저장된 위치로 가라는 명령어이고, `pwd`는 현재 작업 위치 (working directory)의 절대 경로를 출력하라는 명령어이다.   

`dirname` 명령어는 `dirname filename` 형식으로 사용되며, filename의 절대 경로 중에서 슬래시(`/`)와 파일명을 제외한 (즉, "/filename") 경로를 출력한다. 즉, filename의 절대 경로를 출력한다.   
예를 들어 "/home/test/test.sh" 파일이 속한 디렉토리의 절대 경로를 알고 싶을 때, `dirname test.sh` 명렁어를 입력하면 "/home/test"를 출력한다. 만약, 파일의 저장 위치에서 `dirname test.sh`를 입력하면 "."만 출력한다. 

`$0`와 `$BASH_SOURCE`변수는 스크립트 파일의 이름, 즉 **파일명**을 값으로 갖는다.    
하지만 두 변수가 항상 동일한 값을 갖는 것은 아니다. 자세한 내용은 아래를 참고하자. 

## Contents

### method 1: "$0" 명령어 사용
```sh
cur_dir=$(cd $(dirname -- "$0") && pwd) # 스크립트의 절대 경로를 cur_dir 변수에 저장
echo $cur_dir
```
`$0`변수는 스크립트의 파일명을 출력한다. 하지만 만약 `source` 명령어로 스크립트를 실행한다면 `$0`변수 값은 바뀔 수 있다. 즉, `$0`의 값이 항상 동일하다는 보장을 할 수 없다. 

##### [참고] source 명령어란?
> `source`는 파일을 읽고 실행하는 쉘스크립트 명령어이다. `source FILENAME [arguments]` 문법으로 사용되며, 이 명령어가 실행되면 파일의 텍스트(코드)가 Tcl 인터프리터로 보내져 파일이 실행된다. 

##### [참고] `source FILENAME.sh` 과 `bash FILENAME.sh`의 차이점 [ClickMe:)]({% post_url 2021-02-24-linux-shell-source-command %})


### method 2: "$BASH_SOURCE" 명령어 사용
```sh
cur_dir=$(cd $(dirname $BASH_SOURCE) && pwd)
echo $cur_dir
```
`$BASH_SOURCE`는 스크립트가 어느 위치에서 실행되더라도 자신의 (상대) 경로를 저장하고 있는 변수다. 즉 `source` 명령어로 스크립트를 실행해도 이 변수의 값은 바뀌지 않는다.     

a.sh은 b.sh을 실행하는 파일이라고 가정해보자. 
```sh
$cat a.sh
#!/bin/bash
source b.sh
```

b.sh은 `$0`와 `$BASH_SOURCE`을 각각 출력하는 코드를 담고 있다. 
```sh
$cat b.sh
#!/bin/bash
echo "\$0 = '$0'"
echo "\${BASH_SOURCE[0]} = '${BASH_SOURCE[0]}'"
```

이제 a.sh을 실행해보자.
```sh
$ ./a.sh
$0 = './a.sh' 
$BASH_SOURCE[0] = './b.sh'
```
`source`명령어를 사용해 b.sh을 실행하면 `$0` 변수가 더 이상 b.sh 스크립트를 출력하지 않는다. 대신 `$0` 변수의 값은 b.sh을 실행하는 스크립트인 a.sh의 파일명을 갖는다. 결론적으로, 스크립트 변수명을 알고 싶을 땐, 가변적인 변수인 `$0` 보다 값이 변하지 않는 `BASH_SOURCE` 변수를 사용하는 것이 안전하다. 

## Reference
[choosing-between-0-and-bash-source](https://stackoverflow.com/questions/35006457/choosing-between-0-and-bash-source)

