---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
layout: single
author_profile: false
classes: wide
---
A considerable amount of data on the Web is stored in native application formats, e.g., [images](/examples/ppm), binary [TLV-encoded data](/examples/tlv), etc.

While there is a number of special purpose converters and meta data extractors for certain native application formats,  in general, native data resources are not accessible for Linked Data applications.

With LiDL (Linked Data Layouts), we present a [generic concept](/concept), along side with an [RDF vocabulary](https://github.com/linkeddatalayouts/vocabularies), enabling 
* the semantic description of the structural layout of arbitrary native application formats, and
* the declarative mapping of user-defined content fragments of native application data to RDF.

Our [reference implementation](https://github.com/linkeddatalayouts/interpreter) allows for executing a `lidl:Mapping` definition given a native application file and its `lidl:Layout` specification.