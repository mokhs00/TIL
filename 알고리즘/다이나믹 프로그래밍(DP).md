# 다이나믹 프로그래밍(DP) : Dynamic Programming

# 개요
- 큰 문제를 작은 여러 개의 문제로 나누어서 푸는 기법
- 하나의 문제를 한 번만 푼다(중복 제거)
- 보통 부분적으로 특정한 패턴이 반복되는 상황이나
- 중복 계산을 줄여 최적화를 해야하는 상황에 사용된다.
- 예시로 재귀를 이용해 피보나치 수열의 N번째 값을 구하는 경우를 들 수 있다.

# 중요
- 점화식을 찾아 이를 구현하는 것이 포인트!
- 경우의 수를 나열하면서 감을 잡는 게 중요하다.
- 많은 DP문제를 풀어보면서 감을 익히자



# 구현

- DP의 대표적인 사례인 피보나치 수열의 N번째 값을 구하는 경우를 구현해보자

1. 점화식을 찾고 초기 값을 dp[]에 넣어준다.
2. 피보나치 수열의 점화식은 (dp[n] = dp[n-1] + dp[n-2])이다.
3. 기본값으로 dp[0] = 1과 dp[1] = 1을 넣어준다.
4. dp[2]부터 반복문을 돌려 원하는 값을 찾아낸다.

# 구현 코드

https://github.com/mokhs00/CodingTest/blob/master/src/main/java/Algorithm/DP_%ED%94%BC%EB%B3%B4%EB%82%98%EC%B9%98%EC%88%98%EC%97%B4.java