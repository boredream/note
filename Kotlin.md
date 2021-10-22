https://kotlinlang.org/docs/getting-started.html

https://fabiomsr.github.io/from-java-to-kotlin



# 非空

```kotlin
// non-null，a是不能=null的，编译器报错
var a: String

// nonable
var a: String?

// ?相当于包了一层notnull逻辑，!!会抛出NPE
println(a?.length)
println(a!!.length)


```









