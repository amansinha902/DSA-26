# Java Streams — Complete Syntax Cheat Sheet

Quick-reference for every operation, exact syntax, what it returns, and when to reach for it.

---

## How to use this sheet

Every entry follows the same format:
- **Syntax** — exact code shape
- **Returns** — what type you get back (critical for knowing what you can chain next)
- **Use when** — the plain-English trigger for reaching for this tool

---

## PART 1: INTERMEDIATE OPERATIONS
*(Return a Stream — you can keep chaining more operations after these)*

### `filter` — keep elements matching a condition
```java
stream.filter(x -> booleanCondition)
```
- **Returns:** `Stream<T>`
- **Use when:** "keep only the ones where..."
- **Example:** `nums.stream().filter(n -> n > 5)`

### `map` — transform each element, 1-to-1
```java
stream.map(x -> transformedValue)
```
- **Returns:** `Stream<R>`
- **Use when:** "turn each X into a Y"
- **Example:** `words.stream().map(w -> w.toUpperCase())`

### `flatMap` — transform AND flatten, 1-to-many
```java
stream.flatMap(x -> anotherStream)
```
- **Returns:** `Stream<R>` (flattened)
- **Use when:** each element produces its own collection/stream, and you want one merged flat stream
- **Example:** `nestedLists.stream().flatMap(List::stream)`
- **Cartesian pairing pattern:**
```java
list1.stream().flatMap(a -> list2.stream().map(b -> combine(a, b)))
```

### `distinct` — remove duplicates
```java
stream.distinct()
```
- **Returns:** `Stream<T>`
- **Use when:** need unique values, uses `.equals()` to compare
- **Note:** preserves encounter order (unlike collecting into a `Set`)

### `sorted` — sort elements
```java
stream.sorted()                              // natural order
stream.sorted(Comparator.naturalOrder())      // same as above, explicit
stream.sorted(Comparator.reverseOrder())      // descending
stream.sorted(Comparator.comparing(x -> x.getField()))              // by extracted field
stream.sorted(Comparator.comparing(x -> x.getField()).reversed())   // by field, descending
stream.sorted(Comparator.comparing(X::getA).thenComparing(X::getB)) // multi-field
```
- **Returns:** `Stream<T>`
- **Use when:** need elements in a specific order

### `limit` — take only the first N
```java
stream.limit(n)
```
- **Returns:** `Stream<T>`
- **Use when:** "top N", "first N", bounding an infinite stream

### `skip` — drop the first N
```java
stream.skip(n)
```
- **Returns:** `Stream<T>`
- **Use when:** "everything after the first N", or combined with `sorted`+`findFirst` for "Nth element"

### `peek` — look at elements without changing them (debugging only)
```java
stream.peek(x -> System.out.println("debug: " + x))
```
- **Returns:** `Stream<T>` (unchanged)
- **Use when:** debugging a pipeline — never for real side effects in production code

---

## PART 2: TERMINAL OPERATIONS
*(End the pipeline — return a concrete value, not a Stream. No more chaining stream methods after these.)*

### `forEach` — do something with each element, no result
```java
stream.forEach(x -> doSomething(x))
```
- **Returns:** `void`
- **Use when:** printing, side effects, no transformation needed

### `collect` — gather into a collection (see Part 3 for all Collectors)
```java
stream.collect(Collectors.toList())
```
- **Returns:** whatever the collector produces (`List`, `Set`, `Map`, `String`, etc.)

### `count` — how many elements
```java
long count = stream.count();
```
- **Returns:** `long`

### `reduce` — combine everything into one value
```java
Optional<T> result = stream.reduce((a, b) -> combine(a, b));           // no identity
T result = stream.reduce(identity, (a, b) -> combine(a, b));            // with identity
T result = stream.reduce(identity, accumulator, combiner);               // for parallel streams
```
- **Returns:** `Optional<T>` (no identity) or `T` directly (with identity)
- **Use when:** sum, product, concatenation, "combine everything into one thing"
- **Common patterns:**
```java
int sum = nums.stream().reduce(0, (a, b) -> a + b);
int product = nums.stream().reduce(1, (a, b) -> a * b);
String joined = words.stream().reduce("", (a, b) -> a + " " + b).trim();
```

### `anyMatch` / `allMatch` / `noneMatch` — boolean checks (short-circuiting)
```java
boolean b1 = stream.anyMatch(x -> condition);   // true if AT LEAST ONE matches
boolean b2 = stream.allMatch(x -> condition);    // true if ALL match
boolean b3 = stream.noneMatch(x -> condition);    // true if NONE match
```
- **Returns:** `boolean`

### `findFirst` / `findAny` — get one element (short-circuiting)
```java
Optional<T> result = stream.findFirst();   // first in encounter order
Optional<T> result = stream.findAny();      // any one (faster in parallel streams)
```
- **Returns:** `Optional<T>`

### `min` / `max` — find smallest/largest
```java
Optional<T> result = stream.max(Comparator.comparing(x -> x.getField()));
Optional<T> result = stream.min(Comparator.naturalOrder());
```
- **Returns:** `Optional<T>`
- **Note:** needs a `Comparator`, not a simple "extract value" lambda directly

