#  Komunikacja pomiędzy zamiarem Intents i aktywnością.

Zalecane: API 30.

## Wprowadzenie

### Struktura głównej aktywność

**Podstawowym elementem składowym każdej aplikacji mobilnej są Aktywności, które implementują konkretne funkcje programu. Implementacja Aktywności dotyczy zarówno procesów obliczeniowych, jak i interfejsu komunikacji z użytkownikiem. Każda Aktywność może być utożsamiana z interfejsem użytkownika – obowiązuje zasada takiego projektowania Aktywności, aby jedna Aktywność odpowiadała projektowi jednego „ekranu” interfejsu użytkownika (GUI).**

**Ze względu na podstawową rolę Aktywności wprowadzono różne warianty działania Aktywności – dotyczy to różnych metod wymiany danych oraz różnych sposobów wywoływania Aktywności.** Plik `MainActivity.kt` odpowiedzialny jest za kod "głównej aktywności". Użytkownik po zainstalowaniu tego programu na swoim telefonie i uruchomieniu go ujrzy na pierwszym ekranie wszystko to co zostało wygenerowane w metodzie `onCreate()` klasy `MainActivity` rozszerzającej klasę `Activity`.
Dzięki przeładowaniu metody `onCreate()` system wie jaki layout ma wyświetlić. Aplikacja mobilna może składać się z wielu aktywności (okien). Kluczowe jest pytanie co dzieje się w momencie, w którym przechodzimy z jednej aktywności do drugiej.

<p align="center">
<img src="https://developer.android.com/guide/components/images/activity_lifecycle.png" width ="80%"/><br />
<small>Źródło: <a href="https://developer.android.com/guide/components/activities/activity-lifecycle">developer.android.com/guide/components/activities/activity-lifecycle</a></small>
</p>

Pierwszym etapem cyklu życia aktywności jest jej uruchomienie innymi słowy system Android po uruchomieniu przez użytkownika aplikacji przechodzi do głównej aktywności i ją uruchamia (w ten sposób uruchamiana jest pierwsza – główna aktywność w aplikacji). Następnie czyli już po uruchomieniu aktywności w systemie zachodzą zdarzenia takie jak `onCreate()`, `onStart()`, `onResume()` (dokładnie w takiej kolejności). Dzięki przeładowaniu odpowiednich metod mamy tutaj wpływ na to jak zachowa się nasz program. Na przykład, jak zrobiliśmy to poprzednio możemy, przeładować metodę `onCreate()` gdzie definiujemy plik xml z layoutem. Kolejnym etapem jest moment, w którym aktywność jest po prostu uruchomiona. Teraz jest czas na obsługę całej interakcji z użytkownikiem, zdarzeń typu `onClick` itd.

Następnie dochodzimy do najciekawszego fragmentu cyklu, kiedy to mamy trzy możliwości zachowania:

- Inna aktywność innymi działaniami (np. poprzez wciśnięcie przez użytkownika przycisku) wychodzi na pierwszy plan,
- Aktywność (obecna) nie będzie już dłużej widoczna,
- Aktywność (obecna) została zakończona, bądź system ją skasował.

### Intent

Android Intent to obiekt wiadomości używany do żądania innego komponentu aplikacji do wykonania akcji. Intent ułatwia użytkownikom komunikowanie się z komponentem aplikacji na kilka sposobów, takich jak rozpoczęcie działalności, uruchomienie usługi, dostarczenie odbiornika rozgłoszeniowego itp.

Istnieją dwa rodzaje zamiarów w Androidzie:
- Jawne zamiary: ten zamiar spełnia wymagania komponentu aplikacji. Wymaga w pełni
kwalifikowanej nazwy klasy działań lub usług, które chcemy rozpocząć.
- Niejawne zamiary Implicit Intent : ta intencja nie określa nazwy komponentu. Wywołuje
komponent innej aplikacji do obsługi żądania.

Przykład:

```kotlin
intent = Intent(applicationContext, SecondActivity::class.java)
startActivity(intent)
```

### Zadanie

