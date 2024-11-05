# frozen_string_literal: true

require 'english'
require 'json'
require 'date'

task default: %w[server]

def run_command label:, command:
  puts label
  puts `#{command}`
  return if $CHILD_STATUS.success?

  puts "Failure during #{label.inspect} status=#{$CHILD_STATUS.inspect}"
  exit 1
end

def update_prod_configuration
  json = JSON.parse(`curl https://api.github.com/repos/mikebowler/jirametrics/releases`)
  release = json.find { |r| !r['draft'] && !r['prerelease'] }
  name = release['name']
  created = Date.parse(release['created_at']).to_s
  File.open '_config_production.yml', 'w' do |file|
    file.puts "description: \"Current: #{name}, Released: #{created}\""
  end
end

task :deploy do
  update_prod_configuration
  run_command(
    label: 'Building the site',
    command: 'JEKYLL_ENV=production jekyll build --config _config.yml,_config_production.yml'
  )
  run_command(
    label: 'Deploying to S3',
    command: 'aws s3 sync _site s3://jirametrics.org'
  )
  run_command(
    label: 'Invalidating cloudfront cache',
    command: 'aws cloudfront create-invalidation --distribution-id ECIZKMUQI7KP7 --paths "/*"'
  )
end

task :server do
  update_prod_configuration unless File.exist? '_config_production.yml'
  run_command(
    label: 'Run local server on port 4000',
    command: 'jekyll serve --livereload --drafts --config _config.yml,_config_production.yml'
  )
end
