https://www.ietf.org/archive/id/draft-ietf-jsonpath-base-11.html

### History

Jayway implementation is indeed influential, just some numbers for the records: here on GH they have 5.7k stars, 1.1k forks, 50 contributors and it used by 1236 artifacts on maven.
"There is clearly a huge ecosystem that builds on it and we cannot ignore it." - Davide Bettio

Jayway 7.7k stars, 1.5k forks, 78 contributors. Used by 1,690 artifacts on maven.

[TIBCO](https://docs.mashery.com/connectorsguide/GUID-751D8687-F803-460A-BC76-67A37779BE7A.html)

[Oracle](https://docs.oracle.com/cd/E60058_01/PDF/8.0.8.x/8.0.8.0.0/PMF_HTML/JsonPath_Expressions.htm)

Spring

IBM

[?(@.name subsetof ['foo', 'bar'])]

Document: 

[
  {"name":"foo"},
  {"name":"bar"},
  {"name":"foo bar"}
]

JMESPath

Query: [?contains(name,`bar`) == `true`]


JSONata

$[$contains(name, "bar") = true]

[
  {
    "name": "bar"
  },
  {
    "name": "foo bar"
  }
]
