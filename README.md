---
title:
    Título
export_on_save:
    html: true
toc:
    depth_from: 1
    depth_to: 4
    ordered: true
---

# Apuntes PMDM Alumnado

```kotlin {class="line-numbers"}
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

