Krangl Roadmap
==============

For completed items see [change-log](../CHANGES.md).

v0.7
----


* more defined behavior/tests needed for grouped dfs that become empty after filtering

---
API improvemnts


* [Select](src/main/kotlin/krangl/Select.kt#L68) should accept basic type as argument instead of more internal column type
* write and save parquet files https://stackoverflow.com/questions/39728854/create-parquet-files-in-java

---

Improve jvm compatibility and add examples (see roadmap)


* Use JvmName to allow for more strongly typed (see  http://stackoverflow.com/questions/29268526/how-to-overcome-same-jvm-signature-error-when-implementing-a-java-interface)
```kotlin
@JvmName("mutateString")
fun DataFrame.mutate(name: String, formula: (DataFrame) -> List<String>): DataFrame {
    if(this is SimpleDataFrame){
        return addColumn(StringCol(name, formula(this)))
    }else
        throw UnsupportedOperationException()
}

```
---
Performance

* misc consider to use kotlin.collections.ArrayAsCollection

* Setup up benchmarking suite

List copy optimization
* misc consider to use kotlin.collections.ArrayAsCollection --> get rid of toList which always does a full copy internally.
* [ ] 30% flights HOTSPOT: `krangl/Extensions.kt:275` can we get rid fo the array creation?
* [ ] `krangl.SimpleDataFrame.addColumn` should avoid `toMutatbleList`
* [ ] More consistent use of List vs using arrays as column datastore (see [array vs list](http://stackoverflow.com/questions/716597/array-or-list-in-java-which-is-faster)). This would avoid array conversion which are omnipresent in the API at the moment.
* [ ] `get rid of other `toMutableList` and use view instead



Backlog
-------

* remove regrouping in core verbs where possible
* consider to use invoke for row access (potentially decouple more arguable extensions in different namespace?)
* provide equivalent for dplyr::summarize_each and dplyr::mutate_each [#4](https://github.com/holgerbrandl/krangl/issues/4)

* `krangl.head` should use view instead of copy; also consider to use views for grouped data (see https://softwarecave.org/2014/03/19/views-in-java-collections-framework/)


* koma bindings --> http://koma.kyonifer.com/
* Add a `DataFrame.transpose()` method




* Integrate idoms to do enrichment testing with fisher test from [commons-math](http://commons.apache.org/proper/commons-math/apidocs/org/apache/commons/math3/distribution/HypergeometricDistribution.html)


* use for column indices to speed up access




fast column storage
https://github.com/lemire/JavaFastPFOR
http://fastutil.di.unimi.it/

http://nd4j.org/


## Rejected Ideas


* directly access values with `it["foo"]` and not just column object. For the latter DataFrame.cols can be used
    * Not a good idea because all extension function would then be defined for common lists like List<Int> etc. It's more important to keep the namespace clear


---

Provide adhoc/data class conversion for column model [adhoc](https://kotlinlang.org/docs/reference/object-declarations.html#object-expressions)/data class objects
```kotlin
val dataFrame = object : DataFrame() {
    val x = Factor("sdf", "sdf", "sdfd")
    val y = DblCol(Double.MAX_VALUE, Double.MIN_VALUE)
    val z = y + y
}


val newTable = df.map{ data class Foo(val name:String)}

newTable.newCol
newTable.src.x
```

-->  Can not work because data class is not an expression

---

* improve benchmarking by avoid jmv warmup with -XX:CompileThreshold=1 [src](http://stackoverflow.com/questions/1481853/technique-or-utility-to-minimize-java-warm-up-time)

--> rather continue with jmh driven benchmarking subproject


--

Make use of kotlin.Number to simplify API --> Done by adding `NumberCol` but unclear how to actually benefit from it


## Design

https://stackoverflow.com/questions/45090808/intarray-vs-arrayint-in-kotlin --> bottom line: Array<*> can be null