
- jetpack

> `layout` 파일에서 구성요소를 결합하면 활동에서 많은 UI 프레임워크 호출을 삭제할 수 있어 파일이 더욱 단순화되고 유지관리 또한 쉬워집니다. 앱 성능이 향상되며 _**메모리 누수 및 null 포인터 예외**_를 방지할 수 있습니다.

```xml
<TextView
android:text="@{viewmodel.userName}" />

```

- data 태그 내에서 뷰모델 정의

```xml
<layout xmlns:android="<http://schemas.android.com/apk/res/android>"
xmlns:app="<http://schemas.android.com/apk/res-auto>">
<data>
<variable
	name="viewmodel"
	type="com.myapp.data.ViewModel" />
</data>
<ConstraintLayout... /> <!-- UI layout's root element -->
</layout>

```

- 양방향 데이터 바인딩도 가능


---
참고자료
- [DataBinding에 ViewModel, LiveData와 함께 사용하기](https://develop-writing.tistory.com/45)
- [데이터 결합 라이브러리  |  Android 개발자  |  Android Developers](https://developer.android.com/topic/libraries/data-binding?hl=ko)
- [https://github.com/android/databinding-samples](https://github.com/android/databinding-samples)