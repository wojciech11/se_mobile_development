# ViewModel i MVVM

Zalecane: API 30.

## Architektura aplikacji

Patrz [dokumentacja](https://developer.android.com/topic/architecture) oraz [MVVM](https://www.digitalocean.com/community/tutorials/android-mvvm-design-pattern).

## ViewModel

[ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel) to klasa, która jest przeznaczona do przechowywania i zarządzania UI-podobnych danych w cyklu życia kontrolerów interfejsu użytkownika w świadomy sposób.

<p align="center">
<img src="https://developer.android.com/static/images/topic/architecture/ui-layer/udf.png" width="40%"/><br />
<small>Źródło: <a href="https://developer.android.com/topic/architecture/ui-layer/stateholders">developer.android.com/topic/architecture/ui-layer/stateholders</a></small>
</p>

## Zadanie 1

Na poprzednich zajęciach, pokazywaliśmy jak komunikować się między fragmentami z użyciem *Fragment Result API* (patrz [dokumentacja](https://developer.android.com/guide/fragments/communicate)). Teraz zobaczymy jak możemy uzyskać taki sam efekt korzystając z `ViewModel`.

1. Utwórz nowy projekt o nazwie *FragmentsCommViewModel* jako pustą aktywność.


2. Przygotujmy layout do umieszczenia dwóch fragmentów (potem dopracujemy design) w `activity_main.xml`.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <FrameLayout
        android:id="@+id/fragmentOne"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <FrameLayout
        android:id="@+id/fragmentTwo"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

3. Utwórz dwa layouts dla naszych fragmentów:

   - `fragment_one.xml`:

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
          android:layout_width="match_parent"
          android:layout_height="match_parent"
          android:gravity="center"
          android:orientation="vertical"
          android:padding="8dp">

          <EditText
              android:id="@+id/editText"
              android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:layout_marginTop="50dp"
              android:hint="Enter your message"
              android:textColorHint="@android:color/background_dark"
              android:textSize="16sp"
              android:textStyle="bold" />

          <Button
              android:id="@+id/btnSend"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:background="@android:color/holo_blue_dark"
              android:padding="10dp"
              android:text="Send Message"
              android:textColor="@android:color/white"
              android:textStyle="bold" />
      </LinearLayout>
      ```

   - `fragment_two.xml`:

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
     <LinearLayout
         xmlns:android="http://schemas.android.com/apk/res/android"
         android:layout_width="match_parent"
         android:layout_height="match_parent"
         android:gravity="center"
         android:orientation="vertical">

         <TextView
             android:id="@+id/outPutTextView"
             android:layout_width="wrap_content"
             android:layout_height="wrap_content"
             android:gravity="center"
             android:textColor="@android:color/background_dark"
             android:textSize="24sp"
             android:textStyle="bold" />
     </LinearLayout>
     ```

4. Wykorzystajmy [ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel) do dzielenia danymi, utwórz plik kotlin z klasą `ItemViewModel` oraz wpisz następujący kod:

   ```kotlin
   class ItemViewModel : ViewModel() {
    private val mutableText = MutableLiveData<String>()
    val outputText: LiveData<String> get() = mutableText

    fun setText(txt: String) {
        mutableText.value = txt
    }
   }
   ```

   Wykorzystujemy tutaj [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) i [MutableLiveData](https://developer.android.com/reference/android/arch/lifecycle/MutableLiveData).

5. W `build.gradle` (dla **API 30**), w sekcji *dependencies* dodaj wymagają bibliotkę ktx (rozszerzenie, więcej w [dokumentacji](https://developer.android.com/kotlin/ktx/extensions-list)):

   ```java
   dependencies {
     // ...
     implementation 'androidx.activity:activity-ktx:1.5.1'
     implementation 'androidx.fragment:fragment-ktx:1.5.4'
     // ...
   }
   ```

   Proszę nie zapomnieć Synch Gradle, żeby Gradle mógł pobrać biblioteki.

5. Zacznijmy od naszej głównej aktywności `MainActivity`, aby utworzyć instancje naszego modelu do współdzielenia go między fragmentami:

   ```kotlin
   class MainActivity : AppCompatActivity() {


       private val viewModel: ItemViewModel by viewModels()

       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)

           supportFragmentManager.beginTransaction().replace(
               R.id.fragmentOne,
               FragmentOne()
           ).commit()

           supportFragmentManager.beginTransaction().replace(
               R.id.fragmentTwo,
               FragmentTwo()
           ).commit()

           viewModel.outputText.observe(this) { _ ->
               // Perform an action with the latest item data
           }
       }
   }
   ```

6. Teraz czas na zaimplementowanie fragmentów:

    - Dla `FragmentOne.kt`, z tego fragmentu będziemy wysyłać wprowadzony tekst:

      ```kotlin
      class FragmentOne : Fragment() {

          // Using the activityViewModels() Kotlin property delegate from the
          // fragment-ktx artifact to retrieve the ViewModel in the activity scope
          private val viewModel: ItemViewModel by activityViewModels()

          override fun onCreateView(
              inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
          ): View? {
              val rootView = inflater.inflate(R.layout.fragment_one, container, false) as
                      ViewGroup

              val editTextView = rootView.findViewById<TextView>(R.id.editText)

              val btn = rootView.findViewById<Button>(R.id.btnSend)
              btn.setOnClickListener {
                  val textToPass = editTextView?.text.toString()
                  Log.d("FragmentOne", textToPass)

                  viewModel.setText(textToPass)
              }
              return rootView
          }
      }
      ```

   - Dla `FragmentTwo.kt`, tutaj odbieramy i wypisujemy na ekran:

     ```kotlin
     class FragmentTwo : Fragment() {

         // Using the activityViewModels() Kotlin property delegate from the
         // fragment-ktx artifact to retrieve the ViewModel in the activity scope
         private val viewModel: ItemViewModel by activityViewModels()

         override fun onCreateView(
             inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
         ): View? {
             val rootView = inflater.inflate(R.layout.fragment_two, container, false)

             viewModel.outputText.observe(viewLifecycleOwner) { inputText ->
                 // Update the selected filters UI
                 val tv = rootView.findViewById<TextView>(R.id.outPutTextView)
                 tv.text = inputText
             }
             return rootView
         }

     }
     ```

8. Uruchom aplikację, po przetestowaniu zmień layout, aby na siebie oba fragmenty nie nachodziły.

## Zadanie 2

Przegląd przez dostępne data-storage na Androidzie, patrz [dokumentacja](https://developer.android.com/training/data-storage) + zewnętrzne, e.g., [Google Firebase](https://firebase.google.com/) lub oferty w ramach backend-as-a-service (np., [Contentful](https://www.contentful.com/r/knowledgebase/mobile-backend-as-a-service/), [AWS Amplify](https://aws.amazon.com/amplify/)).
