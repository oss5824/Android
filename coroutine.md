# 코루틴
+ 동시성 프로그래밍을 코틀린에 도입한것으로 co(동시)라는 의미를 지니고 있음
+ 코루틴이 시작된 스레드를 중단하지 않고도 비동기를 실행할 수 있어서 기존 멀티 쓰레드보다 효율적인 동작을 함
+ 기존의 스레드를 유지하면서 다른 코루틴을 실행시키는 방식(내부적으로는 멀티 스레드를 사용한다고 함)

## 코루틴과 스레드
+ Background Task라는 점이 비슷하지만 다른 개념을 가짐
+ 코루틴은 실행-종료 되야하는 하나의 Job이라고 하면 스레드는 Job이 실행되는 곳
+ 코루틴은 실행 중간 다른 작업을 하다가 돌아와 다시 작업을 할 수 있지만 쓰레드는 끝날 때까지 계속 됨
+ 스레드에서 여러 코루틴을 동시에 실행할 수 있음
+ runBlocking은 코루틴이 시작되어서 끝날 때까지 현재 스레드를 멈춤

## 코루틴 스코프
+ 코루틴 스코프는 코루틴 컨텍스트를 확장 함수 내부에서 사용하기 위해서 매개체 역할을 담당함
+ 코루틴 컨텍스트는 Job과 dispatcher를 저장하는 일종의 맵
+ 종류
    - GlobalScope: Application의 생명주기와 함께 동작해서 별도로 생명주기에 대한 관리를 하지 않아도 됨
    - CoroutineScope: 필요할 때 열고 닫는 경우 사용 가능
    - ViewModelScope: ViewModel에서 사용하기 위해 제공되고 ViewModel이 소멸될 때 자동으로 취소

## 디스패쳐
+ 디스패쳐는 적당한 스레드를 코루틴에 할당해주고 일시 정지, 재개 등을 담당
+ Default: CPU를 많이 사용하는 곳에 최적화
+ IO: 파일 입출력,네트워크,DB,DISK 등 입출력에 최적화 
+ Main: UI에 대한 접근과 상호작용에 대해 최적화

## 관련메소드
+ launch(), async()
    - Job과 Deffered 인스터스를 반환(Deffered는 Job을 상속한 클래스)
    - 실행이 가능하고 전자는 상태만 관리하고 후자는 상태관리와 결과를 반환 받을 수 있음
    - launch는 동기작업이고 async는 비동기 작업
    - async()는 결과를 받아서 사용할 수 있고 이를 처리하기 위해 await()를 사용함
```kotlin
    // Main안쪽의 async가 비동기로 실행이 되고 Main밖의 토스트 메시지가 먼저 출력이 됨(Main안에서는 작업이 늦어지더라도 Main밖에서는 작업이 계속 진행 됨)
    // 핸들러를 사용해서 정해진 시간만큼 기다리는 것은 근본적인 해결책이 되지는 못함
    CoroutineScope(Dispatchers.Main).launch{
        CoroutineScope(Dispatchers.Default).async{
            // 네트워크 작업
        }.await()
        // UI 업데이트
    }
    Toast.makeText(this,"메시지",Toast.LENGTH_SHORT).show()
```
+ cancel()
    - 동작을 멈추게 해주고 스코프안에 다른 코루틴들이 존재한다면 그 또한 모두 멈춤
+ delay()
    - 정해준 시간만큼의 시간이 지날때까지 다른 코루틴에게 실행을 무한 양보하고, 양보받은 코루틴이 다시 양보한다고 하더라도 시간이 지나지 않았다면 다시 제어권을 넘겨줌

## suspend fun
+ 해당 함수의 호출 이전까지 코드는 실행이 멈추고 suspend fun이 완료되면 그 후의 코드가 실행 됨
+ suspend fun은 코루틴 스코프안에서 사용해야하고 그렇기 때문에 자동으로 백그라운드 스레드와 같이 동작하게 됨

## withContext 등
+ withContext
  - 부모의 스코프와 다른 디스패쳐를 사용해야할 때 withContext()를 사용
```kotlin
    CoroutineScope(Dispatchers.IO).launch{
        // Retrofit 통신

        withContext(Dispatchers.IO){
            // UI 처리
        }
    }
```
+ withTimeout: 시간 내로 실행되지 않는다면 예외 발생
+ withTimeoutOrNull: 시간 내로 실행되지 않으면 null 반환

## 기타 코루틴 빌더
+ produce: 데이터를 채널을 통해 스트림으로 보내는 코루틴을 빌드하고 ReceiveChannel<>을 반환해 해당 채널에서 데이터를 받아 사용 가능
+ actor: 메세지를 받아서 처리하는 actor를 코루틴으로 빌드하고 SendChannel<>을 반환해 해당 채널에서 send()를 통해 actor에게 메세지 전송 가능
