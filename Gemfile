source "https://rubygems.org"

gem "metanorma-cli"
gem "metanorma-oiml", github: "metanorma/metanorma-oiml"
gem "mn2pdf"

# NOTE: metanorma-document (site HTML renderer) lives in Gemfile.renderer,
# not here. The pubid 2.0 / relaton prerelease line it requires cannot
# coexist with the released metanorma-cli stack (metanorma-standoc pins
# relaton-cli ~> 2.1.0, and RubyGems never satisfies a non-prerelease
# requirement with a prerelease version). Use the second Gemfile via
#   BUNDLE_GEMFILE=Gemfile.renderer bundle install
# (see README and .github/workflows/generate.yml).

# metanorma-plugin-lutaml requires lutaml/xmi, which lutaml >= 0.11 no longer
# ships (moved to the lutaml-xmi gem); keep lutaml on the 0.10.x line
gem "lutaml", "~> 0.10.12"
