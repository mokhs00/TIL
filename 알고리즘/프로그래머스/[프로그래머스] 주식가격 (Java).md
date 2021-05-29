# **[프로그래머스 | 스택/큐 Lv.2] 주식가격**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

<U>**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42584)**</U>

[문제 설명 이미지]

## **🤔문제 풀이 방식**
``` js
초 단위로 기록된 주식 가격이 주어짐.

가격이 떨어지지 않은 기간은 몇 초인지 return

가격이 떨어짐은 다음을 의미함

1원, 2원, 3원, 2원, 3원 이면
         3원 -> 2원으로 갈때 떨어진 것.


`스택`에 지난 가격들을 담음.
스택.peek이 다음 주식가격보다 크다면.
스택을 탐색하며 다음 주식가격보다 큰 지난 가격들을 모두 뺌
빼면서 시간 계산.

prices를 돌면서 위 과정을 진행하고

스택에 값이 남아있다면, 모두 빼면서 시간을 계산함.

유지 시간 계산 식은 다음과 같음
유지시간 = 현재시간 - 해당 시간

```
# 구현 코드

<U>**[Github](https://github.com/mokhs00/CodingTest/tree/master/src/main/java/Programmers)**</U>

``` java
import java.util.Stack;

class Solution {
    public int[] solution(int[] prices) {
        int[] answer = new int[prices.length];

        Stack<Integer> stack = new Stack<>();

        stack.add(0);

        //currentTime = 현재 시간 = 배열 인덱스
        for (int currentTime = 1; currentTime < prices.length; currentTime++) {

            Integer preTime = stack.peek();

            if (prices[preTime] > prices[currentTime]) {
                stack.pop();
                answer[preTime] = currentTime - preTime;

                while (!stack.isEmpty()) {
                    Integer peek = stack.peek();
                    if (prices[peek] > prices[currentTime]) {
                        stack.pop();
                        answer[peek] = currentTime - peek;
                    } else {
                        break;
                    }
                }
            }

            stack.add(currentTime);
        }
        int endTime = prices.length - 1;

        while (!stack.isEmpty()) {
            Integer time = stack.pop();
            answer[time] = endTime - time;
        }


        return answer;
    }

}

```

## **✔제출 결과**

[제출 이미지]
