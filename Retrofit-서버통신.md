# Retrofit2
## Retrofit에 대해
+ Retrofit2는 HTTP API 통신을 돕는 라이브러리(Square사에서 만든 네트워킹 라이브러리)
+ 사용이 쉽고 직관적이기 때문에 서버 통신을 할 때 주로 사용 됨(응답 속도가 빠르고 구현방법이 간단)
+ 보통 서버에 JSON 객체를 통해 요청이나 응답을 주고 받음
  - 안드로이드에서는 JOSN 객체를 바로 사용할 수 없기 때문에 JSON Object->JAVA Object의 변환과정이나 그 반대로의 변환과정이 필요
+ 네트워크 요청을 처리하기 위해 OkHttp를 사용하고 enqueue와 excute를 사용해 동기,비동기 처리를 지원

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

# 통신
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
