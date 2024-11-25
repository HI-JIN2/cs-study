
CoroutineDispatcher
## 3-1. CoroutineDispatcher란 무엇인가?
> 코루틴을 보내는 주체

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
CoroutineDispatcher은 추상클래스임. 그래서 다양하게 구현될 수 있으므로 내부를 다 알아야할 필요는 x

## 3-4. CoroutineDispatcher 사용해 코루틴 실행하기
코루틴을 넣어 디스패처에 실행을 요청해보자
#### launch의 파라미터로 CoroutineDispatcher 사용하기
	```kotlin
	fun main() = runBlocking<Unit>{
		val dispatcher = newSingleThreadContext(name="SingleThread")
		 
		launch(context = dispatcher){
			println("[${Thread.currentTherad().name}] 실행")
		}
	}
	```
- launch(dispatcher)로 해도 됨. 인자 받는게 어차피 하나라서..는 모르겠고 첫인자라서

#### 멀티 스레드 디스패처 사용해서 코투린 실행하기

```kotlin
	fun main() = runBlocking<Unit>{
		val multiThreadDispatcher = newFixedThreadContext(nThreads =2, name="MultiThread")
		 
		launch(context = multiThreadDispatcher){
			println("[${Thread.currentTherad().name}] 실행")
		}
		launch(context = multiThreadDispatcher){
			println("[${Thread.currentTherad().name}] 실행")
		}
	}
```

- 이렇게 하면 각각 다른 스레드에서 실행이 된다.
- [MultiThread-1 @coroutine#2]
- [MultiThread-2 @coroutine#3] 

#### 부모 코루틴의 CoroutineDispathcer 사용해 자식 코루틴 실행하기
구조화를 통해서 코루틴 내부에서 새로운 코루틴을 실행할 수 있음
- 바깥쪽 코루틴 = Parent 코루틴 = 부모코루틴
- 내부에서 생성되는 새로운 코루틴 = Child 코루틴 = 자식 코루틴

구조화
1. 코루틴을 계층관계로 만듦
2. 부모 코루틴의 실행 환경을 자식 코루틴에 전달하는데도 사용
3. 자식 코루틴에 dispatcher 객체를 따로 설정해주지 않았다면, 자동으로 부모거를 사용한다.
## 3-5. 미리 정의된 CoroutineDispatcher.