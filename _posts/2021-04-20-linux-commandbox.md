---
layout: article
title: LINUX - ING 배쉬(bash) 명령어 모음
aside:
 toc: true
tags: linux
---

## Contents 
* [String](#string)
* [Control Flow](#control-flow)

---

## String 

### string substitution 

* ${string#pattern}
	* `#pattern`은 `string`과 가장 **처음**으로 매칭되는 pattern을 찾아 제거한다. 
	* ex. `${string#*-}`는 가장 처음 등장하는 `-(dash)`를 찾아 그 앞의 모든 문자열(`*`)부터 `-(dash)`까지의 문자열을 제거한다. 
* ${string%pattern}
	* `%pattern`은 `string`가 가장 **마지막**으로 매칭되는 pattern을 찾아 제거한다. 
	* ex. `${string%.*}`는 가장 마지막에 등장하는 `.(dot)`을 찾아 그 뒤의 모든 문자열까지 포함하여 제거한다. 

```bash
f_name="2021-04-12-bert.md"
echo ${f_name#2021}
# -04-12-bert.md
echo ${f_name#*-}
# 04-12-bert.md
echo ${f_name%md}
# 2021-04-12-bert.
echo ${f_name%.*}
# 2021-04-12-bert
```

---

## Control Flow


---

## Reference


