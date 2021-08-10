# django ORM

장고의 QuerySet의 특징을 정리합니다.

# 기본적으로 Lazy Loading (지연 로딩)

기본적으로 Lazy Loading이다.
이는 아래 코드처럼 실제로 데이터를 사용할 때 쿼리가 발생한다는 말이다

```python

users = User.objects.all()

user_list = list(users) # 이 때 쿼리가 발생!


```

# Eager Loading (즉시 로딩) 하는 법

Lazy Loading은 데이터를 사용할 때 쿼리가 발생하기 때문에 N+1 문제가 발생할 수 있어서 이를 방지하기 위해서 내가 사용할 데이터를 미리 한 번에 가져오는 Eager Loading이 필요한 경우도 있다.

django에서는 `select_related()`와 `prefetch_related()`라는 메서드를 제공한다.

select_related() : join

prefetch_related() : 추가 쿼리 실행

# QuerySet의 구성요소

QuerySet은 1개의 쿼리와 0~N개의 쿼리로 구성되어 있다!

# QuerySet에서 NativeSQL 사용하기

QuerySet API의 raw()를 이용하면 native sql을 사용할 수 있다.

```python
Person.objects.raw('SELECT id, first_name, last_name FROM myapp_person')
```

db table명이 좀 특이한 걸 볼 수 있는데 이는
django가 해당 table을 찾는 과정에서 이름을 밑줄로 결합해 사용하기 때문에
`'앱_model'` 이런 방식으로 해당 table을 찾게되고 위 예시에서는 myapp 이라는 app의 person model과 매핑되는 table을 찾게된다.

## 쿼리 필드를 모델 필드에 매핑

django의 raw()는 쿼리의 결과 필드를 모델의 필드에 자동 매핑한다.

추가로 결과 필드의 순서는 상관없다. 그래서 다음 코드는 같은 결과를 출력한다.

```python
Person.objects.raw('SELECT id, first_name, last_name FROM myapp_person')

Person.objects.raw('SELECT last_name, first_name, id FROM myapp_person')

```

그리고 매핑하는 과정에서 이름을 이용한다.

이로 인해서 다음과 같은 코드가 가능하다.

```python
Person.objects.raw('SELECT pk AS id,
                           first AS first_name,
                           last AS last_name
                    FROM some_other_table')

```

또한 raw()에 파라미터로 `translations`를 넘겨서 다음과 같이 사용할 수도 있다.

```python
name_map = {'pk': 'id', 'first': 'first_name', 'last': 'last_name'}

Person.objects.raw('SELECT * FROM some_other_table', translations=name_map)
```

## 매개변수 전달 raw()

raw()의 params값을 넘겨서 쿼리 안에 특정 값을 매핑할 수도 있다.

```python
>>> lname = 'Kim'
>>> Person.objects.raw('SELECT * FROM myapp_person WHERE last_name = %s', [lname]

```

# Native SQL 실행하기 - QuerySet X

`django.db` 패키지 안에 `connections.cursor()`를 이용해서 QuerySet 없이 native sql을 사용할 수도 있다.

- 결과 raw의 수가 1개라면 `cursor.fetchone()`

- 여러 개라면, `cursor.fetchAll()`

```python
from django.db import connections

def my_custom_sql(self):
    with connection.cursor() as cursor:
        cursor.execute("UPDATE bar SET foo = 1 WHERE baz = %s", [self.baz])
        cursor.execute("SELECT foo FROM bar WHERE baz = %s", [self.baz])
        row = cursor.fetchone()
        # rows = cursor.fetchAll()
    return row

```

python db API에 익숙하지 않은 사람을 위해서 참고로 아래 두 코드는 같은 코드임을 적어둔다.

```python
with connection.cursor() as c:
    c.execute(...)
```

```python
c = connection.cursor()
try:
    c.execute(...)
finally:
    c.close()
```
