DockBook Install
================

This GitHub provides instructions to setup DocBook on Ubuntu 16.04, 18.04 and 20.04 with Apache FOP 2.8.

The instructions include the steps to install packages such as `docbook`, `docbook-xsl`, `docbook-xsl-doc-pdf` and `xsltproc`. The instructions also cover installation of the Java Runtime Environment (JRE) or Development Kit (JDK), if needed.

The instructions are in `docbook-install.pdf`. Once DocBook is setup just run `make-book.sh` to create the book.

make-book.sh
============

`make-book.sh` builds the book on both Ubuntu and Fedora. Ubuntu and Fedora use different paths to the DocBook stylesheets, and `make-book.sh` will detect the location of the stylesheets if it is not specified by the user:

```
# Find docbook.xsl if it is not specified
if [[ -z "${DOCBOOK_XSL}" ]]
then
    DOCBOOK_XSL="$(find /usr/share -name 'docbook.xsl' 2>/dev/null | grep -E '/fo/docbook.xsl$' | head -n 1)"
fi
```

After the block of code runes, `DOCBOOK_XSL` will contain a location like `/usr/share/xml/docbook/stylesheet/docbook-xsl/fo/docbook.xsl`.

Once the location of the stylesheets is determined, `make-book.sh` will create custom stylesheet, `custom.xsl`, to use when building the book:

```
# This magic allows us to build the DocBook on Ubuntu and Fedora.
# Ubuntu and Fedora use different paths to docbook.xsl.
sed "s|!!DOCBOOK_XSL_FILE!!|${DOCBOOK_XSL}|g" custom.xsl.in > custom.xsl
```

The important part of `custom.xsl.in` is shown below. The `custom.xsl` hack is needed because the XSLT translator does not allow a conditionally included file.

```
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:import href="!!DOCBOOK_XSL_FILE!!"/>
    ...
</xsl:stylesheet>
```
