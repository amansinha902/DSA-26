# Java Streams — Practice Revision Sheet
All questions solved during practice, grouped by the core operation each one teaches. Read the tip, try to write it from memory, then check yourself.

---

## FILTER

### Q: Filter even numbers from a list
**Input:** `[1, 2, 3, 4, 5, 6]`
**Output:** `[2, 4, 6]`
**Tip:** Yes/no test on each element → `filter`. `x % 2 == 0`.

### Q: Keep words with even length
**Input:** `["cat", "dog", "lion", "bear", "ox"]` (filter → length % 2 == 0)
**Output:** words where `w.length() % 2 == 0`
**Tip:** Same pattern as above, applied to `.length()` instead of the value itself.

### Q: Count strings with a specific prefix
**Input:** `["Alice", "Bob", "Annie", "Alex", "Charlie"]`
**Output:** `3`
**Tip:** `filter(x -> x.startsWith("A"))` then `.count()`. Remember `.count()` returns `long`, not `Integer`.

### Q: Check if any string contains a substring
**Input:** `["Java", "Stream API", "Lambda"]` → does any contain `"API"`?
**Output:** `true`
**Tip:** "Any/all/none match" phrasing → `anyMatch`/`allMatch`/`noneMatch`, NOT `filter`+`forEach`. Match the shape of the expected answer (boolean vs. list) to the right terminal op.

### Q: Remove null values from a list
**Input:** `["Java", null, "Stream", null, "API"]`
**Output:** `[Java, Stream, API]`
**Tip:** `filter(Objects::nonNull)` — ready-made predicate, same as `x -> x != null`.

### Q: Find words starting with a vowel
**Input:** `["Apple", "Banana", "Orange", "Grapes", "Elephant"]`
**Output:** `[Apple, Orange, Elephant]`
**Tip:** `||` joins full boolean expressions, never plain values — `x.startsWith("A") || x.startsWith("E") || ...`, not `startsWith("A" || "E")`.

### Q: Find common elements between two lists
**Input:** `list1=[1,2,3,4,5]`, `list2=[3,4,5,6,7]`
**Output:** `[3, 4, 5]`
**Tip:** `filter(x -> list2.contains(x))` — the collection being checked comes before the dot, the single element goes inside `contains(...)`. Easy to accidentally flip.

---

## MAP

### Q: Convert list of strings to uppercase
**Input:** `["java", "stream", "api"]`
**Output:** `[JAVA, STREAM, API]`
**Tip:** `map(w -> w.toUpperCase())` — one-to-one transform.

