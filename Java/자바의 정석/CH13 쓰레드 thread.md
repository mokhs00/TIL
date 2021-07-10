# 프로세스와 쓰레드

`프로세스(process)` 란 간단히 말해서 **'실행중인 프로그램(program)'**이다. 프로그램을 실행하면 OS로부터 실행에 필요한 **자원(메모리)를 할당**받아 **프로세스**가 된다.

프로세스는 프로그램을 수행하는데 필요한 데이터와 메모리 등의 자원, 쓰레드로 구성되어 있다.

프로세스의 자원을 이용해서 **실제로 작업을 수행하는 것**이 바로 `쓰레드(thread)`

거의 모든 프로세스에는 최소한 하나 이상의 쓰레드가 존재하며 둘 이상의 쓰레드를 가진 프로세스를 `멀티쓰레드 프로세스` (multi-threaded process)라고 한다.

**프로세스는 작업공간! 쓰레드는 일꾼!**

# 멀티쓰레딩

쓰레드를 여러개 사용하는 것

## 멀티쓰레딩 장점

- CPU의 사용률을 향상시킴
- 자원을 효율적으로 사용할 수 있음
- 사용자에 대한 응답성이 향상됨
- 작업이 분리되어 코드가 간결해짐

## 주의❗

처리해야 하는 쓰레드의 수는 언제나 CPU 코어(core)보다 많기 떄문에 각 코어가 아주 짧은 시간동안 여러 작업을 번갈아가며 수행함 → 이는 우리가 보기에 여러 작업들이 동시에 실행되는 것처럼 보임.

**그래서! 프로세스의 성능이 단순히 쓰레드의 개수에 비례하는 것은 아니며, 하나의 쓰레드를 가진 프로세스보다 두 개의 쓰레드를 가진 프로세스가 오히려 더 낮은 성능을 보일 수 있음!**

**또한 멀티쓰레드 프로세스는 여러 쓰레드가 같은 프로세스 내에서 자원을 공유하면서 작업해야 하기때문에 발생할 수 있는** `동기화(synchronization)`, `교착상태(deadlock)` 와 같은 문제들을 고려해야한다.

# 쓰레드의 구현과 실행

쓰레드를 구현하는 방법은 다음과 같이 2가지이다.

Thread 클래스를 상속받으면 다른 클래스를 상속받지 못해서 Runnable 인터페이스를 구현하는 것이 일반적

어떤 방법을 쓰든 run()의 몸통 부분을 구현하면 됨

- Thread 클래스를 상속

```java
class MyThread extends Thread {
        @Override
        public void run() { ... }
    }
```

- Runnable 인터페이스를 구현

```java
class MyThread implements Runnable {
        @Override
        public void run() { ... }
    }
```

### Thread 생성자

쓰레드에 이름을 정해줄 수 있음

setName도 가능

```java
Thread(Runnable target, String name)
Thread(String name)
void setName(String name)
```

### Thread.currentThread()

현재 쓰레드 가져오는 메서드

getName()으로 쓰레드 이름을 확인할 수도 있음

```java
public static native Thread currentThread(); // 현재 쓰레드 반환
String getName() // 쓰레드 이름 반환
```

### 쓰레드 실행 - Thread.start()

**쓰레드만의 호출 스택이 새로 생김! → run()은 단순히 메서드 실행 start()는 호출 스택 생성도 함**

start()실행 시 바로 실행되는 것이 아니고, 실행대기 상태에 있다가 자기 차례가 되면 실행됨.

실행 대기중인 쓰레드가 없다면 바로 실행 → `스케쥴링`

### 스케쥴링

쓰레드의 실행순서는 OS의 `스케쥴러`가 작성한 스케쥴에 의해 결정됨. 이를 `스케쥴링`이라 함

### 주의❗

**한 번 실행이 종료된 쓰레드는 다시 실행할 수 없음**

즉 하나의 쓰레드에서 start()를 한 번만 호출 가능

만약, 쓰레드 작업을 한 번 더 수행해야 한다면, 새로운 쓰레드를 생성해서 start()를 호출해야함.

**start() 두 번 이상 호출 시 IllegalThreadStateException 발생**

# 컨텍스트 스위칭(context switching)

프로세스 또는 쓰레드 간의 작업 전환을 `컨텍스트 스위칭(context switching)`이라 함

**컨텍스트 스위칭하는데 시간이 소요되는 것 때문에 때로는 멀티 쓰레드 방식이 싱글 쓰레드보다 작업시간이 길 수 있다.**

그래서 단순히 싱글 코어에서 CPU만 사용하는 계산 작업이라면, 멀티쓰레드보다 싱글쓰레드가 더 효율적이다

또한 실행 시간은 멀티쓰레드방식에서 각 쓰레드가 어떤 자원을 공유하느냐에 다르다. 서로 독립된 자원을 사용한다면 멀티쓰레드가 더 빠를 수도 있다.

# 병행(concurrent)

여러 쓰레드가 여러 작업을 동시에 진행하는 것을 `병행(concurrent)`라고 한다.

하나의 작업을 여러 쓰레드가 나눠 처리하는 것을 `병렬(parallel)`이라 한다.

# 쓰레드 그룹(thread group)

`쓰레드 그룹`을 이용해서로 관련된 쓰레드를 그룹으로 관리할 수 있다.

쓰레드 그룹은 보안상의 이유로 자신이 속한 쓰레드 그룹이나 하위 쓰레드 그룹은 변경할 수 있지만 다른 쓰레드 그룹의 쓰레드를 변경할 수 없게 하기 위해서 도입되었다고 한다.

쓰레드를 그룹에 포함시키고 싶다면 쓰레드 생성 시에 다음과 같이 하면 된다.

