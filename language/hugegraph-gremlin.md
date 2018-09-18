## HugeGraph Gremlin

HugeGraph支持[Apache TinkerPop 3](https://tinkerpop.apache.org)的图形遍历查询语言[Gremlin](https://tinkerpop.apache.org/gremlin.html)。 SQL是关系型数据库查询语言，而Gremlin是一种通用的图数据库查询语言，Gremlin可用于创建图的实体（Vertex和Edge）、修改实体内部属性、删除实体，也可执行图的查询操作。

建议通过Gremlin执行图的查询和遍历，而关于图的增加、修改和删除等更新操作则通过HugeGraph的Graph Api接口进行。因为Graph Api对增加、修改和删除操作做了很多优化，执行效率比Gremlin更加高效。

### TinkerPop Features

HugeGraph实现了TinkerPop框架，但是并没有实现TinkerPop所有的特性。

下表列出HugeGraph对TinkerPop各种特性的支持情况：

### Graph Features

Name                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Support
-------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------
Computer             | Determines if the {@code Graph} implementation supports {@link GraphComputer} based processing                                                                                                                                                                                                                                                                                                                                                                | false
Transactions         | Determines if the {@code Graph} implementations supports transactions.                                                                                                                                                                                                                                                                                                                                                                                        | true
Persistence          | Determines if the {@code Graph} implementation supports persisting it's contents natively to disk.This feature does not refer to every graph's ability to write to disk via the Gremlin IO packages(.e.g. GraphML), unless the graph natively persists to disk via those options somehow. For example,TinkerGraph does not support this feature as it is a pure in-sideEffects graph.                                                                         | true
ThreadedTransactions | Determines if the {@code Graph} implementation supports threaded transactions which allow a transactionto be executed across multiple threads via {@link Transaction#createThreadedTx()}.                                                                                                                                                                                                                                                                     | false
ConcurrentAccess     | Determines if the {@code Graph} implementation supports more than one connection to the same instance at the same time. For example, Neo4j embedded does not support this feature because concurrent access to the same database files by multiple instances is not possible. However, Neo4j HA could support this feature as each new {@code Graph} instance coordinates with the Neo4j cluster allowing multiple instances to operate on the same database. | false

### Vertex Features

Name                     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Support
------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------
UserSuppliedIds          | Determines if an {@link Element} can have a user defined identifier. Implementation that do not support this feature will be expected to auto-generate unique identifiers. In other words, if the {@link Graph} allows {@code graph.addVertex(id,x)} to work and thus set the identifier of the newly added {@link Vertex} to the value of {@code x} then this feature should return true. In this case, {@code x} is assumed to be an identifier datat ype that the {@link Graph} will accept. | false
NumericIds               | Determines if an {@link Element} has numeric identifiers as their internal representation. In other words,if the value returned from {@link Element#id()} is a numeric value then this method should be return {@code true}. Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                                                                                          | false
StringIds                | Determines if an {@link Element} has string identifiers as their internal representation. In other words, if the value returned from {@link Element#id()} is a string value then this method should be return {@code true}. Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                                                                                           | false
UuidIds                  | Determines if an {@link Element} has UUID identifiers as their internal representation. In other words,if the value returned from {@link Element#id()} is a {@link UUID} value then this method should be return {@code true}.Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                                                                                         | false
CustomIds                | Determines if an {@link Element} has a specific custom object as their internal representation.In other words, if the value returned from {@link Element#id()} is a type defined by the graph implementations, such as OrientDB's {@code Rid}, then this method should be return {@code true}.Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                         | false
AnyIds                   | Determines if an {@link Element} any Java object is a suitable identifier. TinkerGraph is a good example of a {@link Graph} that can support this feature, as it can use any {@link Object} as a value for the identifier. Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite. This setting should only return {@code true} if {@link #supportsUserSuppliedIds()} is {@code true}.                                        | false
AddProperty              | Determines if an {@link Element} allows properties to be added. This feature is set independently from supporting "data types" and refers to support of calls to {@link Element#property(String, Object)}.                                                                                                                                                                                                                                                                                      | true
RemoveProperty           | Determines if an {@link Element} allows properties to be removed.                                                                                                                                                                                                                                                                                                                                                                                                                               | true
AddVertices              | Determines if a {@link Vertex} can be added to the {@code Graph}.                                                                                                                                                                                                                                                                                                                                                                                                                               | true
MultiProperties          | Determines if a {@link Vertex} can support multiple properties with the same key.                                                                                                                                                                                                                                                                                                                                                                                                               | false
DuplicateMultiProperties | Determines if a {@link Vertex} can support non-unique values on the same key. For this valueto be {@code true}, then {@link #supportsMetaProperties()} must also return true. By default this method, just returns what {@link #supportsMultiProperties()} returns.                                                                                                                                                                                                                             | false
MetaProperties           | Determines if a {@link Vertex} can support properties on vertex properties. It is assumed that a graph will support all the same data types for meta-properties that are supported for regular properties.                                                                                                                                                                                                                                                                                      | false
RemoveVertices           | Determines if a {@link Vertex} can be removed from the {@code Graph}.                                                                                                                                                                                                                                                                                                                                                                                                                           | true

### Edge Features

Name            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Support
--------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------
UserSuppliedIds | Determines if an {@link Element} can have a user defined identifier. Implementation that do not support this feature will be expected to auto-generate unique identifiers. In other words, if the {@link Graph} allows {@code graph.addVertex(id,x)} to work and thus set the identifier of the newly added {@link Vertex} to the value of {@code x} then this feature should return true. In this case, {@code x} is assumed to be an identifier datat ype that the {@link Graph} will accept. | false
NumericIds      | Determines if an {@link Element} has numeric identifiers as their internal representation. In other words,if the value returned from {@link Element#id()} is a numeric value then this method should be return {@code true}. Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                                                                                          | false
StringIds       | Determines if an {@link Element} has string identifiers as their internal representation. In other words, if the value returned from {@link Element#id()} is a string value then this method should be return {@code true}. Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                                                                                           | false
UuidIds         | Determines if an {@link Element} has UUID identifiers as their internal representation. In other words,if the value returned from {@link Element#id()} is a {@link UUID} value then this method should be return {@code true}.Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                                                                                         | false
CustomIds       | Determines if an {@link Element} has a specific custom object as their internal representation.In other words, if the value returned from {@link Element#id()} is a type defined by the graph implementations, such as OrientDB's {@code Rid}, then this method should be return {@code true}.Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite.                                                                         | false
AnyIds          | Determines if an {@link Element} any Java object is a suitable identifier. TinkerGraph is a good example of a {@link Graph} that can support this feature, as it can use any {@link Object} as a value for the identifier. Note that this feature is most generally used for determining the appropriate tests to execute in the Gremlin Test Suite. This setting should only return {@code true} if {@link #supportsUserSuppliedIds()} is {@code true}.                                        | false
AddProperty     | Determines if an {@link Element} allows properties to be added. This feature is set independently from supporting "data types" and refers to support of calls to {@link Element#property(String, Object)}.                                                                                                                                                                                                                                                                                      | true
RemoveProperty  | Determines if an {@link Element} allows properties to be removed.                                                                                                                                                                                                                                                                                                                                                                                                                               | true
AddEdges        | Determines if an {@link Edge} can be added to a {@code Vertex}.                                                                                                                                                                                                                                                                                                                                                                                                                                 | true
RemoveEdges     | Determines if an {@link Edge} can be removed from a {@code Vertex}.                                                                                                                                                                                                                                                                                                                                                                                                                             | true

### Data Type Features

Name               | Description                                                                                                                                                                                                                                                         | Support
------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------
BooleanValues      |                                                                                                                                                                                                                                                                     | true
ByteValues         |                                                                                                                                                                                                                                                                     | true
DoubleValues       |                                                                                                                                                                                                                                                                     | true
FloatValues        |                                                                                                                                                                                                                                                                     | true
IntegerValues      |                                                                                                                                                                                                                                                                     | true
LongValues         |                                                                                                                                                                                                                                                                     | true
MapValues          | Supports setting of a {@code Map} value. The assumption is that the {@code Map} can containarbitrary serializable values that may or may not be defined as a feature itself                                                                                         | false
MixedListValues    | Supports setting of a {@code List} value. The assumption is that the {@code List} can containarbitrary serializable values that may or may not be defined as a feature itself. As this{@code List} is "mixed" it does not need to contain objects of the same type. | false
BooleanArrayValues |                                                                                                                                                                                                                                                                     | false
ByteArrayValues    |                                                                                                                                                                                                                                                                     | true
DoubleArrayValues  |                                                                                                                                                                                                                                                                     | false
FloatArrayValues   |                                                                                                                                                                                                                                                                     | false
IntegerArrayValues |                                                                                                                                                                                                                                                                     | false
LongArrayValues    |                                                                                                                                                                                                                                                                     | false
SerializableValues |                                                                                                                                                                                                                                                                     | false
StringArrayValues  |                                                                                                                                                                                                                                                                     | false
StringValues       |                                                                                                                                                                                                                                                                     | true
UniformListValues  | Supports setting of a {@code List} value. The assumption is that the {@code List} can containarbitrary serializable values that may or may not be defined as a feature itself. As this{@code List} is "uniform" it must contain objects of the same type.           | false

### Gremlin的步骤

HugeGraph支持Gremlin的所有步骤。有关Gremlin的完整参考信息，请参与[Gremlin官网](http://tinkerpop.apache.org/docs/current/reference/)。

步骤         | 说明                                                                                              | 文档
---------- | ----------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------
addE       | 在两个顶点之间添加边                                                                                      | [addE step](http://tinkerpop.apache.org/docs/current/reference/#addedge-step)
addV       | 将顶点添加到图形                                                                                        | [addV step](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step)
and        | 确保所有遍历都返回值                                                                                      | [and step](http://tinkerpop.apache.org/docs/current/reference/#add-step)
as         | 用于向步骤的输出分配变量的步骤调制器                                                                              | [as step](http://tinkerpop.apache.org/docs/current/reference/#as-step)
by         | 与`group`和`order`配合使用的步骤调制器                                                                      | [by step](http://tinkerpop.apache.org/docs/current/reference/#by-step)
coalesce   | 返回第一个返回结果的遍历                                                                                    | [coalesce step](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step)
constant   | 返回常量值。 与`coalesce`配合使用                                                                          | [constant step](http://tinkerpop.apache.org/docs/current/reference/#constant-step)
count      | 从遍历返回计数                                                                                         | [count step](http://tinkerpop.apache.org/docs/current/reference/#addedge-step)
dedup      | 返回已删除重复内容的值                                                                                     | [dedup step](http://tinkerpop.apache.org/docs/current/reference/#dedup-step)
drop       | 丢弃值（顶点/边缘）                                                                                      | [drop step](http://tinkerpop.apache.org/docs/current/reference/#drop-step)
fold       | 充当用于计算结果聚合值的屏障                                                                                  | [fold step](http://tinkerpop.apache.org/docs/current/reference/#fold-step)
group      | 根据指定的标签将值分组                                                                                     | [group step](http://tinkerpop.apache.org/docs/current/reference/#group-step)
has        | 用于筛选属性、顶点和边缘。 支持`hasLabel`、`hasId`、`hasNot` 和 `has` 变体                                          | [has step](http://tinkerpop.apache.org/docs/current/reference/#has-step)
inject     | 将值注入流中                                                                                          | [inject step](http://tinkerpop.apache.org/docs/current/reference/#inject-step)
is         | 用于通过布尔表达式执行筛选器                                                                                  | [is step](http://tinkerpop.apache.org/docs/current/reference/#is-step)
limit      | 用于限制遍历中的项数                                                                                      | [limit step](http://tinkerpop.apache.org/docs/current/reference/#limit-step)
local      | 本地包装遍历的某个部分，类似于子查询                                                                              | [local step](http://tinkerpop.apache.org/docs/current/reference/#local-step)
not        | 用于生成筛选器的求反结果                                                                                    | [not step](http://tinkerpop.apache.org/docs/current/reference/#not-step)
optional   | 如果生成了某个结果，则返回指定遍历的结果，否则返回调用元素                                                                   | [optional step](http://tinkerpop.apache.org/docs/current/reference/#optional-step)
or         | 确保至少有一个遍历会返回值                                                                                   | [or step](http://tinkerpop.apache.org/docs/current/reference/#or-step)
order      | 按指定的排序顺序返回结果                                                                                    | [order step](http://tinkerpop.apache.org/docs/current/reference/#order-step)
path       | 返回遍历的完整路径                                                                                       | [path step](http://tinkerpop.apache.org/docs/current/reference/#addedge-step)
project    | 将属性投影为映射                                                                                        | [project step](http://tinkerpop.apache.org/docs/current/reference/#project-step)
properties | 返回指定标签的属性                                                                                       | [properties step](http://tinkerpop.apache.org/docs/current/reference/#properties-step)
range      | 根据指定的值范围进行筛选                                                                                    | [range step](http://tinkerpop.apache.org/docs/current/reference/#range-step)
repeat     | 将步骤重复指定的次数。 用于循环                                                                                | [repeat step](http://tinkerpop.apache.org/docs/current/reference/#repeat-step)
sample     | 用于对遍历返回的结果采样                                                                                    | [sample step](http://tinkerpop.apache.org/docs/current/reference/#sample-step)
select     | 用于投影遍历返回的结果                                                                                     | [select step](http://tinkerpop.apache.org/docs/current/reference/#select-step)
store      | 用于遍历返回的非阻塞聚合                                                                                    | [store step](http://tinkerpop.apache.org/docs/current/reference/#store-step)
tree       | 将顶点中的路径聚合到树中                                                                                    | [tree step](http://tinkerpop.apache.org/docs/current/reference/#tree-step)
unfold     | 将迭代器作为步骤展开                                                                                      | [unfold step](http://tinkerpop.apache.org/docs/current/reference/#unfold-step)
union      | 合并多个遍历返回的结果                                                                                     | [union step](http://tinkerpop.apache.org/docs/current/reference/#union-step)
V          | 包括顶点与边之间的遍历所需的步骤：`V`、`E`、`out`、`in`、`both`、`outE`、`inE`、`bothE`、`outV`、`inV`、`bothV` 和 `otherV` | [order step](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps)
where      | 用于筛选遍历返回的结果。 支持 `eq`、`neq`、`lt`、`lte`、`gt`、`gte` 和 `between` 运算符                                | [where step](http://tinkerpop.apache.org/docs/current/reference/#where-step)