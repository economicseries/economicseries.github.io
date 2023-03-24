In the draft, a filter expression is a predicate written within "[?" and "]". It evaluates to true or false (or error), and determines whether nodes get selected from the primary query.

To evaluate a filter expression, it is necessary to define rules to derive the truth value for an expression.  

Given the document

```json
[{"a": true},{"a":false}]
```

the draft regards the filter expressions appearing in the queries
 
```
$[?(@.a)]
$[?(@.c)]
``` 

to be existence tests, the two queries produce

```
[{"a": true},{"a": false}]
[]
```

respectively.


The motivation for deriving the truth value for `@.a` and `@.c` as existence tests 
originates with [Goessner's 2007 article](https://goessner.net/articles/JsonPath/),
in particular, from this line

XPath           |JSONPath               |Result
----------------|-----------------------|--------------------------------
//book[isbn]    |$..book[?(@.isbn)]     |filter all books with isbn number


While the need to derive the truth value for an expression is present in all languages, the rules differ.


https://www.w3.org/TR/xpath-31/#id-filter-expression

[{"a": true},{"b":false}]

XPath 3.1

Query: ?*[?a]

Result: {"a": true}

Query: ?*[?b]

Result: 

Query: ?*[?c]

Result: 

Query: ?*[true]

Result: {"a": true},{"b":false}

Query: ?*[false]

Result: 

JMESPath

Query: [?a]

Result: {"a": true}

Query: [?b]

Result: 

Query: [?c]

Result: 

Query: [`true`]

Result: [{"a": true},{"b":false}]

Query: [`false`]

Result: 

JSONata

Query: *[a]

Result: {"a": true}

Query: *[b]

Result: no match

Query: *[c]

Result: no match

Query: *[true]

Result: [{"a": true},{"b":false}]

Query: *[false]

Result: no match

Goessner JSONPath

Query: $[?(@.a)]

Result: [{"a": true}]

Query: $[?(@.b)]

Result: []

Query: $[?(@.c)]

Result: []

Query: $[?(true)]

Result: [{"a": true},{"b":false}]

Query: 

Result: []

Jayway JSONPath
NewtonSoft Json.Net

Query: $[?(@.a)]

Result: [{"a": true}]

Query: $[?(@.b)]

Result: [{"b": false}]

Query: $[?(@.c)]

Result: []

Query: $[?(true)]

Result: Error

Query: [`false`]

Result: Error

the predicate expression is coerced to an xs:boolean value, called the predicate truth value

 "Those items for which the predicate truth value is true are retained, and those for which the predicate truth value is false are discarded."

XML

Document: 

<books>
  <book in-stock="true">Stamboul Train</book>
  <book in-stock="false">The Comedians</book>
</books>  

Query: //book[xs:boolean(@in-stock)]

Results:

<book in-stock="true">Stamboul Train</book>


<books>
  <book isbn="8838939810">Stamboul Train</book>
  <book isbn="">The Comedians</book>
</books>  

Query: //book[@isbn]

Results:

<book in-stock="true">Stamboul Train</book>

<books>
  <book><isbn>8838939810</isbn><title>Stamboul Train</title></book>
  <book><isbn></isbn><title>The Comedians<.title></book>
</books>  

//book[isbn/text()]

<book><isbn>8838939810</isbn><title>Stamboul Train</title></book>
