# Set (집합)

# 특징
- ```순서가 없다.```
- ```중복 허용 X```

# Set in JCF(Java Collection Framework)

JCF에서 제공하는 Set 인터페이스를 상속받아 구현된 클래스들 중 대표적인 것들은 다음과 같다.

> HashSet
- ```가장 많이 쓰는 클래스```. ```해시``` 알고리즘을 이용하여 ```검색 속도가 빠르다```.
```내부적으로 HashMap 인스턴스를 이용```하여 요소를 저장한다. (Value에는 더미 객체를 저장한다.)
```Java 
private transient HashMap<E,Object> map;

// Dummy value to associate with an Object in the backing Map
private static final Object PRESENT = new Object();

// 기본 생성자에서 HashMap 인스턴스를 생성하는 것을 볼 수 있다.
public HashSet() {
    map = new HashMap<>();
}

// add() 호출 시 파라미터로 받은 key에 더미 객체를 저장한다.
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
}
```

> TreeSet
- 데이터가 ```정렬```된 상태로 저장된다.
- ```레드-블랙 트리```로 구현되어서 add, remove 등의 동작시간이 빠르다(log N)
- ```TIP❗``` JDK 1.6부터 pollFirst(), pollLast()를 제공해서 ```이중 우선순위 큐를 구현```할 때 사용할 수 있다. 동작시간도 빠르니 우선순위 큐를 두개 이용해서 구현하는 것보다 빠르다.

> LinkedHashSet
- 저장 순서가 유지되는 HashSet이다.