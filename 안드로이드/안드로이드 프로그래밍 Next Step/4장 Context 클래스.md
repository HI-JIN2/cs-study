 Context는 추상클래스이다.
 구현이 거의 없고 상수 정의와 추상 메서드로 이루어져있다.


![context](https://ericyang505.github.io/android/images/context.png)


Context를 상속 받는 ContextWrapper가 있고
ContextWrapper를 상속받는 친구들이 Activity, Application, Service이다.


객체 지향에서 강조하는 상속 대신 조합을 여기서도 잘 쓴다.
Activity, Application, Service 들은 ContextWrapper을 상속 받지만, 조합의 방식으로 ContextImpl에 있는 메소드를 사용한다.

싱글톤에 관련해서
getApplicationContext는 하나이다
getBaseContext는 각 ContexImpl을 리턴하므로 각각 존재한다.

