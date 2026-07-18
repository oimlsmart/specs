source "https://rubygems.org"

gem "metanorma-cli"
gem "metanorma-oiml", github: "metanorma/metanorma-oiml"
gem "mn2pdf"

# NOTE: metanorma-document (site HTML renderer) is intentionally NOT in this
# bundle. The pubid 2.0 / relaton 2.2.0.pre line it requires cannot coexist
# with the released metanorma-cli stack: metanorma-standoc pins
# relaton-cli ~> 2.1.0 and RubyGems never satisfies a non-prerelease
# requirement with a prerelease version. CI installs the renderer toolchain
# (metanorma-document + pubid/relaton prereleases) as standalone gems; see
# .github/workflows/generate.yml and README.

# metanorma-plugin-lutaml requires lutaml/xmi, which lutaml >= 0.11 no longer
# ships (moved to the lutaml-xmi gem); keep lutaml on the 0.10.x line
gem "lutaml", "~> 0.10.12"
