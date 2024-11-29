
> 코루틴으로부터 결과가 필요할때 어찌해야하나? async를 쓰면 된다!

## 5-1. async 사용해 결괏값 수신하기
`launch`의 반환값은 `job`이다
**`async`의 반환값은 `Defferred`이며, 코루틴으로부터 결괏값을 수신할 수 있다**

Deferred<T>
아! 애초에 그냥 리턴값이 존재함. 제네릭 -> 리턴타입 지정

> 비동기 자체는 값이 올수도 있음.
> 순서를 보장하지 않음


**결과값이 반환될 수 있음**

언제 반환될지 모름. 언제 실행이 완료될지 모름으로

await
- 해당 deferred가 실행완료 될 때까지 그 코루틴을 멈춤
- 결과값이 반환되면 코루틴 재개

해당 job이 완료될때까지 해당 코루틴을 멈춘다는 의미에서 join이랑 비슷~
## 5-2. Defferd는 특수한 형태의 Job이다.
" 모든 코루틴 빌더는 Job 객체를 반환한다"

```kotlin
interface Deferred : Job{
	await(): T
}
```

Job 인터페이스의 서브타입이여~ 예외는 없음

Deferred는 Job의 모든 기능(함수, 프로퍼티)를 사용 할 수 있음
join, cancelled, isActivce ... 이런것~




## 5-3. 복수의 코루틴으로 결괏값 수신하기

ㄷㄷㄷ await의 호출 시점에 따라 순차가 될 수도, 동시에 처리될 수도 있음

- 순차 처리
	```kotlin
	package chapter5.code3  
	  
	import kotlinx.coroutines.*  
	  
	fun main() = runBlocking<Unit> {  
	  val startTime = System.currentTimeMillis() // 1. 시작 시간 기록  
	  val participantDeferred1: Deferred<Array<String>> = async(Dispatchers.IO) { // 2. 플랫폼1에서 등록한 관람객 목록을 가져오는 코루틴  
	    delay(1000L)  
	    return@async arrayOf("James","Jason")  
	  }  
	  val participants1 = participantDeferred1.await() // 3. 결과가 수신 될 때까지 대기  
	  
	  val participantDeferred2: Deferred<Array<String>> = async(Dispatchers.IO) { // 4. 플랫폼2에서 등록한 관람객 목록을 가져오는 코루틴  
	    delay(1000L)  
	    return@async arrayOf("Jenny")  
	  }  
	  val participants2 = participantDeferred2.await() // 5. 결과가 수신 될 때까지 대기  
	  
	  println("[${getElapsedTime(startTime)}] 참여자 목록: ${listOf(*participants1, *participants2)}") // 6. 지난 시간 표시 및 참여자 목록을 병합해 출력  
	}  
	/*  
	// 결과:  
	[지난 시간: 2018ms] 참여자 목록: [James, Jason, Jenny]  
	*/  
	  
	fun getElapsedTime(startTime: Long): String = "지난 시간: ${System.currentTimeMillis() - startTime}ms"
	```


- 병렬 처리
	```kotlin
	package chapter5.code4  
	  
	import kotlinx.coroutines.*  
	  
	fun main() = runBlocking<Unit> {  
	  val startTime = System.currentTimeMillis() // 1. 시작 시간 기록  
	  val participantDeferred1: Deferred<Array<String>> = async(Dispatchers.IO) { // 2. 플랫폼1에서 등록한 관람객 목록을 가져오는 코루틴  
	    delay(1000L)  
	    return@async arrayOf("James","Jason")  
	  }  
	  
	  val participantDeferred2: Deferred<Array<String>> = async(Dispatchers.IO) { // 3. 플랫폼2에서 등록한 관람객 목록을 가져오는 코루틴  
	    delay(1000L)  
	    return@async arrayOf("Jenny")  
	  }  
	  
	  val participants1 = participantDeferred1.await() // 4. 결과가 수신 될 때까지 대기  
	  val participants2 = participantDeferred2.await() // 5. 결과가 수신 될 때까지 대기  
	  
	  println("[${getElapsedTime(startTime)}] 참여자 목록: ${listOf(*participants1, *participants2)}") // 6. 지난 시간 기록 및 참여자 목록 병합  
	}  
	/*  
	// 결과:  
	[지난 시간: 1018ms] 참여자 목록: [James, Jason, Jenny]  
	*/  
	  
	fun getElapsedTime(startTime: Long): String = "지난 시간: ${System.currentTimeMillis() - startTime}ms"
	```


awaitAll -> 병렬처리를 한번에


`val results: List<Array<String›> = awaitAl1(participantDeferred1, participantDeferred2)`

## 5-4. withContext

withContext로 async-await를 대체한다
deferred 객체를 만들지 않고 바로 반환 -> 메모리 절약..? 가독성? 
output은 비슷하지만, 내부 구현은 완전 다르다

- async-await: 새로운 코루틴을 생성해서 작업을 처리 -> 얘도 동기..?
- withContext: 실행 중이던 코루틴을 그대로 유지한채 코루틴의 실행환경(coroutineContext? 코루틴의 실행 스레드?)만 변경해 작업을 처리 -> 동기적으로 처리
	- context만 변경해서 코루틴을 실행 -> 코루틴이 실행하는 스레드를 변경할 수 있음.. 둘다 맞는 말


주의점
withContext는 새로운 코루틴을 만들지 않기 때문에???? 
**하나의 코루틴에서 withContext가 여러번 호출되면, 순차적으로 실행된다**

즉, 복수의 독립적인 작업이 병렬로 실행되는 상황에서 쓰면 안된다


```kotlin
package chapter5.code11  
  
import kotlinx.coroutines.*  
  
fun main() = runBlocking<Unit> {  
  val startTime = System.currentTimeMillis()  
  val helloString = withContext(Dispatchers.IO) {  
    delay(1000L)  
    return@withContext "Hello"  
  }  
  
  val worldString = withContext(Dispatchers.IO) {  
    delay(1000L)  
    return@withContext "World"  
  }  
  
  println("[${getElapsedTime(startTime)}] ${helloString} ${worldString}")  
}  
/*  
// 결과:  
[지난 시간: 2018ms] Hello World  
*/  
  
  
fun getElapsedTime(startTime: Long): String = "지난 시간: ${System.currentTimeMillis() - startTime}ms"
```

위의 경우에는 병렬적으로 실행되는걸 기대했지만 각각 실행되기 때문에 2초가 걸린다
=> 새로운 코루틴을 생성하지 않기 때문에 생기는문제임


runBlocking 함수에서 생기는 코루틴 하나만 생성되고, 쓰임.
그 한 코루틴에서 스레드만 바꿔가며 실행한다.


> 그래서 바꾸려면
> async-await로 바꾸고, await 하는 시점을, 각 코루틴이 다 실행시작을 하고 난 뒤에 하면 된다

```kotlin
package chapter5.code12  
  
import kotlinx.coroutines.*  
  
fun main() = runBlocking<Unit> {  
  val startTime = System.currentTimeMillis()  
  val helloDeferred = async(Dispatchers.IO) {  
    delay(1000L)  
    return@async "Hello"  
  }  
  
  val worldDeferred = async(Dispatchers.IO) {  
    delay(1000L)  
    return@async "World"  
  }  
  
  val results = awaitAll(helloDeferred,worldDeferred)  
  
  println("[${getElapsedTime(startTime)}] ${results[0]} ${results[1]}")  
}  
/*  
// 결과:  
[지난 시간: 1013ms] Hello World  
*/  
  
fun getElapsedTime(startTime: Long): String = "지난 시간: ${System.currentTimeMillis() - startTime}ms"
```