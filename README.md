# tei-proposal
Test repository for a new proposal for TEI

## Requirements:
1. Able to say what unit it's refering to
2. Flexible way of treating citations patterns (regex probably)
3. Hierarchical and able to deal with mixed kind of contents
4. Easy enough to parse so that you can extract a table of contents or list of passage references from your document
5. Able to provide a passage name as well as an identifier


## Proposal 1: Combine refState and cRefPattern
(example constructed for https://github.com/sjhuskey/Calpurnius_Siculus/blob/master/editio.xml)
```XML
<encodingDesc>
  <refsDecl>
    <refState unit="front" match="//front/div[@type='introduction']/div[@type='section']" use="head">
      <cRefPattern matchPattern="Introduction (\w+)" 
                   replacementPattern="#xpath(//front/div[@type='introduction']/div[@type='section'][@n='$1'])"/>
    </refState>
    <refState unit="poem" match="//body/div[@type='edition']/div[@type='textpart']" use="@n" altName="head" delim=".">
      <cRefPattern matchPattern="(\d)" 
                   replacementPattern="#xpath(//body/div[@type='edition']/div[@type='textpart'][@n='$1']"/>
      <refState unit="line" match=".//l[parent::div or parent::lem]" use="@n">
        <cRefPattern matchPattern="(\d)\.(\d)" 
                     replacementPattern="#xpath(//body/div[@type='edition']/div[@type='textpart'][@n='$1]//l[parent::div or parent::lem][@n='$2'])"/>
      </refState>
    </refState>
  </refsDecl>
</encodingDesc>
```
### Notes:
* Satisfies #1 with `@unit` on refState
* Satisfies #2 with a cRefPattern child of the refState. Consider using a different attribute than `@replacementPattern` or otherwise changing its datatype to be an XPath instead of a URI.
* Satisfies #3 with nested refStates. RefStates at the same level should be considered alternates.
* Satisfies #4 with `@match` and `@use`. Nested `@match`es use relative XPaths. 
* Satisfies #5 with `@altName` on refState, which provides an XPath relative to `@match` which references a node containing a name for the passage.
* TODO: algorithm for generating passage IDs at any level using the example.
