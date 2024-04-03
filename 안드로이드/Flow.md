
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


- onStart
- onCompletion
- catch
- collectLatest