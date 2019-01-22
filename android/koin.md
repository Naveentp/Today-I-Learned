# Koin
 **Light weight dependency injection library for Android**

> This is the running notes which I have taken while learning about koin through [caster koin lession](https://caster.io/courses/koin)

**Content**
- [Comparing koin with Dagger](#Comparing-koin-with-Dagger)
- [Koin Dependencies](#Koin-Dependencies)
- [Creating modules](#Creating-modules)
- [Initialize Koin in project](#Initialize-Koin-in-project)
- [Injecting Dependencies into Android components](#Injecting-Dependencies-into-Android-components)
- [Android ViewModel dependencies](#Android-ViewModel-dependencies)
- [Adding dependecies to custom class](#Adding-dependecies-to-custom-class)
- [Lifecycle Scopes](#Lifecycle-Scopes)
- [Properties](#Properties)
- [Parameter injection](#Parameter-injection)
- [Custom logging in koin with Timber](#Custom-logging-in-koin-with-Timber)


### Comparing koin with Dagger
| Dagger | Koin |
|--------|------|
| Compile time validation | Runtime validation (Risk of bugs) |
| Factory classes are auto-generated based on dependency graph. | Follows Service locator pattern, should manually define how objects are constructed. |
| Increases the build time because of compile time code generation | Decreases the build time because of run time code generation |
| Lot of boilerplate, steep learning curve, setup takes time | Less boilerplace, initial overhead avoided|

### Koin Dependencies 
```groovy
def koinVersion = '1.0.0'

// core koin features
implementation "org.koin:koin-android:$koinVersion" 
// view model injection extension
implementation "org.koin:koin-android-viewmodel:$koinVersion" 
// Android scopes (Lifecycle of android components)
implementation "org.koin:koin-android-scope:$koinVersion"
```

### Creating modules
> Create a koltin file called `module.kt` 

```kotlin
val applicationModule = module {
    // Dependency code goes here

    /** 
    * Provides sigleton dependency of Gson instance.
    * single : Single instance injected across the classes.
    **/
    single { Gson() } 

    /** 
    * Provides new dependency of RecyclerAdapter instance.
    * factory : New instance of the definition will be defined.
    **/
    factory { RecyclerAdapter() }

    /**
    * Interface dependecy
    **/
    factory<DataRepository> { DataRepositoryImpl(get()) }

    /**
    * Named Dependecy 
    * When two impl class returns same dependecy (DataRepository in this case),
    * We can differenciate the dependencies with the names
    * USAGE : get("local") will fetch DataRepository of LocalDataRepositoryImpl or
    *  val localDataRepo : DataRepository by inject("local")
    **/
    factory<DataRepository>("local") { LocalDataRepositoryImpl() }
    factory<DataRepository>("remote") { RemoteDataRepositoryImpl(get()) }
}
```

### Initialize Koin in project
```kotlin
class BaseApplication : Application() {

    override fun onCreate(){
        super.onCreate()
        // Instantiates Koin and creates koin context
        startKoin(this, listOf(applicationModule))
    }
}
```

### Injecting Dependencies into Android components
Once koin has been loaded, All activities, services in the project becomes Koin components.
> **Lazy and Eager Injections**
- **Lazy injection** - Definition isn't initialised until the point that is first used.
- **Eager injection** - Definition instance is retrived directly from koin container. With Eager injection, objects will be initialised immediately.

```kotlin
class MyActivity : AppCompatActivity() {
    /**
    * Inject function will lazily inject dependecies into components
    **/
    val adapter : RecyclerAdapter by inject()

    /**
    * get function will immediately (Eagerly) inject dependecies into components
    **/
    val dataRepository = get<DataRepositoryImpl>()
}
```

### Android ViewModel dependencies
```kotlin
// Module creation
viewModel { CurrenciesViewModel(get()) }

// Injecting viewmodel in activity
val currencyViewModel : CurrenciesViewModel by viewModel()
```

### Adding dependecies to custom class

```kotlin
/**
* Custom Class implements `KoinComponent` interface.
* So, it will get access to inject out of the box.
**/
class CurrencyView @JvmOverloads constructor(
    context: Context,
    attr: AttributeSet? = null,
    defStyleAttr: Int = 0)
    : LinearLayout(context, attr, defStyleAttr), KoinComponent {
        
        // injection to customview
        val urlHelper : UrlHelper by inject()

        init{
            View.inflate(context, R.layout.view_currency, this)
        }

        fun setCurrency(currency: Currency){
            text_name.text = currency.name
            text_symbol.text = currency.symbol

            setOnClickListener {
                // on click action logic goes here.
            }
        }
    }
```

### Lifecycle Scopes
> Scope is essentially how long a object deifinition is to exists for.  

`single` - Lifecycle is the same amount of time as the module container.  
`factory` - creates new instance everytime this gets called.

**Creating scope in module class like,**  
`scope("browse") { MyAdapter() }`

**And in activity,**  
`getOrCreateScope("browse")` - Retrieves scope if exists otherwise it'll create it.   
`bindScope(getOrCreateScope("browse"))` - `bindScope` is a android lifecycle aware component which destroys scope when onDestroy triggered.  
`getScope("browse").close()` - to manual close/release a scope dependency.


### Properties
- Create an `assets` directory under `main`. Create property file called `koin.properties`, add <key,value> pair like below.
    ```properties
    DB_NAME=my_app
    SHARED_PREF_NAME=my_app_shared_pref
    ```

- In `BaseApplication` set `loadProperties` to `true` like below,    
    ```kotlin
    startKoin(this, listOf(applicationModule), loadProperties = true)
    ```
- Now, in your activity you can call,
    ```kotlin 
    val dbName: String by property("DB_NAME")
    val sharedPrefName: String by property("SHARED_PREF_NAME")
    ```
- Or Dependency can be added in `module` class like this.
    ```kotlin
    single { UrlHelper(getProperty("DB_NAME"))}
    ```
- `setProperty` function can be used to change the value for the key programatically.

### Parameter injection
> Sometimes you may require arguments they are not accessible until you are inside the class  

Example:  
If `jsonString` to be injected to `CurrenciesViewModel` class
```kotlin
class CurrenciesViewModel constructor(
    dataRepo: DataRepository,
    jsonString : String
) : ViewModel
```
How can we inject it? 

In Modules file,
```kotlin
viewModel { (jsonString: String) -> CurrenciesViewModel(get(), jsonString) }
```

And in Activity class, we can pass jsonString like this,
```kotlin
val currenciesViewModel: CurrenciesViewModel by viewModel {
    val currencyJson = resources
    .openRawResource(R.raw.currency)
    .bufferedReader()
    .use {it.readText()}

    //This will set the jsonString parameter
    parametersOf(currencyJson)
}
```

### Custom logging in koin with Timber 
```kotlin
class KoinLogger : Logger {
    override fun debug(msg: String){
        Timber.d(msg)
    }

    override fun err(msg: String){
        Timber.e(msg)
    }

    override fun info(msg: String){
        Timber.i(msg)
    }
} 

//Pass this class as a `logger` argument in startKoin
startKoin(this, 
    listOf(applicationModule), 
    loadProperties = true, 
    logger = KoinLogger()
    )
```

