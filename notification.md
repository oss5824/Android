# Notification

## 알림생성
```kotlin
  val CHANNEL_ID="MY_CHANNEL_ID"
  val CHANNEL_NAME="MY_CHANNEL_NAME"

  val pendingIntent=getActivity(this,0,Intent(this,MainActivity::class.java),FLAG_ONE_SHOT)
  //notification을 터치했을 때의 화면 이동을 위해서

  var builder = NotificationCompat.Builder(this,CHANNEL_ID)
  .setSmallIcon(R.drawable.ic_launcher_background)
  .setContentTitle("알림 제목")
  .setContentText("알림 내용")
  .setAutoCancel(true)
  .setContentIntent(pendingIntent)
  .build()
```

## 알림채널등록
```kotlin
  if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.O){
    val channel_id=CHANNEL_ID
    val channel_name=CHANNEL_NAME
    val channelDescription="채널 설명"
    val importance=NotificationManager.IMPORTANCE_DEFAULT

    val channel=NoficiationChannel(channel_id,channel_name,importance).apply{
      description=channelDescription
    }

    val notificationManager: NotificationManager=getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
    notificationManager.createNotificationChannel(channel)

    notificationManager.notify(1002,builder)
    //알림표시: nofity(알림의 고유 ID, 알림결과)
  }
```
