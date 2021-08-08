---
layout: article
title: PYTHON - 파이썬 날짜 관련 라이브러리 (datetime, pandas.Timestamp)
aside:
 toc: true
tags: python
---

## Datetime
* `datetime` 모듈은 파이썬 내장 라이브러리 (python built-in library)
* date, time 정보 추출에 초점을 둔 모듈

```python 
from datetime import date, datetime, timedelta 
```

### datetime.datetime 
* `date` 모듈과 `time` 모듈의 기능을 모두 합친 모듈

##### datetime object 생성

* `__new__` 생성자로 생성

```python
bday = datetime(1996,2,27,20,5) # year, month, day, hour, minute, second, microsecond
bday

>>>datetime.datetime(1996, 2, 27, 20, 5)
```

* 생성 메소드 이용 

```python
datetime.today() # datetime.now() 와 동일 

>>>2021-03-11 16:16:18.783095
```

##### attribute 

```python
# 년, 월, 일
print(bday.year)
print(bday.month)
print(bday.day)
print(bday.hour)

>>>1996
>>>2
>>>27
>>>20
```

##### method

* 요일 출력 
	* monday == 0 ~ sunday == 6

```python
print(bday.weekday()) # Tuesday

>>> 1
```

##### timedelta obj를 이용한 날짜 연산

```python
bday - timedelta(days = 4)

>>>datetime.datetime(1996, 2, 23, 20, 5)
``` 

##### 자료형 변환 (다른 타입에서 datetime으로 변환)

* string(iso) -> datetime 

```python
datetime.fromisoformat("2021-03-05")

>>> datetime.date(2021, 3, 5, 0, 0)
```

* string(specific format) -> datetime 

```python
format_slash = "%d/%m/%y"
datetime.strptime("27/02/96", format_slash)

>>> datetime.date(1996, 2, 27, 0, 0)
```

##### 자료형 변환 (datetime에서 다른 타입으로 변환)

* datetime -> string(iso)

```python
bday.isoformat()

>>>'1996-02-27T20:05:00'
```


* datetime -> string(specific format)

```python
format_slash = "%d/%m/%y"
format_dot = "%A %d. %B %Y"
print(bday.strftime(format_slash))
print(bday.strftime(format_dot))

>>>27/02/96
>>>Tuesday 27. February 1996
```

### datetime.timedelta

* date 또는 time 사이의 시간적 간격을 계산 

```python
date.today() - timedelta(days = 365) # 오늘은 2021-03-11

>>>datetime.date(2020, 3, 11)
```


## pandas.Timestamp 

* 파이썬의 `datetime.datetime` object와 동일하게 사용되는 판다스 라이브러리의 객체이다. 판다스 자료 구조 중 시계열 데이터의 데이터 타입으로 사용된다. 

##### 생성

* 입력 파라미터로 `string`, `float`, `int`, `datetime object`를 받는다. 

```python
# string
pd.Timestamp("2021-03-11")
>>> Timestamp('2021-03-11 00:00:00')

# int
pd.Timestamp(year=2021, month=2, day=27)
>>> Timestamp('2021-02-27 00:00:00')

# datetime object 
pd.Timestamp(datetime.today())
>>> Timestamp('2021-03-11 16:55:20.716961')
```

##### 자료형 변환 (다른 타입에서 pd.Timestamp로 변환)

* pd.to_datetime()
	* 입력 파리미터로 `string`, `int/float`, `datetime object`, `Series`, `Dataframe`을 받는다. 

```python
# df의 'date' 컬럼의 값이 string일 경우, 자료형을 pd.Timestamp로 바꾸기
df['date'] = pd.to_datetime(df['date'])
```

##### 자료형 변환 (pd.Timestamp에서 다른 타입으로 변환)

* pd.to_pydatetime()
	* pd.Timestamp 자료형을 입력값으로 받아 파이썬 datetime 자료형으로 바꿔준다. 


## Reference

