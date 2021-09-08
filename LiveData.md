# LiveData  
## LiveData란
+ 관찰 가능한 홀더 클래스로, 관찰 가능한 일반 클래스와 달리 LiveData는 수명 주기를 인식함  
+ Activity, Fragment, Service 등 다른 앱 구성요소의 수명주기를 고려함
+ 수명주기 인식을 통해 활동 수명 주기 상태에 있는 앱 구성요소 관찰자만 업데이트 함  
+ Observer 클래스로 표현되는 관찰자의 수명 주기가 STARTED or RESUMED 상태라면 활성 상태로 간주  
+ 객체의 상태가 DESTROYED로 변경될 때 관찰자를 삭제할 수 있기 때문에 Activity와 Fragment는 LiveData 객체를 안전하게 관찰할 수 있고, 수명주기가 끝나는 즉시 수신 거부되어 누수를 걱정하지 않아도 됨  

## LiveData 이점  
+ 관찰자 패턴을 따르는 LiveData는 데이터가 변경될 때 Observer객체에 알리기 때문에 코드를 통합하여 Observer객체에 UI를 업데이트할 수 있으므로 UI와 데이터 상태의 일치가 보장 됨  
+ 관찰자가 LifeCycle 객체에 결합되어 있어 수명주기가 끝나면 자동으로 삭제되기 때문에 메모리 누수가 없음  
+ 수명주기가 비활성화되면 다시 활성활될 때 돌아온 직후 최신데이터를 수신  
+ 기기회전과 같은 구성 변경이나 프래그먼트가 다시 생성되면 최신데이터를 즉시 수신  

## LiveData 객체 사용  
1. LiveData의 인스턴스를 생성(일반적으로 ViewModel 클래스 내에서 이루어 짐)  
2. onChanged()메서드를 사용해 Observer객체를 만듦(이 메서드는 LiveData가 보유한 데이터 변경시 발생하는 작업을 제어하고 일반적으로 Activity나 Fragment 같은 UI 컨트롤러에 Observer객체를 생성함)  
3. observe()메서드를 사용해 LiveData객체에 Observer객체를 연결함. 여기서 observe()메서드는 LifeCycleOwner객체를 사용하고 이렇게하면 Observer객체가 LiveData객체를 구독하여 변경사항에 관한 알림을 받음  

## LiveData 객체 관찰  
+ 대부분의 경우 onCreate()메서드가 LiveData객체 관찰을 시작하기 적합한 장소
   - onResume()메서드에서 중복 호출을 하지 않도록 하기 위함  
   - 활성 상태가 되는 즉시 표시할 수 있는 데이터가 포함되도록 하기 위함  
+ 마지막으로 활성 상태가 된 이후 값이 변경된 경우에만 업데이트를 받음  

```kotlin  
class NameActivity : AppCompatActivity() {
    private val model: NameViewModel by viewModels()
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        //nameObserver를 매개변수로 전달해 observe()를 호출하면 onChanged()가 출시호출되어 currrentName에 저장된 최신값 제공, LiveData가 currentName에 값을 설정하지 않았으면 onChanged는 호출되지 않음  
        val nameObserver = Observer<String> { newName ->
            nameTextView.text = newName
        }
        model.currentName.observe(this, nameObserver)
    }
}
```

## LiveData 객체 업데이트  
+ LiveData에는 저장된 데이터를 업데이트하는 공개적으로 사용가능한 메서드가 없음  
+ MutableLiveData 클래스는 setValue(T), postValue(T) 메서드가 공개 메서드로 LiveData에 저장된 값을 수정하려면 해당 메서드를 사용해야함  
+ 일반적으로 MutableLiveData는 ViewModel에서 사용되고 ViewModel은 변경불가능한 LiveData객체만 관찰자에게 노출함  
```kotlin  
  btn.setOnClickListener{
    val name="john"
    model.currentName.setValue(name)
  }
```
