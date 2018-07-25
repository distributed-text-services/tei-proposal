# tei-proposal
Test repository for a new proposal for TEI

## Requirements:
1. Able to say what unit it's refering to
2. Flexible way of treating citations patterns (regex probably)
3. Hierarchical and able to deal with mixed kind of contents
4. Easy enough to parse so that you can extract a table of contents or list of passage references from your document
5. Able to provide a passage name as well as an identifier
6. Able to provide different refsDecl depending on the objective

## Proposal 1: Combine refState and cRefPattern
(example constructed for https://github.com/sjhuskey/Calpurnius_Siculus/blob/master/editio.xml)

```XML
<encodingDesc>
  <refsDecl type="canonical">

    <tocItem 
      unit="front"
      discover="//front/div[@type='introduction']/div[@type='section']"
      matchPattern="Introduction (\w+)"
      xPathPattern="//front/div[@type='introduction']/div[@type='section'][@n='$1']"
      >
      <metadataDecl type="dc:title" xPathPattern=".//head" />
      <metadataDecl type="dc:author" xPathPattern="./persName[@type='author']" />
    </tocItem>

    <tocItem 
      unit="poem"
      discover="//body/div[@type='edition']/div[@type='textpart']"
      matchPattern="(\d)"
      xPathPattern="//body/div[@type='edition']/div[@type='textpart'][@n='$1']"
      >

      <tocItem 
        unit="line"
        discover=".//l[parent::div or parent::lem]"
        matchPattern="(\d)\.(\d+)"
        xPathPattern="//body/div[@type='edition']/div[@type='textpart'][@n='$1]//l[parent::div or parent::lem][@n='$2']"
        >
      </tocItem>

    </tocItem>

  </refsDecl>
</encodingDesc>
```
### Notes:
* Satisfies #1 with `@unit` on refState
* Satisfies #2 with a `@matchPattern` inside `tocPattern`
* Satisfies #3 with nested `tocPattern`. RefStates at the same level should be considered alternates.
* Satisfies #4 with `@discover` and `@use`. Nested `@discover`es use relative XPaths.  
* Satisfies #5 with `metadataDecl` which provides an `@xPathPattern` and a `@type` for metadata plurality (epistolary exchange can have different authors, dates, etc.)
* Statisfies #6 with `@type` on `refsDecl`
* TODO: algorithm for generating passage IDs at any level using the example.
