# frozen_string_literal: true

source 'https://rubygems.org'

ruby '3.4.1'

gem 'jekyll'
gem 'jekyll-theme-so-simple', path: '../so-simple-theme'

# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
# gem "github-pages", group: :jekyll_plugins
# If you have any plugins, put them here!
group :jekyll_plugins do
  gem 'jekyll-feed'
  gem 'jekyll-gist'
  gem 'jekyll-image-size'
  gem 'jekyll-paginate'
  gem 'jekyll-redirect-from'
  gem 'jekyll-sitemap'
  gem 'jemoji'
end

# No longer core on ruby 3.0.0
gem 'webrick'

# For link checking
gem 'html-proofer'

gem 'csv'

# # Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# # and associated library.
# platforms :mingw, :x64_mingw, :mswin, :jruby do
#   gem "tzinfo", ">= 1", "< 3"
#   gem "tzinfo-data"
# end

# Performance-booster for watching directories on Windows
# gem "wdm", "~> 0.1.1", :platforms => [:mingw, :x64_mingw, :mswin]

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds since newer versions of the gem
# do not have a Java counterpart.
# gem "http_parser.rb", "~> 0.6.0", :platforms => [:jruby]