W tym zadaniu wywołamy inną aktywność z innej aktywności, używając jawnych zamiarów (`Indend`). Korzystając z zamiaru, wyślemy dane z pierwszej klasy aktywności do drugiej klasy aktywności. Druga klasa aktywności pobiera te dane i wyświetla je w wiadomości Toast.

1. Utwórzmy nowy projekt o nazwie *IntentApp*. W pliku xml dla `MainActivitiy.kt` ustaw `ConstraintLayout` oraz dodaj komponenty `TextView` (id: *textView*) oraz `Button` (id: *button*).

2. Po wykonaniu poprzedniego punktu, `activity_main.xml` powinien wyglądać +/- tak:

   ```xml
   <TextView android:id="@+id/textView"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:layout_marginBottom="8dp"
      android:layout_marginTop="8dp"
      android:text="First Activity"
      android:textSize="18sp"
      app:layout_constraintBottom_toBottomOf="parent"
      app:layout_constraintHorizontal_bias="0.501"
      app:layout_constraintLeft_toLeftOf="parent"
      app:layout_constraintRight_toRightOf="parent"
      app:layout_constraintTop_toTopOf="parent"
      app:layout_constraintVertical_bias="0.172" />
   <Button
     android:id="@+id/button"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:layout_marginBottom="8dp"
     android:layout_marginEnd="8dp"
     android:layout_marginStart="8dp"
     android:layout_marginTop="8dp"
     android:text="Click"
     app:layout_constraintBottom_toBottomOf="parent"
     app:layout_constraintEnd_toEndOf="parent"
     app:layout_constraintStart_toStartOf="parent"
     app:layout_constraintTop_toBottomOf="@+id/textView"
     app:layout_constraintVertical_bias="0.77" />
   ```

3. Dodaj następujący kod do klasy `MainActivity.kt`. W tej klasie tworzymy instancję klasy Intent i dodamy w niej klasę aktywności komponentu `SecondActivity.kt` (zaraz zdefiniujemy tą klasę w pkt 5). Metoda putExtra (klucz, wartość) klasy Intent wysyła dane do klasy `SecondActivity.kt`. Metoda `startActivity()` uruchamia zaś zamiar.

   ```kotlin
   class MainActivity : AppCompatActivity() {
       val id:Int = 69
       val language:String = "Kotlin najlepszy jezyk"

       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)

           val button = findViewById<Button>(R.id.button)
           button.setOnClickListener() {
               // odkomentujemy po utworzeniu SecondActivity
               // intent = Intent(this, SecondActivity::class.java)
               // intent.putExtra("id_value", id)
               // intent.putExtra("language_value", language)
               // startActivity(intent)
           } }
   }
   ```

4. Utwórz drugą aktywność o nazwie `SecondActivity`. W pliku xml dla `SecondActivitiy.kt` ustaw *ConstraintLayout* oraz dodaj komponenty *TextView* (id: secondTextView) oraz *Button* (id: secondButton).

5. W drugiej aktywności, wyświetlimy wiadomość, i damy użytkownikowi możliwość do powrotu do głównej aktywności:

   ```kotlin
   class SecondActivity : AppCompatActivity() {
     override fun onCreate(savedInstanceState: Bundle?) {
         super.onCreate(savedInstanceState)
         setContentView(R.layout.activity_second)

         val bundle:Bundle? = intent.extras
         val id = bundle?.get("id_value")
         val language = bundle?.get("language_value")
         Toast.makeText(applicationContext,id.toString()+" "+language,Toast.LENGTH_LONG).show()

         val button2 = findViewById<Button>(R.id.secondButton)

         button2.setOnClickListener(){
             intent = Intent(this,MainActivity::class.java)
             startActivity(intent)
         }
       }
   }
   ```

6. Dostosuj interfejs graficzny aplikacji wykorzystując wiedzę z poprzednich zajęć.

## Przykład niejawnego zamiaru Implicit Intent

Implicit Intent wywołuje zewnętrzny komponent innej aplikacji do obsługi żądania. Nie określa konkretnie nazwy komponentu np, jeśli chcemy udostępnić dane za pomocą Intent, wywołuje odpowiedni komponent w celu spełnienia żądania.


Przykład:

