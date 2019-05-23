---
title: "LiDL Layout Definition Language"
permalink: "/concept/"
layout: single
author_profile: false
classes: wide
---

The LiDL Layout Defintion Language allows to define structure, expressions and rules that map data in native application formats to the RDF data model.

![image-center](/assets/images/lidl.svg){: .align-center}


## `lidl:Layout`

Every (binary) application format specification is based on the notion of a `lidl:Layout` that MAY define the endianess with which its content is to be interpreted. 

We further distinguish between `lidl:Composite` layouts, and `lidl:Atomic` layouts.

Each user-defined `lidl:Layout` instance MUST successfully validate against the following [SHACL shape](https://www.w3.org/TR/shacl/).
{: .notice--warning}

```
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

lidl:Layout
  a sh:NodeShape ;
  sh:property [
    sh:message "Each layout MAY specify its endianness" ;
    sh:path lidl:endianness ;
    sh:maxCount 1 ;
    sh:in ( lidl:BigEndian lidl:MiddleEndian lidl:LittleEndian )
  ] .
```


### `lidl:Composite`

A `lidl:Composite` layout is made up of an ordered list of several parts or sub-elements, that we refer to as `lidl:Attributes`. 

For example, a simple RGB pixel layout can be described as follows. We will flesh out this simple example a bit later.

```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix img: <http://img.example.org/ns#> .

img:RGBPixel
  a lidl:Composite ;
  lidl:attribute ( _:red _:green _:blue ) .
```

Each user-defined `lidl:Composite` instance MUST successfully validate against the following [SHACL shape](https://www.w3.org/TR/shacl/).
{: .notice--warning}

```
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

lidl:Composite
  a sh:NodeShape ;
  rdfs:subClassOf lidl:Layout ;
  sh:property [
    sh:message "Each Composite layout MUST have exactly one Attribute or an ordered AttributeList." ;
    sh:path lidl:attribute ;
    sh:minCount 1 ;
    sh:maxCount 1 ;
    sh:xone (
      [ sh:node lidl:Attribute ]
      [ sh:node lidl:AttributeList ]
    ) .
```


### `lidl:Atomic`

In contrast to `lidl:Composite` layouts, a `lidl:Atomic` layout does further not decompose. 

Instead it provides an explicit type conversion into an RDFS datatype and specifies its native size in number of bits or bytes.

We provide a number of predefined `lidl:Atomic` layouts, ranging from 

```
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

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
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

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
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

lidl:UTF32
  rdfs:label "An UTF-32 character." ;
  rdfs:isDefinedBy <https://www.unicode.org/reports/tr19/tr19-9.html> ;
  rdfs:seeAlso <https://en.wikipedia.org/wiki/UTF-32> ;
  a lidl:Atomic ;
  lidl:byteSize 3 ;
  lidl:bitSize 32 ;
  lidl:datatype xsd:string .
```  
  
Each user-defined `lidl:Atomic` instance MUST successfully validate against the following [SHACL shape](https://www.w3.org/TR/shacl/).
{: .notice--warning}

```
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

lidl:Atomic
  a sh:NodeShape ;
  rdfs:subClassOf lidl:Layout ;
  sh:property [
    sh:message "Each Atomic layout MUST map to an RDFS datatype" ;
    sh:path lidl:datatype ;
    sh:minCount 1 ;
    sh:maxCount 1
  ] ;
  sh:property [
    sh:message "Each Atomic layout MUST specify its bitSize or byteSize." ;
    sh:or (
      [ sh:path lidl:bitSize ]
      [ sh:path lidl:byteSize ]
    ) ;
    sh:minCount 1 ;
    sh:datatype xsd:integer
  ] .
```


## `lidl:Attribute`

A `lidl:Attribute` encapsulates a specific sub-element of a `lidl:Composite` layout.

Each `lidl:Attribute` instance MUST define the `lidl:Layout` of the encapsulated element.

Each `lidl:Attribute` instance SHOULD specify the number of sub-elements contained within its `lidl:Composite` layout.

Coming back to our RGB pixel layout, we could choose to define the red, green, and blue color components using a `lidl:Attribute` for each color component.

```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix img: <http://img.example.org/ns#> .

img:RGBPixel
  a lidl:Composite ;
  lidl:attribute ( _:red _:green _:blue ) .
  
_:red  
  a lidl:Attribute ;
  lidl:count 1 ;
  lidl:layout lidl:UInt8 .

_:green
  a lidl:Attribute ;
  lidl:count 1 ;
  lidl:layout lidl:UInt8 .
  
_:blue
  a lidl:Attribute ;
  lidl:count 1 ;
  lidl:layout lidl:UInt8 .
```  

Alternatively, all three color components could be modeled using a single `lidl:Attribute`. Eventually, it is up to your purposes.

```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix img: <http://img.example.org/ns#> .

img:RGBPixel
  a lidl:Composite ;
  lidl:attribute [
    a lidl:Attribute ;
    lidl:count 3 ;
    lidl:layout lidl:UInt8
  ] .
```  

If a `lidl:Attribute` instance does not specify the sub-element's count, a LiDL engine will continue to consume sub-elements of the specified layout until the next in order `lidl:Attribute` of the containing `lidl:Composite` layout is encountered.
{: .notice--info}

With this in mind, the specification of a [null-terminated ASCII string](/examples/null_terminated_strings/) layout becomes straight-forward:

```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedASCII
  lidl:attribute ( _:chars _:terminator ) .

_:chars lidl:layout lidl:ASCII .

_:terminator
  lidl:count 1 ; 
  lidl:value "0"^^xsd:hexBinary 
  lidl:layout lidl:Byte .
```

Each user-defined `lidl:Attribute` instance MUST successfully validate against the following [SHACL shape](https://www.w3.org/TR/shacl/).
{: .notice--warning}

```
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

lidl:Attribute
  a sh:NodeShape ;
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
    sh:message "Each attribute SHOULD specify the number of its (sub) elements." ;
    sh:path lidl:count ;
    sh:maxCount 1 ;
    sh:xone (
      [ sh:datatype xsd:integer ]
      [ sh:node lidl:Expression ]
    )           
  ] ;
  sh:property [
    sh:message "Each attribute MAY specify a constant value for its (sub) element(s)." ;
    sh:path lidl:value ;
    sh:maxCount 1 ;
    sh:nodeKind sh:Literal 
  ] .
```


## `lidl:Expression`

In many cases, a constant `lidl:Layout` specification is not capable of precisely addressing every defined Attribute within a specific binary blob instance.

Obvious factors that cannot be captured in constant values are the length of a string value, as it is determined by which content the string would have in a distinct binary resource, or the element count in an array.

LiDL covers these issues by simple mathematical and boolean expressions, conditional expressions, and pattern matching for type inference.

```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix img: <http://img.example.org/ns#> .

img:RGBImage
  lidl:endianness lidl:LittleEndian ;
  lidl:attribute ( _:width _:height _:pixels ) .
  
_:width
  lidl:count 1 ;
  lidl:layout lidl:Int32 .

_:height
  lidl:count 1 ;
  lidl:layout lidl:Int32 .

_:pixels
  lidl:count 1 ;
  lidl:layout img:RGBPixelBuffer .
  
img:RGBPixelBuffer
  lidl:attribute [
      lidl:count [ lidl:mul ( _:width _:height ) ] ;
      lidl:layout img:RGBPixel
  ] .
```

Each user-defined `lidl:Expression` instance MUST successfully validate against the following [SHACL shape](https://www.w3.org/TR/shacl/).
{: .notice--warning}

```
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .

lidl:Expression
  a sh:NodeShape ;
  sh:property [
    sh:path lidl:operator ;
    sh:minCount 1 ;
    sh:maxCount 1 ;
    sh:node lidl:ArgumentList
  ] .

lidl:ArgumentList
  a sh:NodeShape ;
  sh:property [
    sh:path [ sh:zeroOrMorePath rdf:rest ] ;
    sh:hasValue rdf:nil ;
    sh:node lidl:Argument
  ] .

lidl:Argument
  a sh:NodeShape ;
  sh:or (
    [
      sh:hasValue rdf:nil ;
      sh:property [
        sh:path rdf:first ;
        sh:maxCount 0 ;
      ] ;
      sh:property [
        sh:path rdf:rest ;
        sh:maxCount 0 ;
      ] ;
    ]
    [
      sh:not [ sh:hasValue rdf:nil ] ;
      sh:property [
        sh:path rdf:first ;
        sh:maxCount 1 ;
        sh:minCount 1 ;
        sh:or (
          [ sh:nodeKind sh:Literal ]
          [ sh:node lidl:Attribute ]
          [ sh:node lidl:Expression ]
        )
      ] ;
      sh:property [
        sh:path rdf:rest ;
        sh:maxCount 1 ;
        sh:minCount 1 ;
        sh:node lidl:ArgumentList
      ] ;
    ]
  ) .

```


## `lidl:Mapping`