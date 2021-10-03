# MVP
+ 상호 의존성을 떨어뜨려 결합도를 낮출 수 있음

## Model
+ DB나 REST API등의 데이터와 관련된 처리역할을 하는 부분
## View
+ Activity와 Frgment처럼 View를 담당하는 사용자 인터페이스 역할을 하는 부분
## Presenter
+ View에서 이벤트를 받아와 데이터를 가공해 View에 전달하는 역할을 하는 부분

## 처리순서
1. View에서 클릭이벤트와 같은 이벤트가 발생하면 Presenter로 전달
2. Presenter가 Model에 data를 요청
3. Model이 Presenter로 요청한 데이터를 전달
4. Presenter는 Model에서 전달 받은 데이터를 정제하고 View에 전달
5. View는 전달받은 데이터를 사용자에게 보여줌

## 사용예제
+ BaseView
```kotlin
    interface BaseView{
        fun displayError(err: String)
    }
```
+ BasePresenter
```kotlin
    interface BasePresetner<T>{
        fun createView(view: T)
        fun destroyView()
    }
```
+ BaseActivity
```kotlin
    abstract class BaseActivity: AppCompatActivity(){
        override fun onCreate(savedInstanceState: Bundle?){
            super.onCreate(savedInstanceState)
            initPresenter()
        }
        // View와 상호작용할 Presenter주입을 위해
        abstract fun initPresenter()
    }
```
+ PersonData(Model)
```kotlin
    object PersonListData{
        fun getPersonListData():List<Person>{
            //통신이나 DB를 통해서 가져오기
            return listOf(Person())
        }
    }
```
+ Contract
    - View와 Presenter가 구현해야할 인터페이스 정의
```kotlin
    interface PersonContract{
        interface View:BaseView{
            fun disPlayPersonList(list: List<Person>)
            //presenter에서 정제한 personList를 전달받음
        }
        interface Presenter: BasePresenter<View>{
            fun getPersonList()
            //Model에서 data를 받아옴
        }
    }
```
+ Presenter
    - View와 1:1관계를 유지
```kotlin
    class PersonPresenter: PersonContract.Presenter{
        var personView: PersonContract.View?=null
        override fun createView(view: PersonContract.View){
            personView=view
        }
        override fun getPersonList(){
            val list = PersonListData.getPersonListData()
            personView?.displayPersonList(list)
        }
        override fun destroyView(){
            personView=null
        }
    }
```

+ View
    - 사용자 인터페이스 처리
```kotlin
    class PersonActivity: BaseActivity(),PersonContract.View{
        lateinit var personPresenter: PersonPresenter
        override fun onCreate(savedInstanceState: Bundle?){
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_person)
            personPresenter.createView(this)
            initView()
        }
        fun initView(){
           btn.setOnClickListener{
               personPresenter.getPersonList()
           } 
        }
        override fun displayPersonList(list: List<Person>){
            personNameText.text="${list[0].name}"
            personAgeText.text="${list[0].age}"
        }
        override fun onDestory(){
            super.onDestroy()
            personPresenter.destoryView()
        }
        override fun initPresenter(){
            personPresenter=PersonPresenter()
        }
        override fun displayError(err: String){
            //에러 헨들링
        }
    }
```
