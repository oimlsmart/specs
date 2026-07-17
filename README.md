# OIML Encoding Guidelines for Publications in NISO STS

OIML X 999:2026 — the OIML profile of NISO STS, defining how OIML
publications (R/B/D/G/E) are encoded in NISO STS.

## Build

```sh
bundle install
bundle exec metanorma sources/document.adoc
```

Outputs: `sources/document.xml`, `sources/document.html`, `sources/document.pdf`.

## Generating STS from Metanorma Presentation XML

The `metanorma-oiml` gem provides a converter that transforms OIML
Metanorma presentation XML into OIML NISO STS XML.

### API

```ruby
require "metanorma/oiml/sts"

# Convert presentation XML string to STS XML
sts_xml = Metanorma::Oiml::Sts.convert(presentation_xml_string)

# Render STS XML to HTML
sts_html = Metanorma::Oiml::Sts::HtmlRenderer.render(sts_xml, renderer: :xslt)
```

### CLI

```sh
# From metanorma-oiml gem
oiml-to-sts input.presentation.xml output.sts.xml
```

### STS HTML Rendering

The XSLT renderer (`vendor/xslt/oiml-sts2html.xsl`) converts STS XML to
semantic HTML with OIML-branded styling. The `oiml-logo.svg` must be in
the same directory as the output HTML for the logo to display.
