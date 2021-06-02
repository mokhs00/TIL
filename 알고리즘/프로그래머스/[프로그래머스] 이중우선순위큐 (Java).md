# **[프로그래머스 | 힙(Heap) Lv.2] 이중우선순위큐**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

<U>**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42628?language=java)**</U>

[문제 설명 이미지]

## **🤔문제 풀이 방식**

```js

이중 우선 순위 큐.
방법 1:
`ArrayList`를 이용해 매번 정렬을 수행하여 구현한다.
-> `이 문제에서는 정답처리 되지만, 매번 정렬하기 때문에 오래걸림`

방법 2:
`MaxHeap, MinHeap을 이용해 구현한다.`
(MaxHeap or MinHeap)에서 poll한 값을 (MinHeap or MaxHeap)에서 remove하는 방식
-> remove에서 순차적 접근으로 인한 시간 O(N)이 소요

-> 더 줄이는 방법은?

방법 3:
`TreeMap`을 이용한다.
TreeMap은 이진 트리 기반으로 key값이 오름차순으로 정렬된 형태로 저장된다.
따라서 firstKey()와 lastKey()는 O(logN)이 소요됨. remove()도 O(logN)


나머지는 구현 문제이다.
TreeMap으로 요구사항을 구현하면 된다.

```

# 구현 코드

<U>**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%EC%9D%B4%EC%A4%91_%EC%9A%B0%EC%84%A0%EC%88%9C%EC%9C%84_%ED%81%90.java)**</U>

```java

import java.util.TreeMap;

public class 이중_우선순위_큐 {

    public int[] solution(String[] operations) {
        int[] answer = new int[2];
        TreeMap<Integer, Integer> map = new TreeMap<>();

        for (int i = 0; i < operations.length; i++) {
            String[] s = operations[i].split(" ");
            String s1 = s[0];
            String s2 = s[1];

            if (s1.equals("I")) {
                int key = Integer.parseInt(s2);
                Integer orDefault = map.getOrDefault(key, 0);

                map.put(key, orDefault + 1);
            }

            if (s1.equals("D")) {

                if (map.isEmpty()) {
                    continue;
                }

                if (s2.equals("1")) {
                    Integer lastKey = map.lastKey();
                    Integer max = map.get(lastKey);
                    if (max == 1) {
                        map.remove(lastKey);
                    } else {
                        map.put(lastKey, max - 1);
                    }
                }

                if (s2.equals("-1")) {
                    Integer firstKey = map.firstKey();
                    Integer max = map.get(firstKey);
                    if (max == 1) {
                        map.remove(firstKey);
                    } else {
                        map.put(firstKey, max - 1);
                    }


                }
            }
        }

        if (map.isEmpty()) {
            answer[0] = 0;
            answer[1] = 0;
        } else {
            answer[0] = map.lastKey();
            answer[1] = map.firstKey();
        }


        return answer;
    }
}
```

## **✔제출 결과**

[제출 이미지]
