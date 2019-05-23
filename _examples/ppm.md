---
title: "PPM - Netpbm color image format"
layout: single
author_profile: false
---

The PPM, or Portable Pixmap, format was devised to be an intermediate format for use in developing file format conversion systems.
The PPM format is not intended to be an archival format, so it does not need to be too storage efficient. 
Thus, it is one of the simplest formats. 

[Find this example on GitHub!](https://github.com/linkeddatalayouts/vocabularies/tree/master/examples/ppm)

## Defining the PPM layout
 
The basic layout of a PPM files is divided into a Header section and a ImageData section.
 
```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix ppm:	<http://ppm.example.com/ns#> .

ppm:ImageLayout
  lidl:endianness lidl:LittleEndian ;
  lidl:attribute ( ppm:Header ppm:ImageData ) .

ppm:Header
    lidl:count 1 ;
    lidl:layout ppm:HeaderLayout .

ppm:ImageData .
  lidl:count 1 ;
  lidl:layout ppm:ImageDataLayout .
```

### PPM Header 
The Header starts with a two-byte magic number (in ASCII) that identifies the type of file it is and its encoding (ASCII or binary). 
In this example, we use "P6" to indicate the use of the binary PPM format. 

The next two Header elements give the image width and the image height.

The final Header element specifies the maximum value for each color.

```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix ppm:    <http://ppm.example.com/ns#> .

ppm:HeaderLayout
  lidl:attribute ( ppm:Magic ppm:Width ppm:Height ppm:MaxColorValue ) .

ppm:Magic
  lidl:count 2 ;
  lidl:layout lidl:ASCII ;
  lidl:value "P6" .

ppm:Width
  lidl:count 1 ;
  lidl:layout lidl:UInt32 .

ppm:Height
  lidl:count 1 ;
  lidl:layout lidl:UInt32 .

ppm:MaxColorValue
  lidl:count 1 ;
  lidl:layout lidl:UInt8 .
```

### PPM Image Data

The ImageData section contains the actual picture information as a series of RGB values. 

In the PPM format, each pixel is defined by a triplet of values representing how much red, green, and blue (RGB) are present. 

```
@prefix lidl: <https://linkeddatalayouts.github.io/vocabularies/lidl.ttl#> .
@prefix ppm:	<http://ppm.example.com/ns#> .

ppm:ImageDataLayout
  lidl:attribute [
      lidl:count [ lidl:mul ( ppm:Width ppm:Height ) ] ;
      lidl:layout ppm:Pixel 
  ] .

ppm:Pixel
  lidl:attribute [
    lidl:count 3 ;
    lidl:layout lidl:UInt8  
  ] .
```

Please note that the actual number of pixels is defined using a `lidl:Expression`.


## From PPM files to RDF graphs

### The Mapping definition

```
@prefix rr: <http://www.w3.org/ns/r2rml#> .
@prefix rml: <http://semweb.mmlab.be/ns/rml#> .
@prefix ppm:  <http://ppm.example.com/ns#> .
@prefix img: <http://img.example.com/ns#> .
@prefix dcat: <http://www.w3.org/ns/dcat#> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix exif: <http://www.w3.org/2003/12/exif/ns> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ex: <https://github.com/linkeddatalayouts/vocabularies/blob/master/examples/ppm/> .

<>
  rml:logicalSource [ 
    rml:source ex:rocket.ppm ; 
    rml:referenceFormulation ppm:ImageLayout ] ;
  rr:subjectMap [ 
    rr:termType rr:IRI ; 
    rr:class foaf:Image 
  ] ;
  rr:predicateObjectMap [ 
    rr:predicate exif:width ; 
    rr:objectMap [ rml:reference ppm:Width ] 
  ] ;
  rr:predicateObjectMap [ 
    rr:predicate exif:height ; 
    rr:objectMap [ rml:reference ppm:Height ] 
  ] ;
  rr:predicateObjectMap [ 
    rr:predicate img:pixels ; 
    rr:objectMap [ rml:reference ppm:ImageData ] 
  ] .
```

### The resulting RDF graph

```
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix ppm:  <http://ppm.example.com/ns#> .
@prefix dcat: <http://www.w3.org/ns/dcat#> .
@prefix dct: <http://purl.org/dc/terms/> .
@prefix exif: <http://www.w3.org/2003/12/exif/ns> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix ex: <https://github.com/linkeddatalayouts/vocabularies/blob/master/examples/ppm/> .

<>
  a foaf:Image ;
  rdfs:isDefinedBy ppm:ImageLayout ;
  owl:sameAs ex:rocket.ppm ;
  exif:width "640"^^xsd:int ;
  exif:height "427"^^xsd:int ;
  img:pixels [
    a dcat:Distribution ;
    rdfs:isDefinedBy ppm:ImageDataLayout ;
    dcat:downloadURL ex:rocket.bin ;
    dct:format "application/octet-stream" ;
    dcat:byteSize 819840 
  ] .
```
