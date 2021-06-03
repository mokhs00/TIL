# **[프로그래머스 | 정렬 Lv.1] K번째수**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

<U>**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42748)**</U>

[문제 설명 이미지]

## **🤔문제 풀이 방식**

```js
    구현문제.
    '기존 배열은 불변해야하니 주의'

    기존 배열에서 필요한 만큼만 복사해오고,
    정렬은 Arrays.sort()를 이용한다.
```

# 구현 코드

<U>**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/K%EB%B2%88%EC%A7%B8%EC%88%98.java)**</U>

```java
import java.util.Arrays;

class Solution {
    public int[] solution(int[] array, int[][] commands) {
        int[] answer = new int[commands.length];
        int answerIndex = 0;

        for (int[] command : commands) {
            int i = command[0];
            int j = command[1];
            int k = command[2];
            int[] temp = new int[j - i + 1];

            for (int q = 0; q < temp.length; q++) {
                temp[q] = array[i - 1];
                i++;
            }

            Arrays.sort(temp);

            answer[answerIndex++] = temp[k - 1];
        }
        return answer;
    }
}
```

## **✔제출 결과**

[제출 이미지]
