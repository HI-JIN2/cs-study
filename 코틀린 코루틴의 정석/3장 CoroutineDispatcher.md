
CoroutineDispatcher
## 3-1. CoroutineDispatcher란 무엇인가?
코루틴을 보내는 주체

코루틴을 어디로 보냄? 스레드로

코루틴은 일시정지가 가능한 작업(Job)이라서 스레드가 있어야 실행가능함

스레드풀 개념을 여기서도 씀

![[스크린샷 2024-11-08 오후 12.28.56.png]]
![[스크린샷 2024-11-08 오후 12.29.25.png]]

스레드가 다 차면? 일단 대기열에 넣어놓고, 남는 스레드가 있을때 스레드에 보낸다.

CoroutineDispatcher는 코루틴의 실행을 관리하는 주체
 코루틴을 작업 대기열에 적재하고, 스레드가 일할 수 있는 상태라면 코루틴을 스레드에 보낸다. 
## 3-2. 제한된 디스패처와 무제한 디스패처 
1. 제한된 디스패처
	1. 사용할 수 있는 스레드나 스레드풀이 제한됨
	2. 대부분이 이거임
	3. 입출력 작업용 / CPU 작업용 등..
2. 무제한 디스패처
	1. 사용할 수 있는 스레드나 스레드풀이 제한되지 않음
	2. 실행 요청된 코루틴이 이전 코드가 실행되던 스레드에서 계속해서 실행되도록 한다.
		-> 실행되는 스레드가 매번 달라질 수 있고, 특정 스레드로 제한되지 않아 무제한 디스패처라는 이름을 갖게 됨
	3. 11.3장에서 자세히 다룸 일단 패스~

## 3-3. 제한된 디스패처 생성하기.

- 싱글 스레드 디스패처 만들기
```kotlin
val dispatcher: CoroutineDispatcher = newSingleThreadContext(
	name = "SingleThread"
	)
```
- 멀티 스레드 디스패처 만들기
```kotlin
val multiThreadDispatcher: CoroutineDispatcher = newFixedThreadPoolContext(
	Threads = 2,
	name = "MultiThread"
)
```
- 싱글 스레드 디스패처 원형 까보면 이럼
```kotlin
public fun newSingleThreadContext (name: String): CloseableCoroutineDispatcher = newFixedThreadPoolContext (1, name)
```

newFixedThreadPoolContext 까보면 Executor 써서 스레드풀을 만듦
CoroutineDispatcher은 추상클래스임 

코루틴을 넣어 디스패처에 실행을 요청해보자

## 3-4. CoroutineDispatcher 사용해 코루틴 실행하기
- launch의 파라미터로 CoroutineDispatcher 사용하기
	```kotlin

	```


## 3-5. 미리 정의된 CoroutineDispatcher.