```kotlin
intent = Intent(Intent.ACTION_VIEW)
intent.setData(Uri.parse("https://www.devillecloud.com/"))
startActivity(intent)

intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://www.devillecloud.com/"))
startActivity(intent)
```

### Zadanie

1. Utwórz nową aplikację *ImplicitIndent*.

2. W tym zadaniu wywołamy inny komponent z aktywności, używając Implicit Intent. Korzystając z zamiaru wywołamy otwarcie strony internetowej w aplikacji Google Chrome (patrz powyżej).

3. Utwórzmy nowy projekt o nazwie ImplicitIntentApp. W pliku xml dla `MainActivitiy.kt` ustaw `ConstraintLayout` oraz dodaj komponenty TextView oraz Button.Po przyciśnięciu przycisku wywołaj otwarcie dowolnej strony.

4. Dostosuj interfejs graficzny aplikacji wykorzystując wiedzę z poprzednich zajęć.

5. (Dodatkowe) Zastanów się jak wywołać Implicit Intent dla adresu e-mail po przyciśnięciu przycisku albo z poziomu `TextView`. Spróbuj to zrobić.

## Option Menu

Menu opcji systemu Android to zbiór pozycji w menu umożliwiający otwieranie aktywności. Umieszczone jest zwykle w prawym górnym rogu. Menu opcji tworzy się, zastępując funkcję `onCreateOptionsMenu()` .

W tym przykładzie dodamy elementy menu opcji na pasku akcji. Kliknięcie menu pokazuje pozycje w menu opcji, w których możemy wykonać odpowiednią akcję.

### Zadanie

