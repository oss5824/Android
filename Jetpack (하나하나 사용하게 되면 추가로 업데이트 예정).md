# Jetpack (하나하나 사용하게 되면 추가로 업데이트 예정)
+ 개발자가 관심 있는 코드에 집중할 수 있도록 권장사항 준수, 상용구 코드 제거, 모든 Android버전과 기기에서 일관되게 작동하는 코드 작성을 돕는 라이브러리 모음
+ 즉 안드로이드 Jetpack은 개발에 자주 쓰이는 여러 라이브러리들과 툴들을 묶어놓은 것으로 개발자들이 편리하고 빠르고 쉽게 높은 퀄리티의 앱을 개발하도록 도움 
+ Jetpack의 장점 중 하나로 이전 버전들과 호환이 됨

## Jetpack의 구성요소
Jecpack은 크게 4가지 구성요소로 나뉘고 각각 다양한 라이브러리들과 툴들이 존재
1. Architecture
    + DataBinding: xml파일에 Data를 연결해서 사용할 수 있게 도와줌
    + Lifecycles: 안드로이드 activity 생명주기 관련 utility
    + LiveData: 데이터가 변경될 때 실시간으로 View에 알림
    + Navigation: activity, fragment간 이동을 쉽게 함
    + Paging: 대량의 data들을 관리해주는 utility
    + Room: SQLite를 보다 쉽게 사용할 수 있도록 도움
    + WorkManager: 백그라운드 작업을 보다 쉽게 할 수 있도록 함

2. Foundation
    + AppCompat: 하위 안드로이드 앱에서도 최신버전의 sdk를 사용할 수 있도록 함
    + KTX: 코드의 간결성을 도움
    + Multidex: dex관련 utility
    + Test: Test관련 utility

3. Behavior
    + DownloadManager: 용량이 큰 파일의에 대한 다운로드를 service차원에서 관리
    + Media&Playback: 미디언 파일재생 관련 utility
    + Permissions: 권한 utility
    + Sharing: Actionbar의 데이터 공유를 도움

4. UI
    + 애니메이션, 이모지, 플랫폼 관련 utility를 사용할 수 있는 component
