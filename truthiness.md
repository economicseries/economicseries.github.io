## Background

In the draft, a filter expression is a predicate written within "[?" and "]". 
It evaluates to true or false (or invalid.) The nodes for which the predicate 
truth value is true are selected, the ones for which the predicate truth value 
is false are discarded.

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
JavaScript implementation. While rules for defining truth value differed
between languages, they generally agreed that `@.foo` evaluated to 
the value part of a name-value pair matching on 'foo', 
and that a value of `true` evaluated to true and a value of 
`false` evaluated to false.    

- 2011 [Jayway JSONPath](https://github.com/json-path/JsonPath) implemented it's own expression language and followed
Goessner's original suggestion, as did [NewtonSoft Json.Net](https://www.newtonsoft.com/json/help/html/QueryJsonSelectToken.htm).  

As noted, the draft decided to follow Goessner's original suggestion, and be consistent with
Jayway JSONPath and NewtonSoft Json.Net. 

This comment proposes that that decision be revisited for the following reasons.

- It's inconsistent with other JSON query languages including [XPath 3.1](https://www.w3.org/TR/xpath-31/), 
[JMESPath](https://jmespath.org/specification.html), and [JSONata](https://jsonata.org/).


## It's inconsistent with other JSON query languages.

All of these examples use the JSON document

```json
[{"foo": true},{"foo":false}]
```

### Draft

Query: `$[?(@.foo)]`

Result: `[{"foo": true},{"foo": false}]`

Query: `$[?(@.bar)]`

Result: `[]`

Query: `$[?(true)]`

Result: Invalid

Query: `[false]`

Result: Invalid

### XPath 3.1

Query: `?*[?foo]`

Result: `{"foo": true}`

Query: `?*[?bar]`

Result: No match

Query: `?*[true]`

Result: `{"foo": true}{"foo": false}`

Query: `?*[false]`

Result: No match

### JMESPath

Query: `[?foo]`

Result: `[{"foo": true}]`

Query: `[?bar]`

Result: No match

Query: ``[?`true`]``

Result: `[{"foo": true},{"foo": false}]`

Query: ``[?`false`]``

Result: No match

### JSONata

Query: `*[foo]`

Result: `{"foo": true}`

Query: `*[bar]`

Result: no match

Query: `*[true]`

Result: `[{"foo": true},{"foo":false}]`

Query: `*[false]`

Result: no match

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
