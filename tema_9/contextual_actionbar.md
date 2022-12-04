# Contextual ActionBar (CAB)

**Como requisito deberemos:**

- Saber crear un **`RecyclerView`** con mult-iselección.
- Saber crear recursos de menú XML

**Contexto básico:**

- Se superpone una **`CAB`** a la **`AppBar`**.
- El icono de navegación se reemplaza por un **icono de cierre**.
- Las acciones de la **`AppBar`** se reemplazan por **acciones contextuales**.
- Al cerrar la **`CAB`** se **muestra de nuevo la** **`AppBar`**.
- Lo vamos a asociar a un **`RecyclerView`** con mult-iselección.

## Definiremos un recurso menu

1. Crearemos el recurso XML menúen **`res/menu`** denominado por ejemplo **`contextual_actionbar_delete_menu.xml`**, de tal manera que los iconos de las opciones se muestren en el **`ActionBar`** si caben.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto">
        <item
            android:id="@+id/delete_in_cab"
            android:icon="@android:drawable/ic_menu_delete"
            android:title="Eliminar"
            app:showAsAction="ifRoom" />
    </menu>
    ```

2. Definiremos una propiedad privada **`actionMode`** que referenciará al objeto que desplegará el **`CAB`**.

    > :hand: **Importante:** Debe ser el tipo definido en **`androidx.appcompat.view.ActionMode`**

    ```kotlin
    private var actionMode: ActionMode? = null
    ```

3. Definiremos una propiedad privada **`actionModeCallback`** en la clase donde tengamos definido nuestro **`RecyclerView`**. Esta propiedad, referenciará a un objeto que implementa la interfaz **`ActionMode.Callback`**, el cual necesitará nuestro objeto **`actionMode`** para gestionar la creación del interfaz del **`CAB`** y sus listeners.

    ```kotlin
    private val actionModeCallback = object : ActionMode.Callback {
        // Crea la vista del CAB
        override fun onCreateActionMode(mode: ActionMode, menu: Menu): Boolean {
            menuInflater.inflate(R.menu.contextual_actionbar_delete_menu, menu)
            return true
        }
        override fun onPrepareActionMode(mode: ActionMode, menu: Menu): Boolean = false
        override fun onActionItemClicked(mode: ActionMode, item: MenuItem): Boolean {
            return when (item.itemId) {
                R.id.delete_in_cab -> {
                    // Deberemos tener acceso al ReyclerView y su Adapter
                    var adaptador = binding.rvUsuarios.adapter as UsuarioAdapter
                    adaptador.tracker.selection
                             .sorted()      // Ordenamos e
                             .reversed()    // invertimos la selección
                             .forEach { id -> UsuarioController.borra(id.toInt()) }
                    // Actualizamos nuestro Recycler
                    binding.rvUsuarios.getRecycledViewPool().clear();
                    adaptador.notifyDataSetChanged();
                    adaptador.tracker.clearSelection()
                    actionMode = null
                    true
                }
                else -> false
            }
        }
        override fun onDestroyActionMode(mode: ActionMode) {
            // Si pulsamos ← en el CAB borraremos la selección actual.
            (binding.rvUsuarios.adapter as UsuarioAdapter).tracker.clearSelection()
            actionMode = null
        }
    }
    ```

4. Ahora en el *observador* de los items seleccionados en nuestro tracker, vamos a creara una única instancia de objeto **`actionMode`** si hay elementos seleccionados o destruirlo si no los hay asociado a nuestro **`MainActivity`** la cual sustituirá cualquier otro menú de opciones en la mismo.

    ```kotlin
    private fun creaTracker(rv: RecyclerView): SelectionTracker<Long> {
        val t = SelectionTracker.Builder<Long>(
            "selecccion", rv, StableIdKeyProvider(rv),
            LookUp(rv), StorageStrategy.createLongStorage()
        ).withSelectionPredicate(
            SelectionPredicates.createSelectAnything()
        ).build()
        t.addObserver(
            object : SelectionTracker.SelectionObserver<Long>() {
                override fun onSelectionChanged() {
                    if (t.hasSelection()) {
                        // Definimos una única instancia mientras haya items 
                        // seleccionados en nuestro tracker
                        if (actionMode == null) {
                            // En este ámbito this referencia al objeto anónimo que implementa la abstracción del observador.
                            // Por eso debemos usar this@MainActivity para la Activity
                            // o requireActivity() o requireActivity() as AppCompatActivity si estamos en un Fragment.
                            actionMode = this@MainActivity.startSupportActionMode(actionModeCallback)
                        }
                        actionMode?.title = "${t.selection.size()}"
                    } else {
                        // Cuando no hay selección lo destruimos.
                        actionMode?.finish()
                    }
                }
            })
        return t;
    }
    ```

    > :pushpin: **Nota:** **`actionMode.invalidate()`** hace que se vuelva a llamra al **`onCreateActionMode`** en el **`actionModeCallback`** asociado al mismo.