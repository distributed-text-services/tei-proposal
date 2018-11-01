# Revised Proposal
(Changes from <NewProposal.md>: different element and attribute names; `@delim` on child `<structurePath>`s.

`<structurePart>` takes a different angle on the definition of "canonical" references than `<cRefPattern>`.
It permits an application to address and extract parts of the TEI document declaring its structure,
and can provide hints on how to construct canonical references. <structureData> allows TEI documents
to declare how metadata can be extracted from those parts. Instead of telling an application how to
resolve canonical references, as <cRefPattern> does, this tells the application how to build them using
the TEI document as a data source.
        
`<structurePart>` has three required attributes, `@unit`, a label specifying what kind of unit this level
of structure refers to, e.g., "book", "line", or "section"; `@match`, an XPath that tells you how to
find each unit in of the given type in the document; `@use`, an XPath, relative to the element(s) matched
by `@match` that gives a mechanism for uniquely identifying the element(s) referenced by `@match`. This could
be an `@xml:id`, or a labeling attribute like `@n`, or even a positional reference like `position()`. It should
be possible to construct an XPath that will retrieve an element referenced by <structurePart> by constructing
a predicate on the XPath in `@match` using the value for a given element of the XPath in `@use`.
             
`<structurePath>` also has an optional attribute, `@delim`, which can be used as a hint for the construction of 
canonical references, e.g. "1.2" for the poem `<div n="1">` and the `<l n="2">`. In those references,
the value of `@delim` should be assumed to come before the referenced unit. So, in example 2 (again, assuming a 
reference style like "1.2"), the line `<structurePath>` is the one that has a `@delim`.
             
`<structurePath>`s may nest, and a relative XPath in `@match` is assumed to start from the element(s) matched
by the parent `<structurePath>`. See example 2 below. 
             
`<structureData>` provides a way for a document to declare how certain types of data can be extracted from the
structures mapped out by `<structurePath>`. In examples 1 and 2, a Dublin Core Title can be obtained for each
front section or poem by getting the `<head>` element from each.
             
`<structureData>` has two required attributes, `@property` (analogous to `@property` in RDFa), a URI, and `@use`,
a relative XPath exactly like `@use` on `<structurePath>`. 

```xml
<encodingDesc>
    <refsDecl type="DTS">
        <!-- Example 1 -->
        <structurePart unit="front"
            match="//front/div[@type='introduction']/div[@type='section']" use="@n"> 
            <!-- The thing(s) matched by ../@match have titles at the location ./head. 
                 Assume the prefix "dc" has been declared using a <prefixDef> elsewhere.
                 Multiple <head>s in the matched <div> would mean multiple dc:titles being
                 extracted for that div.-->
            <structureData property="dc:title" use="head" /> 
        </structurePart>
        <!-- Example 2 -->
        <structurePart unit="poem"
            match="//body/div[@type='edition']/div[@type='textpart']" use="@n">
            <structureData property="dc:title" use="head" />
            <structurePart unit="line" 
                match=".//l[parent::div or parent::lem]" use="@n" delim=".">
            </structurePart>
        </structurePart>
    </refsDecl>
</encodingDesc>
```
