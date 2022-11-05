# Przetwarzanie asynchroniczne z Coroutines, Notifications, BroadcastReceiver i Services

Zalecane: API 30.

## Coroutines

Co to są coroutines?

### Zadanie 1

1. Utwórz aplikację *AsyncApp* z pustą aktywnością, w której zdefiniujemy takie komponenty jak TextView, Button oraz ProgressBar w RelativeLayout.

   Przykładowy layout:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_above="@+id/btnDoAsync"
        android:gravity="center"
        android:text="Value if returned from the Async, will be displayed here" />

    <Button
        android:id="@+id/btnDoAsync"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:layout_margin="16dp"
        android:text="Start ASYNC task" />

    <ProgressBar
        android:id="@+id/progressBar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/btnDoAsync"
        android:layout_centerHorizontal="true"
        android:visibility="gone" />
   </RelativeLayout>
   ```

2. Przed napisaniem kodu, musimy pobrać dodatkowe bibliotki i dołączyć do projektu (wersje możecie znaleźć na [Kotlin Coroutines releases](https://github.com/Kotlin/kotlinx.coroutines/releases)):

   ```java
   dependencies {
     ...
     implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.4"
     implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.6.4"
     implementation "androidx.activity:activity-ktx:1.5.1"
   }
   ```

   Jest kilka opcji uruchamiania zadań w tle, kilka przykładów znajdziemy w colabie dla [Kotlin Coroutines](https://developer.android.com/codelabs/kotlin-coroutines#4) i [architektura coroutines](https://developer.android.com/topic/libraries/architecture/coroutines) oraz w [najlepszych praktykach](https://developer.android.com/kotlin/coroutines/coroutines-best-practices).

3. Przejdźmy wspólnie z prowadzącym przez przykład z [codelabs o coroutines](https://developer.android.com/codelabs/kotlin-coroutines#4):

   ```kotlin
   /**
   * Wait one second then display a snackbar.
   */
   fun updateTaps() {
      // launch a coroutine in viewModelScope
      viewModelScope.launch {
          tapCount++
          // suspend this coroutine for one second
          delay(1_000)
          // resume in the main dispatcher
          // _snackbar.value can be called directly from main thread
          _taps.postValue("$tapCount taps")
      }
   }
   ```

4. W naszym ćwiczeniu wykorzystamy bardziej złożoną implementację Timera dla zademonstrowania pojęcia równoległości:

   ```kotlin
   package com.example.asyncapp

   import android.os.CountDownTimer
   import androidx.lifecycle.LiveData
   import androidx.lifecycle.MutableLiveData
   import androidx.lifecycle.ViewModel

   class ProgressViewModel : ViewModel()  {

       private val mutableCounter = MutableLiveData<Int>()
       val counter: LiveData<Int> get() = mutableCounter

       private val mutableInProgress = MutableLiveData<Boolean>()
       val inProgress: LiveData<Boolean> get() = mutableInProgress

       private val timer = object: CountDownTimer(4000, 1000) {
           override fun onTick(millisUntilFinished: Long) {
               mutableCounter.value = mutableCounter.value?.plus(1)
           }

           override fun onFinish() {
               mutableInProgress.value = false
           }
       }

       fun start() {
           mutableCounter.value = 0
           mutableInProgress.value = true
           timer.start()
       }
   }
   ```

5. Plik `MainActivity.kt` jest nie co bardziej skomplikowany.

   ```kotlin
   package com.example.asyncapp

   class MainActivity : AppCompatActivity() {

       private val viewModel: ProgressViewModel by viewModels()

       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)

           val btnDoAsync = findViewById<Button>(R.id.btnDoAsync)

           val pb = findViewById<ProgressBar>(R.id.progressBar)

           viewModel.counter.observe(this) { c ->
               // Update the selected filters UI
               val tx = findViewById<TextView>(R.id.textView)
               tx.text = c.toString()
           }

           viewModel.inProgress.observe(this) { isInProgress ->
               if (isInProgress) {
                   pb.visibility = View.VISIBLE
               } else {
                   pb.visibility = View.GONE
               }
           }

           btnDoAsync.setOnClickListener {
               viewModel.start()
           }
       }
   }
   ```

6. Test the application.

### Zadanie 2

Wykonaj ćwiczenie:

- [Colab Kotlin Coroutines](https://developer.android.com/codelabs/kotlin-coroutines#4);
- przeanalizuj przykłady dla [najlepszych praktykach](https://developer.android.com/kotlin/coroutines/coroutines-best-practices).

## Notifications - powiadomienia

[Notifications](https://developer.android.com/develop/ui/views/notifications) w systemie Android są jednym z mechanizmów komunikacji między aplikacjami zainstalowanymi na urządzeniu a użytkownikiem. Powiadomienia wykorzystywane są w przypadku wystąpienia szczególnych zdarzeń, które mogą być ważne i interesujące dla użytkownika. Projektując własny system powiadomień, należy zwrócić uwagę, aby nie były one uciążliwe dla użytkownika. Przykładowymi powiadomieniami stosowanymi w codziennej pracy są:

- aplikacja obsługująca pocztę powiadamia o przychodzącej nowej poczcie; - system Android powiadamia o nowej wersji programu.

Powiadomienia są wyświetlane w górnej części ekranu, jeśli urządzenie jest odblokowane lub, w zależności od ustawień zabezpieczeń, na ekranie blokady, gdy urządzenie jest zablokowane.

### Zadanie

Stwórzmy aplikację, która pozwoli nam przesłać powiadomienie z aplikacji i wyświetlić te powiadomienie na ekranie startowym naszego smartfonu.

1. Utwórz aplikację o nazwie *NotificationApp*.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".MainActivity">

       <TextView
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_centerHorizontal="true"
           android:layout_marginTop="50dp"
           android:text="WSB"
           android:textAlignment="center"
           android:textColor="@android:color/holo_green_dark"
           android:textSize="32sp"
           android:textStyle="bold" />

       <Button
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_centerInParent="true"
           android:onClick="createNotification"
           android:text="create notification" />
   </RelativeLayout>
   ```