```jsx
Thread(ThreadGroup group, String name)
Thread(ThreadGroup group, Runnable target)
Thread(ThreadGroup group, Runnable target, String name)
Thread(ThreadGroup group, Runnable target, String name, long stackSize)
```

자바 어플리케이션이 실행되면, JVM은 main과 system이라는 쓰레드 그룹을 만들고 JVM 운영에 필요한 쓰레드들을 생성해서 이 쓰레드 그룹에 포함시킨다.

우리가 생성하는 모든 쓰레드 그룹은 main쓰레드 그룹 하위 쓰레드 그룹이되며, 쓰레드 그룹을 지정하지 않고 생성한 쓰레드는 자동적으로 main쓰레드 그룹에 속하게 된다.

# 데몬 쓰레드(daemon thread)

`데몬 쓰레드`란 데몬 쓰레드가 아닌 다른 일반 쓰레드의 작업을 돕는 **보조적인 역할을 수행하는 쓰레드**를 말한다. 그렇기에 **일반 쓰레드가 모두 종료되면 데몬 쓰레드는 강제적으로 자동 종료된다.**

데몬 쓰레드는 일반 쓰레드가 모두 종료되면 강제 종료되는 걸 제외하면 일반 쓰레드와 동일하다.

데몬 쓰레드의 예로는 `가비지 컬렉터`, `워드프로세서의 자동저장`, `화면자동갱신` 등이 있다.

## 데몬 쓰레드 생성하기

일반 쓰레드와 작성, 생성 방법이 같고 쓰레드 생성 이후에 `setDaemon(true)` 를 실행하면 된다.

**데몬 쓰레드는 무한루프와 조건문을 이용해서 실행 후 대기하고 있다가 특정 조건이 만족되는 작업을 수행하고 다시 대기하도록 작성한다.**

관련 메서드들은 다음과 같다.

```jsx
boolean isDaemon()
void setDaemon(boolean on)
```

### 데몬 쓰레드 예시

아래 예시는 3초마다 autoSave 값을 판별해서 autoSave()를 알맞게 호출하는 과정을 무한반복하는 쓰레드(t라고 하겠다)를 데몬쓰레드로 설정한 것이다. 아래 예시에서 setDaemon(true)를 호출하지 않으면 해당 프로그램을 강제 종료하기 전까지 쓰레드 t는 종료되지 않는다.

```jsx
public class DaemonThreadTest implements Runnable {
    static boolean autoSave = false;

    public static void main(String[] args) {
        Thread t = new Thread(new DaemonThreadTest());

        t.setDaemon(true); // start() 이전에 해줘야함. 안 해주면 IllegalThreadStateException 발생
        t.start();

        for (int i = 1; i <= 10; i++) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
            }
            System.out.println(i);

            if (i == 5) {
                autoSave = true;
            }
        }

        System.out.println("=======END=======");
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(3 * 1000);
            } catch (InterruptedException e) {
            }
            if (autoSave) {
                autoSave();
            }
        }
    }

    public void autoSave() {
        System.out.println("=====AUTO SAVED=====");
    }
}
```

**실행 결과는 다음과 같음**

![CH13%20%EC%93%B0%EB%A0%88%EB%93%9C%20thread/Untitled.png](CH13%20%EC%93%B0%EB%A0%88%EB%93%9C%20thread/Untitled.png)

**setDaemon(true)를 호출하지 않았을 때 결과**

![CH13%20%EC%93%B0%EB%A0%88%EB%93%9C%20thread/Untitled%201.png](CH13%20%EC%93%B0%EB%A0%88%EB%93%9C%20thread/Untitled%201.png)

# 쓰레드 실행제어

쓰레드 프로그래밍에서 동기화와 스케쥴링을 잘 하기 위해서 관련 메서드와 쓰레드 상태를 숙지하고 있어야한다.

## 관련 메서드

```jsx
static void sleep() // 지정된 시간(ms)동안 일시정지
void join() // 지정된 시간동안 쓰레드가 실행되도록 함.
						// 지정된 시간이 지나거나 작업이 종료되면,
						// join()을 호출한 쓰레드로 돌아와 실행을 계속함

void interrupt() // sleep()이나 join()에 의해 일시정지된 쓰레드를 깨워서 실행대기상태로 만듬
								 // 해당 쓰레드에서 InterruptedException을 발생함으로써 일시정지상태를 벗어나게 됨
@Deprecated(since="1.2") // deprecated됨
void stop()

@Deprecated(since="1.2")
void suspend() // 쓰레드 일시정지. resume()호출시 다시 실행대기상태로
@Deprecated(since="1.2") // deprecated됨
void resume() // suspend에 의해 일시 정지된 쓰레드를 실행대기상태로

static void yield() // 실행중에 자신에게 주어진 실행시간을 다른 쓰레드에게 양보(yield)하고 자신은 실행대기상태가 됨
```

`suspend()`, `resume()` 그리고 `stop()`은 쓰레드를 `교착상태(dead-lock)`로 만들기 쉽기 때문에 deprecated됨

## 쓰레드의 상태

`NEW` : 쓰레드가 생성되고 아직 start()가 실행되지 않은 상태

`RUNNABLE` : 실행 중 또는 실행 가능한 상태

`BLOCKED` : 동기화 블럭에 의해서 일시정지된 상태 (lock이 풀릴 때까지 대기중인 상태)

`WAITING`, `TIMED_WAITING` : 쓰레드의 작업이 종료되지는 않았지만 실행가능하지 않은(unrunnable) 일시정지 상태. `TIMED_WAITING`은 일시정지시간이 지정된 경우를 의미

`TERMINATED` : 쓰레드의 작업이 종료된 상태
