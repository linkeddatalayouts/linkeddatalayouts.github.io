---
title: "Null-terminated strings"
layout: single
author_profile: false
---

## Defining the layout of null-terminated ASCII strings

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedASCII
  lidl:attribute [ 
    # chars
    lidl:order 0 ; 
    lidl:layout lidl:ASCII 
  ] , [ 
    # terminator
    lidl:order 1 ; 
    lidl:count 1
    lidl:layout lidl:Byte ;
    lidl:value "0"^^xsd:hexBinary 
  ] .
```

## Defining the layout of null-terminated UTF-16 strings

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedUTF16
  lidl:attribute [ 
    # chars
    lidl:order 0 ; 
    lidl:layout lidl:UTF16 
  ] , [ 
    # terminator
    lidl:order 1 ; 
    lidl:count 1
    lidl:layout lidl:UInt16 ;
    lidl:value "0"^^xsd:unsignedShort 
  ] .
```

## Defining the layout of null-terminated UTF-32 strings

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedUTF32
  lidl:attribute [ 
    # chars
    lidl:order 0 ; 
    lidl:layout lidl:UTF32 
  ] , [ 
    # terminator
    lidl:order 1 ; 
    lidl:count 1
    lidl:layout lidl:UInt32 ;
    lidl:value "0"^^xsd:unsignedInt
  ] .
```

## Defining the layout of null-terminated UTF-32 strings

Jesus christ! 

```
@prefix lidl:   <http://www.dfki.org/lidl#> .
@prefix string: <http://string.example.com/ns#> .

string:NullTerminatedUTF8 
lidl:attribute [
lidl:order 0;
lidl:layout string:UTF8-String
] , [
lidl:order 1;
lidl:count 1;
lidl:layout lidl:UInt8 ;
lidl:value "0"^^xsd:hexBinary
] .

string:UTF8-String 
lidl:attribute [
lidl:order 0;
lidl:count 1;
lidl:layout string:UTF8-SingleByte ,
string:UTF8-2Byte ,
string:UTF8-3Byte ,
string:UTF8-4Byte
] ,
[
lidl:order 0;
lidl:count 1;
lidl:layout lidl:Byte;
lidl:value [ lidl:not]
]

string:UTF8-SingleByte
a lidl:Composite ;
lidl:attribute [
lidl:order 0 ;
lidl:count 1 ;
lidl:value false ;
lidl:layout lidl:Bit
] , [
lidl:order 1 ;
lidl:count 7 ;
lidl:layout lidl:Bit
] .

 

string:UTF8-2Byte
a lidl:Composite ;
lidl:attribute [
lidl:order 0 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] , [
lidl:order 1 ;
lidl:count 1 ;
lidl:value false ;
lidl:layout lidl:Bit
] , [
lidl:order 2 ;
lidl:count 6 ;
lidl:layout lidl:Bit
] , [
lidl:order 3 ;
lidl:count 1 ;
lidl:layout string:UTF8-FollowByte
] .


string:UTF8-3Byte
a lidl:Composite ;
lidl:attribute [
lidl:order 0 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] , [
lidl:order 1 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] , [
lidl:order 2 ;
lidl:count 1 ;
lidl:value false ;
lidl:layout lidl:Bit
] , [
lidl:order 3 ;
lidl:count 5 ;
lidl:layout lidl:Bit
] , [
lidl:order 4 ;
lidl:count 2 ;
lidl:layout string:UTF8-FollowByte
].


string:UTF8-4Byte
a lidl:Composite ;
lidl:attribute [
lidl:order 0 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] , [
lidl:order 1 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] , [
lidl:order 2 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] , [
lidl:order 3 ;
lidl:count 1 ;
lidl:value false ;
lidl:layout lidl:Bit
] , [
lidl:order 4 ;
lidl:count 4 ;
lidl:layout lidl:Bit
] , [
lidl:order 5 ;
lidl:count 3 ;
lidl:layout string:UTF8-FollowByte
].

string:UTF8-FollowByte
a lidl:Composite ;
lidl:attribute [
lidl:order 0 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] , [
lidl:order 1 ;
lidl:count 1 ;
lidl:value true ;
lidl:layout lidl:Bit
] ,[
lidl:order 2 ;
lidl:count 6 ;
lidl:layout lidl:Bit
] .

```