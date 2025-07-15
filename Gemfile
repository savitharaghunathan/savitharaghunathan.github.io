source "https://rubygems.org"

# Use github-pages gem for GitHub Pages deployment
if ENV['JEKYLL_ENV'] == 'production'
  group :jekyll_plugins do
    gem "github-pages"
  end
else
  # Use standard Jekyll gems for local development
  gem "jekyll", "~> 4.3.0"
  gem "jekyll-feed", "~> 0.17"
  gem "jekyll-seo-tag", "~> 2.8"
  gem "jekyll-sitemap", "~> 1.4"
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo"
  gem "tzinfo-data"
end

gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]
gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby] 