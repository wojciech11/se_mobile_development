# Storage & Content providers

Zalecane: API 30.

## Intro

Dzisiaj poznamy ostatni główny [podstawowy element aplikacji Android](https://developer.android.com/guide/components/fundamentals#Components):

- <s>Activity</s>
- <s>Services</s>
- <s>BroadCast receiver</s>
- Content providers

## Storage

Możliwości https://developer.android.com/training/data-storage :

- App-specific files
- Media
- Documents and other files
- App preferences
- Database with room persistence library

## Content Providers

[Content providers](https://developer.android.com/guide/topics/providers/content-provider-basics) zarządzają dostępem do centralnego repozytorium danych.

Pierwszym krokiem jest zastanowienie się czy rzeczywiście potrzebujesz ContentProvider:

- You want to offer complex data or files to other applications.
- You want to allow users to copy complex data from your app into other apps.
- You want to provide custom search suggestions using the search framework.
- You want to expose your application data to widgets.
- You want to implement the AbstractThreadedSyncAdapter, CursorAdapter, or CursorLoader classes.

<p align="center">
<img src="https://developer.android.com/static/guide/topics/providers/images/content-provider-tech-stack.png" width="75%"/><br />
 Relationship between content provider and other components.<br />
<small>Źródło: <a href="https://developer.android.com/guide/topics/providers/content-provider-basics">developer.android.com/topic/architecture/ui-layer/stateholders</a></small>
</p>


<p align="center">
<img src="https://developer.android.com/static/guide/topics/providers/images/content-provider-interaction.png" width=60%"/><br />
 Relationship between content provider and other components.<br />
<small>Źródło: <a href="https://developer.android.com/guide/topics/providers/content-provider-basics">developer.android.com/topic/architecture/ui-layer/stateholders</a></small>
</p>

### Zadanie

1. Utwórz *ContentProviderApp*, jako pustą aktywność.

2. Dodaj nową klasę kotlin, rozszerzającą `ContentProvider` o nazwie `SampleContentProvider`. Po zaimportowaniu ContentProvider, wybierz wygenerowania wszystkich funkcji do implementacji:

   ```kotlin
   class SampleContentProvider : ContentProvider(){
    override fun onCreate(): Boolean {
        TODO("Not yet implemented")
    }

    override fun query(
        uri: Uri,
        projection: Array<out String>?,
        selection: String?,
        selectionArgs: Array<out String>?,
        sortOrder: String?
    ): Cursor? {
        TODO("Not yet implemented")
    }

    override fun getType(uri: Uri): String? {
        TODO("Not yet implemented")
    }

    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        TODO("Not yet implemented")
    }

    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<out String>?): Int {
        TODO("Not yet implemented")
    }

    override fun update(
        uri: Uri,
        values: ContentValues?,
        selection: String?,
        selectionArgs: Array<out String>?
    ): Int {
        TODO("Not yet implemented")
    }
   }
   ```

3. Omów powyższy kod z prowadzącym zajęcia.
