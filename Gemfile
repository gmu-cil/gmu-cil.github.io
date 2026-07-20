source "https://rubygems.org"
# The site is published by GitHub Pages, which builds with its own pinned
# toolchain (the `github-pages` gem — Jekyll 3.x plus the Pages plugin set).
# We depend on that same gem so `bundle exec jekyll serve` locally matches what
# actually ships; a plain `jekyll` gem would diverge (different Jekyll version,
# and without plugins like jekyll-optional-front-matter that change behavior).
# To upgrade the whole toolchain, run `bundle update github-pages`.
gem "github-pages", group: :jekyll_plugins
# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5"
gem "webrick"
# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.12"
end

# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", :install_if => Gem.win_platform?

