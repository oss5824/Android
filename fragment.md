# Fragment
+ 많은 앱들은 여러가지 이유로 single activity application을 지향
+ 따라서 Fragment로 UI를 구성하는 경우가 많음
## 생명주기 설명
+ onCreate()
    - FragmentManager에 add되었을 때 도달하고 onCreate()콜백함수를 호출
    - onCreate()이전에 onAttach()가 먼저 호출 되니 기억해야함
    - 이 시점에는 아직 Fragment View가 생성되지 않으니 View와 관련된 작업을 두기 적절치 않음
+ onCreateView(),onViewCreated() 
    - onCreateView()의 반환 값으로 정상적인 Fragment View객체를 제공했을 때 Fragment View의 LifeCycle이 생성 됨
    - onCreatView에서 반환된 View 객체는 onViewCreated()의 파라미터로 전달되고 이 때부터 View의 초기값을 설정해주거나 LiveData 옵저빙, Adapter 세팅 등을 해주는 것이 적절
+ onStart()
    - Fragment가 사용자에게 보여질 수 있을 때 호출
    - 이 때부터는 Fragment의 childFragmentManager를 통해 FragmentTransaction을 안전하게 수행할 수 있음
+ onResume()
    - Fragment가 보이는 상태에서 모든 animator와 transition효과들이 종료
    - 사용자와 상호작용할 수 있을 때 onResume콜백이 호출, 즉 onResume이 호출되지 않은 시점에서는 입력을 시도하거나 포커스를 설정하는 등의 작업을 임의로 하면 안됨
+ onPause()
    - Fragment를 떠나기 시작했지만 여전히 visible일 때
+ onStop()
    - Fragment가 아예 화면에 보이지 않을 때
+ onDestroyView()
    - 모든 exit animation과 transition이 완료되고 Fragment가 화면으로부터 벗어났을 때
    - 해당 시점에서 가비지 컬렉터에 의해 수거될 수 있도록 Fragment View에 대한 참조가 모두 제거되야 함
+ onDestroy()
    - Fragment가 제거되거나 FragmentManager가 destory됐을 경우
    - onAttach가 onCreate 이전에 호출 됐던 것처럼 onDetach또한 onDestroy이후에 호출 됨
## FragmentManager
1. add()
   + add(R.id.xx,fragment)
   + 프래그먼트를 중첩해서 화면에 보여줌
   + 뒤로가기 클릭 시 단순 add함수만으로는 스택에 쌓이지 않기 때문에 부모 액티비티가 제거되면서 모든 프래그먼트들이 onDestroy 됨
2. add() + addToBackStack()
   + add(R.id.xx,fragment).addToBackStack(null)
   + add()와 동일하게 프래그먼트를 중첩해서 보여줌
   + 하지만 뒤로가기 클릭시 addToBackStack()을 추가하면 스택에 쌓이므로 뒤로가기 시 스택에 있는 프래그먼트들이 순서대로 onDestroy() 됨
3. replace()
   + 기존의 생성된 프래그먼트들은 모두 onDestroy되며 새로운 프래그먼트 1개만 화면에 보여줌
   + 스택에 프래그먼트들이 쌓이지 않아 뒤로가기 시 부모 Activity와 함께 생성된 프래그먼트 모두 onDestroy 됨
4. replace() + addToBackStack()
   + 화면에 프래그먼트 1개만을 보여주고 중첨되어 보여지지는 않지만 프래그먼트는 stack에 계속 쌓임
   + 새로운 프래그먼트가 replace될 때 이전에 생성된 프래그먼트는 onDestroyView되고 뒤로가기를 하게 되면 이전에 생성된 fragment들이 순서대로 onCreateView를 실행하며 재사용 됨

* 주의사항
    + 프래그먼트를 사용할 때는 항상 프로세스가 중단(시스템에 의한 중단) 되었을 때를 고려해 작업해주어야 함
   
## FragmentFactory

Fragment를 만들 때 보통 아래처럼 만들지만 종종 생성하는 쪽에서 데이터를 인자로 넘겨줘야 하는 경우가 있다.
```kotlin
    val fragment = BookFragment()
```
하지만 프래그먼트를 상속 받을 땐 인자가 없는 기본 생성자를 반드시 포함해야하기 때문에 단지 생성자에 인자를 넘겨만 주는 방식으로는 오류가 발생 

1. newInstance()를 활용하는 법
```kotlin
    //Fragment쪽
    companion object{
        fun newInstance():BookFragment(){
            val fragment=BookFragment()
            val args=Bundle()
            args.putInt("value",1)
            fragment.arguments=args
            return fragment
        }
    }
    //사용하는쪽
    val num = 1
    val fragment = BookFragment.newInstance(num)
```

2. FragmentFactory 사용
```kotlin
class FragmentFactoryImpl(priavte val age: Int):FragmentFactory(){
    override fun instantiate(classLoader: ClassLoader,className:String):Fragment{
        return when(className){
            BookFragment::class.java.name->{
                BookFragment()
            }
            CookBookFragment::class.java.name->{
                CookBookFragment(age)
            }
            MathBookFragment::class.java.name->{
                MathBookFragment().apply{
                    arguments=Bundle().apply{
                        putInt("num",age)
                    }
                }
            }
            else -> super.initiate(classLoader,className)
        }
    }
}

//사용 하는 쪽
override fun onCreate(~~){
    super.onCreate(~~)
    supportFragmentManager.fragmentFactory=FragmentFactoryImpl(1)// 이 코드를 화면이 붙기 전에 먼저 선언해주어야 함. 그러면 빈 생성자를 굳이 생성할 필요가 없으며 bundle에 데이터를 넣지 않아도 재생성 상황에서 데이터가 보존 됨
    setContentView(~~)
    ~~
    btn.setOnClickListener{
        fragment=supportFragmentNamager.fragmentFactory.instantiate(classLoader,BookFragment:;class.java.name)
        supportFrgmentManager.beginTransaction().replace(R.id.frame_layout,fragment).commitNow()
    }
}

```
