## Kotlin `Synthetic` Binding  
-   This plugin not only generates `findViewById` code for us but also build a local view cache.

-   `HASH_MAP` is the default View caching strategy. It can be also be changed to `SPARSE_ARRAY` or `NO_CACHE`.  
    ```kotlin
    @ContainerOptions(CacheImplementation.SPARSE_ARRAY)
    class MainActivity : AppCompatActivity() { }
    ```
    
-   It's easy to build cache on any class using `LayoutContainer` interface. 
    ```kotlin
    import kotlinx.android.extensions.LayoutContainer

    class ViewHolder(override val containerView: View) : ViewHolder(containerView), LayoutContainer {
        fun setup(title: String) {
            itemTitle.text = "Hello World!"
        }
    }
    ```
** `containerView` is overridden from the `LayoutContainer` interface