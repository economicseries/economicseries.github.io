
Goessner JSONPath

Query: `$[?(@.foo)]`

Result: `[{"foo": true}]`

Query: `$[?(@.bar)]`

Result: `[]`

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

