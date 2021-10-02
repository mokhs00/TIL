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


