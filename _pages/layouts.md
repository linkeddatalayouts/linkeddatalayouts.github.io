---
title: "LiDL Layout Definition Language"
permalink: "/layouts/"
layout: single
author_profile: false
classes: wide
---

The LiDL Layout Defintion Language allows to define structure, rules and expressions that map data in native application formats to the RDF data model.

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
Instead it provides an explicit type conversion into an RDFS datatype and its native size in number of bits or bytes.

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

## `lidl:Attribute`

A `lidl:Attribute` encapsulates a specific sub-element of a `lidl:Composite` layout and allows to specify the sub-element's order and multiplicity with respect to its containing `lidl:Composite`. 

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
    sh:or (
        [ sh:class lidl:Atomic ]
        [ sh:node lidl:Atomic ]
        [ sh:class lidl:Composite ]
        [ sh:node lidl:Composite ]
    )
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
