- [개요](#개요)

# 개요
django로 개발하면서 공부한 내용을 기록합니다.


# django.urls utility functions

## reverse()
  - views에 기록한 name 또는 함수로 url패턴을 찾아온다.
  - args를 추가할 수도 있다.
  ``` python
  from django.urls import reverse

  reverse(views.boards)

  reverse('boards')

  # 이런 식의 활용도 가능
  return redirect(reverse('boards'))

  # args 추가
  reverse('board', args=[1])
  ```



# django utils.dateparse

## parse_datetime

- datetime 형식을 문자열로 파싱해준다

``` python 
>>> from django.utils.dateparse import parse_datetime
>>> parse_date('2021-10-02')
datetime.date(2021, 10, 2)
```

## 기본에 충실하자 : python의 dateutil.parser.parse()

``` python
>>> from dateutil.parser import parse
>>> parse('2020-10-10')
datetime.datetime(2020, 10, 10, 0, 0)
>>> parse('2020-10-10').date()
datetime.date(2020, 10, 10)

```