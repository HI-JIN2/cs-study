> 코루틴 빌더 함수 runBlocking, launch

코루틴 빌더 함수의 반환은 Job 객체이다.  runBlocking, launch 이런 애들도 반환값은 job임. job으로 코루틴의 상태를 추적하고 제어하는데 사용한다.,

코루틴은 일시중지하고 나중에 이어서 실행하는게 가능함.

> job 객체를 이용해 코루틴 간 순차 처리하는 방법, 코루틴의 상태를 확인하고 조작하는 방법


## 4-1. join을 사용한 코루틴 순차 처리

코루틴을 각각 실행시키면 일반적으로 순차처리가 아니라 병렬처리가 되어버린다. 

![[스크린샷 2024-11-26 오후 12.16.18.png]]

실행중이던 job에 join()을 붙인다. 그럼면 그게 다 되기 전까지 runBlocking 자체가 중지된다. (=뒤에 거가 실행되지 않는다) 


호출순서가 중요함. join 보다 먼저 호출된건 실행되고, Join 다음에 온게 join 작업 다 되고 나서 실행되는 것임

## 4-2. joinAll을 사용한 코루틴 순차 처리
복수의 코루틴의 실행이 모두 끝날때까지 일시중지

![[스크린샷 2024-11-26 오후 2.30.51.png]]
## 4-3. CoroutineStart.LAZY사용해코루틴지연시작하기
코루틴은 지연시작이 가능하다

```kotlin
fun main() = runBlocking<Unit>{
	val starttime = System.currentTimeMillis()
	val lazyJob: Job = launch(start=CoroutineStart.LAZY){
		print("[${getElapsedTime(startTime)}] 지연실행")
	}
	delay(1000L)
	lazyJob().start()
}
```

1초 후 실행

## 4-4. 코루틴 취소하기


