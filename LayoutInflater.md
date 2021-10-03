# LayoutInflater 
## LayoutInflater 
+ LayoutInflater는 안드로이드에서 view를 만드는 가장 기본적인 방법  
+ Fragment의 View를 만들거나 RecyclerView에서 ViewHolder를 만들 때, CustomView에서 xml로 정의된 View를 merge할 때 등 여러 곳에서 사용 됨  
+ 안드로이드의 Activity는 보여지는 화면을 구성하는 xml과 내부적으로 동작하는 java코드로 구성 됨  
+ xml파일에 원하는 view 태그를 만들었다고 해서 버튼과 같은 것들이 만들어진 것은 아니고 이것을 메모리에 올려놓아야하는데 이렇게 view를 메모리에 올려 객체화 하는 것을 inflation이라고 함  
+ view를 inflate하기 위해 메모리를 할당하는 코드가 필요하지만 이 코드를 직접 만들지 않아도 xml 파일에만 만들면 view를 볼 수 있고, 이것은 메모리에 할당되지 않은 것이 아니라 Layout Inflater가 알아서 inflation을 수행한 것  
+ 앱을 만들면 setContentView(R.layout.activity_main)과 같은 함수가 실행되도록 되어있는데 이 함수의 내부에서 layout inflater가 실행되어 view들을 객체화 하는 것임(그렇기 때문에 setContentView()를 실행하기 전에는 해당 레이아웃의 view를 불러오지 못함)  
## LayoutInflater 생성하기  
+ Context.getSystemService()  
  - 가장 기본적인 방법으로 context에서 LayoutInflater를 가져오는 방법  
```  
  val inflater: LayoutInflater = context.getSystemService(Context.LAYOUT_INFLATER_SERVICE)  
```  

+ Activity.getLayoutInflater()  
  - Activity에서는 LayoutInflater를 쉽게 얻어올 수 있도록 getLayoutInflater()를 제공  
  - Activity는 자신 window의 LayoutInflater를 사용  
```  
  val infalter: LayoutInflater = getLayoutInflayer()
```  

+ LayoutInflater.from()  
  - 가장 자주 사용하는 방법으로, LayoutInflater에 static으로 정의되있는 LayoutInflater.from을 통해 LayoutInflater를 만드는 방법  
  - 내부적으로는 context.getSystemService를 호출하고 있으며 같은 context에서는 같은 객체를 리턴하기 때문에 멤버 변수로 선언해 놓지 않고 필요할 때마다 호출해서 사용해도 됨  

## View inflate하기  
+ inflater에서 view객체를 만들기 위해서는 inflate를 사용하면 됨  
+ inflate(resource: Int, root: ViewGroup?, attachToRoot: Boolean)  
  - resource: View를 만들고 싶은 레이아웃 파일의 id  
  - root: 생성될 View의 parent를 명시  
  - attachToRoot: true로 설정해줄 경우 root의 자식 View로 자동으로 추가됨(내가 사용할 때는 보통 false로 작성했음)  
  - return: attachRoot에 따라서 return 값이 달라지고 true일 경우 root가 false일 경우 XML내 최상위 뷰가 리턴 됨      
