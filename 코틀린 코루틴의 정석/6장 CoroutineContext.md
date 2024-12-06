context 자리에 CoroutineName이랑 CoroutineDispatcher을 썼었음. 얘네가 CoroutinceContext 구성요소다

----
> 이전에, Context가 뭔지 부터 알아보자
> https://charlezz.com/?p=1080
> 안드로이드에서 context는 **애플리케이션 환경에 대한 전역적인 정보**이다. 구현은 안드로이드 시스템에 의해 제공되는 추상클래스.
> context를 이용함으로써 시스템 레벨의 정보를 얻을 수 있는 메소드를 쓸 수 있음
> activity context는 application context를 상속받음.
> 대부분의 UI 작업은 activity context를 이용. (application context로 호출하면 에러남) 


CoroutineContext = 코루틴의 실행 환경을 설정한다.

구성요소
1. CoroutineName
2. CoroutineDispatcher
3. Job
4. CoroutineExceptionHandler


각 구성요소가 key-값으로 구성됨

근데 키로 대입하는게 아니라 + 연산자를 씀

바꿀려면 다시 + 하면됨

