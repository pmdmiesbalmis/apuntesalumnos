## View Model 📦

Normalmente usaremos esto para guardar un estado de nuestra aplicacion/fragmento/activity

Para implementar la VM necesitamos estas lineas de codigo en `build.gradle`

```kotlin
configurations {
    all {
        exclude group: 'androidx.lifecycle', module: 'lifecycle-runtime-ktx'
        exclude group: 'androidx.lifecycle', module: 'lifecycle-viewmodel-ktx'
    }
}
```

y dentro de  `dependencies`

```kotlin
dependencies {
    implementation "androidx.activity:activity-ktx:1.4.0"
    implementation "androidx.fragment:fragment-ktx:1.4.1"
...
```

y ahora la clase

```kotlin
class MainVM : ViewModel(){
    val nombreDescriptivo : MutableLiveData<TU_CLASE> by lazy { MutableLiveData< TU_CLASE>() }
    val getNombreDescriptivo: LiveData<TU_CLASE> get() = nombreDescriptivo
    fun setNombreDescriptivo(item: TU_CLASE) {
        curContact.value = item
    }
}
```

Importamos lo que haga falta 

**USO**

Ahora la parte importante, acceder:

**ACTIVITY**

```kotlin
val mainVM : MainVM by viewModels()
```

**FRAGMENT**

```kotlin
private val mainVM : MainVM by activityViewModels()
```

y desde este objeto podremos llamar a esas propiedades que teniamos antes

```kotlin
mainVM.setNombreDescriptivo("EJEMPLO")
mainVM.getNombreDescriptivo.value = "EJEMPLO"
```