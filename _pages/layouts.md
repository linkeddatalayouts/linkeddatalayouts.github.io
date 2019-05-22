---
title: "LiDL Layout Definition Language"
permalink: "/layouts/"
layout: single
author_profile: false
classes: wide
---

The LiDL Layout Defintion Language allows to define structure, rules and expressions that map data in native application formats to the RDF data model.

![image-center](/assets/images/lidl.svg){: .align-center}


## `lidl:Layout`

Every (binary) application format specification is based on the notion of a `lidl:Layout` that MAY define the endianess with which its content is to be interpreted. 

```
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <http://www.dfki.org/lidl#> .

lidl:Layout
  a sh:NodeShape ;
  sh:property [
    sh:message "Each layout MAY specify its endianness" ;
    sh:path lidl:endianness ;
    sh:maxCount 1 ;
    sh:in ( lidl:BigEndian lidl:MiddleEndian lidl:LittleEndian )
  ] .
```
*<sub>Each user-defined `lidl:Layout` instance MUST successfully validate against the above [SHACL shape](https://www.w3.org/TR/shacl/).</sub>*

We further distinguish between `lidl:Composite` layouts, and `lidl:Atomic` layouts.

### `lidl:Composite`

A `lidl:Composite` layout is made up of several parts or sub-elements, that we refer to as `lidl:Attributes`. 

```
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <http://www.dfki.org/lidl#> .

lidl:Composite
  a sh:NodeShape ;
  rdfs:subClassOf lidl:Layout ;
  sh:property [
    sh:message "Each Composite layout MUST have some attributes" ;
    sh:path lidl:attribute ;
    sh:minCount 1 ;
    sh:node lidl:Attribute
  ] .
```
*<sub>Each user-defined `lidl:Composite` instance MUST successfully validate against the above [SHACL shape](https://www.w3.org/TR/shacl/).</sub>*

### `lidl:Atomic`

In contrast to `lidl:Composite` layouts, a `lidl:Atomic` layout does further not decompose. 

Instead it provides an explicit type conversion into an RDFS datatype and specifies its native size in number of bits or bytes.

```
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <http://www.dfki.org/lidl#> .

lidl:Atomic
  a sh:NodeShape ;
  rdfs:subClassOf lidl:Layout ;
  sh:property [
    sh:message "Each Atomic layout MUST map to an RDFS datatype" ;
    sh:path lidl:datatype ;
    sh:minCount 1 ;
    sh:maxCount 1
  ] ;
  sh:or (
    [
      sh:property [
        sh:message "Each Atomic layout MUST specify its bitSize or byteSize." ;
        sh:path lidl:bitSize ;
        sh:minCount 1 ;
        sh:datatype xsd:integer
      ]
    ]
    [
      sh:property [
        sh:message "Each Atomic layout MUST specify its bitSize or byteSize." ;
        sh:path lidl:byteSize ;
        sh:minCount 1 ;
        sh:datatype xsd:integer
      ]
    ]
  ) .
```
*<sub>Each user-defined `lidl:Atomic` instance MUST successfully validate against the above [SHACL shape](https://www.w3.org/TR/shacl/).</sub>*

We provide a number of predefined `lidl:Atomic` layouts, ranging from 

```
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix lidl: <http://www.dfki.org/lidl#> .

lidl:Bit
  rdfs:label "A bit." ;
  a lidl:Atomic ;
  lidl:bitSize 1 ;
  lidl:datatype xsd:Boolean .
```

over

```
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix lidl: <http://www.dfki.org/lidl#> .

lidl:Float32
  rdfs:label "A 32-bit IEEE754 floating point number." ;
  rdfs:isDefinedBy <https://ieeexplore.ieee.org/document/4610935> ;
  rdfs:seeAlso <https://en.wikipedia.org/wiki/IEEE_754> ;
  a lidl:Atomic ;
  lidl:byteSize 4 ;
  lidl:bitSize 32 ;
  lidl:datatype xsd:float .
```

to 

```
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix lidl: <http://www.dfki.org/lidl#> .

lidl:UTF32
  rdfs:label "An UTF-32 character." ;
  rdfs:isDefinedBy <https://www.unicode.org/reports/tr19/tr19-9.html> ;
  rdfs:seeAlso <https://en.wikipedia.org/wiki/UTF-32> ;
  a lidl:Atomic ;
  lidl:byteSize 3 ;
  lidl:bitSize 32 ;
  lidl:datatype xsd:string .
```

Feel free to specify your own!

## `lidl:Attribute`

A `lidl:Attribute` encapsulates a specific sub-element of a `lidl:Composite` layout.

Each `lidl:Attribute` instance MUST define the `lidl:Layout` of the encapsulated element.

Each `lidl:Attribute` instance SHOULD specify the sub-element's order and count with respect to its containing `lidl:Composite`.

If a `lidl:Attribute` instance does not specify the sub-element's order, a LiDL engine will continue to consume sub-elements of the specified layout until the next in order attribute of the containing `lidl:Composite` layout is matched.
{: .notice--info}

```
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <http://www.dfki.org/lidl#> .

lidl:Attribute
  a sh:NodeShape ;
  sh:property [
    sh:message "Each attribute SHOULD define its order in a layout." ;
    sh:path lidl:order ;
    sh:maxCount 1 ;
    sh:datatype xsd:integer ;
  ] ;
  sh:property [
    sh:message "Each attribute MUST specify the layout of its (sub) elements." ;
    sh:path lidl:layout ;
    sh:minCount 1 ;
    sh:maxCount 1 ;
    sh:or (
        [ sh:class lidl:Atomic ]
        [ sh:node lidl:Atomic ]
        [ sh:class lidl:Composite ]
        [ sh:node lidl:Composite ]
    )
  ] ;
  sh:property [
    sh:message "Each attribute MAY specify the value of its (sub) element." ;
    sh:path lidl:value ;
    sh:maxCount 1 ;
    sh:nodeKind sh:Literal ;
  ] ;
  sh:xone (
    [
      sh:property [
        sh:message "Each attribute SHOULD specify the number of its (sub) elements." ;
        sh:path lidl:count ;
        sh:maxCount 1 ;
        sh:datatype xsd:integer ;
      ]
    ]
    [
      sh:property [
        sh:message "Each attribute SHOULD specify the number of its (sub) elements." ;
        sh:path lidl:count ;
        sh:maxCount 1 ;
        sh:node lidl:Expression ;
      ]
    ]
  ) .
```
*<sub>Each user-defined `lidl:Attribute` instance MUST successfully validate against the above [SHACL shape](https://www.w3.org/TR/shacl/).</sub>*