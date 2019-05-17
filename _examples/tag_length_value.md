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
  lidl:attribute tlv:Tag, tlv:Length , tlv:Value .
```

## TLV Tags

In this example, we encode a `tlv:Tag` using a single byte.

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix tlv: <http://tlv.example.com/ns#> .

tlv:Tag
  lidl:order 0;
  lidl:count 1 ;
  lidl:layout lidl:Byte .
```

## TLV Length

For encoding a `tlv:Length`, we choose four bytes.

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix tlv: <http://tlv.example.com/ns#> .
tlv:Length
  lidl:order 1;
  lidl:count 1 ;
  lidl:layout lidl:Integer32 .
```  

## TLV Value 

For brevity, a `tlv:Value` is simply a byte sequence of given length.

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix tlv: <http://tlv.example.com/ns#> . 

tlv:Value
  lidl:order 2;
  lidl:count tlv:Length ;
  lidl:layout lidl:Byte .
```