### `toArray` — convert to array
```java
Object[] arr = stream.toArray();
String[] arr = stream.toArray(String[]::new);   // typed array
```
- **Returns:** array

---

## PART 3: COLLECTORS (used inside `.collect(...)`)

### Basic collections
```java
.collect(Collectors.toList())
.collect(Collectors.toSet())
.collect(Collectors.toCollection(TreeSet::new))    // custom collection type
```

### `toMap` — build a Map, one entry per element
```java
.collect(Collectors.toMap(
    x -> keyFromX,
    x -> valueFromX
))
.collect(Collectors.toMap(
    x -> keyFromX,
    x -> valueFromX,
    (existing, incoming) -> existing         // merge function — REQUIRED if keys can collide
))
.collect(Collectors.toMap(
    x -> keyFromX,
    x -> valueFromX,
    (existing, incoming) -> existing,
    TreeMap::new                               // custom Map type (e.g. sorted)
))
```
- **Returns:** `Map<K, V>`
- **Throws `IllegalStateException`** if two elements produce the same key and no merge function is given

### `joining` — combine Strings
```java
.collect(Collectors.joining())                        // no separator
.collect(Collectors.joining(", "))                       // with delimiter
.collect(Collectors.joining(", ", "[", "]"))                // delimiter + prefix + suffix
```
- **Returns:** `String`

### `counting`
```java
.collect(Collectors.counting())
```
- **Returns:** `Long`

### Numeric aggregation
```java
.collect(Collectors.summingInt(x -> x.getIntField()))         // → Integer
.collect(Collectors.summingDouble(x -> x.getDoubleField()))     // → Double
.collect(Collectors.averagingInt(x -> x.getIntField()))          // → Double
.collect(Collectors.averagingDouble(x -> x.getDoubleField()))     // → Double
.collect(Collectors.summarizingInt(x -> x.getIntField()))          // → IntSummaryStatistics
```
`IntSummaryStatistics` gives you `.getMin()`, `.getMax()`, `.getAverage()`, `.getSum()`, `.getCount()` all at once.

### `groupingBy` — the big one (full signatures)
```java
// 1-arg: classifier only, default downstream is toList()
.collect(Collectors.groupingBy(x -> bucketKey))
// → Map<K, List<T>>

// 2-arg: classifier + downstream collector
.collect(Collectors.groupingBy(x -> bucketKey, downstreamCollector))
// → Map<K, WhateverDownstreamProduces>

// 3-arg: classifier + map factory (e.g. sorted) + downstream collector
.collect(Collectors.groupingBy(x -> bucketKey, TreeMap::new, downstreamCollector))
// → TreeMap<K, WhateverDownstreamProduces>, keys sorted
```

**Common downstream collectors, plugged into the 2nd argument:**
```java
Collectors.groupingBy(x -> key, Collectors.counting())                              // Map<K, Long>
Collectors.groupingBy(x -> key, Collectors.summingInt(x -> x.getVal()))              // Map<K, Integer>
Collectors.groupingBy(x -> key, Collectors.mapping(x -> x.getName(), Collectors.toList()))  // Map<K, List<String>>
Collectors.groupingBy(x -> key, Collectors.mapping(x -> x.getName(), Collectors.toSet()))   // Map<K, Set<String>>
Collectors.groupingBy(x -> key, Collectors.maxBy(Comparator.comparing(x -> x.getVal())))    // Map<K, Optional<T>>
Collectors.groupingBy(x -> key, Collectors.groupingBy(x -> innerKey))                       // nested: Map<K, Map<K2, List<T>>>
Collectors.groupingBy(x -> key, Collectors.collectingAndThen(innerCollector, finisher))     // unwrap/transform result
```

### `partitioningBy` — special case of groupingBy, always exactly 2 buckets
```java
.collect(Collectors.partitioningBy(x -> booleanCondition))
// → Map<Boolean, List<T>>  — keys are ALWAYS true/false, never custom

.collect(Collectors.partitioningBy(x -> booleanCondition, downstreamCollector))
// → Map<Boolean, WhateverDownstreamProduces>
```

