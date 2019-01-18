# Koin
> Light weight dependency injection library for Android
---
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
