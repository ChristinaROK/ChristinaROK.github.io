---
layout: article
title: PYTHON - 모듈? 패키지? 사실 알고보면 쉬운 개념 (feat. 파이썬 공식 문서)
aside:
 toc: true
tags: python

---

## 모듈과 패키지는 파이썬 초심자가 프로그램을 짜다가 욕심이 점점 많아졌기 때문에 생겨났다.



### 1. Interpreter

cmd창에 `python` 을 입력하면 python interpreter가 실행된다.

```python
>>a=1
>>b=2
>>print(a+b)
# 3
>>exit()
# ???
```

변수에 값을 저장하고 더했다. 잘 된다! 하지만 interpreter를 종료한 후 다시 실행하면?    

기껏 저장한 변수 a, b를 재사용할 수 없다. ㅠㅠ   

변수와 함수를 재사용할 수 없다는 것이 interpreter의 한계이다.    

### 2. Script

변수를 저장하고 싶은 욕심이 생긴다. 그래서 text editor인  Visual Studio Code를 깔았다.    

새로운 파일을 만들어 `calculate.py` 라고 파일명을 붙인다. 이제 파이썬 파일, 즉 script가 생성됐다.    

 ```python
 # calculate.py
 a=1
 b=1
 print(a+b)
 ```

script에서 변수 a, b를 정의했으니 이제 변수 재사용이 가능하다!    

하지만 덧셈을 하다보니 뺄셈, 나눗셈, 곱셈 등등 다양한 함수를 구현해보고 싶어졌다. 그리고 변수 a, b 의 값도 바꿔보고 싶은 욕심이 생겼다.   

### 3. Module

Script를 기능 단위로 분리하면 module이 된다 .   

Module 파일은  `.py` 로 끝나며, 변수, 함수, 클래스가 구현 된다.    

Module의 특징은 바로 **import** 가 가능하단 점이다. 내가 실행하고 싶은 프로그램(또는 스크립트)에서 덧셈, 뺄셈, 나눗셈, 곱셈 함수를 사용하고 싶을 때 매번 구현할 필요 없이 `calculate` 모듈만 import하면 모듈 내부의 모든 것을 재사용할 수 있다. 물론 module 내부에서도 다른 module을 import 할 수 있다. (아래 예시에 `import sys` 를 참고!)    

이제 모듈 덕분에 프로그램 관리를 체계적으로 할 수 있다! 🤩

```python
# calculate.py
import sys

def add (a,b):
  return a+b

def subtract(a,b):
  return a-b

if __name__=="__main__":
  if len(sys.argvs)==3:
    a = sys.argv[1]
    b = sys.argv[2]
    print(f"Add Result: {add(a,b)}")
    print(f"Subtract Result: {subtrct(a,b)}")
	else:
    print(f"USAGE : {sys.argv[0]} a b")
```

* Module 더 알아보기

  * `__name__ ` 변수

    * 모듈 자체를 실행하는 경우

      ```sh
      $ python calculate.py 1 2 
      >> Add Result: 3
      >> Subtract Result: -1
      ```

      모듈 자체가 실행될 때 `__name__` 변수는 `__main__` 이 된다. 즉, 지금 실행하고 있는 모듈 파일이 메인 (최상위) 실행 파일이야~!!! 라고 알려주는 것이다. 

    * 다른 실행 파일에서 모듈을 import 하는 경우

      ```python
      # run.py
      import calculate
      
      print(__name__)
      print(calculate.__name__)
      ```

      ```sh
      $ python run.py
      >> __main__
      >> calculate
      ```

       `run.py` 실행 파일에서 `calculate` 모듈을 import했다. 그러면 실행 파일의  `__name__` 변수의 값은   `__main__` 이 되지만 모듈의 `__name__` 값은 모듈 이름이  된다. 

  * path

    * 모듈을 import할 때 파이썬은 모듈을 어디서 찾아 올까?
      * 먼저 파이썬은 build-in module에서 같은 이름의 모듈이 있는지 탐색한다. (`sys`, `os`는 build-in module의 한 예이다.)
      * 다음으로 파이썬은 `sys.path` 경로에서 모듈을 찾는다 .`sys.path` 에 등록된 경로는 다음과 같다.
        * 실행 파일의 디렉토리의 경로
        * `PYTHONPATH` (파이썬 환경 변수)

### 4. Package

Module로 코드 재사용도 가능해졌는데 도대체 package는 왜 등장한 것인가?!   

Package는 module을 **체계화** 하고 싶은 욕심에 만들어졌다.    

체계화라는 말이 어려운데, 쉽게 말하면 module를 트리 구조로 관리하고 싶은 것이다.    

```bash
sound/                          //Top-level package
      __init__.py               //Initialize the sound package
      formats/                  //Subpackage for file format conversions
              __init__.py
              wavread.py
              wavwrite.py
              aiffread.py
              aiffwrite.py
              auread.py
              auwrite.py
              ...
      effects/                  //Subpackage for sound effects
              __init__.py
              echo.py
              surround.py
              reverse.py
              ...
      filters/                  //Subpackage for filters
              __init__.py
              equalizer.py
              vocoder.py
              karaoke.py
              ...
```

파이썬 공식 문서에서 가져온 패키지 예시다.    

sound라는 (상위) 패키지와 format, effects, filters 등의 하위 패키지가 상,하위 계층을 갖는다. 그래서 특정 하위 패키지의 모듈을 찾아서 부르기가 편해졌다!    

에코 모듈을 import하고 싶으면  `import sound.effects.echo` 처럼 `.` 으로 계층을 타고 가면 된다.    

* Package 더 알아보기 
  *  `__init__.py`
    * 파일과 디렉토리들이 package가 되려면 가장 상위 디렉토리에 init 파일이 필요하다. 파일 내용은 없어도 된다. (python3.3 버전 부터는 해당 파일이 없어도 패키지로 인식한다. 하지만 하위 버전 파이썬과 호환을 위해서는 파일을 만들어주자!)
  * absolute VS relative import 
    * [관련 포스트로 이동!](/posts/2021-03-17-path.md)

### 결론 

인간의 욕심은 좋은 것이다 😄



## Reference

* [python official document](https://docs.python.org/3/tutorial/modules.html)

