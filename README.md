# Iíndice

- [Iíndice](#iíndice)
  - [Apuntes PMDM Alumnado](#apuntes-pmdm-alumnado)
  - [Titulo2](#titulo2)


## Apuntes PMDM Alumnado

- Texto **jdfhsj** sdkljflkj `djfslkjf` 

## Titulo2

```kotlin
recyclerView.setHasFixedSize(true)
val adaptador = Adaptador(datos,this)
adaptador.setHasStableIds(true)
recyclerView.adapter = adaptador
tracker = SelectionTracker.Builder<Long>(
            "selecccion",
            recyclerView,
            StableIdKeyProvider(recyclerView),
            LookUp(recyclerView),
            StorageStrategy.createLongStorage()
        ).withSelectionPredicate(
            SelectionPredicates.createSelectAnything()
        ).build()
adaptador.setTracker(tracker)
```

