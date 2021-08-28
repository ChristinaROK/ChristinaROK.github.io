---
layout: article
title: PYTHON - jupyter notebook 커널에 새로운 가상 환경 설치 및 삭제 방법
aside:
 toc: true
tags: python

---

## Jupyter kernel에 특정 conda 가상 환경을 설치하고 삭제하는 방법

### 1. jupyter에 kernel 설치 

우선 새로운 가상 환경을 만든다. 

```sh
conda create --name my_env # 새로운 가상 환경 생성
conda activate my_env # 새로 만든 가상 환경을 실행
```

그 다음으로는 juypter에 새로 만든 가상 환경 kernel을 생성 한다.

```sh
conda install -c anaconda ipykernel # ipykernel 패키지 설치 
python -m ipykernel install --user --name=my_env # ipykernel에 새로운 가상 환경 등록
```

이 때 `--name` 옵션은 jupyter notebook에서 해당 가상 환경을 어떤 이름으로 표시(display) 할 것인지를 적는 것이다. 따라서 꼭 가상 환경 이름과 같을 필요는 없다.    



이제 주피터 노트북을 접속해 new 탭을 클릭하면 "my_env" kernel이 새로 생성된 것을 볼 수 있다.    

주피터 노트북에서 현재 내가 접속한 가상 환경이 어떤 것인지 알고 싶을 땐 다음과 같이 입력한다. 

```python
import sys
print(sys.executable)
# "/home/.../anaconda3/envs/my_env/bin/python" 
```

현재 my_env 가상 환경에서 파이썬이 실행 중임을 알 수 있다. 

### 2. jupyter에서 kernel 삭제

아주 간단하다. 터미널에서 다음을 입력한다. 

```sh
jupyter kernelspec uninstall my_env
```

주피터 노트북을 새로 고침하면 "my_env" kernel이 사라진 것을 확인할 수 있다. 



## Reference 

1. [stack overflow 1](https://stackoverflow.com/questions/42635310/remove-kernel-on-jupyter-notebook)