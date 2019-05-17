---
title: "Tag Length Value encoding scheme"
layout: single
author_profile: false
---

TLV (type-length-value or tag-length-value) is an encoding scheme used for optional information element in a certain protocol.

## TLV Sequences

Each TLV representation is a finite `tlv:Sequence` consisting of `tlv:Chunk`s.

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix tlv: <http://tlv.example.com/ns#> .

tlv:Sequence
  lidl:attribute [
    lidl:order 0;
    lidl:layout tlv:Chunk .
  ]
```

## TLV Chunks

Each `tlv:Chunk` indicates 
- the kind of value that this part of the message represents (binary code, often simply alphanumeric), 
- the length of the contained value (typically in bytes) .

In addition, each `tlv:Chunk` contains a value of variable size.
 
```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix tlv: <http://tlv.example.com/ns#> .

tlv:Chunk
  lidl:attribute tlv:Tag, tlv:Length , tlv:Value .
  
tlv:Tag
  lidl:order 0;
  lidl:elementCount 1 ;
  lidl:layout lidl:Byte .
  
tlv:Length
  lidl:order 1;
  lidl:elementCount 1 ;
  lidl:layout lidl:Integer32 .
  
tlv:Value
  lidl:order 2;
  lidl:elementCount tlv:Length ;
  lidl:layout lidl:Byte .
```