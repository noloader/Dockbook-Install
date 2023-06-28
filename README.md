DockBook Install
================

This GitHub provides instructions to setup DocBook on Ubuntu 16.04, 18.04 and 20.04 with Apache FOP 2.8.

The instructions include the steps to install packages such as `docbook`, `docbook-xsl`, `docbook-xsl-doc-pdf` and `xsltproc`. The instructions also cover installation of the Java Runtime Environment (JRE) or Development Kit (JDK), if needed.

The instructions are in `docbook-install.pdf`. Once DocBook is setup just run `make-book.sh` to create the book.

Customizations
==============

DocBook allows you to change the style of your book by using a custom stylesheet. For example, you can change the font family, font size, paragraph indents, left margin, or right margin. Since this book's `custom.xsl` is created on the fly, you should make your changes to `custom.xsl.in`. `custom.xsl.in` will be used to create the `custom.xsl` used when building the book.

The XSLT translator consumes `custom.xsl` like so:

```
xsltproc --xinclude custom.xsl book.xml > "${BOOKNAME}.fo"
```

This DocBook already has changes applied in `custom.xsl.in`. The font family and font size were changed to 11 point Arial to make the book easier on the eyes. Margins and outdents were also changed to use more of the available page.

make-book.sh
============

`make-book.sh` builds the book on both Ubuntu and Fedora. Ubuntu and Fedora use different paths to the DocBook stylesheets, so different `custom.xsl` are needed. `make-book.sh` will create a `custom.xsl` using the path to the DocBook stylesheets for the site.

`make-book.sh` will detect the location of the stylesheets if it is not specified by the user. After the code runs, `DOCBOOK_XSL` will contain a location like `/usr/share/xml/docbook/stylesheet/docbook-xsl/fo/docbook.xsl`.

```
DOCBOOK_XSL="$(find /usr/share -name 'docbook.xsl' 2>/dev/null | grep -E '/fo/docbook.xsl$' | head -n 1)"
```

`make-book.sh` will then create the custom stylesheet, substituting the location for `!!DOCBOOK_XSL!!` in the custom style sheet.

```
# This magic allows us to build the DocBook on Ubuntu and Fedora.
# Ubuntu and Fedora use different paths to docbook.xsl.
sed "s|!!DOCBOOK_XSL_FILE!!|${DOCBOOK_XSL}|g" custom.xsl.in > custom.xsl
```

And the corresponding part of `custom.xsl.in` is shown below.

```
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:import href="!!DOCBOOK_XSL_FILE!!"/>
    ...
</xsl:stylesheet>
```

The remainder of `make-book.sh` lints the sources, creates the formatted object, builds the book and optimizes the book.
