# Test
+ 코드 안정성 검사
+ 리팩토링했을 때 기존의 로직을 다시 검사
+ 종류
    - 유닛 테스트
        * 유닛단위(메소드,클래스,컴포넌트 등)의 기능을 테스트하는 방식
        * JVM에서 실행
        * test패키지 내에서 진행
    - UI 테스트
        * 사용자 인터렉션(클릭이벤트,텍스트 입력 등) 테스트
        * 안드로이드 기기 필요
        * androidTest패키지 내에서 진행
+ 기본적으로 프로젝트 처음 생성시 테스트 관련 패키지가 제공되고 라이브러리가 추가되어 있음
+ 매서드별로 테스트 해볼 수 있고 error가 발생하는 경우 출력하여 알려줌
```kotlin
    // 이와 같은 유닛테스트 파일이 작성되어있음
    class ExampleUnitTest {
        @Test
        fun addition_isCorrect() {
            assertEquals(4, 2 + 2)
            assertEquals(4, 2 + 1)// 에러를 발생시키는 경우
        }
    }
```

## Junit
+ Java의 Unit Test 프레임워크
+ 수동테스트
    - 시간이 많이걸리고 인건비 증가
    - 사람의 손으로하는 작업이라 신뢰성 떨어짐
    - 프로그래밍 불가
+ 자동테스트
    - 빠르게 테스트가능
    - 사람손보다 안정적
    - 프로그래밍 가능
 
 + 기본 메소드
```java
    void assertEquals(expect,actual)// 기대값과 결과가 동일한지
    void assertTrue(condition) //조건이 참인지
    void assertFalse(object) //객체가 null인지
    void assertNotSame(unexpect,actual)// 객체 참조가 동일한 객체를 가리키지 않는지
    void assertArrayEqauls(expectArray,actualArray) //배열 같은지
```

+ 어노테이션
    - @Test: 테스트로 실행될 수 있음을 Junit에게 알려줌
    - @Before: 테스트 실행전 객체를 만들거나 초기화 과정에 사용
    - @After: 자원을 해제할 때 사용
    - @BeforeClass: public static method에 추가하면 test 메소드 전 한번만 실행 가능
    - @AfterClass: 모든 테스트가 끝난 후 실행
    - @Ignore: 테스트를 무시하는데 사용
    - @Rule: 해당 Test클래스에서 사용할 Rule 정의

+ 에스프레소
    - onView(): View 연결, 동작처리
    - withText(text): 연결되어있는 textView의 text와 text String값이 같은지 판별
    - withId(R.id.xxx): R.id.xxx라는 id에 맞는 View를 찾음
    - perform(click()): View 액션(왼쪽은 클릭) 처리
    - click(): 클릭이벤트

## Mockito
+ Mock Framework로 Junit과 함께 사용가능
+ Unit Test는 많은 클래스가 다른 클래스의 의존해서 클래스별로 테스트해야한다는 단점이 있음
+ mock객체는 데이터 공급자가 되어 테스트할 때 조건이 매번 동일한 mock 객체를 받아 사용할 수 있음
+ Unit테스트시 의존성 때문에 테스트가 어려웠을 객체들을 가짜객체를 통해 세팅하여 쉽게 사용할 수 있음
```java
    MockInstance instance=mock(MockInstance.class);
    // mock 객체 생성
```

## Test함수 기본 구조
1. Given: 테스팅에 필요한 action 전의 객체를 만드는 작업
2. When: 실제 Test하려는 action
3. Then: assert부분으로 결과에 대한 작성
