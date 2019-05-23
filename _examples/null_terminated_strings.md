---
title: "Null-terminated strings"
layout: single
author_profile: false
---

## Null-terminated ASCII string 

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedASCII
  lidl:attribute (
    [ lidl:layout lidl:ASCII ] 
    [ lidl:count 1 ;
      lidl:layout lidl:Byte ;
      lidl:value "0"^^xsd:hexBinary ]
  ) .
```

## Null-terminated UTF-16 string

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedUTF16
  lidl:attribute (
    [ lidl:layout lidl:UTF16 ] 
    [ lidl:count 1 ;
      lidl:layout lidl:UInt16 ;
      lidl:value "0"^^xsd:unsignedShort ] 
  ) .
```

## Null-terminated UTF-32 string

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedUTF32
  lidl:attribute (
    [ lidl:layout lidl:UTF32 ]
    [ lidl:count 1 ;
      lidl:layout lidl:UInt32 ;
      lidl:value "0"^^xsd:unsignedInt ]
  ) .
```

## Null-terminated UTF-8 string

Jesus christ! 

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedUTF8 
  lidl:attribute (
    [ lidl:layout string:UTF8-String ] 
    [ lidl:count 1;
      lidl:layout lidl:UInt8 ;
      lidl:value "0"^^xsd:hexBinary
    ] 
  ) .

string:UTF8-String 
  lidl:attribute (
    [ lidl:count 1;
      lidl:layout string:UTF8-SingleByte ,
      string:UTF8-2Byte ,
      string:UTF8-3Byte ,
      string:UTF8-4Byte ] 
  ) .

string:UTF8-SingleByte
  a lidl:Composite ;
  lidl:attribute (
    [ lidl:count 1 ;
      lidl:value false ;
      lidl:layout lidl:Bit ]
    [ lidl:count 7 ;
      lidl:layout lidl:Bit ]
  ) .

string:UTF8-2Byte
  a lidl:Composite ;
  lidl:attribute (
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ]
    [ lidl:count 1 ;
      lidl:value false ;
      lidl:layout lidl:Bit ]
    [ lidl:count 6 ;
      lidl:layout lidl:Bit ]
    [ lidl:count 1 ;
      lidl:layout string:UTF8-FollowByte ]
  ) .

string:UTF8-3Byte
  a lidl:Composite ;
  lidl:attribute (
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ]
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ] 
   [ lidl:count 1 ;
     lidl:value false ;
     lidl:layout lidl:Bit ]
   [ lidl:count 5 ;
     lidl:layout lidl:Bit ]
   [ lidl:count 2 ;
     lidl:layout string:UTF8-FollowByte ]
  ) .

string:UTF8-4Byte
  a lidl:Composite ;
  lidl:attribute (
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ]
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ]
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ]
    [ lidl:count 1 ;
      lidl:value false ;
      lidl:layout lidl:Bit ]
    [ lidl:count 4 ;
      lidl:layout lidl:Bit ]
    [ lidl:count 3 ;
      lidl:layout string:UTF8-FollowByte ]
  ) .

string:UTF8-FollowByte
  a lidl:Composite ;
  lidl:attribute (
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ]
    [ lidl:count 1 ;
      lidl:value true ;
      lidl:layout lidl:Bit ]
    [ lidl:count 6 ;
      lidl:layout lidl:Bit ]
  ) .
```