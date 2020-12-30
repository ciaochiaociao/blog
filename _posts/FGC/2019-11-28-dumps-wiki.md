---
title:  Dumps Wikipedia/Wikidata
---

**Doing SQL queries on the current database dump**

You can do SQL queries on the current database dump
using [Quarry](https://quarry.wmflabs.org/) (as a replacement for the
disabled [Special:Asksql](https://en.wikipedia.org/wiki/Special:Asksql) page).

Database schema

**SQL schema**

*See also: [mw:Manual:Database
layout](https://www.mediawiki.org/wiki/Manual:Database_layout)*

The sql file used to initialize a MediaWiki database can be
found [here](https://phabricator.wikimedia.org/diffusion/MW/browse/master/maintenance/tables.sql).

**XML schema**

The XML schema for each dump is defined at the top of the file. And also
described in the [MediaWiki export help
page](https://meta.wikimedia.org/wiki/Help:Export#Export_format).

Help to parse dumps for use in scripts

-   [Wikipedia:Computer help
    > desk/ParseMediaWikiDump](https://en.wikipedia.org/wiki/Wikipedia:Computer_help_desk/ParseMediaWikiDump) describes
    > the [Perl](https://en.wikipedia.org/wiki/Perl) Parse::MediaWikiDump
    > library, which can parse XML dumps.

-   [Wikipedia preprocessor
    > (wikiprep.pl)](http://www.cs.technion.ac.il/~gabr/resources/code/wikiprep) is
    > a [Perl](https://en.wikipedia.org/wiki/Perl) script that
    > preprocesses raw XML dumps and builds link tables, category
    > hierarchies, collects anchor text for each article etc.

-   [Wikipedia SQL dump
    > parser](https://github.com/svick/Wikipedia-SQl-dump-parser) is a
    > .NET library to read MySQL dumps without the need to use MySQL
    > database

-   [WikiDumpParser](https://github.com/MartinRichards23/WikiDumpParser) a
    > .NET Core libary to parse the database dumps.

-   [Dictionary
    > Builder](https://github.com/newca12/dictionary-builder) is a Java
    > program that can parse XML dumps and extract entries in files

-   [Scripts for parsing Wikipedia
    > dumps](https://github.com/napsternxg/WikiUtils) Python based
    > scripts for parsing sql.gz files from wikipedia dumps.

**Doing Hadoop MapReduce on the Wikipedia current database dump**

You can do Hadoop MapReduce queries on the current database dump, but
you will need an extension to the InputRecordFormat to have each
\<page\> \</page\> be a single mapper input. A working set of java
methods (jobControl, mapper, reducer, and XmlInputRecordFormat) is
available at [Hadoop on the
Wikipedia](https://tpmoyer-gallery.appspot.com/hadoopWikipedia)

Help to import dumps into MySQL

See:

-   [mw:Manual:Importing XML
    > dumps](https://www.mediawiki.org/wiki/Manual:Importing_XML_dumps)

-   [m:Data dumps](https://meta.wikimedia.org/wiki/Data_dumps)

Wikipedia
=========

[[https://github.com/mattzheng/ChineseWiki]{.underline}](https://github.com/mattzheng/ChineseWiki)

-   Wikipedia Extractor提取出來的結果，會去掉很多空格與括號裡面的內容；

-   gensim.corpora.wikicorpus.WikiCorpus處理，問題更嚴重，因為它連所有標點都去掉了。

[[https://pypi.org/project/wiki-dump-reader/]{.underline}](https://pypi.org/project/wiki-dump-reader/)

Wikidata
========

\*\*[[https://github.com/panx27/wikidata-dump-processor/blob/master/README.md]{.underline}](https://github.com/panx27/wikidata-dump-processor/blob/master/README.md)

[[https://github.com/maxlath/import-wikidata-dump-to-couchdb\#installation]{.underline}](https://github.com/maxlath/import-wikidata-dump-to-couchdb#installation)

[[https://www.wikidata.org/wiki/Help:Data\_type]{.underline}](https://www.wikidata.org/wiki/Help:Data_type)

[[https://www.wikidata.org/wiki/Special:ListProperties/wikibase-sense]{.underline}](https://www.wikidata.org/wiki/Special:ListProperties/wikibase-sense)

[[https://www.wikidata.org/wiki/Category:Properties\_by\_datatype]{.underline}](https://www.wikidata.org/wiki/Category:Properties_by_datatype)

[[https://www.wikidata.org/wiki/Wikidata:Database\_reports/Constraint\_violations/All\_properties]{.underline}](https://www.wikidata.org/wiki/Wikidata:Database_reports/Constraint_violations/All_properties)

[[https://www.wikidata.org/wiki/Wikidata:Database\_reports/List\_of\_properties/all]{.underline}](https://www.wikidata.org/wiki/Wikidata:Database_reports/List_of_properties/all)

wikidata-filter, wikidata-sdk:
------------------------------

[[https://github.com/maxlath/wikibase-sdk/blob/master/docs/simplify\_claims.md]{.underline}](https://github.com/maxlath/wikibase-sdk/blob/master/docs/simplify_claims.md)

[[https://www.mediawiki.org/wiki/Wikibase/DataModel\#Dates\_and\_times]{.underline}](https://www.mediawiki.org/wiki/Wikibase/DataModel#Dates_and_times)

[https://github.com/maxlath/wikidata-filter/blob/master/docs/cli.md]{.underline}

nice -n+19 pigz -d \< latest-all.json.gz \| nice -n+19
load-balance-lines wikidata-filter \--claim P31:Q5 \> humans.ndjson

nice -n+19 load-balance-lines wikidata-filter \--claim P31:Q5 \>
humans.ndjson

nice -n+19 load-balance-lines wikidata-filter \--languages
zh,zh-tw,zh-cn \--omit references \--simplify \'keepQualifiers=true\' \<
wikidata-20191118-all\_10.json \> hans\_hant\_sim.ndjson

nice -n+19 load-balance-lines wikidata-filter \--languages
en,zh,zh-tw,zh-cn \--omit references \--simplify \'keepQualifiers=true\'
\< wikidata-20191118-all\_10.json \> hans\_hant\_sim.ndjson

// fix wikibase-sense, wikibase-form not implemented error (by cwhsu)

if (\[\'wikibase-sense\', 'wikibase-form'\].includes(datatype)){

console.error(err)

}

// throw err // fix wikibase-sense not implemented error (by cwhsu)

return null

**nice -n+19 load-balance-lines wikidata-filter -p \--sitelink zhwiki
\--languages zh,zh-tw,zh-cn \--type item \--omit type \--simplify
\'keepQualifiers=true&keepRichValues=true&keepTypes=true\' \<
wikidata-20191118-all.json \> hans\_hant\_sim.ndjson**

**!nice -n+19 load-balance-lines wikidata-filter -p \--languages
zh,zh-tw,zh-cn,en \--type property \--omit type \--simplify**
**\'keepQualifiers=true&keepRichValues=true&keepTypes=true\' \<
wikidata\_properties.json \> wikidata\_properties\_sim.json**

**nice -n+19 load-balance-lines wikidata-filter -p \--sitelink
zhwiki,enwiki \--languages zh,zh-tw,zh-cn,en \--type property \--omit
type \--simplify '**keepAll**=true' \< wikidata\_properties.json \>
wikidata\_properties\_sim.json**

parallel decompress: pigz, lbzip2, pbzip2

\# Use root/example as user/password credentials

version: \'3.1\'

services:

db:

image: mariadb

restart: always

environment:

MYSQL\_ROOT\_PASSWORD: example

adminer:

image: adminer

restart: always

ports:

\- 8080:8080

Download
========

where
-----

[[https://dumps.wikimedia.org/backup-index.html]{.underline}](https://dumps.wikimedia.org/backup-index.html)

> Wikipedia:
> [[https://dumps.wikimedia.org/zhwiki/]{.underline}](https://dumps.wikimedia.org/zhwiki/)
>
> Wikidata:
> [[https://dumps.wikimedia.org/wikidatawiki]{.underline}](https://dumps.wikimedia.org/wikidatawiki)
>
> JSON dumps (recommend)
> [[https://dumps.wikimedia.org/wikidatawiki/entities/]{.underline}](https://dumps.wikimedia.org/wikidatawiki/entities/)
>
> RDF dumps
> ([[https://www.mediawiki.org/wiki/Wikibase/Indexing/RDF\_Dump\_Format\#Truthy\_statements]{.underline}](https://www.mediawiki.org/wiki/Wikibase/Indexing/RDF_Dump_Format#Truthy_statements))
>
> Terse RDF Triple Language (Turtle)
>
> [[https://dumps.wikimedia.org/wikidatawiki/entities/latest-all.ttl.bz2]{.underline}](https://dumps.wikimedia.org/wikidatawiki/entities/latest-all.ttl.bz2)
>
> N-Triples (NT)
>
> [[https://dumps.wikimedia.org/wikidatawiki/entities/latest-all.nt.bz2]{.underline}](https://dumps.wikimedia.org/wikidatawiki/entities/latest-all.nt.bz2)

Downloaded:
-----------

[[https://dumps.wikimedia.org/zhwiki/20191120/]{.underline}](https://dumps.wikimedia.org/zhwiki/20191120/)

[[https://dumps.wikimedia.org/wikidatawiki/20191120/]{.underline}](https://dumps.wikimedia.org/wikidatawiki/20191120/)

Tools
=====

\*wikidata-dump-processor
[[https://github.com/panx27/wikidata-dump-processor]{.underline}](https://github.com/panx27/wikidata-dump-processor)

[[https://github.com/maxlath/wikidata-filter]{.underline}](https://github.com/maxlath/wikidata-filter)

[[https://github.com/inventaire/entities-search-engine/tree/wikidata-subset-search-engine]{.underline}](https://github.com/inventaire/entities-search-engine/tree/wikidata-subset-search-engine)

[[https://www.wikidata.org/wiki/Wikidata:Tools]{.underline}](https://www.wikidata.org/wiki/Wikidata:Tools)

[[https://www.wikidata.org/wiki/Wikidata:Tools/For\_programmers]{.underline}](https://www.wikidata.org/wiki/Wikidata:Tools/For_programmers)

Dump to CouchDB (Apache)
[[https://github.com/maxlath/import-wikidata-dump-to-couchdb\#installation]{.underline}](https://github.com/maxlath/import-wikidata-dump-to-couchdb#installation)

**SPARQL query builder**
[[https://www.wikidata.org/wiki/Wikidata:Tools/Query\_data]{.underline}](https://www.wikidata.org/wiki/Wikidata:Tools/Query_data)

[[https://www.wikidata.org/wiki/Wikidata:List\_of\_properties]{.underline}](https://www.wikidata.org/wiki/Wikidata:List_of_properties)

[[https://www.mediawiki.org/wiki/Wikidata\_Query\_Service/User\_Manual]{.underline}](https://www.mediawiki.org/wiki/Wikidata_Query_Service/User_Manual)

standalone server (using blazegraph, a graph DB) (other graph db:
Dgraph)

  **Triples**   **Est. Size on Disk (GBs)**
  ------------- -----------------------------
  10M           .84 GB
  100M          8.4 GB
  1B            83.8 GB

  **Edges (triples)**   **RAM (GB)**
  --------------------- --------------
  10M                   4GB
  100M                  4GB
  200M                  8GB
  500M                  16GB
  750M                  24GB
  1B                    32GB

83.3GB disk size and 32 GB RAM for 1B triples

**5B triples for Wikidata =\> 415GB disk size and 160 RAM required**

Others:
-------

(For books)
[[https://github.com/inventaire/inventaire]{.underline}](https://github.com/inventaire/inventaire)
(source code of
[[https://inventaire.io/]{.underline}](https://inventaire.io/))

Libre collaborative resource mapper powered by open-knowledge

NodeJS + CouchDB + ElasticSearch

Wikidata + Wikipedia + Wikicommons

MongoDB
=======

/etc/mongod.conf

/var/lib/mongodb

/var/log/mongodb

mkdir -p /data/db

[[https://docs.mongodb.com/manual/administration/production-notes/\#hardware-considerations]{.underline}](https://docs.mongodb.com/manual/administration/production-notes/#hardware-considerations)

[[https://docs.mongodb.com/manual/mongo/]{.underline}](https://docs.mongodb.com/manual/mongo/)

[[https://docs.mongodb.com/manual/reference/program/mongod/]{.underline}](https://docs.mongodb.com/manual/reference/program/mongod/)

WiredTiger
----------

The
[WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)
storage engine is multithreaded and can take advantage of additional CPU
cores. Specifically, the total number of active threads (i.e. concurrent
operations) relative to the number of available CPUs can impact
performance:

-   Throughput *increases* as the number of concurrent active operations
    increases up to the number of CPUs.

-   Throughput *decreases* as the number of concurrent active operations
    exceeds the number of CPUs by some threshold amount.

Starting in MongoDB 3.4, the default WiredTiger internal cache size is
the larger of either:

-   50% of (RAM - 1 GB), or

-   256 MB.

Recommended ulimit Settings
---------------------------

Every deployment may have unique requirements and settings; however, the
following thresholds and settings are particularly important for mongod
and mongos deployments:

-f (file size): unlimited

-t (cpu time): unlimited

-v (virtual memory): unlimited \[1\]

-l (locked-in-memory size): unlimited

-n (open files): 64000

-m (memory size): unlimited \[1\] \[2\]

-u (processes/threads): 64000

RDF
===

RDF represents information using [[semantic
triples]{.underline}](https://en.wikipedia.org/wiki/Semantic_triple),
which comprise a subject, predicate, and object. Each item in the triple
is expressed as a
Web [[URI]{.underline}](https://en.wikipedia.org/wiki/URI).

RDF stands for **R**esource **D**escription **F**ramework

-   RDF is a framework for describing resources on the web

-   RDF is designed to be read and understood by computers

-   RDF is not designed for being displayed to people

    -   RDF descriptions are not designed to be displayed on the web.

-   RDF is written in XML

    -   The XML language used by RDF is called RDF/XML.

-   RDF is a part of the W3C\'s Semantic Web Activity

    -   Web information has exact meaning

    -   Web information can be understood and processed by computers

    -   Computers can integrate information from the web

-   RDF is a W3C Recommendation from 10. February 2004

RDF uses Web identifiers (URIs) to identify resources.

RDF describes resources with properties and property values.

A **Resource** is anything that can have a URI, such as
[[https://www.w3schools.com/rdf]{.underline}](https://www.w3schools.com/rdf)

-   A **Property** is a Resource that has a name, such as \"author\" or
    \"homepage\"

-   A **Property value** is the value of a Property, such as \"Jan Egil
    Refsnes\" or \"https://www.w3schools.com\" (note that a property
    value can be another resource)

-   \<?xml version=\"1.0\"?\>\
    \
    \<RDF\>\
      \<Description about=\"https://www.w3schools.com/rdf\"\>\
        \<author\>Jan Egil Refsnes\</author\>\
        \<homepage\>https://www.w3schools.com\</homepage\>\
      \</Description\>\
    \</RDF\>

The combination of a Resource, a Property, and a Property value forms
a **Statement** (known as the **subject, predicate and object** of a
Statement).

Statement: \"The author of https://www.w3schools.com/rdf is Jan Egil
Refsnes\".

-   The subject of the statement above is: https://www.w3schools.com/rdf

-   The predicate is: author

-   The object is: Jan Egil Refsnes

an RDF document (includes multiple RDF statements)

Title Artist Country Company Price Year

Empire Burlesque Bob Dylan USA Columbia 10.90 1985

Hide your heart Bonnie Tyler UK CBS Records 9.90 1988

\<?xml version=\"1.0\"?\>\
\
\<rdf:RDF\
xmlns:rdf=\"http://www.w3.org/1999/02/22-rdf-syntax-ns\#\"\
xmlns:cd=\"http://www.recshop.fake/cd\#\"\>\
\
\<rdf:Description\
rdf:about=\"http://www.recshop.fake/cd/Empire Burlesque\"\>\
  \<cd:artist\>Bob Dylan\</cd:artist\>\
  \<cd:country\>USA\</cd:country\>\
  \<cd:company\>Columbia\</cd:company\>\
  \<cd:price\>10.90\</cd:price\>\
  \<cd:year\>1985\</cd:year\>\
\</rdf:Description\>\
\
\<rdf:Description\
rdf:about=\"http://www.recshop.fake/cd/Hide your heart\"\>\
  \<cd:artist\>Bonnie Tyler\</cd:artist\>\
  \<cd:country\>UK\</cd:country\>\
  \<cd:company\>CBS Records\</cd:company\>\
  \<cd:price\>9.90\</cd:price\>\
  \<cd:year\>1988\</cd:year\>\
\</rdf:Description\>\
.\
.\
.\
\</rdf:RDF\>

RDF Elements:

The main elements of RDF are the root element, \<RDF\>, and the
\<Description\> element, which identifies a resource.

> . The \<rdf:RDF\> Element -
>
> \<rdf:RDF\> is the root element of an RDF document. It defines the XML
> document to be an RDF document. It also contains a reference to the
> RDF namespace:
>
> The \<rdf:Description\> Element -
>
> The \<rdf:Description\> element identifies a resource with the about
> attribute.
>
> The \<rdf:Description\> element contains elements that describe the
> resource

Properties as Attributes
------------------------

The property elements can also be defined as attributes (instead of
elements):

\<?xml version=\"1.0\"?\>\
\
\<rdf:RDF\
xmlns:rdf=\"http://www.w3.org/1999/02/22-rdf-syntax-ns\#\"\
xmlns:cd=\"http://www.recshop.fake/cd\#\"\>\
\
\<rdf:Description\
rdf:about=\"http://www.recshop.fake/cd/Empire Burlesque\"\
cd:artist=\"Bob Dylan\" cd:country=\"USA\"\
cd:company=\"Columbia\" cd:price=\"10.90\"\
cd:year=\"1985\" /\>\
\
\</rdf:RDF\>

Properties as Resources
-----------------------

The property elements can also be defined as resources:

\<?xml version=\"1.0\"?\>\
\
\<rdf:RDF\
xmlns:rdf=\"http://www.w3.org/1999/02/22-rdf-syntax-ns\#\"\
xmlns:cd=\"http://www.recshop.fake/cd\#\"\>\
\
\<rdf:Description\
rdf:about=\"http://www.recshop.fake/cd/Empire Burlesque\"\>\
  \<cd:artist rdf:resource=\"http://www.recshop.fake/cd/dylan\" /\>\
  \...\
  \...\
\</rdf:Description\>\
\
\</rdf:RDF\>

In the example above, the property artist does not have a value, but a
reference to a resource containing information about the artist.

RDF Containers
--------------

RDF containers are used to describe group of things.

The following RDF elements are used to describe groups: \<Bag\>,
\<Seq\>, and \<Alt\>.

Bag: may contain duplicate values, unordered

\<rdf:Bag\>\
      \<rdf:li\>John\</rdf:li\>\
      \<rdf:li\>Paul\</rdf:li\>\
      \<rdf:li\>George\</rdf:li\>\
      \<rdf:li\>Ringo\</rdf:li\>\
    \</rdf:Bag\>

Seq: may contain duplicate values, ordered

\<rdf:Seq\>\
      \<rdf:li\>George\</rdf:li\>\
      \<rdf:li\>John\</rdf:li\>\
      \<rdf:li\>Paul\</rdf:li\>\
      \<rdf:li\>Ringo\</rdf:li\>\
    \</rdf:Seq\>

Alt: The \<rdf:Alt\> element is used to describe a list of alternative
values (the user can select only one of the values).

\<rdf:Alt\>\
      \<rdf:li\>CD\</rdf:li\>\
      \<rdf:li\>Record\</rdf:li\>\
      \<rdf:li\>Tape\</rdf:li\>\
    \</rdf:Alt\>

RDF Collections
---------------

The rdf:parseType=\"Collection\" Attribute:

As seen in the previous chapter, a container says that the containing
resources are members - it does not say that other members are not
allowed.

RDF collections are used to describe groups that can ONLY contain the
specified members.

A collection is described by the attribute rdf:parseType=\"Collection\".

\<rdf:Description\
rdf:about=\"http://recshop.fake/cd/Beatles\"\>\
  \<cd:artist rdf:parseType=\"Collection\"\>\
    \<rdf:Description rdf:about=\"http://recshop.fake/cd/Beatles/George\"/\>\
    \<rdf:Description rdf:about=\"http://recshop.fake/cd/Beatles/John\"/\>\
    \<rdf:Description rdf:about=\"http://recshop.fake/cd/Beatles/Paul\"/\>\
    \<rdf:Description rdf:about=\"http://recshop.fake/cd/Beatles/Ringo\"/\>\
  \</cd:artist\>\
\</rdf:Description\>

Notation3 (.n3)
---------------

**\@prefix** **dc:** \<http://purl.org/dc/elements/1.1/\>.

\<http://en.wikipedia.org/wiki/Tony\_Benn\>

**dc:title** \"Tony Benn\";

**dc:publisher** \"Wikipedia\".

**\<rdf:RDF**

xmlns:rdf=\"http://www.w3.org/1999/02/22-rdf-syntax-ns\#\"

xmlns:dc=\"http://purl.org/dc/elements/1.1/\"**\>**

**\<rdf:Description**
rdf:about=\"http://en.wikipedia.org/wiki/Tony\_Benn\"**\>**

**\<dc:title\>**Tony Benn**\</dc:title\>**

**\<dc:publisher\>**Wikipedia**\</dc:publisher\>**

**\</rdf:Description\>**

**\</rdf:RDF\>**

Turtle (syntax) .ttl
--------------------

Turtle provides a way to group three URIs to make a triple, and provides ways to abbreviate such information, for example by factoring out common portions of URIs.
-------------------------------------------------------------------------------------------------------------------------------------------------------------------

Turtle syntax is similar to that of [[SPARQL]{.underline}](https://en.wikipedia.org/wiki/SPARQL), an [[RDF query language]{.underline}](https://en.wikipedia.org/wiki/RDF_query_language).
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

\<http://example.org/person/Mark\_Twain\>

\<http://example.org/relation/author\>

\<http://example.org/books/Huckleberry\_Finn\> .

**\@prefix** **rdf:** \<http://www.w3.org/1999/02/22-rdf-syntax-ns\#\> .

**\@prefix** **dc:** \<http://purl.org/dc/elements/1.1/\> .

**\@prefix** **ex:** \<http://example.org/stuff/1.0/\> .

\<http://www.w3.org/TR/rdf-syntax-grammar\>

**dc:title** \"RDF/XML Syntax Specification (Revised)\" ;

**ex:editor** \[

**ex:fullname** \"Dave Beckett\";

**ex:homePage** \<http://purl.org/net/dajobe/\>

\] .

NT (N-Triples) .nt
------------------

 It is a line-based, plain
text [[serialisation]{.underline}](https://en.wikipedia.org/wiki/Serialisation) format
for [[RDF]{.underline}](https://en.wikipedia.org/wiki/Resource_Description_Framework) (Resource
Description Framework) graphs, and a subset of
the [[Turtle]{.underline}](https://en.wikipedia.org/wiki/Turtle_(syntax)) (Terse
RDF Triple Language) format. N-Triples should not be confused
with [[Notation3]{.underline}](https://en.wikipedia.org/wiki/Notation_3) which
is a superset of Turtle. N-Triples was designed to be a simpler format
than Notation 3 and Turtle,

### Ex1

\<http://www.w3.org/TR/rdf-syntax-grammar\>
\<http://purl.org/dc/elements/1.1/title\> \"RDF/XML Syntax Specification
(Revised)\" .

\<http://www.w3.org/TR/rdf-syntax-grammar\>
\<http://example.org/stuff/1.0/editor\> \_**:bnode** .

\_**:bnode** \<http://example.org/stuff/1.0/fullname\> \"Dave Beckett\"
.

\_**:bnode** \<http://example.org/stuff/1.0/homePage\>
\<http://purl.org/net/dajobe/\> .

### Ex2

\<http://www.w3.org/2001/sw/RDFCore/ntriples/\>
\<http://www.w3.org/1999/02/22-rdf-syntax-ns\#type\> ↵

\<http://xmlns.com/foaf/0.1/Document\> .

\<http://www.w3.org/2001/sw/RDFCore/ntriples/\>
\<http://purl.org/dc/terms/title\> \"N-Triples\"@*en-US* .

\<http://www.w3.org/2001/sw/RDFCore/ntriples/\>
\<http://xmlns.com/foaf/0.1/maker\> \_**:art** .

\<http://www.w3.org/2001/sw/RDFCore/ntriples/\>
\<http://xmlns.com/foaf/0.1/maker\> \_**:dave** .

\_**:art** \<http://www.w3.org/1999/02/22-rdf-syntax-ns\#type\>
\<http://xmlns.com/foaf/0.1/Person\> .

\_**:art** \<http://xmlns.com/foaf/0.1/name\> \"Art Barstow\".

\_**:dave** \<http://www.w3.org/1999/02/22-rdf-syntax-ns\#type\>
\<http://xmlns.com/foaf/0.1/Person\> .

\_**:dave** \<http://xmlns.com/foaf/0.1/name\> \"Dave Beckett\".

Same as below in RDF/XML format

**\<rdf:RDF** xmlns=\"http://xmlns.com/foaf/0.1/\"

xmlns:dc=\"http://purl.org/dc/terms/\"

xmlns:rdf=\"http://www.w3.org/1999/02/22-rdf-syntax-ns\#\" **\>**

**\<Document**
rdf:about=\"http://www.w3.org/2001/sw/RDFCore/ntriples/\"**\>**

**\<dc:title** xml:lang=\"en-US\"**\>**N-Triples**\</dc:title\>**

**\<maker\>**

**\<Person** rdf:nodeID=\"art\"**\>**

**\<name\>**Art Barstow**\</name\>**

**\</Person\>**

**\</maker\>**

**\<maker\>**

**\<Person** rdf:nodeID=\"dave\"**\>**

**\<name\>**Dave Beckett**\</name\>**

**\</Person\>**

**\</maker\>**

**\</Document\>**

**\</rdf:RDF\>**

Comparison of Notation3, Turtle, and N-Triples
----------------------------------------------

Feature Notation3 Turtle N-Triples

Character encoding UTF-8 UTF-8 ASCII

Directives

\@base Yes Yes No

\@forAll Yes No No

\@forSome Yes No No

\@keywords Yes No No

\@prefix Yes Yes No

Lists

() (DAML lists) Yes Yes No

{ ... } (statement lists) Yes No No

Literals

true / false (Boolean) Yes Yes No

xsd:decimal (decimal arbitrary length) Yes Yes No

xsd:double (decimal double) Yes Yes No

xsd:integer (decimal integer) Yes Yes No

Syntactic sugar

RDF paths Yes No No

QNames Yes Yes No

a/\@a (equiv. to rdf:type) Yes Yes No

\[\] (shorthand for blank node) Yes Yes No

=\> (x implies y) Yes No No

\<= (y implies x) Yes No No

= (x is equivalent to y) Yes No No

, (repeat object in list) Yes Yes No

; (repeat subject/verb in list) Yes Yes No

N-Quads .nq
-----------

The related *N-Quads* superset extends N-Triples with an optional
context value at the fourth position.

\<http://one.example/subject1\> \<http://one.example/predicate1\>
\<http://one.example/object1\> \<http://example.org/graph3\> . *\#
comments here*

*\# or on a line by themselves*

\_**:subject1** \<http://an.example/predicate1\> \"object1\"
\<http://example.org/graph1\> .

\_**:subject2** \<http://an.example/predicate2\> \"object2\"
\<http://example.org/graph5\> .

TriG (syntax) .trig
-------------------

This example encodes three interlinked named graphs:

-   http://www.example.org/exampleDocument\#G1

-   http://www.example.org/exampleDocument\#G2

-   http://www.example.org/exampleDocument\#G3

**\@prefix** **rdf:** \<http://www.w3.org/1999/02/22-rdf-syntax-ns\#\> .

**\@prefix** **xsd:** \<http://www.w3.org/2001/XMLSchema\#\> .

**\@prefix** **swp:** \<http://www.w3.org/2004/03/trix/swp-1/\> .

**\@prefix** **dc:** \<http://purl.org/dc/elements/1.1/\> .

**\@prefix** **ex:** \<http://www.example.org/vocabulary\#\> .

**\@prefix** **:** \<http://www.example.org/exampleDocument\#\> .

**:G1** { **:Monica** **ex:name** \"Monica Murphy\" .

**:Monica** **ex:homepage** \<http://www.monicamurphy.org\> .

**:Monica** **ex:email** \<mailto:monica\@monicamurphy.org\> .

**:Monica** **ex:hasSkill** **ex:Management** }

**:G2** { **:Monica** **rdf:type** **ex:Person** .

**:Monica** **ex:hasSkill** **ex:Programming** }

**:G3** { **:G1** **swp:assertedBy** \_**:w1** .

\_**:w1** **swp:authority** **:Chris** .

\_**:w1** **dc:date** \"2003-10-02\"\^\^*xsd:date* .

**:G2** **swp:quotedBy** \_**:w2** .

**:G3** **swp:assertedBy** \_**:w2** .

\_**:w2** **dc:date** \"2003-09-03\"\^\^*xsd:date* .

\_**:w2** **swp:authority** **:Chris** .

**:Chris** **rdf:type** **ex:Person** .

**:Chris** **ex:email** \<mailto:chris\@bizer.de\> }

DBPedia
=======

**DBpedia** (from \"DB\" for
\"[[database]{.underline}](https://en.wikipedia.org/wiki/Database)\") is
a project aiming to extract [[structured
content]{.underline}](https://en.wikipedia.org/wiki/Structured_content) from
the information created in
the [[Wikipedia]{.underline}](https://en.wikipedia.org/wiki/Wikipedia) project.
This structured information is made available on the [[World Wide
Web]{.underline}](https://en.wikipedia.org/wiki/World_Wide_Web).[^[\[2\]]{.underline}^](https://en.wikipedia.org/wiki/DBpedia#cite_note-crystallization-2) DBpedia
allows users to [[semantically
query]{.underline}](https://en.wikipedia.org/wiki/Semantic_query) relationships
and properties of Wikipedia resources, including links to other
related [[datasets]{.underline}](https://en.wikipedia.org/wiki/Dataset).[^[\[3\]]{.underline}^](https://en.wikipedia.org/wiki/DBpedia#cite_note-LinkedData3Sat-3) [[Tim
Berners-Lee]{.underline}](https://en.wikipedia.org/wiki/Tim_Berners-Lee) described
DBpedia as one of the most famous parts of the decentralized [[Linked
Data]{.underline}](https://en.wikipedia.org/wiki/Linked_Data) effort.[^[\[4\]]{.underline}^](https://en.wikipedia.org/wiki/DBpedia#cite_note-Berners-LeeTalis-4)

[[https://databus.dbpedia.org/dbpedia/collections/pre-release-2019-08-30]{.underline}](https://databus.dbpedia.org/dbpedia/collections/pre-release-2019-08-30)

infobox no Chinese

JSON-LD .jsonld
===============

**JSON-LD** (JavaScript Object Notation for Linked Data), is a method of
encoding [[Linked
Data]{.underline}](https://en.wikipedia.org/wiki/Linked_Data) using [[JSON]{.underline}](https://en.wikipedia.org/wiki/JSON). JSON-LD
is designed around the concept of a \"context\" to provide additional
mappings from JSON to
an [[RDF]{.underline}](https://en.wikipedia.org/wiki/Resource_Description_Framework) model. 

{

\"\@context\": {

**\"name\"**: \"http://xmlns.com/foaf/0.1/name\",

**\"homepage\"**: {

\"\@id\": \"http://xmlns.com/foaf/0.1/workplaceHomepage\",

\"\@type\": \"\@id\"

},

**\"Person\"**: \"http://xmlns.com/foaf/0.1/Person\"

},

\"\@id\": \"https://me.example.com\",

\"\@type\": \"Person\",

**\"name\"**: \"John Smith\",

**\"homepage\"**: \"https://www.example.com/\"

}

RDF/XML
=======

**RDF/XML** is a
syntax,[^[\[1\]]{.underline}^](https://en.wikipedia.org/wiki/RDF/XML#cite_note-syntaxspec-1) defined
by
the [[W3C]{.underline}](https://en.wikipedia.org/wiki/W3C),[^[\[2\]]{.underline}^](https://en.wikipedia.org/wiki/RDF/XML#cite_note-timelinehistory-2) to
express
(i.e. [[serialize]{.underline}](https://en.wikipedia.org/wiki/Serialization))
an [[RDF]{.underline}](https://en.wikipedia.org/wiki/Resource_Description_Framework) graph
as an [[XML]{.underline}](https://en.wikipedia.org/wiki/XML) document.

SPARQL
======

**SPARQL Protocol and RDF Query Language**) is an [[RDF query
language]{.underline}](https://en.wikipedia.org/wiki/RDF_query_language)---that
is,
a [[semantic]{.underline}](https://en.wikipedia.org/wiki/Semantic_Query) [[query
language]{.underline}](https://en.wikipedia.org/wiki/Query_language) for [[databases]{.underline}](https://en.wikipedia.org/wiki/Database)---able
to retrieve and manipulate data stored in [[Resource Description
Framework
(RDF)]{.underline}](https://en.wikipedia.org/wiki/Resource_Description_Framework) format.

**PREFIX** **foaf**: \<http://xmlns.com/foaf/0.1/\>

**SELECT** ?name

?email

**WHERE**

{

?person **a** **foaf**:**Person** .

?person **foaf**:**name** ?name .

?person **foaf**:**mbox** ?email .

}