1. Utwórz nowy projekt *OptionMenuApp* oraz wybierz szablon `BasicActivity`. Sprawdź swój plik `activity_main.xml` oraz porównaj go z tym poniższym. W razie braków uzupełnij go.

   **Ten kod jest generowany automatycznie podczas tworzenia podstawowej aktywności.**

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.coordinatorlayout.widget.CoordinatorLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".MainActivity">

       <com.google.android.material.appbar.AppBarLayout
           android:layout_width="match_parent"
           android:layout_height="wrap_content"
           android:theme="@style/Theme.OptionMenuApp.AppBarOverlay">

           <androidx.appcompat.widget.Toolbar
               android:id="@+id/toolbar"
               android:layout_width="match_parent"
               android:layout_height="?attr/actionBarSize"
               android:background="?attr/colorPrimary"
               app:popupTheme="@style/Theme.OptionMenuApp.PopupOverlay" />

       </com.google.android.material.appbar.AppBarLayout>

       <include layout="@layout/content_main" />

       <com.google.android.material.floatingactionbutton.FloatingActionButton
           android:id="@+id/fab"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_gravity="bottom|end"
           android:layout_marginEnd="@dimen/fab_margin"
           android:layout_marginBottom="16dp"
           app:srcCompat="@android:drawable/ic_dialog_email" />

   </androidx.coordinatorlayout.widget.CoordinatorLayout>
   ```

<!-- 2. Pora dodać więcej opcji, przejdź do pliku `content_main.xml`, porównaj go w  -->

2. Pora dodać więcej opcji w menu, zacznijmy od zdefiniowanie etykiet. W pliku `strings.xml`, dodajmy *action_share* i *action_exit*, zmień wartość dla *app_name*:

   ```xml
   <resources>
       <!-- ... -->
       <string name="app_name">Nasze Menu</string>
       <string name="action_settings">Settings</string>

       <string name="action_activity">Hello World</string>
       <string name="action_share">Share</string>
       <string name="action_exit">Exit</string>
       <!-- ... -->
   </resources>
   ```

3. Przejdź do `menu_main.xml`i dodaj 3 nowe pozycje:

   ```xml
   <item
       android:id="@+id/action_activity"
       android:orderInCategory="100"
       android:title="@string/action_activity"/>

   <item
       android:id="@+id/action_share"
       android:title="@string/action_share"
       app:showAsAction="never"/>
    <item
       android:id="@+id/action_exit"
       android:title="@string/action_exit"
       app:showAsAction="never"/>
   ```

4. Dodajmy akcję do wciśniecia pozycji w menu, przejdź do klasy `MainActivity.kt` i wyszukaj funkcji: `override fun onOptionsItemSelected`. Dodajmy kolejne pozycje do kodu akcji dla każdego itemu:

   ```kotlin
   // ...
   override fun onOptionsItemSelected(item: MenuItem): Boolean {
    return when (item.itemId) {
        R.id.action_settings -> {
          Toast.makeText(applicationContext, "Click on Settings", Toast.LENGTH_LONG).show()
          return true
        }
        R.id.action_activity -> {
            Toast.makeText(applicationContext, "Click on Activity", Toast.LENGTH_LONG).show()
            return true
        }
        R.id.action_share -> {
            Toast.makeText(applicationContext, "Click on Share", Toast.LENGTH_LONG).show()
            return true
        }
        R.id.action_exit -> {
            Toast.makeText(applicationContext, "Click on exit", Toast.LENGTH_LONG).show()
            return true
        }
        //
        else -> super.onOptionsItemSelected(item)
    }
   }
   // ...
   ```

5. Powyższe Toasty zastąp wywołaniem Intentów do nowych aktywności.Utwórz nowe aktywności dla Intentów.

## Splash Screen

[Splash Screen](https://developer.android.com/develop/ui/views/launch/splash-screen) to pierwszy ekran, który zwykle jest widoczny dla użytkownika po uruchomieniu aplikacji. Na ekranie powitalnym przez krótki czas wyświetlane są niektóre animacje lub logo aplikacji. Dzięki takiemu rozwiązaniu aplikacja może się spokojnie załadować pobierając choćby dane z zewnątrz.

### Zadanie

1. Utwórz nowy projekt o nazwie *SplashScreenApp* z *Empty Activity*.

2. Zaktualizuj plik `res/values/themes/themes.xml`, tak jak poniżej:

   ```xml
   <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
       <item name="colorPrimary">@color/purple_500</item>
       <item name="colorPrimaryDark">@color/purple_500</item>
       <item name="colorAccent">@color/teal_700</item>
   </style>

   <style name="AppTheme.NoActionBar">
       <item name="windowActionBar">false</item> <item name="windowNoTitle">true</item>
   </style>
   ```

3. Teraz czas utworzyć Activity dla efektu Splash screen. Utwórz Empty Activity `SplashScreenActivity.kt`.

4. W `activity_splash_screen.xml` dodaj *ImageView* i *TextView* oraz pasek dostępu. Powinnieneś plus/minus uzyskać:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout
     xmlns:android="http://schemas.android.com/apk/res/android"
     xmlns:app="http://schemas.android.com/apk/res-auto"
     xmlns:tools="http://schemas.android.com/tools"
     android:layout_width="match_parent"
     android:layout_height="match_parent"
     tools:context=".MainActivity">

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="198dp"
        android:layout_height="505dp"
        app:layout_constraintBottom_toTopOf="@+id/progressBar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:srcCompat="@tools:sample/backgrounds/scenic" />

    <ProgressBar
        android:id="@+id/progressBar"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="200dp"
        android:layout_height="33dp"
        tools:layout_editor_absoluteX="105dp"
        tools:layout_editor_absoluteY="655dp" />
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

5. Implementacja naszego Splash screenu:

   ```kotlin
   class SplashScreenActivity : AppCompatActivity() {

       private val splashTimeOut: Long = 3000 // 3 sec

       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)

           setContentView(R.layout.activity_splash_screen)
           Handler(Looper.getMainLooper()).postDelayed({
               // This method will be executed once the timer is over
               // Start your app main activity
               startActivity(Intent(this, MainActivity::class.java))
               // close this activity
               finish()
           }, splashTimeOut)
       }
   }
   ```

   Przeanalizuj kod.

6. W layout naszej głównej aktywności nie zmieniamy nic.

7. W manifeście `AndoridManifest.xml`, zmodyfikuj sekcję dotyczącej aktywności dla Splash screen:

   ```xml
   <activity
       android:name=".SplashScreenActivity"
       android:theme="@style/AppTheme.NoActionBar"
       android:exported="true">

       <intent-filter>
           <action android:name="android.intent.action.MAIN" />
           <category android:name="android.intent.category.LAUNCHER" />
       </intent-filter>

       <meta-data
           android:name="android.app.lib_name"
           android:value="" />
   </activity>
   ```

## Navigator Drawer

[Navigation Drawer](https://m3.material.io/components/navigation-drawer/overview) to panel, który wysuwa się z lewej strony ekranu i zawiera szereg opcji dostępnych do wyboru przez użytkownika, zwykle przeznaczonych do ułatwienia nawigacji do innej części aplikacji np. kolejnych aktywności.

### Zadanie

1. Utwórz nowy projekt o nazwie *NavigationDrawerApp* jako Empty Activity.

2. Dodaj poniższy styl w pliku `res/values/themes/themes.xml`:

   ```xml
   <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
       <item name="colorPrimary">@color/purple_500</item>
       <item name="colorPrimaryDark">@color/purple_500</item>
       <item name="colorAccent">@color/teal_700</item>
   </style>

   <style name="AppTheme.NoActionBar">
       <item name="windowActionBar">false</item> <item name="windowNoTitle">true</item>
   </style>
   ```

   Teraz będziemy dodawać pliku układu `content_main.xml`, `nav_header.xml` i `nav_menu.xml`.

3. W pliku `nav_header.xml` (`res/layout`) umieść:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
     xmlns:app="http://schemas.android.com/apk/res-auto"
     android:layout_width="match_parent"
     android:layout_height="200dp"
     android:background="#4CAF50"
     android:gravity="bottom"
     android:orientation="vertical"
     android:paddingLeft="15dp"
     android:paddingBottom="15dp">

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        app:srcCompat="@mipmap/ic_launcher" />


    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Title"
        android:textColor="#FFFFFF"
        android:textStyle="bold" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="This is our navigation drawer"
        android:textColor="#FFFFFF" />
   </LinearLayout>
   ```

