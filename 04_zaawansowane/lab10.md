# Firebase Auth - Konfiguracja konta oraz usługi logowania (dodatkowe)

Dzięki Firebase możemy dodawać do aplikacji takie funkcje, jak uwierzytelnianie użytkowników, baza danych, przechowywanie plików, powiadomienia i inne funkcje bez konieczności zarządzania serwerem i dodawania innych aplikacji.
Dzięki Firebase możemy zezwolić użytkownikom na logowanie się do aplikacji przy użyciu wielu dostawców. Uwierzytelnianie Firebase obsługuje uwierzytelnianie za pomocą poczty e-mail i hasła, numeru telefonu, konta Google, Facebooka, Twittera i innych.

Oficjalna dokumentacja: [firebase.google.com/docs/auth/android/start](https://firebase.google.com/docs/auth/android/start).

## Zadanie

### Tworzymy nowy projekt

1. W aplikacji Android Studio wybierz pustą aktywność *Empty Activity* oraz nazwij projekt *FirebaseLoginApp*.

2. Po uruchomieniu projektu z zakładki Tools wybierz Firebase. Pojawi się okienko Asystenta Firebase.

### Konfiguracja Integracji z Firebase

W okienku Asystenta Firebase wybierz *Authentication*, potem *Authenticate with Google*, a następnie *Connect to Firebase*. Dodatkowo w tym oknie Android Studio wyświetla krótki opis kroków, które należy przeprowadzić.

1. Wybierz: *(1) Connect Your app to Firebase* -> utwórz projekt -> connect;
2. W *(2)* dodaj SDK na poziomie projektu;
3. W *(3)* w drugim punkcie, wybierz link w *Google in the [Sign-in method] tab of*
4. Otworzy się okno przeglądarki, naciśniej *Get started*, oraz wybierz *Email/password*


### Aplikacja

1. Pamiętaj, by w pliku manifestu (`manifests/AndroidManifest.xml`) dodać zezwolenie na połączenie z Internetem.

   ```xml
   <uses-permission android:name="android.permission.INTERNET" />
   ```

2. Najpierw layout dla `activity_registration.xml`:

   ```kotlin
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".RegistrationActivity">

    <EditText
        android:id="@+id/email_edit_text"
        android:layout_width="0dp"
        android:layout_height="wrap_content"


        android:layout_marginTop="32dp"
        android:ems="10"
        android:hint="Enter email"
        android:inputType="text"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/password_edit_text"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:ems="10"
        android:hint="Enter password"
        android:inputType="textPassword"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/email_edit_text" />

    <EditText
        android:id="@+id/re_password_edit_text"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:ems="10"
        android:hint="Repeat password"
        android:inputType="textPassword"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/password_edit_text" />

    <Button
        android:id="@+id/reg_button"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:onClick="registerUser"
        android:text="Register"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/re_password_edit_text" />
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

3. W `activity_login.xml`, dodamy analogicznie ten sam zestaw 2 EditText oraz Button. Układ może wyglądać tak:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto" xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent" android:layout_height="match_parent" android:padding="16dp" tools:context=".LoginActivity">

    <EditText android:id="@+id/login_email_edit_text" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_marginTop="32dp" android:ems="10"
        android:hint="Enter email" android:inputType="text" app:layout_constraintEnd_toEndOf="parent" app:layout_constraintStart_toStartOf="parent" app:layout_constraintTop_toTopOf="parent" />
    <EditText
        android:id="@+id/login_password_edit_text" android:layout_width="0dp"
        android:layout_height="wrap_content" android:layout_marginTop="24dp"
        android:ems="10"
        android:hint="Enter password"
        android:inputType="textPassword" app:layout_constraintEnd_toEndOf="parent" app:layout_constraintStart_toStartOf="parent" app:layout_constraintTop_toBottomOf="@+id/login_email_edit_text" />
    <Button
        android:id="@+id/login_button"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:onClick="loginUser"
        android:text="Login"
        app:layout_constraintEnd_toEndOf="parent" app:layout_constraintStart_toStartOf="parent" app:layout_constraintTop_toBottomOf="@+id/login_password_edit_text" />
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

4. Teraz, gdy mamy wszystkie ekrany, których potrzebujemy do naszej aplikacji, połączmy je ze sobą. W pliku xml dla MainActivity utwórz 2 buttony oraz pole TextView.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/launch_login_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:onClick="gotoLogin"
        android:text="Login"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textView" />

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:text="Firebase Cat"
        android:textAppearance="@style/TextAppearance.AppCompat.Large"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/launch_reg_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:onClick="gotoReg"
        android:text="Register"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/launch_login_button" />
   </androidx.constraintlayout.widget.ConstraintLayout>
   ```

5. Następnie w pliku `MainActivity.kt` dodaj poniższe funkcje:

   ```kotlin
   public fun gotoLogin(view: View) {
       startActivity(Intent(this, LoginActivity::class.java))
   }
   public fun gotoReg(view: View) {
       startActivity(Intent(this, RegistrationActivity::class.java))
   }
   ```

   Utwórz brakujące klasy `LoginActivity` i `RegistrationActivity`, powinny rozszerzać `AppCompatActivity()`.

6. Teraz dodajmy uwierzytelnienie w pliku `RegistrationActivity.kt` i dodaj następujący kod:

   ```kotlin
   class RegistrationActivity : AppCompatActivity() {

    public fun registerUser(view: View) {
        var email: String = findViewById<EditText>(R.id.email_edit_text).text.toString()
        var password: String = findViewById<EditText>(R.id.password_edit_text).text.toString()

        auth.createUserWithEmailAndPassword(email, password).addOnCompleteListener { task ->
            if (task.isSuccessful) {
                Toast.makeText(this, "Registration Successful", Toast.LENGTH_SHORT).show()
                startActivity(Intent(this, CatActivity::class.java))
            } else {
                Toast.makeText(this, "An error occurred", Toast.LENGTH_SHORT).show()
            }
        }
    }
   }
   ```



7. Następnie dodajmy funkcję logowania do naszej aplikacji. Aby to zrobić, otwórz plik `LoginActivity.kt` i dodaj następujący kod:

   ```kotlin
   class LoginActivity : AppCompatActivity() {

       fun loginUser(view: View) {
           val email: String = findViewById<EditText>(R.id.login_email_edit_text).text.toString()
           val password: String = findViewById<EditText>(R.id.login_password_edit_text).text.toString()

           auth.signInWithEmailAndPassword(email, password).addOnCompleteListener { task ->
               if (task.isSuccessful) {
                   Toast.makeText(this, "Login successful", Toast.LENGTH_SHORT)
                       .show()
                   startActivity(Intent(this, CatActivity::class.java))
               } else {
                   Toast.makeText(
                       this, "Unable to login. Check your input or try again later",
                       Toast.LENGTH_SHORT
                   ).show()
               }
           }
       }
   }
   ```

   Ta metoda wywołuje metodę signInWithEmailAndPassword() uwierzytelniania Firebase.

8. Utwórz dodatkowo aktywność `CatActivity`, która będzie wywoływana po poprawnym
   zalogowaniu. Aplikacja ta będzie przedstawiała śmieszny mem z kotem bądź film.

9. Do ekranów logowania i rejestracji dodaj własne logo aplikacji nad formularzem.

10. Dodaj logowanie do aplikacji za pomocą Facebooka obok logowania do Firebase.
   Poeksperymentuj z innymi dostawcami logowania. Dokumentacja : https://firebase.google.com/docs/auth/android/facebook-login
