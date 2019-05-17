---
permalink: /examples/
title: "Examples"
layout: single
author_profile: false
---

## PPM - Netpbm color image format

The basic layout of a PPM files is divided into a Header and an ImageData section.
 
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

### PPM Header 

The Header starts with a two-byte magic number (in ASCII) that identifies the type of file it is and its encoding (ASCII or binary). 
In this example, we use "P6" to indicate the use of the binary PPM format. 

The next two Header elements give the image width and the image height.

The final Header element specifies the maximum value for each color.

```
@prefix lidl: <http://www.dfki.org/lidl#> .
@prefix ppm:	<http://ppm.example.com/ns#> .

ppm:HeaderLayout
  lidl:attribute ppm:Magic ;
  lidl:attribute ppm:Width ;
  lidl:attribute ppm:Height ;
  lidl:attribute ppm:MaxColorValue .

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

### PPM Image Data

The actual picture information is a series of RGB values. In the PPM format, each pixel is defined by a triplet of values representing how much red, green, and blue (RGB) are present. 

```
@prefix lidl: <http://www.dfki.org/lidl#> .
@prefix ppm:	<http://ppm.example.com/ns#> .

ppm:ImageDataLayout
  lidl:attribute [
      lidl:order 0 ;
      lidl:count [ lidl:mul ( _:width _:height ) ] ;
      lidl:layout ppm:Pixel 
  ] .

ppm:Pixel
  lidl:attribute [
    lidl:order 0;
    lidl:count 3 ;
    lidl:layout lidl:UInt8  
  ] .

```