4. W pliku `content_main.xml` dodaj FrameLayout tak jak poniżej. AppBarLayout to grupa widoków, najczęściej używana do zawijania paska narzędzi, który zapewnia wiele funkcji.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
     xmlns:app="http://schemas.android.com/apk/res-auto"
     android:layout_width="match_parent"
     android:layout_height="match_parent"
     android:orientation="vertical">

     <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <androidx.appcompat.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light">

            <FrameLayout
                android:id="@+id/main_fragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />

        </androidx.appcompat.widget.Toolbar>

     </com.google.android.material.appbar.AppBarLayout>

     <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="This is my content area." />
     </LinearLayout>

   </LinearLayout>
   ```

5. W pliku `activitiy_main.xml` zawrzyj:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.drawerlayout.widget.DrawerLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:id="@+id/drawer_layout"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       tools:context=".MainActivity">

       <include
           layout="@layout/content_main"
           android:layout_width="match_parent"
           android:layout_height="match_parent" />

       <com.google.android.material.navigation.NavigationView
           android:id="@+id/nav_view"
           android:layout_width="wrap_content"
           android:layout_height="match_parent"
           android:layout_gravity="start"
           android:fitsSystemWindows="true"
           app:headerLayout="@layout/nav_header"
           app:menu="@menu/nav_menu" />

   </androidx.drawerlayout.widget.DrawerLayout>
   ```

   Układy, które będą w Navigation Drawer muszą mieć układ DrawerLayout jako widok nadrzędny. Nasze inne kody projektu interfejsu użytkownika będą w tym zawarte.

   Na dole naszego DrawerLayout użyliśmy NavigationView, który generuje Navigation Drawer. Aby korzystać z tego widoku, zaimplementowaliśmy bibliotekę o nazwie `com.google.android.material:material`.

   NavigationView zawiera głównie dwie rzeczy, jedna jest headerLayout do projektowania sekcję nagłówka, a drugi to menu , które wyświetli wymienione opcje w naszej nawigacji. Aby zaprojektować układ nagłówka , utworzyliśmy plik zasobów układu `nav_header.xml` i plik zasobów menu `nav_menu.xml` do utworzenia opcji listy.

