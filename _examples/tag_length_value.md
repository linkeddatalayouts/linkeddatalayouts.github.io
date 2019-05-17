---
title: "Tag Length Value encoding scheme"
layout: single
author_profile: false
---

TLV (type-length-value or tag-length-value) is an encoding scheme used for optional information element in a certain protocol.

[Find this example on GitHub!](https://github.com/linkeddatalayouts/vocabularies/tree/master/examples/TLV)

## TLV Sequences

Each TLV representation is a finite `tlv:Sequence` of arbitrary length consisting of `tlv:Chunk`s.

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

Each `tlv:Chunk` consists of  
- the kind of value that this part of the message represents (binary code, often simply alphanumeric), 
- the length of the contained value (typically in bytes),
- a value of variable size.
 
```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix tlv: <http://tlv.example.com/ns#> .

tlv:Chunk
  lidl:attribute _:tag , _:length , _:value .
  
_:tag
  lidl:order 0;
  lidl:count 1 ;
  lidl:layout lidl:Byte .
  
_:length
  lidl:order 1;
  lidl:count 1 ;
  lidl:layout lidl:Integer32 .
  
_:value
  lidl:order 2;
  lidl:count _:length ;
  lidl:layout lidl:Byte . 
```