### Q: Square numbers, then keep squares over 50
**Input:** `nums` → square each, keep > 50
**Tip:** Order matters: `map` first (to get squares), THEN `filter` (can't filter on squared values before they exist).

### Q: Reverse each string in a list
**Input:** `["Java", "Stream", "API"]`
**Output:** `[avaJ, maertS, IPA]`
**Tip:** `map(x -> new StringBuilder(x).reverse().toString())`. Remember `.reverse()` needs parentheses — it's a method, not a field.

### Q: Convert list of strings to sentence case
**Input:** `["hello", "WORLD", "jAVA", "stREAM"]`
**Output:** `[Hello, World, Java, Stream]`
**Tip:** Split first letter from the rest: `Character.toUpperCase(w.charAt(0)) + w.substring(1).toLowerCase()`. "Treat first char differently from the rest" → always `charAt(0)` + `substring(1)`.

---

## FOREACH

### Q: Print "even"/"odd" next to each number
**Input:** `nums` → e.g. `"4 is even"`
**Tip:** `forEach` with a multi-line `{ }` block containing `if`/`else`. No `return` needed — `forEach`'s lambda doesn't produce a value, just performs an action.

---

## REDUCE

### Q: Sum of numbers in a list
**Input:** `[1, 2, 3, 4, 5]`
**Output:** `15`
**Tip:** `reduce(0, (a,b) -> a+b)`. **With an identity value → returns plain `int`, NOT `Optional`.** Without identity → returns `Optional<Integer>`.

### Q: Find the longest string
**Input:** `["Java", "Stream", "API", "Development"]`
**Output:** `Development`
**Tip:** `reduce((x,y) -> x.length() > y.length() ? x : y)` — no identity here, so wrap result with `.orElse(...)`.

### Q: Concatenate strings with reduce
**Input:** `["Stream", "API", "is", "powerful"]`
**Output:** `Stream API is powerful`
**Tip:** `reduce((x,y) -> x + " " + y)`. (For simple joining, `Collectors.joining(" ")` is simpler and more idiomatic — reduce is for when combining logic is more complex than a fixed delimiter.)

### Q: Concatenate strings in REVERSE order
**Input:** `["Stream", "API", "is", "awesome"]`
**Output:** `awesome is API Stream`
**Tip:** Flip the lambda parameters: `reduce((x,y) -> y + " " + x)`. Stream still walks left-to-right — flipping params changes whether new elements get prepended or appended to the running result. Trace 2-3 steps by hand if unsure.

### Q: Sum of squares of even numbers
**Input:** `[1,2,3,4,5,6]`
**Output:** `56`
**Tip:** Chain `filter` → `map` → `reduce`: filter evens, square them, sum. Classic three-stage combo — recognize this shape often.

---

## SORTED + COMPARATOR

### Q: Sort a list in descending order
**Input:** `[3, 5, 1, 4, 2]`
**Output:** `[5, 4, 3, 2, 1]`
**Tip:** `sorted((a,b) -> Integer.compare(b,a))` (flip args) or `sorted(Comparator.reverseOrder())`.

### Q: Find Nth largest element
**Input:** `[10, 20, 50, 40, 30]`, N=3 → `Output: 30`
**Tip:** Sort descending, `.skip(N-1).findFirst()`. **Nth element sits at index N-1** — easy off-by-one.

### Q: 2nd max element (distinct values)
**Input:** `[10,20,50,40,30]` → `Output: 40`
**Tip:** Same as above, but add `.distinct()` before `.skip()` if duplicates should only count once. `distinct()` is intermediate (keeps stream alive) — unlike `collect(toSet())`, which ends the pipeline and loses order.

### Q: Top 3 highest-scoring students (custom objects)
**Tip:** `Comparator.comparing(x -> x.score).reversed()` for descending by a field — NOT `Comparator.reverseOrder(fieldExtractor)`, which doesn't take an argument at all. Then `.limit(3)`, then `.map(...)` to names.

### Q: Find all palindromic strings
**Input:** `["radar", "level", "world", "java"]`
**Output:** `[radar, level]`
**Tip:** `filter(w -> w.equals(new StringBuilder(w).reverse().toString()))`. Comparing against a reversal, not building one — simpler tool than the IntStream index trick.

---

## GROUPINGBY (all variations — deep dive)

### Q: Group words by first letter
**Tip:** `groupingBy(w -> w.charAt(0))`. Check: classifier returns `Character`, so Map key type is `Character`, not `String`.

### Q: Group words by first letter, count instead of list
**Tip:** `groupingBy(w -> w.charAt(0), Collectors.counting())` → `Map<Character, Long>` — `counting()` always returns `Long`.

### Q: Group words by length, store UPPERCASE versions
**Tip:** `groupingBy(w -> w.length(), Collectors.mapping(w -> w.toUpperCase(), Collectors.toList()))`. `mapping`'s 2nd arg is always a `Collectors.something()`, never a plain lambda.

### Q: Count duplicate fruit occurrences (`Function.identity()`)
**Input:** `["apple","apple","banana","apple","orange","banana","papaya"]`
**Output:** `{banana=2, orange=1, papaya=1, apple=3}`
**Tip:** `groupingBy(w -> w, Collectors.counting())` — group elements by themselves to tally duplicates. `Function.identity()` means the same as `w -> w`.

### Q: Group custom objects (Employee) by age, keep full objects
**Tip:** No downstream needed at all — plain `groupingBy(e -> e.getAge())` if the task wants full objects, not just one field.

### Q: Group Items by name, sum quantity per group
**Tip:** `groupingBy(i -> i.getName(), Collectors.summingInt(i -> i.getQty()))`. Check field carefully (qty vs price) and check `summingInt` → `Integer` return type match.

### Q: Group Employees by age, store only names
**Tip:** `groupingBy(e -> e.getAge(), Collectors.mapping(e -> e.getName(), Collectors.toList()))`. Classifier decides the BUCKET; mapping's transform decides what's STORED — easy to swap these two fields by mistake.

### Q: Group Items by price into a Set of names, sorted by price (TreeMap)
**Tip:** 3-argument groupingBy, all inside ONE set of parens: `groupingBy(classifier, mapFactory, downstream)` → `groupingBy(i -> i.getPrice(), TreeMap::new, Collectors.mapping(i -> i.getName(), Collectors.toSet()))`.

### Q: Same as above, but sorted DESCENDING
**Tip:** Can't use bare `TreeMap::new` once you need a custom order — write `() -> new TreeMap<>(Comparator.reverseOrder())` instead.

### Q: Find most common word length (nested Map lookup)
**Tip:** `groupingBy` + `counting()` gives a `Map`. To find the max, you must convert BACK to a stream: `map.entrySet().stream().max(Comparator.comparing(Map.Entry::getValue))`. A plain `Map` has no `.max()` of its own.

### Q: Highest-paid employee per department, unwrapped (no Optional)
**Tip:** `groupingBy(e -> e.getDepartment(), Collectors.collectingAndThen(Collectors.maxBy(Comparator.comparing(e -> e.getSalary())), Optional::get))`. Build nested collectors from the INSIDE OUT: comparator → maxBy → collectingAndThen → groupingBy.

### Q: Group employees by department, average salary
**Input:** salaries by dept → **Output:** `{HR=55000.0, IT=77500.0, Finance=70000.0}`
**Tip:** `groupingBy(e -> e.department, Collectors.averagingDouble(x -> x.salary))`. `averagingDouble`/`averagingInt` always return `Double`.

### Q: Group anagram words together
**Input:** `["listen","silent","enlist","google","elbow","below"]`
**Output:** `{eilnst=[listen,silent,enlist], eggloo=[google], below=[elbow,below]}`
**Tip:** Classifier can be an entire mini-pipeline, not just a field: `word -> word.chars().sorted().mapToObj(c -> String.valueOf((char)c)).collect(Collectors.joining())`. Sorted letters = anagram signature.

---

## PARTITIONINGBY

### Q: Split numbers into even/odd
**Input:** `[1..9]`
**Output:** `{false=[1,3,5,7,9], true=[2,4,6,8]}`
**Tip:** Takes a plain `Predicate` (returns boolean directly) — NOT a custom-label classifier like groupingBy. Keys are ALWAYS `true`/`false`, never renamed.

### Q: Partition palindromes vs non-palindromes
**Tip:** Same idea, predicate = the palindrome check from the sorted section above.

---

## FLATMAP

### Q: Flatten a list of lists
**Input:** `[[1,2,3],[4,5],[6,7,8,9]]`
**Output:** `[1,2,3,4,5,6,7,8,9]`
**Tip:** `flatMap(x -> x.stream())` — no extra "combine" step needed, flatMap merges automatically.

### Q: Cartesian product — pair every element of list1 with every element of list2
**Input:** `list1=[1,2,3]`, `list2=[4,5]`
**Output:** `[(1,4),(1,5),(2,4),(2,5),(3,4),(3,5)]`
**Tip:** Nested shape: `list1.stream().flatMap(i -> list2.stream().map(j -> "(" + i + "," + j + ")"))`. "For each X, produce several Y's, then flatten" = flatMap wrapping an inner map. Name lambda params consistently — using a different name inside than the outer lambda's parameter is a common bug.

---

## COLLECTORS.TOMAP

### Q: Map words to their length
**Tip:** `toMap(w -> w, w -> w.length())`. Needs BOTH key and value lambda reading from the SAME original element — can't `map()` first, since that discards the original.

### Q: Map products to prices
**Tip:** Same shape: `toMap(x -> x.name, x -> x.price)`.

---

## COLLECTORS.JOINING

### Q: Join words with a comma delimiter
**Input:** `["Java","Stream","API"]`
**Output:** `Java, Stream, API`
**Tip:** `Collectors.joining(", ")` — the argument is a plain STRING delimiter, not a combining lambda (that's what `reduce` is for). Double-check the exact delimiter text character by character.

---

## INDEX-BASED STREAMING (IntStream.range as positions)

### Q: Reverse a string using streams
**Input:** `"hello"` → **Output:** `"olleh"`
**Tip:** No built-in `.reverse()` on Stream. Generate mirrored positions: `IntStream.range(0, s.length()).map(i -> s.length()-1-i)`, then `.mapToObj(i -> String.valueOf(s.charAt(i)))`, then `.collect(Collectors.joining())`. `mapToObj` bridges primitive `IntStream` → object `Stream` (needed since `.map` on IntStream only goes int→int).

### Q: Running sum of a list
**Input:** `[1,2,3,4,5]` → **Output:** `[1,3,6,10,15]`
**Tip:** `IntStream.range(0, list.size()).map(i -> list.subList(0,i+1).stream().mapToInt(x->x).sum()).boxed().collect(toList())`. Honest caveat: a plain loop with a running total is more efficient — this recomputes overlapping work each step.

---

## STREAM.ITERATE (sequence generation)

### Q: Generate first 10 Fibonacci numbers
**Output:** `[0,1,1,2,3,5,8,13,21,34]`
**Tip:** Each value needs the PREVIOUS TWO, so iterate over a pair `int[]{prev,curr}`: `Stream.iterate(new int[]{0,1}, p -> new int[]{p[1], p[0]+p[1]}).limit(10).map(p -> p[0])`. Always need `.limit()` — iterate is infinite by default.

---

## OPTIONAL / MAX / MIN

### Q: Find maximum in a list
**Input:** `[10,20,30,40,50]` → **Output:** `50`
**Tip:** `max((a,b) -> Integer.compare(a,b))` or `max(Integer::compare)`. Returns `Optional<Integer>` — unwrap with `.orElseThrow()`.

### Q: Find average of numbers
**Input:** `[10,20,30,40,50]` → **Output:** `30.0`
**Tip:** `mapToInt(x->x).average()` returns `OptionalDouble` (primitive-specific Optional), not plain `Double`. Unwrap with `.orElse(0.0)`.

---

## STRING-SPECIFIC PATTERNS (`.chars()`, char comparisons)

### Q: First non-repeated character in a string
**Input:** `"swiss"` → **Output:** `w`
**Tip:** `input.chars().mapToObj(c -> (char)c).filter(c -> input.indexOf(c) == input.lastIndexOf(c)).findFirst()`. If first occurrence == last occurrence, it appears exactly once.

### Q: Character frequency count
**Input:** `"success"` → **Output:** `{s=3, u=1, c=2, e=1}`
**Tip:** `input.chars().mapToObj(c -> (char)c).collect(groupingBy(x->x, counting()))`.

### Q: Most frequent character in a string
**Input:** `"success"` → **Output:** `s`
**Tip:** Build the frequency map above, THEN `.entrySet().stream().max(Comparator.comparing(Map.Entry::getValue)).map(Map.Entry::getKey).orElseThrow()`.

### Q: Check if two strings are anagrams
**Input:** `"triangle"`, `"integral"` → **Output:** `true`
**Tip:** Sort both strings' characters, compare results: `s.chars().mapToObj(c -> String.valueOf((char)c)).sorted().collect(joining())`, then `.equals(...)`. Anagrams always produce identical sorted output.

---

## DUPLICATE-DETECTION (HashSet.add() trick)

### Q: Find duplicate elements in a list
**Input:** `[1,2,3,4,2,5,1]` → **Output:** `[1,2]`
**Tip:** `Set<Integer> seen = new HashSet<>();` outside the stream, then `filter(x -> !seen.add(x))`. `.add()` returns `false` if already present — that's your duplicate signal. NOT parallel-safe (mutates external state).

---

## MIXED / MULTI-CONCEPT (combine several tools)

### Q: 2nd max element in a list
**Tip:** See "sorted + comparator" section — `sorted(reverseOrder).distinct().skip(1).findFirst()`.

### Q: Filter, group, and sort a Map by value
**Input:** `Map.of("A",5,"B",15,"C",10,"D",20)` → filter values > 10 → **Output:** `[B, D]`
**Tip:** `map.entrySet().stream().filter(x -> x.getValue()>10).map(x -> x.getKey()).collect(toList())`. Entry objects need `.getKey()`/`.getValue()` to extract pieces.

### Q: Sort a list by element frequency, descending
**Input:** `[4,6,2,6,4,4,6,2]` → **Output:** `[4,4,4,6,6,6,2,2]` (or preserving relative tie order)
**Tip:** Two approaches:
1. **Efficient:** `groupingBy(x->x, counting())` once, then `distinct().sorted(comparing(x -> freqMap.get(x)).reversed())`, then `flatMap(x -> Collections.nCopies(freqMap.get(x).intValue(), x).stream())`.
2. **Simpler but O(n log n) recomputation:** `sorted(comparing(x -> Collections.frequency(numbers, x)).reversed())` directly on the original list — no distinct/flatMap needed, but recomputes frequency on every comparison.
Comparators CAN reach outside the element itself into external maps/lists — not limited to intrinsic properties like `.length()`.

### Q: Median of a list
**Input:** `[3,1,4,2,5]` → **Output:** `3.0`
**Tip:** Sort into a real `List` first (streams can't natively answer "what's in the middle" without knowing size). Then plain Java: odd size → `sorted.get(size/2)`; even size → average of `sorted.get(size/2 -1)` and `sorted.get(size/2)`, divide by `2.0` (not `2`, to force decimal division).

### Q: Reverse a string using StringBuilder inside a stream (vs. pure streams)
**Tip:** `new StringBuilder(input).reverse().toString()` alone is the honest simplest answer if allowed. The `IntStream.range` version is specifically for when an interviewer says "no StringBuilder.reverse(), do it via streams."

---

## THE #1 DEBUGGING HABIT (applies everywhere above)

Before chaining a method or declaring a variable type, ask: **"what type does the thing on my left just produced?"**
- `reduce` with identity → plain value. Without identity → `Optional`.
- `counting()` → `Long`. `summingInt` → `Integer`. `averagingInt`/`averagingDouble` → `Double`.
- `collect(toList/toSet/toMap)` → ends the pipeline, no more stream methods after.
- `max`/`min`/`findFirst`/`findAny` → `Optional<T>`.
- `average()`/`max()`/`min()` on a PRIMITIVE stream (IntStream etc.) → `OptionalDouble`/`OptionalInt`, not the boxed `Optional<T>`.

Almost every bug across all practice sessions traced back to skipping this one check.