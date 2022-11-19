# Google Firebase Realtime Database

Zalecane: API 30.

## Wprowadzenie

Dzięki Firebase możemy dodawać do aplikacji takie funkcje, jak uwierzytelnianie użytkowników, baza danych, przechowywanie plików, powiadomienia i inne funkcje bez konieczności zarządzania serwerem i dodawania innych aplikacji.
Firebase Realtime Database to hostowana w chmurze baza danych NoSQL, która umożliwia przechowywanie i synchronizowanie danych między użytkownikami w czasie rzeczywistym za pomocą technologii JSON. Synchronizacja w czasie rzeczywistym ułatwia użytkownikom dostęp do danych z dowolnego urządzenia oraz z dowolnej technologii strony internetowej czy aplikacji mobilnej. Gdy aplikacje przechodzą w tryb offline, bazy danych czasu rzeczywistego używają lokalnej pamięci podręcznej na urządzeniu do obsługi i przechowywania zmian. Gdy urządzenie przechodzi do trybu online, dane lokalne są automatycznie synchronizowane.

Informacje: https://firebase.google.com/docs/database/

Więcej: https://firebase.google.com/docs/database/android/read-and-write

## Zadanie (WIP)

1. W aplikacji Android Studio wybierz pustą aktywność Empty Activity oraz nazwij projekt *FirebaseCrudApp*.

2. Po uruchomieniu projektu z zakładki Tools wybierz Firebase. Pojawi się okienko Asystenta Firebase.

3. W okienku Asystenta Firebase wybierz Realtime Database a następnie Connect to Firebase. Dodatkowo w tym oknie Android Studio wyświetla krótki opis kroków, które należy przeprowadzić.

4. Konsola Firebase uruchomi się w domyślnej przeglądarce. Na stronie głównej konsoli kliknij Dodaj nowy lub wybierz istniejący projekt.

5. Pamiętaj, by w pliku manifestu dodać zezwolenie na połączenie z Internetem.

   ```xml
   <uses-permission android:name="android.permission.INTERNET" />
   ```

6. W okienku Asystenta Firebase kliknij przycisk Dodaj zestaw SDK uwierzytelniania Firebase do aplikacji. Spowoduje to dodanie niezbędnej wtyczki Firebase do twojego projektu. Zaakceptuj następnie okno dialogowe potwierdzenia edycji `build.gradle`, aby kontynuować.

7. Po podłączeniu projektu Android Studio do projektu Firebase, w panelu Firebase kliknij "Baza danych", a następnie w sekcji Baza danych w czasie rzeczywistym kliknij "Utwórz bazę danych". Wybierz opcję "Rozpocznij w trybie testowym" i kliknij włącz.

8. W zasadach bazy danych dodaj zapis zezwalający na odczyt i zapis do bazy danych:

   ```json
   {
   "rules": {
      ".read": true,
     ".write": true
     }
   }
   ```

### Zapis/wstawianie danych do Firebase Realtime Database

Dane Firebase są zapisywane w odwołaniu FirebaseDatabase i pobierane przez dołączenie asynchronicznego detektora do odwołania. Detektor jest wyzwalany raz dla początkowego stanu danych i ponownie za każdym razem, gdy dane się zmieniają.

Do odczytu lub zapisu danych z bazy danych potrzebujemy instancji `DatabaseReference`:


```kotlin
private lateinit var firebaseDatabaseReference: DatabaseReference

firebaseDatabaseReference = FirebaseDatabase.getInstance().reference
```

W przypadku podstawowych operacji zapisu musimy użyć metody setValue() w celu zapisania danych w określonym odwołaniu, zastępując wszelkie istniejące dane w tej ścieżce.

```kotlin
private lateinit var firebaseDatabaseReference: DatabaseReference

firebaseDatabaseReference = FirebaseDatabase.getInstance().reference

dataReference = firebaseDatabaseReference dataReference.child("node_name").setValue(@Nullable Object value)
```

Dane Firebase są pobierane przez dołączenie asynchronicznego detektora do odwołania do bazy danych Firebase. Detektor jest wyzwalany raz dla początkowego stanu danych i ponownie za każdym razem, gdy dane się zmieniają.

