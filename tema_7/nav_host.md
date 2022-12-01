
## NavHost 📌

Primero lo creamos en nuestro xml

```xml
    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/idRepresentativo"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:name="androidx.navigation.fragment.NavHostFragment"
        app:defaultNavHost="true"
        app:navGraph="@navigation/NOMBRE_DE_NUESTRO_NAV" 
        />
```

Después crearemos un `nav_graph` hay varias maneras, pues la que mas te guste

Un código de ejemplo es el siguiente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/nav_graph"
    app:startDestination="@id/listContact3">
    <fragment
        android:id="@+id/listContact3"
        android:name="com.example.projectexamen1.fragments.ListContact"
        android:label="fragment_list_contact"
        tools:layout="@layout/fragment_list_contact" >
        <action
            android:id="@+id/action_listContact3_to_createContact"
            app:destination="@id/createContact" />
    </fragment>
    <dialog
        android:id="@+id/createContact"
        android:name="com.example.projectexamen1.fragments.CreateContactDialog"
        android:label="fragment_create_contact"
        tools:layout="@layout/fragment_create_contact" /><action android:id="@+id/action_global_listContact3" app:destination="@id/listContact3"/>
</navigation>
```

Pero la interfaz gráfica preparada para esto es buena y se puede usar bien para realacionar los fragments entre ellos

Existen varios tipos de enlaces entre fragments para navegar:

- Global

- Return

- ToSelf

- To destination ...

Estos enlaces/relaciones son lo que despues referenciaremos para poder realizar un cambio entre fragments dinámicamente

Para navegar entre fragments usaremos esta syntaxis

```kotlin
// Desde un fragment que este dentro del graph
var nv = findNavController()
nv.navigate(R.id.action_listContact3_to_createContact) // Navegar
```