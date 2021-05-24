# **[프로그래머스 | 해시 Lv.2] 위장**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42578)**

[문제 설명 이미지]

## **🤔문제 풀이 방식**


``` js
서로 다른 옷 조합의 수를 return 해야한다.
*최소 한 개의 옷은 입어야한다.

즉 집합의 수를 구하고 공집합을 제외하면 된다.

1. 카테고리 별로 옷을 정리해두고 -> Map 이용
2. (옷의 수 + 1(안 입는 경우))를 count에 곱해주고
3. 1(옷을 아무것도 안 입는 경우)을 빼준다
```

# 구현 코드

**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%EC%9C%84%EC%9E%A5.java)**

``` java 
import java.util.HashMap;
import java.util.Set;

// https://programmers.co.kr/learn/courses/30/lessons/42578
public class 위장 {    
    public int solution(String[][] clothes) {
        int answer = 1;

        HashMap<String, Integer> map = new HashMap<>();

        for (String[] clothe : clothes) {
            String category = clothe[1];
            Integer orDefault = map.getOrDefault(category, 0);
            map.put(category, orDefault + 1);
        }

        Set<String> keySet = map.keySet();
        for (String key : keySet) {
            Integer get = map.get(key);
            answer *= (get + 1);
        }
        answer--;

        return answer;
    }
}

```
## **✔제출 결과**

[제출 이미지]