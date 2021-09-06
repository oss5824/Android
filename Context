# Context  
+ Context는 안드로이드 개발의 대부분에서 찾아볼 수 있고 가장 중요하게 고려해야할 부분 중 하나(Context에 대한 이해가 필요)  
+ Context를 잘못 사용할 시 어플리케이션에서 메모리 누수가 발생할 수 있음   
+ Context는 Activity, Service등의 base class  
+ Context는 말 그대로 안드로이드에서의 "문맥,맥락"이라고 생각하면 됨  
  - Application의 현재 상태에 대한 맥락  
  - Application/Activity에 관한 정보를 얻을 수 있음  
  - Resources/DB/SharedPreferences에 접근할 때 사용되기도 함  
  - Application/Activity의 부모 클래스  
  - Application의 resource획득, 새로운 activity시작, view생성, systemservice 획득 등의 역할을 함   
+ 자주 사용하는 Context에는 두 가지 종류가 있음  
  - Application Context  
    * Application에 관한 정보를 담은 Application단의 Context  
    * Singleton 객체로 존재하고 getApplicationContext함수를 통해 접근 가능, applicationContext변수를 통해 접근 가능  
    * 이 Context는 어플리케이션 단의 생명주기와 바인딩 되어있음  
    * Application context는 현 activity의 context와 분리된 상황에 사용할 수 있고, activity context를 대입하게 된다면 application이 유지되는 동안 activity에서 참조가 계속 발생하고 그렇게 되면 activity는 가비지 콜렉팅이 되지 않아 메모리 누수가 발생하게 됨  
    * 예를 들어, Room에서 초기화를 할 때 context가 필요한데 Room은 전체 프로젝트에서 지속적으로 사용하는 Singleton 객체기 때문에 Application Context를 대입하는 것이 좋음   
  - Activity Context  
    * Activity에 관한 정보를 담은 Activity단의 Context, Activity마다 존재  
    * 이 Context는 Activity에서 사용가능하고, Activity의 생명주기와 바인딩 되어있음(Activity의 생명주기와 동일 시 되는 Context가 필요할 때 사용하면 됨)  
    * 예를 들어, Activity에서사용되는 Dialog, Toast 등의 UI작업에 필요한 Context는 ActivityContext를 사용하면 됨    
+ Activity나 Service 이외의 컴포넌트/객체에서 Context 참조가 필요하다면 Application Context로 참조하는 것을 강력히 권장한다고 함  
