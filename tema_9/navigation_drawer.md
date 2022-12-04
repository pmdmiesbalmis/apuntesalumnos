# Navigation Drawer Modal

Como requisito deberemos saber crear recursos de menú XML

Podremos tener las siguientes partes:

- Cabecera (opcional)
- Opción Seleccionada (overlay)
- Opción
- SubMenu (opcional)

## Definiremos un recurso menu

1. Crearemos el recurso XML menúen **`res/menu`** denominado por ejemplo **`nav_drawer_menu.xml`**

    > :pushpin: **Nota:** Los meto en un grupo con **`android:checkableBehavior="single"`** y la opción inicial seleccionada **`android:checked="true"`**. De esta manera aparecerá un '*overlay*' sobre la opción seleccionada actual.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        tools:showIn="navigation_view">
        <group
            android:id="@+id/group1"
            android:checkableBehavior="single">
            <item
                android:id="@+id/item1"
                android:icon="@drawable/ic_baseline_favorite_24"
                android:title="item1"
                android:checked="true" />
            <item
                android:id="@+id/item2"
                android:icon="@drawable/ic_baseline_favorite_24"
                android:title="item2" />
            <item
                android:id="@+id/item3"
                android:icon="@drawable/ic_baseline_favorite_24"
                android:title="item3" />
        </group>
    </menu>
    ```

    > :pushpin: **Nota:** El atributo **`tools:showIn="navigation_view"`** me mostrará el menú en el diseño preliminar como un '*navigation_drawer*'.

2. Si decidimos poner **cabecera** definiremos el recurso xml con el layout de la misma en **`res/layout`** por ejemplo **`nav_drawer_header_layout.xml`**.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="150dp"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:background="@color/design_default_color_primary"
        android:fitsSystemWindows="true">

        <ImageView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_gravity="center_vertical"
            android:scaleType="fitCenter"
            android:scaleX="2"
            android:scaleY="2"
            android:src="@drawable/ic_launcher_foreground" />

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom"
            android:gravity="center"
            android:text="Drawer header"
            android:textColor="@color/white"
            android:layout_margin="5dp"
            android:textSize="30dp" />
    </FrameLayout>
    ```

3. Para que sea *modal*. En la actividad principal definiremos la siguiente estructura:

    ```puml {style="zoom:1.25"}
    @startsalt
    {
        {T
            + **DrawerLayout**
            ++ **CoordinatorLayout**
            +++ AppBarLayout
            ++++ MaterialToolbar
            +++ FrameLayout (**Contenido en pantalla**)
            ++++ FragmentContainerView (**Dinámico**)
            ++ **NavigationView**
        }
    }
    @endsalt
    ```

    Recordemos que al definir nuestro **`AppBarLayout`** deberemos ocultar el **`ActionBar`** en el tema principal.

    ```xml
        ...
        <item name="windowActionBar">false</item>
        <item name="windowNoTitle">true</item>
    </style>
    ```

    y en el **`main_activity.xml`** añadiremos la estructura anterior.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <androidx.drawerlayout.widget.DrawerLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/drawer_layout"
        tools:context=".MainActivity">

        <androidx.coordinatorlayout.widget.CoordinatorLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <com.google.android.material.appbar.AppBarLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:fitsSystemWindows="true">

                <com.google.android.material.appbar.MaterialToolbar
                    android:id="@+id/topAppBar"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    app:title="@string/app_name"
                    style="@style/Widget.MaterialComponents.Toolbar.Primary"
                    app:layout_collapseMode="pin"/>
            </com.google.android.material.appbar.AppBarLayout>

            <!-- Aquí iría al contenido que puede ser un 
                 FrameLayout con un fragment dinámico -->

        </androidx.coordinatorlayout.widget.CoordinatorLayout>

        <com.google.android.material.navigation.NavigationView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:id="@+id/navigation_view"
            android:layout_gravity="start"
            app:headerLayout="@layout/nav_drawer_header_layout"
            app:menu="@menu/nav_drawer_menu"
            android:fitsSystemWindows="true"/>
    </androidx.drawerlayout.widget.DrawerLayout>
    ```

4. Añadiremos el código de gestión en **`class MainActivity : AppCompatActivity() { ...`**

    Definiremos **propiedades privadas**...

    ```kotlin
    private lateinit var binding: ActivityMainBinding
    // Activador y Desactivador del navigation_drawer
    private lateinit var toggle: ActionBarDrawerToggle
    ```

    En el **onCreate**...

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        // Para que funciones los menús si los añadimos.
        setSupportActionBar(binding.topAppBar)

        // Instanciamos Activador y Desactivador del navigation_drawer
        toggle = ActionBarDrawerToggle(this,
            binding.drawerLayout,
            binding.topAppBar,
            R.string.navigation_open,
            R.string.navigation_close)
        // Lo asignamos al drawer_layout
        binding.drawerLayout.addDrawerListener(toggle)
        // Asignamos al navigation_view del navigation_drawer
        // un listener para las opciones de navegación selccionadas.
        binding.navigationView.setNavigationItemSelectedListener(getNavigationListener())
    }
    ```

    Listener para las opciones de navegación ...

    ```kotlin
    private fun getNavigationListener() 
    : NavigationView.OnNavigationItemSelectedListener {
        return NavigationView.OnNavigationItemSelectedListener{ menuItem ->
            when (menuItem.itemId) {
                // ...
            }
            // Marcamos la opción como checked para ponerle el overlay
            menuItem.isChecked = true
            // Colapsamos el Drawer
            binding.drawerLayout.close()
            true
        }
    }
    ```

    Asociamos el **icono de menú de nevegación** en la  AppBar con nuestro navigation_drawer ...

    ```kotlin
    override fun onPostCreate(savedInstanceState: Bundle?) {
        super.onPostCreate(savedInstanceState)
        toggle.syncState()
    }

    override fun onConfigurationChanged(
        newConfig: android.content.res.Configuration,
    ) {
        if (newConfig != null) super.onConfigurationChanged(newConfig)
        toggle.onConfigurationChanged(newConfig)
    }
    ```
