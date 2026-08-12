source "https://rubygems.org"

group :jekyll_plugins do
  gem "github-pages"
end

# Security minimums for Dependabot alerts in Gemfile.lock (transitive deps)
gem "nokogiri", ">= 1.19.4"
gem "addressable", ">= 2.9.0"
gem "faraday", ">= 2.14.3"
gem "rexml", ">= 3.4.2"
gem "json", ">= 2.19.9"

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]