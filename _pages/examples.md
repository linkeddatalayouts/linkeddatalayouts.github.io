---
permalink: /examples/
title: "Examples"
layout: single
author_profile: false
---

The basic layout of a PPM files is divided into a Header section and a ImageData section.
 
```
@prefix lidl: <http://www.dfki.org/lidl#> .
@prefix ppm:	<http://ppm.example.com/ns#> .

ppm:ImageLayout
  lidl:endianess lidl:LittleEndian ;
  lidl:attribute ppm:Header , ppm:ImageData .

ppm:Header
    lidl:order 0 ;
    lidl:count 1 ;
    lidl:layout ppm:HeaderLayout .

ppm:ImageData .
  lidl:order 0;
  lidl:count 1 ;
  lidl:layout ppm:ImageDataLayout .
```

The Header starts with a two-byte magic number (in ASCII) that identifies the type of file it is and its encoding (ASCII or binary). 
The magic number is a capital P followed by a single-digit number. In this example, we use "P6" to indicate the use of the binary PPM format. 
The next two Header elements give the image width and the image height.
The final Header element specifies the maximum value for each color.

```
ppm:HeaderLayout
  lidl:attribute ppm:Magic , ppm:Width , ppm:Height , ppm:MaxColorValue .

ppm:Magic
  lidl:order 0 ;
  lidl:count 2 ;
  lidl:layout lidl:ASCII .
  lidl:value "P6" .

ppm:Width
  lidl:order 1 ; 
  lidl:count 1 ;
  lidl:layout lidl:UInt32 .

ppm:Height
  lidl:order 2 ; 
  lidl:count 1 ;
  lidl:layout lidl:UInt32 .

ppm:MaxColorValue
  lidl:order 3 ; 
  lidl:count 1 ;
  lidl:layout lidl:UInt8 .
```
