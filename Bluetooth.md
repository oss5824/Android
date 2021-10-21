# 블루투스
+ Bluetooth API를 사용해 작업 수행 가능
    - 다른 블루투스 기기 스캔
    - 서비스 검색을 통해 다른 기기에 연결
    - 페어링된 블루투스 기기에 대해서 로컬 블루투스 어댑터 쿼리
    - 다중 연결관리
    - 기기 간 데이터 전송 및 수신

## 기본 사항
+ 블루투스로 서로 데이터를 전솧아기 위해 페어링 프로세스를 사용해 통신 채널을 형성해야함(다른 기기는 서비스 검색 프로세스를 이용해 검색 가능한 기기 찾을 수 있음)
+ 페어링 요청을 수락하면 두 기기는 보안키를 교환해 연결프로세스를 완료
+ 이후에 사용될 때를 대비해 기기가 캐싱해둠
+ 페어링과 연결프로세스가 완료된 후 정보 교환
+ 세션이 완료된 후 페어링 요청을 시작한 기기가 자신을 검색 가능한 기기와 연결해준 채널을 해제
+ 두 기기는 연결된 상태로 유지되며 누군가 삭제하지 않고 범위 내에 있으면 향후 세션에서도 자동으로 다시 서로 연결할 수 있음

## 권한
+ 블루투스 기능을 사용하기 위해서는 두 개의 권한을 선언해야 함
+ 첫 번째는 BLUETOOH이고 이 것은 연결 요청, 연결 수락 및 데이터 전송과 같은 블루투스 통신을 수행하는데 필요
+ 두 번째는 ACCESS_FINE_LOCATION으로 블루투스 스캔을 사용해 사용자 위치에 대한 정보를 수집할 수 있기 때문
+ 앱에서 기기 검색을 시작하거나 블루투스 설정을 조작하려면 BLUTOOTH 권한 외에 BLUTOOTH_ADMIN 권한도 선언해야 함

## 프로필 작업
+ 블루투스 프로필은 기기 간에 블루투스 기반 통신에 대한 무선 인터페이스 사양이고, Hands-Free 프로필이 있고 무선 헤드셋에 핸드폰을 연결하기 위해 두 기기가 Hands-Free를 지원해야 함
+ 프로필 작업 단계
    - 기본 어댑터를 가져옴
    - BluetoothProfile.ServiceListener를 설정하고 이것은 서비스에 연결되었거나 연결이 끊어졌을 때 BluetoothProfile IPC 클라이언트에 알림
    - getProfileProxy()를 사용해 프로필과 연결된 프로필 프록시 객체와 연결을 설정하고 아래에서 프로필 프록시 객체는 BluetoothHeadset의 인스턴스
    - onServiceConnected()에서 프로필 프록시 객체에 대한 핸들을 가져옴
    - 프로필 프록시 객체가 있는 경우 해당 객체를 사용해 연결 상태를 모니터링하고 해당 프로필과 관련된 다른 작업 수행 가능

```kotlin
    var bluetoothHeadset: BluetoothHeadset?=null
    val bluetoothAdapter: BluetoothAdapter?=BluetoothAdapter.getDefaultAdapter()
    // default 어뎁터 가져오기

    private val profileListener=object:BluetoothProfile.ServiceListener{
        override fun onServiceConnected(profile: Int, proxy: BluetoothProfile){
            if(profile==BluetoothProfile.HEADSET){
                bluetoothHeadset=proxy as BluetoothHeadset
            }
        }
        override fun onServiceDisconnected(profile:Int){
            if(profile==BluetoothProfile.HEADSET){
                bluetoothHeadset=null
            }
        }
    }

    bluetoothAdapter?.getProfileProxy(context,profileListener,BluetoothProfile.HEADSET)
    // proxy에 연결

    bluetoothAdapter?.closeProfileProxy(BluetoothProfile.HEADSET,bluetoothHeadset)
```

## 설정
+ 앱이 블루투스를 사용해 통신하기 위해 블루투스가 기기에서 지원되는지 확인하고 지원되는 경우 활성화 해야 함
    - BluetoothAdapter를 가져옴
        * 모든 Bluetooth의 activity는 BluetoothAdapter가 필요로 하고 가져오기 위해선 getDefaultAdapter()메스드를 호출
        * getDefaultAdapter()가 null을 반환하면 블루투스를 지원하지 않는 것 
    - Bluetooth를 활성화함
        * isEnabled()를 호출해 현재 블루투스가 활성화 되었는지 확인하고 false의 경우에는 비활성화 된 것이고 활성화 요청을 하기 위해선 startActivityForReulst()를 호출하고 ACTION_REQUEST_ENABLE을 인텐트 작업에서 전달( 이것은 시스템 설정을 통해 블루투스 활성화를 요청) 
        * Bluetooth 활성화에 성공하면 Activity가 onActivityResult() 콜백에서 RESULT_OK 결과 코드를 수신하고 오류로 인해 활성화하지 못한 경우 결과 코드는 RESULT_CANCELED 
    - 옵션으로 앱이 ACTION_STATE_CHANGED 브로드캐스트 인텐트를 수신대기 할 수 있고 이것은 블루투스 상태가 변경될 떄 마다 시스템이 브로드 캐스트하고, 앱이 블루투스에 적용된 런타임 변경사항을 감지해야 하는 경우 이 브로드캐스트를 수신 대기하는 것이 유용할 수 있음

## 기기 찾기
+ BluetoothAdapter를 사용하면 기기 검색을 통하거나 페어링 된 기기 목록을 쿼리하면 원격 블루투스 기기를 찾을 수 있음
+ 검색은 주변 지역을 검색해 블루투스 지원 기기가 있는지 찾고 그에 관한 정보를 요청하는 스캔 절차
+ 페어링과 연결된 것은 차이가 있음
    - 페어링은 두 기기가 서로간의 존재를 알고, 인증에 사용할 수 있는 공유 링크 키를 가지고 있으며 서로 암호화된 연결을 설정할 수 있다는 것을 의미
    - 연결은 기기가 채널을 공유하고 있고 데이터를 서로 전송할 수 있다는 것을 의미

## 페어링된 기기 쿼리
+ 검색을 수행하기 전 페어링 된 기기 집합을 쿼리해 원하는 기기가 있는지 확인하는 것이 좋고, getBondedDevices()를 호출
+ 페어링된 기기를 나타내는 BluetoothDevice객체 세트가 반환되고 기기의 이름과 MAC주소를 가져올 수 있음
+ 블루투스 기기와 연결을 시작하기 위해선 연결된 BluetoothDevice 객체에서는 MAC주소만 있으면 되고 해당 주소는 getAddress()를 호출하여 검색
```kotlin
    val pairedDevices: Set<BluetoothDevice>?=bluetoothAdapter?.bondedDevices
    pairedDevices?.forEach{device->
        val deviceName=device.name
        val deviceHardwareAddress=device.address // MAC주소
    }
```

## 검색
+ 검색을 위해선 startDiscovery()를 호출하고 이 프로세스는 비동기식이고 검색이 성공적으로 시작되었는지를 나타내는 bool 값을 반환
+ 앱이 검색된 기기에 대해 정보를 수신하기 위해선 ACTION_FOUND인텐트에 대한 BraodcastReceivcer를 등록해야 함
+ 시스템이 각 기기에 대해 인텐트를 브로드캐스트함