---
title: Non-Normative Guidance in Using NIEM with JSON
layout: specification
ndr-href: https://reference.niem.gov/niem/specification/naming-and-design-rules/3.0/NIEM-NDR-3.0-2014-07-31.html
json-api-href: http://www.w3.org/TR/json-ld-api/
---

## Contents
{:.no_toc}

* This line is a placeholder to generate the table of contents
{:toc}

## Authors
{:.no_toc}

* Scott Renner <srenner@mitre.org>
* Webb Roberts <webb.roberts@gtri.gatech.edu>, @webb
* Leila Tite <Leila.Tite@co.hennepin.mn.us>

## Abstract
{:.no_toc}

{% capture abstract-text %}
This document provides guidance from the [NIEM Technical Architecture
Committee (NTAC)](https://www.niem.gov/meet-us/ntac) for using the
National Information Exchange Model ([NIEM](http://niem.gov)) with
JavaScript Object Notation (JSON).  NIEM provides a well-established
standard for defining information exchanges. JSON is specified by
[RFC4627](#bibrfc4627).
{% endcapture %}
{{ abstract-text }}

## Status
{:.no_toc}

This guidance is non-normative. It discusses possible NIEM conformance
rules for JSON data, but it does not establish any specification for
such conformance.

Readers are invited to provide feedback on this document by entering
an issue at
[https://github.com/NIEM/NIEM.github.io/issues](https://github.com/NIEM/NIEM.github.io/issues)
or sending email to <niem-comments@lists.gatech.edu>.

<div id="body-start"></div>

## Introduction

{{ abstract-text }}

This document is provided as guidance, not as a normative specification. It
discusses possible NIEM conformance rules for JSON data, but it does not
establish any specification for such conformance. This document is the first
step in establishing a repeatable and interoperable methodology for using JSON
to represent NIEM-conformant information exchanges. The guidelines presented by
this document should be in alignment with later normative specifications that
define NIEM-conformant JSON.

### Audience

The primary target of this guidance is the developer who has a NIEM IEPD that
defines one or more XML messages, and who wishes to exchange JSON
representations of those messages, instead of XML. For these users, this
document supplies patterns for converting an NIEM XML document (i.e., an IEP, an
information exchange package) into a semantically-equivalent JSON serialization.

<!-- what are these? - @webb

* A description of NIEM conformance for JSON serialization of IEPs
* Suggestions for implementing a translator from an XML serialization
  of a particular IEP class to JSON, or the reverse

-->

This document also provides guidance for the developer who wishes to use
NIEM definitions in a JSON object design. Guidance for developers
who wish to map existing JSON objects to NIEM XML may be provided at a
later date.

### Overview

This document describes NIEM's methodology for creating consistent,
interoperable JSON messages that are based on NIEM-conformant XML schemas. Core
aspects of this guidance are:

1. The guidance describes JSON messages based on NIEM Schemas.
1. The JSON messages are expressed as JSON-LD, a scalable JSON framework for
   linked data.
1. The names, relationships and structures in JSON messages are based on names,
   relationships and structures described by NIEM-conformant XML schemas.
1. The details of how JSON-LD represents NIEM XML data is based on the NIEM
   Naming and Design Rules's mapping between XML (and XML Schema) and RDF.

### The NIEM data model and RDF {#niem-and-rdf}

The [NIEM Naming and Design Rules (NDR)]({{page.ndr-href}}) is the main document that
explains the meaning of NIEM&endash;conformant XML schemas and XML instance
documents. The framework that the NIEM NDR relies on for meaning is the Resource
Description Framework (RDF), which defines a data model that is the basis for
Semantic Web technologies. NIEM defines the meaning of conformant XML schemas
and XML instance documents in terms of RDF. This document leverages the NDR's
RDF to map NIEM XML schemas and XML instance documents to JSON-LD.

Although NIEM is, at its core, defined in terms of RDF, most users of NIEM do
not have to understand much about RDF; NIEM is primarily discussed in terms of
XML and XML schema: rules about elements, attributes, simple and complex types,
etc. Similarly, users of this guidance document, and of NIEM's JSON-LD
representation, will not need to understand much about RDF. The few RDF concepts
this document explicitly requires are explained without requiring deep
understanding of the underlying RDF concepts.

The
[NIEM NDR Section 5, &ldquo;The NIEM conceptual model&rdquo;]({{page.ndr-href}}#section_5)
describes the conceptual model of NIEM, and provides a mapping between
NIEM-conformant XML schemas and XML instance documents. This section is the
basis for much of what appears in this document. For example:

* [Section 5.6.1, &ldquo;Resource IRIs for XML Schema components and information items&rdquo;]({{page.ndr-href}}#section_5.6.1)
  describes how to find an internationalized resource identifier (IRI) for a
  schema component or XML element or attribute, using the namespace name and
  local name of the component's qualified name (QName). This is the basis for
  the translation of element names into JSON-LD terms.
* [Section 5.6.5.4, &ldquo;Element as a simple property of an object or association&rdquo;]({{page.ndr-href}}#section_5.6.5.4),
  describes the RDF for an element of an object or association type. This is the
  basis for how a NIEM-conformant XML element is translated into corresponding
  JSON-LD.

These, and other sections of the NDR are the basis of the translation from NIEM
XML to JSON-LD. A goal of this document is to explain the translations from NIEM to
JSON-LD sufficiently, so that the reader does not need to understand the NDR's
mappings to RDF, nor the JSON-LD's RDF basis.

### Use of JSON-LD

This document describes the translation of NIEM-conformant XML schemas and XML
instance documents into JSON, using JSON-LD as the target JSON
representation. JSON-LD (JavaScript Object Notation for Linking Data) is a
language-independent data format for representing Linked Data, expressed as
JSON. JSON-LD provides a lightweight mechanism that allows data to be linked
across websites. The syntax is easy for humans to read and easy for machines to
parse and generate.

There are several reasons that JSON-LD is a good fit for NIEM. One reason for
choosing JSON-LD is context mechanism, which allows names in JSON-LD to look
like XML qualified names (QNames). As described [above](#niem-and-rdf), the NDR
defines how to translate element QNames to JSON-LD IRIs. These IRIs can be represented in a short form using a JSON-LD context. For example, the XML qualified name&hellip;

> `nc:PersonName`

&hellip;with a declared XML namespace prefix&hellip;

> `xmlns:nc="http://release.niem.gov/niem/niem-core/3.0/"`

&hellip;corresponds to the resource IRI&hellip;

> `http://release.niem.gov/niem/niem-core/3.0/#PersonName`

Given the right context, this IRI can be shortened. Given the JSON-LD data&hellip;

```javascript
{
  "http://release.niem.gov/niem/niem-core/3.0/#quantityUnitText" : "dozen"
}
```

&hellip;and the JSON-LD context&hellip;

```javascript
{
   "nc" : "http://release.niem.gov/niem/niem-core/3.0/#"
}
```

&hellip;a compact form of the same data can be represented as&hellip;

```javascript
{
  "@context": {
    "nc": "http://release.niem.gov/niem/niem-core/3.0/#"
  },
  "nc:quantityUnitText": "dozen"
}
```

Another reason to use JSON-LD to represent NIEM data is that JSON-LD explicitly
supports a set of algorithms for manipulating JSON-LD data. The process of going
from a long form of JSON-LD to a short form is the compaction algorithm,
described by
[JSON-LD 1.0 Processing Algorithms and API, Section 2.2, &ldquo;Compaction&rdquo;]({{page.json-api-href}}#compaction).

Similarly, the JSON-LD specifications define a process of expanding JSON-LD into
a canonical long-form, which inlines context information, and expands IRIs into
their long forms. This is described by
[JSON-LD 1.0 Processing Algorithms and API, Section 2.1, &ldquo;Expansion&rdquo;]({{json-api-href}}#expansion). The
expanded form of the above data follows:

```javascript
[
  {
    "http://release.niem.gov/niem/niem-core/3.0/#quantityUnitText": [
      {
        "@value": "dozen"
      }
    ]
  }
]
```

Note that there are more features here than were in the initial data. Expanded
form makes explicit what is implicit in JSON-LD's short form. For example, in
short form, a key in an object may have a single value or may an array of
values. In expanded form there will always be an array of values, even if the
array contains only a single value. 

Using JSON-LD as the target for NIEM data allows developers to use the data
several ways; all of these methods are legitimate ways to access NIEM JSON-LD data:

1. A developer can work with the data as if it is plain JSON, without worrying
   about details of JSON-LD.
1. A developer can use JSON-LD tools to transform the data prior to processing,
   including using compaction against a known context, expansion, flattening, or framing.
1. A developer can convert JSON-LD to RDF and use RDF techniques, such as SPARQL
   queries.

## JSON-LD representation of NIEM XML {#xml-to-json}

This section walks through the transformation of a NIEM XML instance document
into corresponding JSON-LD data. Each section within highlights the
transformation of a NIEM or XML concept into corresponding JSON-LD. The full
source XML appears in [an appendix below](#full-example-xml). The resulting JSON-LD also appears in [an appendix below](#full-example-json).

### Namespaces &amp; JSON-LD context

NIEM uses namespaces to organize its objects into domains. The namespaces are
defined in the root element of a NIEM IEP. While NIEM element names tend to be
unique across domains, this isn't guaranteed. To uniquely specify a NIEM
element, the name must be combined with a namespace prefix.

JSON-LD provides a similar mechanism. JSON-LD uses International
Resource Identifiers (IRIs) to uniquely identify objects. Prefixes for these
IRIs are defined in the context and can then be used in object names. This
functionality can be used to represent XML namespaces, including the concept of
short-hand prefixes.

The root element (or document element) of a NIEM IEP is converted to a
JSON object with two pairs.  One key is the JSON-LD reserved term
`@context`. The key for the other pair is the QName of the root
element. So, for example, the JSON-LD for the `CrashDriverInfo` IEP is:

```javascript
{
    "@context" : {
        "exch" : "http://example.com/CrashDriver/1.0/#",
        "j" : "http://release.niem.gov/niem/domains/jxdm/5.1/#",
        "nc" : "http://release.niem.gov/niem/niem-core/3.0/#",
        "geo" : "http://release.niem.gov/niem/adapters/geospatial/3.0/",
        "gml" : "http://www.opengis.net/gml/3.2",
        "structures" : "http://release.niem.gov/niem/structures/3.0/#",
        "rdf" : "http://www.w3.org/1999/02/22-rdf-syntax-ns#"
    },
    "exch:CrashDriverInfo" : {
        <!-- remainder of JSON-LD for the CrashDriverInfoElement goes here -->
    }
}
```

The `@context` value must have a pair for every namespace declaration
that is in scope and is referenced within the root element. Notice how
there is a `#` character appended to the URIs from the namespace
declarations. This is needed to make the [JSON-LD expanded term
definition](https://www.w3.org/TR/json-ld/#the-context) align with the
[resource IRI for the schema
component](https://reference.niem.gov/niem/specification/naming-and-design-rules/3.0/niem-ndr-3.0.html#section_5.6.1).
For example, the JSON-LD term `exch:CrashDriverInfo` should expand to
`http://example.com/CrashDriver/1.0/#CrashDriverInfo`, because that is the
IRI for the `CrashDriverInfo` element declaration in the IEPD schema.

The pair in `@context` with the `rdf` key is there for a reason, which
will be presented later.

Apart from the `@context` pair, the JSON-LD representation of the root
element is the same as the representation of any element with complex
content.

### Element with Complex Content

Consider the `j:CrashDriver` element in our example IEP.  Its JSON-LD
representation is

```javascript
  "j:CrashDriver" : {
    { "nc:RoleOfPerson" : <!-- RoleOfPerson element content --> },
    { "j:DriverLicense" : <!-- DriverLicense element content --> }
  }
```

The content of an element with complex content is converted to a JSON
object with one pair for the name of each attribute and one pair for
the name of each child element in that content. For an IEP that is
NIEM-conforming, there can't be a collision between attribute and
child element names, because of the [camel-case
rule](https://reference.niem.gov/niem/specification/naming-and-design-rules/3.0/niem-ndr-3.0.html#section_10.8.1).
For an IEP that is not NIEM conforming, it is possible to have an
attribute and a child element with the same name. This document has no
guidance for that case; developers are on their own.

Note that there is only one pair for each child
element *name*, no matter how many times that element appears in the
content.  It is now time to discuss...

### Repeatable Elements

The element `nc:PersonMiddleName` is repeatable in the IEPD schema
definition of `nc:PersonNameType`. The `PersonName` element is
converted to this JSON

```javascript
  "nc:PersonName" : {
    { "nc:PersonGivenName" : <!-- PersonGivenName content --> },
    { "nc:PersonMiddleName" : [
        <!-- Content of 1st PersonMiddleName element --> ,
        <!-- Content of 2nd PersonMiddleName element --> ] },
    { "nc:PersonSurName" : <!-- PersonSurName content --> }
  }
```

A repeatable element is converted into a JSON object with an array
value. The array contains one JSON object for the content of each
element instance, in their order of appearance.

The schema is consulted to determine whether an element is
*repeatable*, and not the the XML to see if the element is actually
*repeated*. If a repeatable element appears at all, it gets an
array. This consistency makes life easier for developers who are
writing code to access the data as plain JSON. For example, in Perl,
given a reference to the JSON object for `nc:Person`, the code to
access the contents of the first `PersonMiddleName` element would be

```perl
$r->{"nc:PersonName"}->{"nc:PersonMiddleName"}->[0]
```

For example, in Javascript, if we had a reference to
the JSON object for `nc:Person`, we would access the contents of the
first `PersonMiddleName` element with

```javascript
repeated_elements_niem_json["nc:PersonName"]["nc:PersonMiddleName"][0]; 
```

If the JSON serialization used an array only for elements that are actually *repeated*,
then it would be necessary to test whether the array is there, with Perl code like:

```perl
ref($r->{"nc:PersonName"}->{"nc:PersonMiddleName"}) eq 'HASH' ?
    $r->{"nc:PersonName"}->{"nc:PersonMiddleName"} :
    $r->{"nc:PersonName"}->{"nc:PersonMiddleName"}->[0]
```

with Javascript code like

```javascript
(typeof repeated_elements_niem_json["nc:PersonName"]["nc:PersonMiddleName"] === 'object'
             ) ? (
                    repeated_elements_niem_json["nc:PersonName"]["nc:PersonMiddleName"][0]
             ) : (
                    repeated_elements_niem_json["nc:PersonName"]["nc:PersonMiddleName”]
             ); 
```


and that would get ugly pretty fast.

Observe that with this guidance, the same JSON is produced for these
two `Parent` elements:

```xml
|<Parent>          |    <Parent>          |
|    <Repeated/>   |        <Repeated/>   |
|    <Repeated/>   |        <Child/>      |
|    <Child/>      |        <Repeated/>   |
|</Parent>         |    </Parent>         |
```

Therefore it is up to the developer to ensure that these two elements have the
same meaning in the IEP.  If the meaning depends on the difference in
element ordering, then this guidance does not apply, and the developer
is on his own.  However, if the meaning does depend on this
difference, then the IEPD design is bad, conflicting 
with the [NIEM Conceptual
Model](https://reference.niem.gov/niem/specification/naming-and-design-rules/3.0/niem-ndr-3.0.html#section_5).

### Element with Simple Content and Attributes

The `nc:PersonGivenName` element has simple content and
attributes. Its JSON representation is

```javascript
  "nc:PersonGivenName" : {
    "nc:sequenceID" : 1,
    "rdf:value" : "Peter"
  }
```

The element's simple content is represented by the pair with the key
`rdf:value`.  That content requires a pair with *some* special key;
the representation can't be `{ "nc:PersonGivenName" : "Peter" }`,
because then there is no place to put the attributes.  The special key
could have some magic syntax, such as `"."`, and that would work for
JSON and JSON-LD consumers.  This guidance chooses `rdf:value`,
because that results in good RDF for consumers converting the JSON-LD
serialization into RDF, and still works for JSON and JSON-LD
consumers.  This is the reason for the `rdf` key in the `@context`
object.

Obviously this will break if an element in the IEP has `rdf:value` as
an attribute.  Fortunately, there is no good reason to do that in a NIEM IEP. 

### Element with Simple Content

The `nc:Date` element has simple content and no attributes.  Its JSON
representation is

```javascript
  "nc:Date" : {
    "rdf:value" : "1893-05-04"
  }
```

Now, since there are no attributes, this
simple content could instead be represnted by 

```javascript
  "nc:Date" : "1893-05-04"
```

but then when developers wanted to access the simple content, they
would have to always test whether the value was a string or an object,
writing Perl

```perl
ref($r->{"nc:Date"}) eq 'HASH' ?
    $r->{"nc:Date"}->{"rdf:value"} :
    $r->{"nc:Date"}
```

writing Javascript

```javascript
(typeof normal_simple_content_json["nc:Date"] ==='object'
             ) ? (
                    niem_simple_content_json["nc:Date"]["rdf:value"]
             ) : (
                    normal_simple_content_json["nc:Date"]
             ); 
```

As with repeatable elements, life is easier for the developers with
a consistent representation for all simple elements.

### Element with Numeric or Boolean Content

When the IEPD schema defines a numeric type for a simple element, the
value of the JSON pair is a number.  Likewise, when the schema defines
a boolean type, the value of the JSON pair is `true` or `false`.  For
example, the representations of `nc:MeasureDecimalValue` and
`exch:PersonSuspectedSpaceAlienIndicator` are:

```javascript
  "nc:MeasureDecimalValue" : {
    {"rdf:value" : 9.7 }
  }

  "exch:PersonFictionalCharacterIndicator" : {
    {"rdf:value" : true }
  }
```

### Elements with Empty or Nilled Content

The value of an empty element such as `<E/>` is represented by the
empty string, and the value of an explicitly nilled element such as
`<E xsi:nil="true"/>` is represented by JSON's `null`, like this:

```
  "E" : { "rdf:value" : "" } 
  "E" : { "rdf:value" : null }
```

However, reference elements, which have the
`structures:ref` attribute in addition to `xsi:nil`, receive special
handing, described below.

### ID Attributes

The `structures:id` attribute and any other ID attribute is
represented by the JSON-LD reserved key `@id`. The value of the `@id`
pair is formed by creating an IRI with the ID
attribute's value. The resulting string is a _Node Identifier_
in JSON-LD. For example, the
representation for `<nc:Person structures:id="P01">` is

```javascript
{ "nc:Person" : {
    "@id" : "http://example.org/Person/P01" },
    <!-- child elements go here --> }
  }
```

You can leave off the "http://example.org/Person/" part of the node identifier, 
and then the json-ld processor will generate a relative IRI that is only valid for
that instance of the document. Use a prefix that is part of a domain that
you control, even if the resource is not available publicly on the web.

### References and IDREF attributes

The value of a `structures:ref` attribute and any other IDREF attribute is
converted into a node reference to the @id of the corresponding JSON-LD resource.  
For example, the representation for
`<nc:RoleOfPerson structures:ref="P01" xsi:nil="true"/>` is

```javascript
{ "nc:RoleOfPerson" : {
    "@id" : "http://example.org/Person/P01" }
}
```

Observe that in JSON-LD, an object containing a pair with the `@id`
key may be a node reference or an identified node. The difference is
whether the object contains any *other* pairs; i.e. exactly one pair
is a reference, two or more pairs is an identified node.

Observe also that the `xsi:nil` attribute is useful only for schema validation, and does
not appear in the JSON-LD representation.

### Other XSI Attributes

For example, `xsi:type`.

### Abstract Elements and Substitution Groups

Because this guidance does not seek to replicate in JSON the underlying XML Schema
powering NIEM, substitution groups are simply represented by replicating the XML
instance structure, where the substitution has already taken
place. Abstract elements do not appear at all.  For example,

```xml
<nc:Person>
    <!-- Date replaces DateRepresentation -->
    <nc:PersonBirthDate>
        <nc:Date>1893-05-04</nc:Date>
    </nc:PersonBirthDate>
</nc:Person>
```

becomes

```javascript
    "nc:Person" : {
        "nc:PersonBirthDate" : {
            "nc:Date" : {
                "rdf:value" : "2006-05-04"
            }
        }
    }
```

### Augmentations

> &mdash;@webb Is this now fixed to match what we do in [the NDR]({{page.ndr-href}}#section_5.6.5.6). &mdash;@leilatite

NIEM has two kinds of augmentation elements. The most common is a
container element that is derived from `AugmentationType` and has a
name ending in `Augmentation`; for example, `LicenseAugmentation` in
the sample IEP. These augmentation container elements are important
for schema validation, but have no role in the conceptual model, and
so they do not appear in the JSON representation. So, for example, the
representation of `j:DriverLicense` is

```javascript
  "j:DriverLicense" : {
    "j:DriverLicenseCardIdentification" : {
        "nc:IdentificationID" : {
            "rdf:value" : "A1234567" }
    },
    "nc:ItemLengthMeasure" : {
        "nc:MeasureDecimalValue" : {
            "rdf:value" : 9.7 },
        "nc:LengthUnitCode" : {
            "rdf:value" : "CMT" }
    }
  }
```

> It seems like it would be more straightforward for j:DriverLicenseCardIdentification
> to have an @id of "A1234567" here. Not sure what jurisdiction issued this
> space alien's DriverLicense, but if this were a referencable link we could
> go there and find out other details, such as when it was issued, when it expires
> and if there are any restrictions. That would get rid of two blank nodes and
> the nc:IdentificationID node. &mdash;@leilatite

The other kind of augmentation element is an element declared in the
substitution group of an `AugmentationPoint`.  For example, the IEPD
schema includes the following element declaration:

```xml
<xs:element name="PersonFictionalCharacterIndicator" type="niem-xs:boolean"
  substitutionGroup="nc:PersonAugmentationPoint">
    <xs:annotation><xs:documentation>
        True if this person is a fictional character in a literary work.
    </xs:documentation></xs:annotation>     
</xs:element>
```

Augmentation elements of this kind are treated the same as any other
child element.

### Metadata
> We are still working on figuring out how to represent metadata in JSON-LD.
> The NDR says to hang metadata on RDF statements, but it is not clear
> what would be the best way to do that. One option is if the XML contains
> metadata, then create an RDF statement and hang the metadata off of that.
> Metadata on objects can be handled with rdf:value, but metadata on
> associations is harder. &mdash;@webb, &mdash;@leilatite

One way to do that is through reification. The JSON-LD would look
something like example from [stack overflow](http://stackoverflow.com/questions/33925116/how-to-refer-to-rdf-statements-in-json-ld-how-to-statement-about-statements):

```json
{
  "@context": {
    "rdf": "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
    "subject": { "@id": "rdf:subject", "@type": "@id" },
    "predicate": { "@id": "rdf:predicate", "@type": "@id" },
    "object": { "@id": "rdf:object", "@type": "@id" },
    "j": "http://release.niem.gov/niem/domains/jxdm/5.1/#",
    "structures": "http://release.niem.gov/niem/structures/3.0/#"
  },
  "@type": "rdf:Statement",
  "subject": "j:Charge",
  "predicate": "structures:metadata",
  "object": { "@id": "j:JusticeMetadata" },
  "j:CriminalInformationIndicator": {"rdf:value" : true }
}
```

### Adapter Elements

> I plan to add a Location element with GML content.  Then we can say
> that you're really on your own with adapted content... but the NIEM
> rules will often work.  I think they work for GML.  Let's see,
> anyway. &mdash;@iamdrscott

> Do we want to specify some other kind of example? It sounds like GML could be problematic.
> The GML people have geo-json.  &mdash;@leilatite

### Completed json-ld example from NIEM iep

> This is still incomplete.  &mdash;@leilatite
>
> And I changed the IEP, but haven't yet revised the JSON-LD &mdash;@iamdrscott

```json
{% include_relative sample-iepd/iep-samples/iep3.jsonld %}
```

## Implementing Translators

Discuss an **easy button** transfrom from NIEM XML to JSON-LD, treating input
like a *canned query* with very little optionality, transforming to JSON-LD
using XSLT3's JSON capability.

## Additional guidance

> Suggestions for the developer who wishes to use NIEM definitions in
> a JSON object design. Guidance for developers who wish to map
> existing JSON objects to NIEM XML may be provided at a later
> date. My envisoned restructuring of the document ends at this
> point. Everything that follows is old or leftover material; might
> need to be inserted somewhere above.
> &mdash;@iamdrscott


### Expressing types

> How do we put type info in the LD? &mdash;@webb

The @type keyword is used to associate a type with a node. The concept of a node type and a value type are different. A _node type_ specifies the type of thing that is being described, like a Person, Location, or Event. A _value type_ specifies the data type of a particular value, such as an integer, a floating point number, or a date. Using @type keywords is optional. In general, the NIEM IEPD uses XML Schema define node types or to constrain value types so it would not be necessary for the JSON-LD to repeat all of that.

### External XML content

> We should list options here, including:
>
>   1. You know your XML, come up with a good JSON-LD mapping for it.
>   2. use rdf:XMLLiteral to carry over the XML as a blob
>   3. Use some other JSON syntax for that content, like GeoJSON {How would an alien JSON vocabulary be used within a JSON-LD instance? Maybe this breaks JSON-LD.}

### Guidelines for reading LD 

> Clarify that you probably want to code against the *expanded* LD form of data,
> not compacted, so that code will consistently get at everything whether or not
> it's been encoded the way you expected. &mdash;@webb

### Linking contexts via HTTP headers

References [JSON-LD Specification Section 6.8, &ldquo;Interpresting JSON as JSON-LD&rdquo;](http://www.w3.org/TR/json-ld/#interpreting-json-as-json-ld).

## Roadmap for future work

### NIEM Conformance

Describe NTAC's planned path for NIEM-conformant JSON.

### Support for transforming between XML and JSON

Describe approaches or tools for transforming.

<div id="body-end"></div>

## References

* <a name="ndr53">[NIEM Naming and Design Rules, Section 5](https://reference.niem.gov/niem/specification/naming-and-design-rules/3.0/niem-ndr-3.0.html#section_5.3)</a>
* <a name="bibiso111794">ISO 11179-4: ISO/IEC 11179-4 Information Technology — Metadata Registries (MDR) — Part 4: Formulation of Data Definitions Second Edition, 15 July 2004. Available from [http://standards.iso.org/ittf/PubliclyAvailableStandards/c035346_ISO_IEC_11179-4_2004(E).zip](http://standards.iso.org/ittf/PubliclyAvailableStandards/c035346_ISO_IEC_11179-4_2004(E).zip)</a>
* <a name="bibiso111795">ISO 11179-5: ISO/IEC 11179-5:2005, Information technology — Metadata registries (MDR) — Part 5: Naming and identification principles. Available from [http://standards.iso.org/ittf/PubliclyAvailableStandards/c035347_ISO_IEC_11179-5_2005(E).zip](http://standards.iso.org/ittf/PubliclyAvailableStandards/c035347_ISO_IEC_11179-5_2005(E).zip)</a>
* <a name="bibjsonld">JSON-LD: Manu Sporny, Gregg Kellogg, Markus Lanthaler, Editors. 16 January 2014. W3C Recommendation. &ldquo;[A JSON-based Serialization for Linked Data]( https://www.w3.org/TR/json-ld/).&rdquo; Available from [https://www.w3.org/TR/json-ld/](https://www.w3.org/TR/json-ld/)</a>
* <a name="bibrfc4627">RFC4627: D. Crockford. &ldquo;[The application/json Media Type for JavaScript Object Notation (JSON) (RFC 4627)](http://www.ietf.org/rfc/rfc4627.txt).&rdquo; July 2006. RFC. Available from  [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)</a>
* <a name="bibrdfconcepts">[RDF-Concepts](https://www.w3.org/TR/2014/PR-rdf11-concepts-20140109/)</a> 
Richard Cyganiak, David Wood, Markus Lanthaler, Editors. 09 January 2014. W3C Proposed Recommendation.
RDF 1.1 Concepts and Abstract Syntax. Available from 
[http://www.w3.org/TR/rdf11-concepts/](http://www.w3.org/TR/rdf11-concepts/)
* <a name="bibschema">[Schema.Org](http://schema.org/)</a>
* <a name="BlankNodeIDs">[Blank Node Identifiers](https://www.w3.org/TR/json-ld/#dfn-blank-node-identifier)</a>

## Full example: XML instance document {#full-example-xml}

The following XML document is the XML form for the full example from [Section 2, above](#xml-to-json).

```xml
{% include_relative full-example.xml %}
```

## Full example: JSON data {#full-example-json}

The following JSON data is a compact JSON-LD form of the full example from [Section 2, above](#xml-to-json).

```javascript
{% include_relative full-example.jsonld %}
```


## Selected NIEM Elements from Examples

* <a name="Person">[NIEM nc:Person object in the SSGT](https://niem.gtri.gatech.edu/niemtools/ssgt/SSGT-GetProperty.iepd?propertyKey=nx-115)</a>
* <a name="PersonEyeColorCode">[NIEM j:PersonEyeColorCode object in the SSGT](https://niem.gtri.gatech.edu/niemtools/ssgt/SSGT-GetProperty.iepd?propertyKey=nx-13a)</a>
* <a name="PersonGivenName">[NIEM nc:PersonGivenName object in the SSGT](https://niem.gtri.gatech.edu/niemtools/ssgt/SSGT-GetProperty.iepd?propertyKey=nx-13s)</a>
* <a name="PersonMiddleName">[NIEM nc:PersonMiddleName object in the SSGT](https://niem.gtri.gatech.edu/niemtools/ssgt/SSGT-GetProperty.iepd?propertyKey=nx-152)</a>
* <a name="PersonName">[NIEM nc:PersonName object in the SSGT](https://niem.gtri.gatech.edu/niemtools/ssgt/SSGT-GetProperty.iepd?propertyKey=nx-158)</a>
* <a name="personNameCommentText">[NIEM nc:personNameCommentText object in the SSGT](https://niem.gtri.gatech.edu/niemtools/ssgt/SSGT-GetProperty.iepd?propertyKey=nx-2wz)</a>
* <a name="personNameInitialIndicator">[NIEM
  nc:personNameInitialIndicator object in the
  SSGT](https://niem.gtri.gatech.edu/niemtools/ssgt/SSGT-GetProperty.iepd?propertyKey=nx-22i)</a>

## Old material; may still need a new home

> _I cut this out of the introduction because it no longer fits the
> document flow. Some of these points appear in the new introduction,
> some do not.
> &mdash;@iamdrscott

Using JSON-LD as a vocabulary for JSON exchanges supports the mapping of NIEM
XML/XSD to [RDF-Concepts](#bibrdfconcepts) because JSON-LD is capable of serializing any RDF
graph or dataset. The goal is to maintain the structure
and component names of NIEM exchanges. This takes advantage of the semantic precision
possible with NIEM and increases the likelihood that the exchange will be understood
in the same way between exchange partners. NIEM describes information exchanges formatted as
Information Exchange Package Documentation [IEPD](https://reference.niem.gov/niem/specification/model-package-description/3.0/model-package-description-3.0.html#section_3.2.2).  
An Information Exchange Package (IEP) is an information message payload serialized as XML or JSON-LD and transmitted in some way, for example over a communications network.

NIEM uses XML _qualified names_, which consist of a _namespace name_ and a _local part_. 
JSON-LD uses a _context_, which maps an IRI to a _local term_ within the document.
Using a similar construct for the JSON-LD makes the JSON more easily readable.

> This intro should describe the overarching approach: 
> 
>   * Use JSON-LD as a JSON vocabularly
>   * Support NIEM's XML/XSD-to-RDF mapping
>   * Maintain structure and component names of NIEM exchanges
>   * Map XSD QNames to URIs & use JSON-LD to shorten URIs back to QNames (*sigh*)
> 
> &mdash;@webb

### Audience

The intended audience includes:

* JSON developers who want to leverage NIEM as a data model to take advantage of
  the precise meaning of NIEM data elements.
* NIEM developers who want to serialize NIEM XML into JSON for display,
  interchange, or storage purposes.

Please provide feedback on this document by entering an issue at [https://github.com/NIEM/NIEM.github.io/issues](https://github.com/NIEM/NIEM.github.io/issues) or sending email to [niem-comments@lists.gatech.edu](mailto:niem-comments@lists.gatech.edu).

### Resource Description Framework (RDF)

NIEM is based on RDF. To ensure good mapping between JSON-LD and NIEM, some RDF
concepts are more visible in the proposed JSON-LD than might be expected.

RDF has the concept of two types of objects, a node object (an RDF resource with
an IRI, or a blank node), and a value object (an RDF literal). Under the
[NIEM Naming and Design Rules (NDR) Section 5]({{href_ndr}}#section_5.3), which deals with RDF,
NIEM XML is mapped like this:

* the value of an element is a node object
* the value of an attribute is a value object
* the simple content of an element is a value object

For consistency in both development and interpretation, this document recommends
fully expanded JSON-LD, as per the examples provided.

### Non-Normative

This document provides guidance. It is not a specification of normative
rules. Community feedback to this document may result in a full specification at
a later date. Adherence to the guidance in this document does not confer "NIEM
conformance" on the resulting work.

### Contract-First Development

This document focuses on contract-first development, where the data to be
exchanged is agreed upon before development. This document is designed to help
JSON developers create JSON-LD that maps directly to NIEM XML. Future guidance may
later be provided regarding mapping more arbitrary forms of JSON to NIEM XML
objects.

### No Attempt to Replicate Schema

This document focuses on creating JSON instance documents, not in replicating
NIEM entirely in JSON-LD. JSON-LD's *context* object can be used to enforce data
types and object structure, if desired, but this document does not attempt to
replicate NIEM objects in this manner.

### Basic Structure

> NOTE: I think this needs to describe the overall approach to existing NIEM
> users, instead of explaining NIEM to JSON people. There's no reason for us to
> be reiterating ISO 11179 and use of namespaces at this stage. &mdash;@webb

> Start with a simple example of XML-to-JSON-LD &mdash;@webb

### JSON-LD: JSON for linking data

This approach to NIEM uses JSON-LD, described at [JSON-LD.org](http://json-ld.org):

> JSON-LD is a lightweight Linked Data format. It is easy for humans to read and
> write. It is based on the already successful JSON format and provides a way to
> help JSON data interoperate at Web-scale. JSON-LD is an ideal data format for
> programming environments, REST Web services, and unstructured databases such
> as CouchDB and MongoDB.

JSON-LD has features that make it useful for exchanging NIEM data:

* It maintains the RDF data model, which is the basis for NIEM schemas and NIEM
  data.
* It provides an intuitive way to handling namespace prefixes.
* It provides a consistent mapping for NIEM data that will scale across a large
  number of information exchanges.

> Others? &mdash;@webb

> It would be good to say something more about the importance and benefits of Linked Data,
> but is this the right place? 
>
> * browsing and discovery approach to finding information
> * distributed SPARQL queries
> * Linked Open Data. There are more and more Open Data initiatives, which allow data to be
>   cross-referenced on the web.
> * Ability to reinterpret JSON without @context as JSON-LD using some context without modifying
>   the JSON instance. [as seen here](http://www.w3.org/TR/json-ld/#interpreting-json-as-json-ld)
>
> &mdash;@leilatite

### Mapping XML qualified names to JSON-LD IDs

JSON-LD object IDs and properties IRIs, much like URIs. NIEM schemas use XML
namespaces; this results in the names of NIEM elements, attributes, and types
being qualified names. A qualified name consists of:

1. a **namespace name**: a URI that defines a namespace; a *namespace* is defined by [XML Namespaces](http://www.w3.org/TR/1999/REC-xml-names-19990114/#NT-QName) as:

   > <q>An XML namespace is a collection of names, identified by a URI reference [RFC2396], 
   > which are used in XML documents as element types and attribute names.</q>
   
1. a **local part**: a simple string that provides the local part of the qualified name.

[The NIEM NDR]({{page.ndr-href}}#section_5.6.1) maps qualified names
to URIs as:

* If namespace name ends with #: concatenate(namespace name, local part)
* Otherwise: concatenate(namespace name, #, local part)

This provides a way to use the names of NIEM elements and attributes as JSON-LD
properties. It also provides IDs for NIEM types. 

### Naming Conventions

Adhere to existing NIEM names for elements and attributes. Follow
[ISO-11179-4](#bibiso111794) and [ISO-11179-5](#bibiso111795) when creating new
names. The benefits of the naming convention in terms of consistency and
contextual meaning outweigh dealing with slightly longer names than may be
desired.

In particular, do not substitute NIEM names with names from
[schema.org](#bibschema). For example, do not substitute schema:familyName for
nc:PersonSurName.

### Code Tables

Currently, we know of no mechanism for enforcing enumerated values in
JSON-LD. Code tables should be represented by strings, with the understanding
that the validity of individual codes in instances will not be validated in any
way when represented as JSON-LD.

> Does
> [Metadata Vocabulary for Tabular Data](http://w3c.github.io/csvw/metadata/#json-ld-context)
> take care of this? See also
> [JSON-LD Dialect](http://w3c.github.io/csvw/metadata/#json-ld-dialect). &mdash@TommySezSoWhat

#### RDF {#thisisanote}

The [NIEM conceptual model]({{page.ndr-href}}#section_5) is based on
the RDF data model described in [RDF-Concepts](#bibrdfconcepts). NIEM
defines a mapping from the XML components in IEPs and IEPD schemas to
equivalent RDF triples. As of this writing, there is no automated
translator for that mapping.

Another reason for choosing JSON-LD is that it is a concrete RDF
syntax as described in [RDF-Concepts](#bibrdfconcepts). There is a
mapping between JSON-LD objects and equivalent RDF tuples. This
mapping is implemented in open-source translators. This means that the
JSON-LD serialization of an IEP can be automatically translated to
Turtle or RDF/XML and processed in that form.

This establishes two paths from a NIEM IEP to RDF

* Direct, using the mapping in the [NDR]({{page.ndr-href}}#section_5.6)
* Indirect, first following the guidance in this document to produce a
  JSON-LD serialization, then converting that JSON-LD to RDF

The NTAC intent is that the RDF created by both paths will be
consistent. This may entail future revisions to the
[NDR]({{page.ndr-href}}#section_5.6).

## NIEM Conformance

> Haven't written this yet &mdash;@iamdrscott

