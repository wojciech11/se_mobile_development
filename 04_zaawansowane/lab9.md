# RecyclerView i  SharedPreferences

Zalecane: API 30.

## RecyclerView:

Wyświetlanie elementów w siatkach jest obecnie bardzo powszechnym wzorcem w aplikacjach mobilnych. Użytkownik widzi kolekcję elementów i może je przewijać. Platforma Android udostępnia narzędzie zwane RecyclerView, które jest następcą ListView. W porównaniu z ListView jest bardziej zaawansowane i elastyczne. ReyclerView za pomocą adaptera bierze widoki (np. CardView) oraz dane i łączy je wykorzystując ViewHolder. ViewHolder jak nazwa wskazuje jest odpowiedzialny za przechowywanie widoku i łączenie danych.

Więcej na: [developer.android.com/develop/ui/views/layout/recyclerview](https://developer.android.com/develop/ui/views/layout/recyclerview)

### Zadanie

1. Utwórz nasz nowy projekt o nazwie *RecyclerViewApp*, zacznij pustej aktywności.

2. Dodaj znależności do `gradle.build`:

   ```gradle
   implementation 'androidx.recyclerview:recyclerview:1.2.1'
   implementation 'androidx.cardview:cardview:1.0.0'
   ```

3. Będziemy jeszcze potrzebowali zdjęcie kota, można pobrać zdjęcie z https://en.wikipedia.org/wiki/Siberian_cat lub skorzystać z google-a. Pobrane zdjęcie przekopuj do `res/drawable/` i zmień nazwę na `cat` niezmieniając rozszerzenia pliku.

4. Layout dla naszej głównej aktywności w `activity_main.xml`:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

5. Utwórz nowy plik w katalogu `layout` o nazwie `photo_layout.xml`, który będzie składał się z siatki i przedstawiał karty ze zdjęciami kotów.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="10dp"
    app:cardBackgroundColor="@android:color/white"
    app:cardCornerRadius="5dp"
    app:cardElevation="8dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <ImageView
            android:id="@+id/image"
            android:layout_width="match_parent"
            android:layout_height="150dp"
            android:scaleType="fitXY"
            android:src="@color/design_default_color_surface" />

        <TextView
            android:id="@+id/title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginLeft="14dp"
            android:layout_marginRight="10dp"
            android:text="Title"
            android:textColor="@android:color/black"
            android:textSize="21dp" />

        <TextView
            android:id="@+id/desc"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginLeft="14dp"
            android:layout_marginRight="10dp"
            android:layout_marginBottom="10dp"
            android:text="Desc"
            android:textSize="18dp" />
    </LinearLayout>
   </androidx.cardview.widget.CardView>
   ```

6. Dodaj nowy plik o nazwie `DataModel.kt` z zadeklarowanymi zmiennymi : title, desc, image:

   ```kotlin
   package com.example.recyclerviewapp

   data class MyDataModel(
       var title: String,
       var desc: String,
       var image: Int
   )
   ```

7. Następnie zainicjuj adapter tworząc nową klasę o nazwie `PhotoAdapter`:

   ```kotlin
   class PhotoAdapter(var context: Context) : RecyclerView.Adapter<PhotoAdapter.ViewHolder>() {
    var dataList = emptyList<MyDataModel>()
    internal fun setDataList(dataList: List<MyDataModel>) {
        this.dataList = dataList
    }

    //Tworzymy ViewHolder odpowiadający za bezpośrednie odniesienie do każdego z widoków z elementami danych
    class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        var image: ImageView

        var title: TextView
        var desc: TextView

        init {
            image = itemView.findViewById(R.id.image)
            title = itemView.findViewById(R.id.title)
            desc = itemView.findViewById(R.id.desc)
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): PhotoAdapter.ViewHolder {
        // tworzymy uklad
        var view = LayoutInflater.from(parent.context).inflate(
            R.layout.photo_layout, parent,
            false
        )
        return ViewHolder(view)
    }

    // wypełniamy danymi przez bindowanie
    override fun onBindViewHolder(holder: PhotoAdapter.ViewHolder, position: Int) {
        //pobieramy dane na podstawie pozycji
        var data = dataList[position]

        // ustawiamy widoki elementów na podstawie modelu danych
        holder.title.text = data.title
        holder.desc.text = data.desc
        holder.image.setImageResource(data.image)
    }

    // łączna liczba elementów
    override fun getItemCount() = dataList.size
   }
   ```


8. W głównej aktywności tworzymy w pliku `MainActivity.kt`:

   ```kotlin
   class MainActivity : AppCompatActivity() {

    private lateinit var photoAdapter: PhotoAdapter
    private var dataList = mutableListOf<MyDataModel>()


    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        var recyclerView = findViewById<RecyclerView>(R.id.recyclerView)

        //inicjujemy siatkę z 2 polami w rzędzie
        recyclerView.layoutManager = GridLayoutManager(applicationContext,2)
        photoAdapter = PhotoAdapter(applicationContext)
        recyclerView.adapter = photoAdapter

        //dodajemy dane
        dataList.add(MyDataModel("Kot","Rudy",R.drawable.cat))
        dataList.add(MyDataModel("Kot","Srebny",R.drawable.cat))
        dataList.add(MyDataModel("Kot","Rudy",R.drawable.cat))
        dataList.add(MyDataModel("Kot","Rudy",R.drawable.cat))

        photoAdapter.setDataList(dataList)

    }
   }
   ```

## SharedPreferences

[SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences) czyli preferencje wspólne umożliwiają aplikacjom przechowywanie w nich danych i pobieranie danych w postaci kluczy i wartości. Dane przechowywane są do czasu usunięcia przez użytkownika. Mechanizm SharedPrefernces zezwala między innymi na to, że z danych które przechowuje mogą korzystać wszystkie komponenty aplikacji, a co najważniejsze po zamknięciu i ponownym uruchomieniu naszego programu mamy w dalszym ciągu dostęp do zapisanych informacji.


Aby wykorzystać `SharedPreferences` musimy je zainicjować.

```kotlin
val sharedPreferences: SharedPreferences = this.getSharedPreferences(sharedPrefFile, Context.MODE_PRIVATE)
```

Zapis danych za pomocą `SharedPreferences`:

```kotlin
editor.putInt("age_key", age)
editor.putString("nameFirst_key", namefirst)

