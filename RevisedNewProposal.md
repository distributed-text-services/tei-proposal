# Revised Proposal
(Changes from [NewProposal.md](NewProposal.md): different element and attribute names; `@delim` on child `<structurePath>`s.

`<citeStructure>` takes a different angle on the definition of "canonical" references than [`<cRefPattern>`](http://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-cRefPattern.html).
It permits an application to address and extract parts of the TEI document declaring its structure,
and can provide hints on how to construct canonical references. <citeData> allows TEI documents
to declare how metadata can be extracted from those parts. Instead of telling an application how to
resolve canonical references, as <cRefPattern> does, this tells the application how to build them using
the TEI document as a data source.
        
`<citeStructure>` has three required attributes, `@unit`, a label specifying what kind of unit this level
of structure refers to, e.g., "book", "line", or "section"; `@match`, an XPath that tells you how to
find each unit in of the given type in the document; `@use`, an XPath, relative to the element(s) matched
by `@match` that gives a mechanism for uniquely identifying the element(s) referenced by `@match`. This could
be an `@xml:id`, or a labeling attribute like `@n`, or even a positional reference like `position()`. It should
be possible to construct an XPath that will retrieve an element referenced by <citeStructure> by constructing
a predicate on the XPath in `@match` using the value for a given element of the XPath in `@use`.
             
`<citeStructure>` also has an optional attribute, `@delim`, which can be used as a hint for the construction of 
canonical references, e.g. "1.2" for the poem `<div n="1">` and the `<l n="2">`. In those references,
the value of `@delim` should be assumed to come before the referenced unit. So, in example 2 (again, assuming a 
reference style like "1.2"), the line `<citeStructure>` is the one that has a `@delim`.
             
`<citeStructure>`s may nest, and a relative XPath in `@match` is assumed to start from the element(s) matched
by the parent `<citeStructure>`. See example 2 below. 
             
`<citeData>` provides a way for a document to declare how certain types of data can be extracted from the
structures mapped out by `<citeStructure>`. In examples 1 and 2, a Dublin Core Title can be obtained for each
front section or poem by getting the `<head>` element from each.
             
`<citeData>` has two required attributes, `@property` (analogous to `@property` in RDFa), a URI, and `@use`,
a relative XPath exactly like `@use` on `<citeStructure>`. 

```xml
<encodingDesc>
    <refsDecl type="DTS">
        <!-- Example 1 -->
        <citeStructure unit="front"
            match="//front/div[@type='introduction']/div[@type='section']" use="@n"> 
            <!-- The thing(s) matched by ../@match have titles at the location ./head. 
                 Assume the prefix "dc" has been declared using a <prefixDef> elsewhere.
                 Multiple <head>s in the matched <div> would mean multiple dc:titles being
                 extracted for that div.-->
            <citeData property="dc:title" use="head" /> 
        </citeStructure>
        <!-- Example 2 -->
        <citeStructure unit="poem"
            match="//body/div[@type='edition']/div[@type='textpart']" use="@n">
            <citeData property="dc:title" use="head" />
            <citeStructure unit="line" 
                match=".//l[parent::div or parent::lem]" use="@n" delim=".">
            </citeStructure>
        </citeStructure>
    </refsDecl>
</encodingDesc>
```
