# **[프로그래머스 | 스택/큐 Lv.2] 기능개발**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42586?language=java)**

[문제 설명 이미지]

## **🤔문제 풀이 방식**
``` js
진도가 100%일때 서비스에 반영.
뒤에 있는 기능이 100%를 넘었더라도,
앞에 있는 기능이 100%를 넘지 않았다면
앞에 있는 기능이 배포될 때 함께 배포됨.

`각 배포마다 완료되는 기능의 수`를 return
-> 먼저 배포되어야 하는 기능이 100% 일때 그 다음 작업들을 탐색하며 100%인 경우 같이 배포 됨.

- 모든 작업을 Queue에 넣고
- 현재 시간을 나타내는 변수 time을 둔다.
- time을 증가시키면서 현재 진행도(진행도(p)  + (작업속도(s) + 현재시간(time)))가 100%를 넘었다면, 
- 해당 작업을 queue에서 빼준다.
- time은 1씩 증가시키는 것보다 효율적인 방법이 있었다.
- `현재 작업을 완료하는데 필요한 시간`만큼 time을 증가시키는 방법이다. 식은 다음과 같다
- (100 - 진행도(p)) / 속도(s) = `현재 작업을 완료하는데 필요한 시간`
```
# 구현 코드

**[Github](https://github.com/mokhs00/CodingTest/tree/master/src/main/java)**

``` java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    
    public int[] solution(int[] progresses, int[] speeds) {
        int[] answer;
        ArrayList<Integer> answerList = new ArrayList<>();


        Queue<Job> progressesQueue = new LinkedList<>();

        for (int i = 0; i < progresses.length; i++) {
            progressesQueue.offer(new Job(progresses[i], speeds[i]));
        }

        int time = 0;
        // 해당 시간에 완료된 작업 수
        int count = 0;
        while (!progressesQueue.isEmpty()) {
            Job poll = progressesQueue.poll();

            // 진행도
            int p = poll.progress;
            // 작업 속도
            int s = poll.speed;


            if (p + (s * time) < 100) {
                // count가 있다면 이미 완료된 작업들이 있으므로 answerList에 count를 추가하고
                // count를 0으로 초기화
                if (count > 0) {
                    answerList.add(count);
                }
                count = 0;

                // 현재 작업이 완료될 때까지 time을 진행하고 완료 판정
                time = (int) Math.ceil((double) (100 - p) / s);

                count++;
            } else {
                count++;
                continue;
            }

        }

        // 마지막 작업을 완료한 후에도 count 추가.
        answerList.add(count);

        answer = new int[answerList.size()];
        for (int i = 0; i < answerList.size(); i++) {
            answer[i] = answerList.get(i);
        }
        return answer;
    }

    class Job {
        int progress;
        int speed;

        public Job(int progress, int speed) {
            this.progress = progress;
            this.speed = speed;
        }
    }
}


```

## **✔제출 결과**

[제출 이미지]