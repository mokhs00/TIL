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
