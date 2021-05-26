# **[프로그래머스 | 해시 Lv.3] 베스트앨범**

## **📖문제설명**

**자세한 문제 설명은 아래 링크를 참고해주세요.**

**[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42579)**

[문제 설명 이미지]

## **🤔문제 풀이 방식**
``` js
구현 문제.
장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범으로 출시해야한다.
노래는 고유 번호로 구분,

노래를 수록하는 `정렬` 기준은 다음과 같다.

[노래를 수록하는 정렬 기준]
-` 속한 노래가 많이 재생된 장르`를 먼저 수록.
    -> `속한 노래의 총 재생 수를 세야함.`
- `장르 내에서 많이 재생된 노래`를 먼저 수록
    -> `장르 내에서 가장 많이 재생된 노래 순으로 정렬`
- `장르 내에서 재생 횟수가 같은 노래` 중에서는 `고유 번호가 낮은 노래`를 먼저 수록.
    -> `정렬 순서를 재생 횟수가 같을 때는 고유번호로 함.`

[결론]

노래 클래스(노래번호, 재생 수)를 만들고 HashMap에 장르별로 넣음.
이 때 재생 수 오름차순으로 정렬해서 넣어둠
-> 정렬을 위해 우선순위 큐 `PriorityQueue` 이용

또 다른 HashMap에 `장르 별 총 재생 수`를 담음.

이를 가지고 주어진 정렬 기준에 따라 answerList에 정렬해둔 값을 return

```
# 구현 코드

**[Github](https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Programmers/%EB%B2%A0%EC%8A%A4%ED%8A%B8%EC%95%A8%EB%B2%94.java)**

``` java
import java.util.*;

// https://programmers.co.kr/learn/courses/30/lessons/42579?language=java
public class 베스트앨범 {
    
    public int[] solution(String[] genres, int[] plays) {
        ArrayList<Integer> answerList = new ArrayList<>();
        int[] answer;


        HashMap<String, Integer> totalCount = new HashMap<>();
        HashMap<String, PriorityQueue<Song>> songs = new HashMap<>();

        for (int i = 0; i < genres.length; i++) {
            String g = genres[i];
            int p = plays[i];
            Integer totalCountOrDefault = totalCount.getOrDefault(g, 0);
            totalCount.put(g, totalCountOrDefault + p);

            PriorityQueue<Song> songsOrDefault = songs.getOrDefault(g, new PriorityQueue<Song>(new Comparator<Song>() {
                @Override
                public int compare(Song o1, Song o2) {

                    if (o1.count == o2.count) {
                        return o1.index - o2.index;
                    }
                    return o2.count - o1.count;
                }
            }));

            songsOrDefault.offer(new Song(i, p));
            songs.put(g, songsOrDefault);
        }

        // 장르 별로 순서를 기록함.
        Set<String> keySet = totalCount.keySet();

        // 장르 클래스를 만들고 totalCount 기준 오름차순 정렬
        PriorityQueue<Genre> genrePriorityQueue = new PriorityQueue<>(new Comparator<Genre>() {
            @Override
            public int compare(Genre o1, Genre o2) {
                return o2.totalCount - o1.totalCount;
            }
        });

        for (String key : keySet) {
            Integer count = totalCount.get(key);
            genrePriorityQueue.offer(new Genre(key, count));
        }

        while (!genrePriorityQueue.isEmpty()) {
            Genre genrePoll = genrePriorityQueue.poll();

            String genre = genrePoll.genre;
            PriorityQueue<Song> queue = songs.get(genre);
            if (queue.size() < 2) {
                answerList.add(queue.poll().index);
            } else {
                for (int i = 0; i < 2; i++) {
                    answerList.add(queue.poll().index);
                }
            }

        }

        answer = new int[answerList.size()];

        for (int i = 0; i < answerList.size(); i++) {
            answer[i] = answerList.get(i);
        }


        return answer;
    }

    class Genre {
        String genre;
        int totalCount;

        public Genre(String genre, int totalCount) {
            this.genre = genre;
            this.totalCount = totalCount;
        }
    }


    class Song {
        int index;
        int count;

        public Song(int index, int count) {
            this.index = index;
            this.count = count;
        }
    }


}
```

## **✔제출 결과**

[제출 이미지]
