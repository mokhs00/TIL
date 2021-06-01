# **[프로그래머스 | 힙(Heap) Lv.3] 디스크 컨트롤러**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

<U>**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42627)**</U>

[문제 설명 이미지]

## **🤔문제 풀이 방식**
``` js
'작업의 요청부터 종료까지 걸린 시간의 평균'을 가장 줄이는 방법으로 처리하면
평균이 얼마가 되는지 return('*소수점 이하는 버림')하는 문제.


'작업의 요청부터 종료까지 걸린 시간을 평균을 줄이는 방법' :
    `현재 시간`을 기준으로 `이전에 요청된 작업들` 중 `가장 빨리 끝나는 작업(비용이 가장 적은)을 선택해서 실행한다.`



'다음과 같이 풀이 방향을 설정한다.'

편의를 위해 `Job이라는` 클래스는 만들고 `request(요청 시간), cost(비용)`을 멤버변수로 한다.

그리고 현재 시간을 기준으로 `이전에 요청된 작업들`과 `아직 요청되지 않은 작업들`을 구분하기 위해 아래와 같이 `2개의 PriorityQueue`를 만든다.
`readyQueue`는 작업의 `요청 시간 순으로 정렬`하는 PriorityQueue. 말 그대로 이후에 요청될, 즉. 대기하고 있는 작업들을 큐에 담는다.
`runningQueue`는 `현재시간을 기준으로 이전에 요청된 작업들을 담는다.`

이걸 구현하면 아래와 같다.

```
# 구현 코드

<U>**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%EB%94%94%EC%8A%A4%ED%81%AC_%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC.java)**</U>

``` java
package Programmers;

import java.util.Comparator;
import java.util.PriorityQueue;

// https://programmers.co.kr/learn/courses/30/lessons/42627?language=java
class 디스크_컨트롤러{
    

    public int solution(int[][] jobs) {
        int answer = 0;


        PriorityQueue<Job> readyQueue = new PriorityQueue<>(new Comparator<Job>() {
            @Override
            public int compare(Job o1, Job o2) {
                return o1.request - o2.request;
            }
        });

        PriorityQueue<Job> runningQueue = new PriorityQueue<>(new Comparator<Job>() {
            @Override
            public int compare(Job o1, Job o2) {
                return o1.cost - o2.cost;
            }
        });

        for (int i = 0; i < jobs.length; i++) {
            int request = jobs[i][0];
            int cost = jobs[i][1];

            readyQueue.offer(new Job(request, cost));
        }


        int currentTime = 0;
        while (!readyQueue.isEmpty()) {

            while (!readyQueue.isEmpty() && readyQueue.peek().request <= currentTime) {
                Job poll = readyQueue.poll();
                runningQueue.offer(poll);
            }

            if (!runningQueue.isEmpty()) {
                Job runningPoll = runningQueue.poll();

                currentTime += runningPoll.cost;

                answer += currentTime - runningPoll.request;
            } else {
                currentTime++;
            }

        }


        // runningQueue에 작업이 남아있을 수 있다. 이를 모두 완료한다.
        while (!runningQueue.isEmpty()) {
            Job runningPoll = runningQueue.poll();

            currentTime += runningPoll.cost;

            answer += currentTime - runningPoll.request;
        }

        // 평균
        answer /= jobs.length;
        return answer;
    }

    class Job {
        int request;
        int cost;

        public Job(int request, int cost) {
            this.request = request;
            this.cost = cost;
        }

        @Override
        public String toString() {
            return "Job{" +
                    "request=" + request +
                    ", cost=" + cost +
                    '}';
        }
    }
}


```

## **✔제출 결과**

[제출 이미지]
