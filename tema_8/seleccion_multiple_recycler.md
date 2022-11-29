# Selección múltiple en RecyclerView

Como requisito previo, deberemos saber definir y rellenar un componente **`RecyclerView`**.

## En el Holder

1. Necesitamos un método para identificar un item del Recycler.

    ```kotlin
    fun getItemDetails() : ItemDetailsLookup.ItemDetails<Long> =
    object : ItemDetailsLookup.ItemDetails<Long>() {
      override fun getPosition(): Int = adapterPosition
      override fun getSelectionKey(): Long? = itemId
    }
    ```

2. Al método **`bind`** le pasaremos un objecto **`tracker`** con los identificadores de los items seleccionados para resaltar la vista de los mismos.

    ```kotlin
    fun bind(entity: Entidad, tracker: SelectionTracker<Long>?) {
      // Asociación de la entidad con los widgets.

      if (tracker?.isSelected(adapterPosition.toLong()) ?: false)
        v.background = ColorDrawable(Color.CYAN)
      else 
        v.background = ColorDrawable(Color.LTGRAY)
    }
    ```

## Definición de la clase de búsqueda en el Recycler

Definiremos la clase **`LookUp.kt`**. Una instancia de esta clase la pasaremos al *builder* del objeto **`tracker`** y le permitirá obtener un **identificador** del item seleccionado mediante el **`RecyclerView`** asociado.

```kotlin
class LookUp (private val rv: RecyclerView) : ItemDetailsLookup<Long>() {
  override fun getItemDetails(event: MotionEvent) : ItemDetails<Long>? {
    val view = rv.findChildViewUnder(event.x, event.y)

    // Llamamos al getItemDetails() que hemos definido en el Holder
    return if (view != null) (rv.getChildViewHolder(view) as Holder).getItemDetails()
           else null
  }
}
```

## En el Adapter

1. Añadiremos la propiedad pública **`tracker`** para asignarle un objeto de este tipo tras su creación **una vez hayamos asignado el *adapter***.
  
    ```kotlin
    lateinit var tracker: SelectionTracker<Long>
    ```

2. Invalidaremos el método **getItemId** que usará el **`tracker`**

    ```kotlin
    override fun getItemId (pos: Int): Long = pos.toLong()
    ```

## En el MainActivity o Fragment donde esté definido el RecyclerView

1. Crearemos una instancia del objeto tracker que guardará los identificadores de los items seleccionados en el **`RecyclerView`**

    ```kotlin
    private fun creaTracker(rv: RecyclerView): SelectionTracker<Long> {
        val t = SelectionTracker.Builder<Long>(
            "selecccion", rv, StableIdKeyProvider(rv),
        // Instancia de la la clase que le permitirá al tracker obtener un 
            LookUp(rv), 
            StorageStrategy.createLongStorage()
        ).withSelectionPredicate(
            SelectionPredicates.createSelectAnything()
        ).build()
        // Añadiremos un objeto abstracto SelectionObserver<Long> para gestionar los
        // cambios en la selección actual del tracker.
        t.addObserver(
            object : SelectionTracker.SelectionObserver<Long>() {
                override fun onSelectionChanged() {
                    if (t.hasSelection()) {
                        // Aquí añadiremos la gestión de los items seleccionados.
                        var cadena = StringBuilder()
                        t.selection?.forEach { id -> cadena.append(id.toString() + " ") }
                        Toast.makeText(this@MainActivity, cadena, Toast.LENGTH_SHORT).show()
                    }
                }
            })
        return t;
    }
    ```

2. Modificamos algunas propiedades tras la creación **`RecyclerView`**, el **`Adapter`** y creamos al **`tracker`** pasándoselo al **`Adapter`**.

    ```kotlin
    val rv = findViewById<RecyclerView>(R.id.rvUsuarios)
    // Si el RecyclerView no va a cambiar de tamaño. Con esta propiedad optimizamos
    // su funcionamiento.
    rv.setHasFixedSize(true)

    var adapter = UsuarioAdapter(UsuarioController.usuarios())
    // Establecemos que cada item sea identificado con un identificador único.
    adapter.setHasStableIds(true)

    rv.adapter = adapter
    rv.layoutManager = LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false)

    // Creamos el tracker después de que el rv tenga un adapter
    adapter.tracker = creaTracker(rv)
    ```
