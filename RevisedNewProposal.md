# Revised Proposal

The TEI element [`<cRefPattern>`](http://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-cRefPattern.html) defines a mechanism for resolving "canonical" references into machine-actionable URIs. While it permits automated resolution of these references, it does not provide for the automated *construction* of references. So while TEI documents can declare how citations should be treated, they can't actually declare how those citations should be made. This means systems that might want to do things with a document's citation structure, like automatically chunk a large document into smaller pieces or generate an automatic table of contents are at a disadvantage.

We are proposing two new elements to allow documents to declare their citation structure and to specify how structural infomration may be extracted from them:

`<citeStructure>` permits an application to address and extract parts of the TEI document declaring its structure,
and can provide hints on how to construct canonical references. Instead of telling an application how to
resolve  references, as `<cRefPattern>` does, this tells the application how to *build* them using
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
    <refsDecl>
        <!-- Example 1: Units of the introductory front matter may be cited, e.g. "Intro. 1" -->
        <citeStructure unit="front"
            match="//front/div[@type='introduction']/div[@type='section']" 
            use="@n" delim="Intro. "> 
            <!-- The thing(s) matched by ../@match have titles at the location ./head. 
                 Assume the prefix "dc" has been declared using a <prefixDef> elsewhere.
                 Multiple <head>s in the matched <div> would mean multiple dc:titles being
                 extracted for that div.-->
            <citeData property="dc:title" use="head" /> 
        </citeStructure>
        <!-- Example 2: Poems may be cited as, e.g. "1", lines as, e.g. "1.5" -->
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
The following example is the equivalent of the one given for `<cRefPattern>` in https://www.tei-c.org/release/doc/tei-p5-doc/en/html/SA.html#SACR and could be used (with a different algorithm) both to resolve citations of the type "Matt 5:7" and/or to generate a set of resolvable citations at any level.
```xml
<encodingDesc>
  <refsDecl>
    <citeStructure unit="book" match="//body/div" use="@n">
      <citeStructure unit="chapter" match="div" use="position()" delim=" ">
        <citeStructure unit="verse" match="div" use="position()" delim=":"/>
      </citeStructure>
    </citeStructure>
  </refsDecl>
</encodingDesc>
```
