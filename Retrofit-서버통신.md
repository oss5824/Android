# Retrofit2
## REST API
+ 분산 시스템 설계를 위한 아키텍쳐 스타일
  - 아키텍쳐: 시스템을 설계하기 위한 설계도(개발자가 활용할 수 있는 지침서 정도)
+ REST API에는 여러 규칙이 있고 이러한 규칙을 어긴다면 REST하지 못한 것이고, REST API의 규칙을 전부 다 지키면 RESTful한 것
+ REST의 구성
  - HTTP URI = 자원: 식별자를 이용하면 URL, URL의 상위개념
  - HTTP Method = 행위: ex)GET,POST
  - Type = 표현 방식: ex)Content-type: application/json
+ REST 제약조건
  - Uniform interface: 자원은 유일하게 식별 가능해야하고, http method로 표현을 담아야하고, 메세지는 스스로 설명해야하고(self-descriptive), 하이퍼링크를 통해 애플리케이션의 상태가 전이(HATEOAS)되어야 함
  - Stateless: 요청에 대해 클라이언트의 context가 서버에 저장되어서는 안됨 
  - Cacheable: 클라이언트는 응답을 캐싱할 수 있어야함
  - Client-Server 구조
  - Code on demand: 서버에서 코드를 클라이언트에게 보내서 실행하게 할 수 있어야 함
  - Layered System(계층형구조): 클라이언트는 서버에 직접 연결되었는지 미들웨어에 연결되었는지 알 필요가 없어야 함
+ 표준 스키마가 없기 때문에 결국 API 문서를 만들어야 함
+ 행위에 대한 메소드가 제한적


## Retrofit에 대해
+ Retrofit2는 HTTP API 통신을 돕는 라이브러리(Square사에서 만든 네트워킹 라이브러리)
+ 사용이 쉽고 직관적이기 때문에 서버 통신을 할 때 주로 사용 됨(응답 속도가 빠르고 구현방법이 간단)
+ 보통 서버에 JSON 객체를 통해 요청이나 응답을 주고 받음
  - 안드로이드에서는 JOSN 객체를 바로 사용할 수 없기 때문에 JSON Object->JAVA Object의 변환과정이나 그 반대로의 변환과정이 필요
+ 네트워크 요청을 처리하기 위해 OkHttp를 사용하고 enqueue와 excute를 사용해 동기,비동기 처리를 지원
+ Okhttp는 REST API, HTTP 통신을 간편하게 구현할 수 있도록 다양한 기능을 제공해주는 라이브러리, Retrofit라이브러리의 베이스가 됨

## Retrofit 객체 생성
+ 서버와 통신을 하기 위해 Retrofit 객체 필요
+ Retrofit.Build()를 통해 객체를 생성할 수 있음
+ 어디서든 필요할 수 있기 때문에 싱글톤 패턴을 이용해 객체를 생성
```kotlin
object RetrofitBuilder{
    private val retrofit = Retrofit.Builder()
    .baseUrl("http://")
    .addConverterFactory(GsonConverterFactory.create())
    .build()

    //interface를 만든 후 적용 가능
    /*
        private val _api = retrofit.create(UserInterface::class.java)

        val api
                get()=_api
    */
}
```

## Retrofit 요청과 응답
+ 통신을 할 때, JSON객체를 통해 데이터를 주고 받음
+ 서버에 정의된 변수의 이름과 클라이언트에 정의된 변수의 이름이 같다면 스스로 대응시켜주지만 그렇지 않으면 @SerializedName 어노테이션을 사용해줘야 함
```kotlin
data class UserInfo(
    @SerializedName("email")val id: String,
    @SerializedName("password")val pw: String,
    val nick: String
)
data class UserInfoResponse(
    val user: List<UserInfo>
)
```

## API 인터페이스
+ 서버와 통신하기 위해서는 API에 따라 Retrofit 객체가 구현할 API Interface를 정의함
+ GET HTTP Request
```kotlin
interface UserInterface{
    @GET("user")
    fun getUserInfo(@Query("email")id:String,
                @Query("password")pw:String,
                @Query("nick")nick:String): Call<UserInfoResponse>
}
```
+ 통신을 할 때 비동기 처리를 해줘야 하기 때문에 Retrofit2가 제공하는 Call 객체를 사용해 Callback 구현

## 통신
```kotlin
    RetrofitBuilder.api.getUserInfo(email,password,nick)
    .enqueue(object: Callback<UserInfoResponse>{
        override fun onResponse(call: Call<UserInfoResponse>, response: Response<UserInfoResponse>) {
		if(response.isSuccessful()) { 
		} else {
		}
	}
	override fun onFailure() {
	}
    })
```
+ Callback 객체를 응답이 날라오면 처리할 수 있도록 해줌
+ Callback 객체를 익명 인터페이스로 만든 다음 override 해야하는 메서드를 구현해줌

## RxJava 활용
```kotlin
  interface UserInterface{
      @GET("user")
      fun getUserInfo(@Query("email")id:String,
                  @Query("password")pw:String,
                  @Query("nick")nick:String): Observable<UserInfoResponse>
                  // rxjava를  활용하기 위해서는 Call을 return 하는  것이 아닌 Observable을 return 해주어야 함
  }

  val retrofit = Retrofit.Builder().baseUrl("http://")
  .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
  .addConverterFactory(GsonConverterFactory.create())
  .build()

  val api = retrofit.create(UserInterface::class.java)

  val disposable=api.getUserInfo(email,password,nick).subscribeOn(Schedulers.io())
  .observeOn(AndroidSchedulers.mainThread())
  .subscribe(
    {info->Toast.makeText(this,info.toString(),Toast.LENGTH_SHORT).show()},
    {error0>Toast.makeText(this,error.toString(),Toast.LENGTH_SHORT).show()}
  )
```
