#source 'https://rubygems.org'
source 'https://ruby.taobao.org'

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']
gem 'rake'
