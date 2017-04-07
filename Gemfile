source 'https://rubygems.org'

gem 'github-pages', group: :jekyll_plugins

group :test do
  # Used to test the sit as part of CI. Run
  # bundle exec htmlproofer ./_site --only-4xx --check-html
  # and htmlproofer will check the validity of all the HTML and that the links
  # all work (specifically that they don't return a 5xx error. 4xx is fine as
  # its assumed the called service is temporarily down)
  gem "html-proofer"
end