Aby odczytać dane na ścieżce i nasłuchiwać zmian, należy użyć metody `addValueEventListener()` lub `addListenerForSingleValueEvent()` w celu dodania `ValueEventListener` do `DatabaseReference`.

Możemy użyć metody `onDataChange()` do odczytania statycznej migawki zawartości na danej ścieżce, tak jak istniała ona w czasie zdarzenia. Ta metoda jest wyzwalana raz, gdy słuchacz jest podłączony i ponownie za każdym razem, gdy dane, w tym dzieci, ulegają zmianie. Wywołanie zwrotne zdarzenia jest przekazywane jako migawka zawierająca wszystkie dane w tej lokalizacji, w tym dane podrzędne. Jeśli nie ma danych, migawka zwróci wartość false po wywołaniu metody `exists()` i wartość null po wywołaniu metody `getValue()`.

### Usuwanie danych z bazy danych Firebase Realtime Database

Najprostszym sposobem usunięcia danych jest wywołanie metody `removeValue()` w odniesieniu do lokalizacji tych danych.

Można również usunąć, określając null jako wartość dla innej operacji zapisu, takiej jak `setValue()` lub `updateChildren()`. Tej techniki można użyć w metodzie updateChildren() w celu usunięcia wielu elementów podrzędnych w jednym wywołaniu interfejsu API.

### Przechowywanie danych w trybie offline w bazie danych w czasie rzeczywistym

Firebase zapewnia doskonałe wsparcie, jeśli chodzi o dane offline. Automatycznie przechowuje dane w trybie offline, gdy nie ma połączenia z Internetem. Gdy urządzenie połączy się z Internetem, wszystkie dane zostaną przesłane do bazy danych w czasie rzeczywistym. Jednak włączenie trwałości danych powoduje przechowywanie danych w trybie offline, nawet po ponownym uruchomieniu aplikacji. Trwałość danych można włączyć, wywołując poniższy kod jednowierszowy

```kotlin
Firebase.database.setPersistenceEnabled(true)
```
### Dalsza praca z aplikacją. Dodajemy aktywności do projektu

1. W kolejnym kroku pracy nad aplikacją w pliku `activity_main.xml` dodaj widżety TextView, Button oraz EditText. Pomocniczny kod poniżej:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/edt_input"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="50dp"
        android:hint="message"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/btn_submit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="48dp"
        android:text="submit"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.755"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/edt_input" />

    <TextView
        android:id="@+id/txt_display"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

2. W pliku `MainActivity.kt` dodaj poniższy kod:

   ```kotlin
   class MainActivity : AppCompatActivity(), View.OnClickListener {
    lateinit var myRef: DatabaseReference
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContentView (R.layout.activity_main)
        val database = Firebase.database
        myRef = database.getReference("message")
        myRef.setValue("My First Realtime Database App")

        btn_submit.setOnClickListener (this)

        // Odczyt danych
        myRef.addValueEventListener(object : ValueEventListener {
            override fun onDataChange(dataSnapshot: DataSnapshot) {
                val value = dataSnapshot.getValue()
                txt_display.setText("Pobrano tekst: $value")
            }

            override fun onCancelled(error: DatabaseError) {
                Log.w("Status", "Błąd pobrania danych.", error.toException())
            }
        })
    }

    override fun onClick(v: View?) {
        when (v!!.id) {
            R.id.btn_submit -> {
                myRef.setValue(edt_input.text.toString())
            }
        }
    }
   }
   ```

3. Prześledźmy kod:

   Tworzymy obiekt referencji do bazy danych Firebase lateinit var myRef : DatabaseReference

   Tworzymy obiekt bazy danych:

   ```kotlin
   val database = Firebase.database
   ```

   Ustawiamy klucz dla danych pobranych z bazy.

   ```kotlin
   myRef = database.getReference("message")
   ```

   Ustawiamy wartości klucza

   ```kotlin
   myRef.setValue("My First Realtime Database App")
   ```

4. W najprostszej implementacji dodaliśmy pole message do bazy danych, do której możemy przekazywać informację i ją wyświetlać.

5. W dalszej części pracy nad aplikację należy samodzielnie dodać metodę dodającą kilka wiadomości, wyświetlającą wszystkie wiadomości i usuwającą wybraną wiadomość.
