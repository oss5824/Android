# biniding
## ViewBinding  
+ 뷰 바인딩은 Activity나 Fragment와 같은 뷰와 상호 작용할 때 보다 쉽게 코드를 작성할 수 있도록 지원  
+ 간단하게 뷰와 상호작용할 때 사용하는 findViewById를 사용하지 않고 뷰 컴포넌트를 접근할 수 있도록 도와줌  
+ Type-Safe(레이아웃 내에서 정확한 view 타입을 찾아 맵핑함)  
+ Null-Safe(레이아웃에 없는 id를 findViewById했을 때의 NullPointerException 방지)
+ ViewBinding을 사용하기 위해서는 build.gradle 파일에 viewBinding요소를 추가해줘야 함  
+ Activity에서의 ViewBinding 사용  
   - 뷰 바인딩을 설정하면 xml파일과 연동되는 바인딩 클래스를 자동으로 생성해주고, 뷰바인딩 클래스 이름은 연동되어있는 해당 Activity이름을 반전시킨 이름으로 생성 됨  
   - ex) MainActivity->ActivityMainBinding  
```kotlin
  val activityMainBinding: ActivityMainBinding=ActivityMainBinding.inflate(layoutInflater)
  setContentView(activityMainBinding.root)
  //컴포넌트 접근방법
  activityMainBinding.textView.text="hello!"
```  
+ Fragment에서 ViewBinding 사용  
  - HomeFragment가 있다면 클래스에서 뷰바인딩 클래스 이름은 FragmentHomeBinding.  
```kotlin
  class HomeFragment: Fragment(){

    //추후에 뷰가 사라질 때(메모리에서 삭제) 뷰 바인딩 객체도 같이 날리기 위해 전체 fragment에서 변수를 생성해줌
    private var fragmentHomeBinding: FragmentHomeBinding?=null
    
    //뷰가 생성되었을 때 프레그먼트와 레이아웃을 연결
     override fun onCreateView(inflater: LayoutInflater,
        container: ViewGroup?,savedInstanceState: Bundle?
    ): View?{
      val binding = FragmentHomeBinding.inflate(inflater,container,false)
      fragmentHomeBinding = binding
      return fragmentHomeBinding!!.root
    }
  }
```

## DataBinding  
+ DataBinding은 간단하게 xml파일에 Data를 연결해서 사용할 수 있게 도와주며 Android JetPack라이브러리의 하나의 기능  
+ 애플리케이션 로직과 레이아웃을 binding하는데 필요한 과정을 최소화  
+ 보통 MVVM 패턴을 구현할 때 LiveData와 함께 거의 필수적으로 사용함
+ DataBinding의 사용법  
  - gradle의 dataBinding의 enabled true로 설정  
  - 최상단 루트를 layout태그로 감싸줌  
  - 이벤트를 만들 때 참조할 데이터바인딩 변수명이 필요하기 때문에 data, variable태그를 추가하고 name에는 변수명을 type에는 데이터 바인딩을 통한 이벤트를 세팅할 (패키지명 + 액티비티명 or 프래그먼트 명)을 적어줌  
  - data 태그 내에선 import도 가능하고 variable도 만들 수 있음  
  - 레이아웃 내에서 표현식을 사용하기 위해서는 '@{}' 구문을 사용  
```xml
  <layout>
    <data>
      <variable
        name="main"
        type="com.example.MainActivity"/>
    </data>

    <LinearLayout>
      <TextView
            android:text='@{test.text}' 
            android:visibility='@{test.isClicked == true ? View.GONE : View.VISIBLE}' />
    </LinearLayout>
  </layout>
```

```kotlin
class MainActivity: AppCompatActivity(){
  private lateinit var binding: ActivityMainBinding
  var text="testVariable"
  var isClicked=false

  override fun onCreate(savedInstanceState: Bundle?){
    super.onCreate(savedInstanceState)
    binding=DataBindingUtil.setContentView(this,R.layout.activity_main)

    binding.main=this

    binding.btnChange.setOnClickListener{
      isClicked=!isClicked

      //Data가 변동되면 binding된 View들에 Data변화를 알려줌
      binding.invalidateAll()
    }
  }
}
```