2. W pliku `MainActivity.kt` dodaj:

   ```kotlin
   class MainActivity : AppCompatActivity() {
    private var count: Int = 0
    private val channelId = "10001"
    private val defaultChannelId = "default"

    override fun onResume() {
        super.onResume()
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        title = "KotlinApp"
        count = 0
    }

    fun createNotification(view: View) {
        count++
        val notificationIntent = Intent(applicationContext, MainActivity::class.java)
        notificationIntent.putExtra("fromNotification", true)
        notificationIntent.flags = Intent.FLAG_ACTIVITY_CLEAR_TOP or Intent.FLAG_ACTIVITY_SINGLE_TOP
        val pendingIntent = PendingIntent.getActivity(this, 0, notificationIntent, 0)
        val notificationManager =
            getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        val builder = NotificationCompat.Builder(applicationContext, defaultChannelId)

        builder.setContentTitle("My Notification")
        builder.setContentIntent(pendingIntent)
        builder.setContentText("Hello World, to jest napis")
        builder.setSmallIcon(R.drawable.ic_launcher_foreground)
        builder.setAutoCancel(true)
        builder.setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL)
        builder.setNumber(count)
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val importance = NotificationManager.IMPORTANCE_HIGH
            val notificationChannel =
                NotificationChannel(channelId, "NOTIFICATION_CHANNEL_NAME", importance)
            builder.setChannelId(channelId)
            notificationManager.createNotificationChannel(notificationChannel)
        }
        notificationManager.notify(System.currentTimeMillis().toInt(), builder.build())
    }
   }
   ```

4. W manifeście (`manifest/AndroidManifest.xml`) dodaj zezwolenia:

   ```xml
   <uses-permission android:name="android.permission.VIBRATE" />
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
   ```

