
```kotlin
viewModelScope.launch {  
	getUserInfoUseCase().onStart {  
		_uiState.update { it.copy(loading = true) }  
	}.onCompletion {  
		_uiState.update { it.copy(loading = false, error = true) }  
	}.catch { e ->  
		_uiState.update {  it.copy(  error = true,  toastMessage = App.appContext.getString(R.string.not_found)  )  
	}  
	Timber.e(e.toString())  
	}.collectLatest { result ->  
		Timber.d(result.toString())  
		result.result?.apply {  
		if (this.nickname.isNullOrBlank()) {  
			_uiState.update {  it.copy(  isNicknameNull = true,  toastMessage = App.appContext.getString(R.string.set_nickname)  )  }  
		} else {  
			_uiState.update {  it.copy(  isNicknameNull = false,  toastMessage = (String.format(App.appContext.getString(R.string.hello_user),this.nickname)))  
			}  
		}  
	}  
}
```


- onStart: Flow가 수집되기 전에 지정된 action을 먼저 수행하는 Flow를 반환
- onCompletion:  Flow가 완료,취소 또는 예외 발생시에 지정된 action을 호출 하는 Flow를 반환
- catch: 예외 처리

```
fun some() {
  viewModelScope.launch {
    try {
      // call suspend function
    } catch(e: Exception) {}
  }
}
```

- collectLatest: 항상 최신의 정보를 수집
	- collectLatest vs collect
		- 새로운 데이터가 들어왔을 때 어떻게 처리하냐의 차이
		- collect: 이전 데이터의 처리가 끝난 후에 새로운 데이터를 처리함
		- collectLatest: 이전 데이터의 처리를 강제 종료시키고 새로운 데이터를 처리함

> 참고자료
> - https://velog.io/@devoks/Kotlin-Flow
> - https://kotlinworld.com/252