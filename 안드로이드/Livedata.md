
- jetpack
- 관찰가능한 데이터 홀더 클래스
- 수명주기를 인식한다 → 수명 주기 상태에 있는 구성 요소만 업데이트를 한다! `onStart`, `onResume`
`onDestroyed` 일 때는 관찰자 삭제

⇒ 메모리 누수 걱정이 없다!

- 이점
	- UI와 데이터 상태의 일치 보장
	- 중지된 활동으로 인한 비정상 종료 없음
	- 메모리 누수 없음
	- 수명주기 자동으로 처리
	- 최신 상태의 데이터로 유지
	- 리소스 공유

- 사용법
	1. 보통 `LiveData`는 `ViewModel` 클래스에서 만들어진다.
	2. `activity/fragment`에서 `Observer` 객체를 만든다. 객체 안에 `onChanged()` 메서드를 정의한다.
	3. `observe()` 메소드를 사용해서 `LiveData` 객체에 `Observer` 객체를 연결한다. `observe()` 메소드는 `LifecycleOwner` 객체를 사용. `Observer` 가 `LiveData` 객체를 구독하여 변경사항에 대해 인지한다.

- `LiveData` 객체 observe
	- `onCreate`에서 observe하기 좋음
	- 이유
		- `onResume`에서 중복 호출하지 않기 위해서
		- `activity/fragment` 에서 즉시 표시되는 데이터가 포함되게 하기 위해서.
	
- `LiveData` 객체 업데이트
	- 수정가능한 `MutableLiveData` 의 `setValue()`(기본스레드)나 `postValue()`(백드라운드 스레드)를 사용해서 수정한다.
	- `MutableLiveData`
		- get() 그때마다 받아오고
		- = 연산자는 객체복사
		- 얘만 프라이빗→ 뷰모델에서만 건들일 수 있겠끔~
	- `LiveData`
		- **메인 스레드**(UI 스레드) 그래서 과부화
		- 클린 아키텍쳐가 깨짐
		- 그래서 flow를 씀 (쓰레드 가능)
		- 그렇게 되면 observer는 `onChange()` 메서드를 호출한다.


+) transformation map

단점: 라이브데이터는 안드로이드 플랫폼에 종속되어 잇음. 클린아키텍쳐에서는 도메인 레이어는




참고 자료
- https://velog.io/@changhee09/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-LiveData
- [LiveData 개요  |  Android 개발자  |  Android Developers](https://developer.android.com/topic/libraries/architecture/livedata?hl=ko)