### `mapping` — transform elements before collecting (usually inside groupingBy)
```java
Collectors.mapping(x -> transform(x), Collectors.toList())
Collectors.mapping(x -> transform(x), Collectors.toSet())
Collectors.mapping(x -> transform(x), Collectors.joining(", "))
```
- **1st arg:** transform lambda (like `map`'s lambda)
- **2nd arg:** a `Collectors.something()` — never a plain lambda

### `collectingAndThen` — run a collector, then transform its result
```java
Collectors.collectingAndThen(innerCollector, finishingFunction)
```
- **Common use — unwrap an Optional:**
```java
Collectors.collectingAndThen(
    Collectors.maxBy(Comparator.comparing(x -> x.getVal())),
    Optional::get                                                    // or: opt -> opt.get()
)
```

### `teeing` (Java 12+) — combine results of two collectors into one
```java
.collect(Collectors.teeing(
    collector1,
    collector2,
    (result1, result2) -> combine(result1, result2)
))
```

---

## PART 4: STREAM CREATION

```java
collection.stream()                                    // from any Collection
Stream.of(a, b, c)                                       // from individual values
Arrays.stream(array)                                       // from an array
IntStream.range(0, 5)                                        // 0,1,2,3,4 (exclusive end)
IntStream.rangeClosed(0, 5)                                     // 0,1,2,3,4,5 (inclusive end)
Stream.iterate(seed, x -> next(x)).limit(n)                       // sequence generation, MUST limit
Stream.generate(() -> value).limit(n)                                // repeated/random generation, MUST limit
Files.lines(path)                                                       // lazy line-by-line file read
Stream.empty()                                                            // empty stream
"someString".chars().mapToObj(c -> (char) c)                                // stream of characters from a String
```

---

## PART 5: PRIMITIVE STREAMS

```java
IntStream, LongStream, DoubleStream          // avoid autoboxing overhead

// Converting INTO primitive streams:
stream.mapToInt(x -> x.getIntField())
stream.mapToDouble(x -> x.getDoubleField())

// Converting OUT of primitive streams:
intStream.boxed()                              // IntStream → Stream<Integer>
intStream.mapToObj(i -> someObject(i))           // IntStream → Stream<T>, any type

// Extra methods only on primitive streams:
intStream.sum()
intStream.average()                              // → OptionalDouble
intStream.summaryStatistics()                       // → IntSummaryStatistics
```

---

## PART 6: OPTIONAL — methods you'll actually use

```java
optional.isPresent()                       // boolean — is there a value?
optional.isEmpty()                          // boolean — opposite (Java 11+)
optional.get()                                // unwrap — throws if empty, use carefully
optional.orElse(defaultValue)                   // unwrap with fallback
optional.orElseGet(() -> computeDefault())        // unwrap with LAZY fallback
optional.orElseThrow()                              // unwrap or throw NoSuchElementException
optional.orElseThrow(() -> new CustomException())     // unwrap or throw custom exception
optional.ifPresent(x -> doSomething(x))                  // run code only if present
optional.map(x -> transform(x))                             // transform the value inside, if present
```

---

## PART 7: DECISION TABLE — "which operation do I need?"

| I want to... | Use |
|---|---|
| Keep elements matching a condition | `filter` |
| Transform each element | `map` |
| Transform + flatten nested collections | `flatMap` |
| Remove duplicates | `distinct` |
| Sort | `sorted` + `Comparator` |
| Take first N | `limit` |
| Skip first N | `skip` |
| Combine into one value (sum, product, concat) | `reduce` |
| Check if any/all/none match | `anyMatch` / `allMatch` / `noneMatch` |
| Get one matching element | `findFirst` / `findAny` |
| Find biggest/smallest | `max` / `min` with `Comparator` |
| Count elements | `count` |
| Bucket elements by any property | `groupingBy` |
| Bucket into exactly true/false | `partitioningBy` |
| Bucket AND transform what's in each bucket | `groupingBy` + `mapping` |
| Bucket AND count each bucket | `groupingBy` + `counting` |
| Bucket AND sum a field per bucket | `groupingBy` + `summingInt` |
| Bucket AND find max/min per bucket | `groupingBy` + `maxBy`/`minBy` |
| Group by two levels | nested `groupingBy` |
| Unwrap an Optional result from a collector | `collectingAndThen` |
| Build a Map (one entry per element) | `toMap` |
| Join Strings together | `joining` |
| Use index/position instead of value | `IntStream.range(...).mapToObj(i -> ...)` |
| Pair every element of A with every element of B | `flatMap` wrapping inner `.stream().map(...)` |

---

## PART 8: THE TYPE-TRACKING HABIT (the single most useful debugging tool)

Before chaining anything, or before declaring a variable type, ask: **"what does the thing on my left just produced?"**

| This call... | ...produces this type |
|---|---|
| `.stream()` on anything | `Stream<T>` |
| `.filter()`, `.map()`, `.sorted()`, `.distinct()`, `.limit()`, `.skip()` | `Stream<T>` (still chainable) |
| `.collect(toList())` | `List<T>` |
| `.collect(toSet())` | `Set<T>` |
| `.collect(toMap(...))` | `Map<K,V>` |
| `.collect(groupingBy(...))` | `Map<K, List<T>>` (or whatever downstream says) |
| `.collect(joining(...))` | `String` |
| `.collect(counting())` | `Long` |
| `.collect(summingInt(...))` | `Integer` |
| `.collect(averagingInt(...))` | `Double` |
| `.reduce(identity, ...)` | `T` (plain value) |
| `.reduce(...)` (no identity) | `Optional<T>` |
| `.max()` / `.min()` | `Optional<T>` |
| `.findFirst()` / `.findAny()` | `Optional<T>` |
| `.anyMatch()` / `.allMatch()` / `.noneMatch()` | `boolean` |
| `.count()` | `long` |

Once you know what you're holding, you immediately know what's legal to do next — this one check prevents the majority of stream compile errors.