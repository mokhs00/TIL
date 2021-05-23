# **[프로그래머스 | 해시 Lv.2] 전화번호 목록 (Java)**

# 📖문제설명

**자세한 문제 설명은 아래 링크를 참고해주세요.**

**[문제링크](https://programmers.co.kr/learn/courses/30/lessons/42577)**

[문제 설명 이미지]

# 🤔문제 풀이 방식
``` js
전화번호부에 적힌 번호 중
한 번호가 다른 번호의 접두어인 경우 false를 return
아닌 경우 true를 return

1. `startsWith`로 완전 탐색 시 `n^2` -> `시간 초과`가 발생했다.
따라서 `시간을 줄여야한다.`
그 방법은 사전적 `정렬`을 이용하는 것
배열을 미리 정렬해두면, 119 11923213 12 1234 이런 식으로 정렬되어서
바로 뒤에 문자만 확인해주면 접두어인지 판별할 수 있다.

2. `hash`, `set`를 이용한 방식
Set 자료형에 phone_book배열의 값을 모두 넣어주고,
phone_book을 탐색하면서
phone_book[i]를 j까지 substring으로 자른 값이 Set에 포함되는지 확인.
포함된다면 접두어가 존재하는 것.
`따라서 HashSet을 이용한다`
-> `HashSet.contains는 HashSet 내부적으로 Hash Table로 구현되어 있어 탐색 속도가 빠르다`
```
    
# 구현 코드

**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%EC%A0%84%ED%99%94%EB%B2%88%ED%98%B8_%EB%AA%A9%EB%A1%9D.java)**


``` java
import java.util.HashSet;
import java.util.Set;

// https://programmers.co.kr/learn/courses/30/lessons/42577?language=java
public class 전화번호_목록 {    
    public boolean solution(String[] phone_book) {
        boolean answer = true;

        Set<String> set = new HashSet<>();

        for (int i = 0; i < phone_book.length; i++) {
            set.add(phone_book[i]);
        }

        for (int i = 0; i < phone_book.length; i++) {
            for (int j = 1; j < phone_book[i].length(); j++) {
                if (set.contains(phone_book[i].substring(0, j))) {
                    return false;
                }
            }
        }


        return answer;
    }
}


```
# ✔제출 결과

[제출 이미지]