6. W pliku `nav_menu.xml` utwórzmy menu, które jest elementem głównym i może mieć podrzędne elementy podrzędne / podrzędne, takie jak element i grupa. Utwórz katalog `res/menu`, a w nim plik `nav_menu.xml`.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <menu xmlns:android="http://schemas.android.com/apk/res/android">
     <group>
        <item
            android:id="@+id/nav_profile"
            android:title="Profile" />

        <item
            android:id="@+id/nav_messages"
            android:icon="@android:drawable/btn_plus"
            android:title="Messages" />

        <item
            android:id="@+id/nav_friends"
            android:icon="@android:drawable/btn_plus"
            android:title="Friends" />
     </group>

     <item android:title="Account Settings">
        <menu>
            <item
                android:id="@+id/nav_update"
                android:icon="@android:drawable/btn_plus"
                android:title="Update Profile" />
            <item
                android:id="@+id/nav_logout"
                android:icon="@android:drawable/ic_dialog_alert"
                android:title="Sign Out" />
        </menu>
     </item>
   </menu>
   ```

   Dostosuj wygląd menu np. ikony do swoich potrzeb.

7. Na końcu wszystko złóżmy w jedną całość z `MainActivity.kt`.

   ```kotlin

   class MainActivity : NavigationView.OnNavigationItemSelectedListener, AppCompatActivity() {

    lateinit var toolbar: Toolbar
    lateinit var drawerLayout: DrawerLayout
    lateinit var navView: NavigationView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        toolbar = findViewById(R.id.toolbar)

        drawerLayout = findViewById(R.id.drawer_layout)
        navView = findViewById(R.id.nav_view)

        val toggle = ActionBarDrawerToggle(this, drawerLayout, toolbar, 0, 0)

        drawerLayout.addDrawerListener(toggle)
        toggle.syncState()
        navView.setNavigationItemSelectedListener(this)
    }

    override fun onNavigationItemSelected(item: MenuItem): Boolean {

        when (item.itemId) {
            R.id.nav_profile -> {
                Toast.makeText(this, "Profile clicked", Toast.LENGTH_SHORT).show()
            }
            R.id.nav_messages -> {
                Toast.makeText(this, "Messages clicked", Toast.LENGTH_SHORT).show()
            }
            R.id.nav_friends -> {
                Toast.makeText(this, "Friends clicked", Toast.LENGTH_SHORT).show()
            }
            R.id.nav_update -> {
                Toast.makeText(this, "Update clicked", Toast.LENGTH_SHORT).show()
            }
            R.id.nav_logout -> {
                Toast.makeText(this, "Sign out clicked", Toast.LENGTH_SHORT).show()
            }
        }
        drawerLayout.closeDrawer(GravityCompat.START)
        return true
    }
   }
   ```

8. Zamiast Toastów zaimplementuj Intenty do utworzonych przez siebie aktywności.

9. ActionBarDrawerToggle służy do wyświetlania wskaźnika szuflady na pasku aplikacji, który wymaga 5 argumentów.

   1. Kontekst bieżącej działalności
   2. Zmienna DrawerLayout
   3. Zmienna paska narzędzi
   4. Komunikat o otwarciu menu za pośrednictwem ciągu zasobów
   5. Komunikat o zamknięciu menu za pomocą ciągu zasobów

10. Następnie dodaliśmy ActionBarDrawerToggle do naszego widoku DrawerLayout i zsynchronizowaliśmy go. Teraz wyświetli wskaźnik menu na naszym pasku aplikacji.

11. Musimy również wykryć interakcję użytkownika z wymienionymi opcjami w Szufladzie nawigacji. w tym celu będziemy musieli zaimplementować słuchacza w naszej klasie za pomocą NavigationView.OnNavigationItemSelectedListener, pozwoli to nam przesłonić funkcję onNavigationItemSelected (item: MenuItem). Wewnątrz tej funkcji możemy ustawić, co się stanie po kliknięciu której opcji.

12. Zadanie: Zaprojektuj aplikację zawierającą wszystkie poznane dotąd elementy w Android Studio.
