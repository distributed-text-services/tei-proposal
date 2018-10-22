```xml
<encodingDesc>
  <refsDecl type="DTS"><!-- Need to add @type -->
    <tocElement unit="front"
          match="//front/div[@type='introduction']/div[@type='section']" use="head">
      	<metadataDecl type="title" match="./head" />
      	<metadataDecl type="author" match="./persName[@type='author']" />
    </tocElement>
    <tocElement unit="poem"
          match="//body/div[@type='edition']/div[@type='textpart']" use="@n" delim=".">
      <tocElement unit="line" 
          match=".//l[parent::div or parent::lem]" use="@xml:id">
      </tocElement>
    </tocElement>
  </refsDecl>
</encodingDesc>
```