5. (dodatkowe) Zmodyfikuj program by notyfikacje były wyświetlane w innej kolorystyce. Zastanów się jak dodać ikonę/obrazek do powiadomienia oraz wywołać intencję po kliknięciu w powiadomienie. Zapoznaj się z  [dokumentacją](https://developer.android.com/develop/ui/views/notifications/build-notification).

## BroadcastReceiver

BroadcastReceiver to komponent który umożliwia rejestrację zdarzeń systemowych lub aplikacji. Wszyscy odbiorcy zdarzenia są powiadamiani przez środowisko wykonawcze systemu Android o wystąpieniu tego zdarzenia np. możemy otrzymać informację po włączeniu trybu samolotowego, że ten tryb jest uruchomiony. Więcej o broadcast znajdziesz w [dokumentacji](https://developer.android.com/guide/components/broadcasts).

### Zadanie

1. Utwórzmy aplikację, która pozwoli nam zmienić status sieci WiFi czy też nie. Stwórz aplikację o nazwie *BroadCastReceiver*.

2. Layout:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Switch
        android:id="@+id/wifiSwitch"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true" />
   </RelativeLayout>
   ```

3. Utwórzmy teraz BroadcastReceiver w pliku `MainActivity.kt`:


   ```kotlin
   package com.example.broadcastreceiver

   class MainActivity : AppCompatActivity() {
    lateinit var wifiSwitch: Switch
    lateinit var wifiManager: WifiManager

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        wifiSwitch = findViewById(R.id.wifiSwitch)



        wifiSwitch.setOnCheckedChangeListener { _, isChecked ->
            if (isChecked) {
                val panelIntent = Intent(Settings.Panel.ACTION_WIFI)
                startActivityForResult(panelIntent, 1);
            } else {
                val panelIntent = Intent(Settings.Panel.ACTION_WIFI)
                startActivityForResult(panelIntent, 0);
            }
        }
    }

    override fun onStart() {
        super.onStart()
        val intentFilter = IntentFilter(WifiManager.WIFI_STATE_CHANGED_ACTION)
        registerReceiver(wifiStateReceiver, intentFilter)
    }

    private val wifiStateReceiver: BroadcastReceiver = object : BroadcastReceiver() {
        override fun onReceive(p0: Context?, p1: Intent?) {
            Log.println( Log.INFO,"wifi receiver", "received!")
            Log.println(Log.INFO, "wifi receiver", intent.getIntExtra(
                WifiManager.EXTRA_WIFI_STATE,
                WifiManager.WIFI_STATE_UNKNOWN
            ).toString())
            when (intent.getIntExtra(
                WifiManager.EXTRA_WIFI_STATE,
                WifiManager.WIFI_STATE_UNKNOWN
            )) {

                WifiManager.WIFI_STATE_ENABLED -> {
                    wifiSwitch.isChecked = true
                    wifiSwitch.text = "WiFi is ON"
                    Toast.makeText(this@MainActivity, "Wifi is On", Toast.LENGTH_SHORT).show()
                }

                WifiManager.WIFI_STATE_DISABLED -> {
                    wifiSwitch.isChecked = false
                    wifiSwitch.text = "WiFi is OFF"
                    Toast.makeText(this@MainActivity, "Wifi is Off", Toast.LENGTH_SHORT).show()
                }

                WifiManager.WIFI_STATE_UNKNOWN -> {
                    wifiSwitch.text = "WiFi status changed"
                    Toast.makeText(this@MainActivity, "Wifi is Unknown", Toast.LENGTH_SHORT).show()
                }
            }
        }
    }
   }
   ```

4. W Manifeście dołącz zezwolenie na zmianę statusu WiFi.

   ```xml
   <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
   ```

5. (dodatkowe) Dodaj do swojej aplikacji *ImageView* z logo WiFi. Dostosuj aplikację do pracy w trybie poziomym.

<!-- 6. (dodatkowe) Spróbuj zmodyfikować tą aplikację by sprawdzała czy uruchomiony jest tryb samolotowy czy nie. Spróbuj również wykonać aplikację uruchamiającą i wyłączającą latarkę. Czy jest to możliwe? -->

## Services

[Services](https://developer.android.com/guide/components/services) czyli usługi systemu Android to składniki aplikacji, które mogą wykonywać długotrwałe operacje w tle i nie zapewniają interfejsu użytkownika. Sterowanie usługami realizowane jest zazwyczaj z Aktywności i do niej przekazywane są wyniki działania usług. Dla systemu operacyjnego Android Usługi mają wyższy priorytet niż Aktywności, dlatego w przypadku braku zasobów ich działanie jest wstrzymywane na samym końcu, gdy brak jest możliwości uruchomienia nowej aplikacji.

<p align="center">
<img src="https://developer.android.com/static/images/service_lifecycle.png" width="40%"/><br />
<small>Źródło: <a href="https://developer.android.com/guide/components/services">developer.android.com/guide/components/services</a></small>
</p>

### Zadanie 1

1. Utwórz aplikację o nazwie *AlarmManagerApp* z dwoma przyciskami buton.
2. Plik `activity_main.xml` powinien wyglądać mniej więcej tak:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center_horizontal"
    android:orientation="vertical"
    android:padding="16sp">

    <Button
        android:id="@+id/btnStartService"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:text="Start Service Alarm" />

    <Button
        android:id="@+id/btnStopService"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:text="Cancel Service" />
   </LinearLayout>
   ```

3. Musimy poinformować, że będziemy chcieli korzystać z alarmu:

  ```xml
  <uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM"/>
  ```

4. Utwórz plik `MainActivity.kt` i wklej poniższe fragmenty kodu w odpowiednie miejsca.
  ```kotlin
  class MainActivity : AppCompatActivity() {

    private lateinit var btnStart: Button
    private lateinit var btnStop: Button
    lateinit var pendingIntent: PendingIntent

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        btnStart = findViewById<Button>(R.id.btnStartService)
        btnStop = findViewById<Button>(R.id.btnStopService)


        btnStart.setOnClickListener {
            startService(Intent(this, MyAlarmService::class.java))
            Toast.makeText(baseContext, "Start Service", Toast.LENGTH_LONG).show()
        }

        btnStop.setOnClickListener {
            stopService(Intent(this, MyAlarmService::class.java))
            Toast.makeText(baseContext, "Stop Service", Toast.LENGTH_LONG).show()
        }
    }
  }
  ```

5. Dodajmy teraz plik `MyAlarmService.kt`, a w nim:

   ```kotlin
   class MyAlarmService : Service() {
    private var player: MediaPlayer? = null

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {

        player = MediaPlayer.create(this, Settings.System.DEFAULT_RINGTONE_URI)

        player!!.isLooping = true

        player!!.start()

        return START_STICKY
    }

    override fun onDestroy() {
        super.onDestroy()
        player!!.stop()
    }

    override fun onBind(intent: Intent?): IBinder? {
        return null
    }
   }
   ```

6. W `manifest/AndroidManifest.xml` musimy zadeklarować nasz serwis (pod elementem `application`):

   ```xml
   <application ...>
      <service android:name=".MyAlarmService" />
   </application>
   ```

7. Przetestuj aplikację.

<!-- Wykorzystamy serwis alarmu (patrz [dokumentacja](https://developer.android.com/training/scheduling/alarms)): -->

### Zadanie 2

Wspólnie z prowadzącym omówimy sekcję [Extending the Service class](https://developer.android.com/guide/components/services#ExtendingService).
