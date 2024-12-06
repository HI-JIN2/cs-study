context 자리에 CoroutineName이랑 CoroutineDispatcher을 썼었음. 얘네가 CoroutinceContext 구성요소다

----
> 이전에, Context가 뭔지 부터 알아보자
> https://charlezz.com/?p=1080
> 안드로이드에서 context는 **애플리케이션 환경에 대한 전역적인 정보**이다. 구현은 안드로이드 시스템에 의해 제공되는 추상클래스.
> context를 이용함으로써 시스템 레벨의 정보를 얻을 수 있는 메소드를 쓸 수 있음
> activity context는 application context를 상속받음.
> 대부분의 UI 작업은 activity context를 이용. (application context로 호출하면 에러남) 


==CoroutineContext = 코루틴의 실행 환경을 설정한다.==

- 구성요소
1. CoroutineName
2. CoroutineDispatcher
3. Job
4. CoroutineExceptionHandler

	
	각 구성요소가 key-값으로 구성됨.
	 동일한 키에 중복값 허용x
	 
- 대입
	근데 키로 대입하는게 아니라 + 연산자를 씀.
	조합하는 느낌.
	동일키에 또 추가되면 나중에 추가된게 덮어씌워짐.
	
	바꿀려면 다시 + 하면됨

---
- 구성요소에 접근하려면 고유한 키가 필요하다.
	`.Key`
	
	`[coroutineName]`이나 `[coroutineName.Key]`로 하면 구성요소 중 coroutineName에 대한 정보를 가져올 수 있다.


- 제거하기
	`minusKey()` coroutineContext에서 해당요소만 제거되어서 된다.
	 -> 유의할 점: CoroutineContext 객체가 그대로 유지되고, **해당 요소가 제거된 새로운 CoroutineContext 객체가 반환됨!!** 그래서 이전 객체에는 남아있음. 새로 반환 객체를 써야함.




