# Annotation  
+ 어노테이션은 자바 또는 코틀린에서 사용 됨  
+ 어노테이션은 부가기능을 코드에 비침투적으로 추가할 수 있는 수단  
+ 어노테이션은 멤버 변수, 함수, 클래스 등 다양한 곳에 위치 시킬 수 있고, 다양한 기능을 가진 다양한 어노테이션이 있음  
+ 정의된 어노테이션 뿐만이 아닌 스스로 정의한 커스텀 어노테이션을 만들어 사용할 수도 있음  
    
+ JvmName  
  - 코틀린을 바이트코드로 변환할 때 JVM 시그니쳐를 변경할 때 사용  
  - 자바에서 호출되는 코틀린함수의 이름이 변경된다는 의미  
```  
  fun foo(a: List<String>){}  
  fun foo(a: List<Int>){}  
```  
  - 위 두개의 함수는 바이트코드로 변경될 때 시그니쳐가 동일(List의 Generic이 구별되지 않기 때문에 에러가 발생)  
 ```  
  @JvmName("fooListString")
  fun foo(a: List<String>){}
  @JvmName("fooListInt")  
  fun foo(a: List<Int>){}  
```  
  - @JvmName을 사용하게 되면 Signature를 변경할 수 있음  

+ JvmStatic  
  - @JvmStatic은 static변수의 get/set함수를 자동으로 만들라는 의미  
  - @JvmStatic은 Companion에 등록된 변수를 자바의 static처럼 선언하기 위한 annotation  

+ JvmField  
  - @JvmField는 get/set을 생성하지 말라는 의미  

+ Throws  
  - @Throws는 코틀린 함수가 예외를 던질 수 있다는 것을 표시  
  - ex. @Throws(NumberFormatException::class)  

+ JvmOverloads  
  - @JvmOverloads는 코틀린 함수의 오버로딩 메소드들을 생성  
  - @JvmOverlads는 코틀린 코드만 사용하면 사용할 필요가 없는 annotation이고 자바에서 코틀린 코드를 사용할 때 코틀린의 기본인자 개념을 적용하기 위해 사용되는 것         
