source "https://rubygems.org"

gem "metanorma-cli"
gem "metanorma-oiml", github: "metanorma/metanorma-oiml"
gem "mn2pdf"

# NOTE: metanorma-document (site HTML renderer) is intentionally NOT in this
# bundle — its pubid ~> 2.0.0.pre.alpha dependency conflicts with the pubid
# 1.x line the metanorma-cli/relaton stack requires. CI installs it as a
# standalone gem from the metanorma/metanorma-document repo (see
# .github/workflows/generate.yml); >= 0.2.11 is required for body rendering.

# metanorma-plugin-lutaml requires lutaml/xmi, which lutaml >= 0.11 no longer
# ships (moved to the lutaml-xmi gem); keep lutaml on the 0.10.x line
gem "lutaml", "~> 0.10.12"