editor.putString("nameLast_key", namelast)
editor.apply()
editor.commit()
```

Pobranie danych za pomocą SharedPreferences:

```kotlin
outputFirstName.setText(sharedNameFirstValue).toString() outputLastName.setText(sharedNameLastValue).toString() outputAge.setText(sharedAgeValue.toString())
```



Plik Shared Preferences znajduje się w katalogu data/data/{application package}/share_prefs. Aby uzyskać dostęp do SharedPreferences, skorzystać z pomocy jednej z następujących metod.

```kotlin
getPreferences()
getSharedPreferences()
getDefaultSharedPreferences()
val sharedPreferences: SharedPreferences = this.getSharedPreferences(String preferences_fileName,int mode)
```

Tutaj preference_file_name jest nazwą wspólnego pliku preferencji, a format pliku to tzw. tryb operacyjny.


Aby usunąć dane preferencji aplikacji, wywołujemy następujące metody:

- `editor.remove("klucz")` - usuwa określone kluczowe wartości
- `edytor.clear()` - usuwa wszystkie dane preferencji

Więcej na [developer.android.com/training/data-storage/shared-preferences](https://developer.android.com/training/data-storage/shared-preferences).

### Zadanie

W tym przykładzie pobierzemy dane wejściowe (imię, nazwisko i wiek) z pola `EditText` i zapiszemy je w pliku preferencji. Po kliknięciu przycisku możemy pobrać i wyświetlić dane w `TextView`.

1. Utwórzmy nowy projekt o nazwie *SharedPreferencesTest* jako pustą aktywność.

2. Wprowadź następujący layout dla głównej aktywności:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
     xmlns:app="http://schemas.android.com/apk/res-auto"
     xmlns:tools="http://schemas.android.com/tools"
     android:layout_width="match_parent"
     android:layout_height="match_parent"
     tools:context=".MainActivity">

     <TableLayout
         android:layout_width="match_parent"
         android:layout_height="match_parent"
         android:layout_marginStart="8dp"
         android:layout_marginTop="8dp"
         android:layout_marginEnd="8dp"
         android:layout_marginBottom="8dp"
         app:layout_constraintBottom_toBottomOf="parent"
         app:layout_constraintEnd_toEndOf="parent"
         app:layout_constraintStart_toStartOf="parent"
         app:layout_constraintTop_toTopOf="parent">

         <TableRow>

             <TextView
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="0"
                 android:layout_marginLeft="10sp"
                 android:text="First Name"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />


             <EditText
                 android:id="@+id/firstName"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="1"
                 android:layout_marginStart="50sp"
                 android:layout_marginLeft="50sp"
                 android:hint="First Name"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />
         </TableRow>


         <TableRow>

             <TextView
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="0"
                 android:layout_marginStart="10sp"
                 android:layout_marginLeft="10sp"
                 android:text="Last Name"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />


             <EditText
                 android:id="@+id/lastName"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="1"
                 android:layout_marginStart="50sp"
                 android:layout_marginLeft="50sp"
                 android:hint="Last Name"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />


         </TableRow>

         <TableRow>

             <TextView
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="0"
                 android:layout_marginStart="10sp"
                 android:layout_marginLeft="10sp"
                 android:text="Enter Age"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />

             <EditText
                 android:id="@+id/editAge"
                 android:layout_width="201dp"
                 android:layout_height="wrap_content"
                 android:layout_column="1"
                 android:layout_marginStart="50sp"
                 android:layout_marginLeft="50sp"
                 android:hint="Age"

                 android:inputType="number"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />
         </TableRow>


         <TableRow android:layout_marginTop="60dp">

             <TextView
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="0"
                 android:layout_marginStart="10sp"
                 android:layout_marginLeft="10sp"

                 android:text="Your Age"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />

             <TextView
                 android:id="@+id/textViewShowAge"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="1"
                 android:layout_marginStart="50sp"
                 android:layout_marginLeft="50sp"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />
         </TableRow>

         <TableRow android:layout_marginTop="20dp">

             <TextView
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="0"
                 android:layout_marginStart="10sp"
                 android:layout_marginLeft="10sp"
                 android:text="First Name"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />


             <TextView
                 android:id="@+id/textViewShowFirstName"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="1"
                 android:layout_marginStart="50sp"
                 android:layout_marginLeft="50sp"


                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />
         </TableRow>

         <TableRow android:layout_marginTop="20dp">

             <TextView
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="0"
                 android:layout_marginStart="10sp"
                 android:layout_marginLeft="10sp"
                 android:text="Last Name"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />

             <TextView
                 android:id="@+id/textViewShowLastName"
                 android:layout_width="wrap_content"
                 android:layout_height="wrap_content"
                 android:layout_column="1"
                 android:layout_marginStart="50sp"
                 android:layout_marginLeft="50sp"
                 android:textAppearance="@style/Base.TextAppearance.AppCompat.Medium" />
         </TableRow>

         <TableRow android:layout_marginTop="20dp">

             <Button
                 android:id="@+id/save"
                 android:layout_width="match_parent"
                 android:layout_height="match_parent"
                 android:text="Save" />

             <Button
                 android:id="@+id/view"
                 android:layout_width="match_parent"
                 android:layout_height="match_parent"
                 android:text="View" />
         </TableRow>

         <TableRow android:layout_marginTop="20dp">
             <Button
                 android:id="@+id/clear"
                 android:layout_width="match_parent"
                 android:layout_height="match_parent"
                 android:text="Clear" />
         </TableRow>

     </TableLayout>


   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

3. W pliku `MainActivity.kt` musimy zadeklarować używanie `SharedPreferences`, dodawanie wartości do przechowywania oraz możliwość usunięcia tych danych.

  ```kotlin
  class MainActivity : AppCompatActivity() {

    val sharedPrefFile = "kotlinsharedpreference"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)


        val inputAge = findViewById<EditText>(R.id.editAge)
        val inputFirstName = findViewById<EditText>(R.id.firstName)

        val inputLastName = findViewById<EditText>(R.id.lastName)

        val outputAge = findViewById<TextView>(R.id.textViewShowAge)
        val outputFirstName = findViewById<TextView>(R.id.textViewShowFirstName)
        val outputLastName = findViewById<TextView>(R.id.textViewShowLastName)


        val btnSave = findViewById<Button>(R.id.save)
        val btnView = findViewById<Button>(R.id.view)
        val btnClear = findViewById<Button>(R.id.clear)


        //deklaracja sharedpreferences
        val sharedPreferences: SharedPreferences =
            this.getSharedPreferences(sharedPrefFile, Context.MODE_PRIVATE)

        btnSave.setOnClickListener(View.OnClickListener {
            val age = Integer.parseInt(inputAge.text.toString())
            val namefirst: String = inputFirstName.text.toString()
            val namelast: String = inputLastName.text.toString()
            val editor = sharedPreferences.edit()
            editor.putInt("age_key", age)
            editor.putString("nameFirst_key", namefirst)
            editor.putString("nameLast_key", namelast)
            editor.apply()
            editor.commit()
        })


        btnView.setOnClickListener {
            val sharedAgeValue = sharedPreferences.getInt("age_key", 0)
            val sharedNameFirstValue = sharedPreferences.getString("nameFirst_key", "Stan")
            val sharedNameLastValue = sharedPreferences.getString("nameLast_key", "Cortez")
            if (sharedAgeValue.equals(0) && sharedNameFirstValue.equals("Stan")) {
                outputFirstName.setText("First Name: ${sharedNameFirstValue}").toString()
                outputAge.setText("default Age: ${sharedAgeValue.toString()}")
            } else if (sharedAgeValue.equals(0) && sharedNameLastValue.equals("Cortez")) {
                outputLastName.setText("Last Name: ${sharedNameLastValue}").toString()
                outputAge.setText("default Age: ${sharedAgeValue.toString()}")
            } else if (sharedAgeValue.equals(0) && sharedNameFirstValue.equals("Stan") && sharedNameLastValue.equals(
                    "Cortez"
                )
            ) {
                outputFirstName.setText("First Name: ${sharedNameFirstValue}").toString()
                outputLastName.setText("Last Name: ${sharedNameLastValue}").toString()
                outputAge.setText("default Age: ${sharedAgeValue.toString()}")
            } else {
                outputFirstName.setText(sharedNameFirstValue).toString()
                outputLastName.setText(sharedNameLastValue).toString()
                outputAge.setText(sharedAgeValue.toString())
            }
        }

        //funkcja odpowiadająca za czyszczenie
        btnClear.setOnClickListener(View.OnClickListener {
            val editor = sharedPreferences.edit()
            editor.clear()
            editor.apply()
            outputFirstName.setText("").toString()
            outputLastName.setText("").toString()
            outputAge.setText("")
        })
    }
  }
  ```

4. W jaki sposób usunąć trwale dane zapisane w SharedPreferences? Czy można je zapisać do pliku tekstowego? Jeśli tak to w jaki sposób? Podaj przykładowe rozwiązanie oraz zaimplementuj je w tej aplikacji. W jaki sposób sprawdzisz czy dane wpisane w tej aplikacji mogą zostać przesłane do innej?
