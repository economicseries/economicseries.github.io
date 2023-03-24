In the draft, a filter expression is a predicate written within "[?" and "]". 
It evaluates to true or false (or error), and determines whether nodes get selected from the primary query.

To evaluate a filter expression, it is necessary to define rules to derive the truth value for an expression.  

According to the rules in the draft, given the document

```json
[{"foo": true},{"foo":false}]
```

the filter expressions appearing in the queries
 
```
$[?(@.foo)]
$[?(@.bar)]
``` 

are existence tests, and these queries produce

```
[{"foo": true},{"foo": false}]
[]
```

respectively.


The motivation for deriving the truth value for `@.foo` and `@.bar` as existence tests 
originates with [Goessner's 2007 article](https://goessner.net/articles/JsonPath/),
in particular, from this line

XPath           |JSONPath               |Result
----------------|-----------------------|--------------------------------
//book[isbn]    |$..book[?(@.isbn)]     |filter all books with isbn number

Thereafter, implementations split. 

- Implementations that used JavaScript (or another dynamic language) for 
evaluating filter expressions evaluated `@.foo` and `@.bar` to truth values 
according to the rules of that language. These included Goessner's 
JavaScript implementation. While rules for defining truth value differ
between languages, they generally agree that `@.foo` evaluated to 
the value part of a name-value pair matching on 'foo', 
and that a value of `true` evaluated to true and a value of 
`false` evaluated to false.    

- 2011 Jayway JSONPath implemented it's own expression language and followed
Goessner's original suggestion, as did NewtonSoft Json.Net.  


While the need to derive the truth value for an expression is present in all languages, the rules differ.


https://www.w3.org/TR/xpath-31/#id-filter-expression

[{"foo": true},{"bar":false}]

XPath 3.1

Query: ?*[?foo]

Result: {"foo": true}

Query: ?*[?bar]

Result: 

Query: ?*[?c]

Result: 

Query: ?*[true]

Result: {"foo": true},{"bar":false}

Query: ?*[false]

Result: 

JMESPath

Query: [?foo]

Result: {"foo": true}

Query: [?bar]

Result: 

Query: [?c]

Result: 

Query: [`true`]

Result: [{"foo": true},{"bar":false}]

Query: [`false`]

Result: 

JSONata

Query: *[foo]

Result: {"foo": true}

Query: *[bar]

Result: no match

Query: *[c]

Result: no match

Query: *[true]

Result: [{"foo": true},{"bar":false}]

Query: *[false]

Result: no match

Goessner JSONPath

Query: $[?(@.foo)]

Result: [{"foo": true}]

Query: $[?(@.bar)]

Result: []

Query: $[?(@.c)]

Result: []

Query: $[?(true)]

Result: [{"foo": true},{"bar":false}]

Query: 

Result: []

Jayway JSONPath
NewtonSoft Json.Net

Query: $[?(@.foo)]

Result: [{"foo": true}]

Query: $[?(@.bar)]

Result: [{"bar": false}]

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
