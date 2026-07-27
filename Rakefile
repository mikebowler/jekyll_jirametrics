# frozen_string_literal: true

require 'english'
require 'json'
require 'date'
require 'yaml'

task default: %w[server]

SITE_URL = 'https://jirametrics.org'

# The documentation pages, in reading order, that get concatenated into llms-full.txt for LLM
# ingestion. Deliberately curated: setup and reference pages an agent needs to stand the tool up.
# Anything not listed here (drafts, the marketing home page, the long changelog) is left out.
LLMS_FULL_PAGES = %w[
  install.md connecting_to_jira.md quickstart.md usage.md
  config_top_level.md config_standard_project.md config_project.md config_cycletime.md
  config_charts.md config_file_html.md config_file_csv.md config_aggregated_project.md
  troubleshooting.md faq.md mcp.md forecasting.md quality_report.md security.md
].freeze

def parse_front_matter raw
  match = raw.match(/\A---\s*\n(?<front>.*?)\n---\s*\n(?<body>.*)\z/m)
  return [{}, raw] unless match

  [YAML.safe_load(match[:front]) || {}, match[:body]]
end

def permalink_map
  Dir.glob('*.md').each_with_object({}) do |file, map|
    front, = parse_front_matter File.read(file, encoding: 'UTF-8')
    map[file] = front['permalink'] if front['permalink']
  end
end

def markdown_for_llms body, links
  # Resolve {% link foo.md %} to absolute URLs so the concatenated text keeps working links.
  body = body.gsub(/\{%\s*link\s+(?<file>\S+)\s*%\}/) { "#{SITE_URL}#{links[Regexp.last_match(:file)]}" }
  # Drop the remaining Liquid tags (includes, image sizing) and kramdown attribute lists like
  # {: .tip } — they carry no text an LLM can use.
  body = body.gsub(/\{%.*?%\}/m, '')
  body.gsub(/^\{:.*\}\s*$/, '').strip
end

def generate_llms_full
  links = permalink_map
  out = +"# JiraMetrics — full documentation\n\n"
  out << "The JiraMetrics documentation pages, concatenated for LLM ingestion. " \
         "See #{SITE_URL}/llms.txt for a shorter index with per-page links.\n"
  LLMS_FULL_PAGES.each do |file|
    front, body = parse_front_matter File.read(file, encoding: 'UTF-8')
    out << "\n\n#{'=' * 80}\n"
    out << "# #{front['title'] || File.basename(file, '.md')}\n"
    out << "Source: #{SITE_URL}#{front['permalink']}\n" if front['permalink']
    out << "#{'=' * 80}\n\n#{markdown_for_llms(body, links)}\n"
  end
  File.write 'llms-full.txt', out
  puts "Generated llms-full.txt (#{LLMS_FULL_PAGES.length} pages, #{out.bytesize} bytes)"
end

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
  name = release['tag_name']
  created = Date.parse(release['created_at']).to_s
  File.open '_config_production.yml', 'w' do |file|
    file.puts "description: \"Current: #{name}, Released: #{created}\""
  end
end

task :llms do
  generate_llms_full
end

# llms-full.txt is generated on deploy only (not for the local `server` task) so local testing
# doesn't churn it.
task deploy: :llms do
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
  update_prod_configuration # unless File.exist? '_config_production.yml'
  run_command(
    label: 'Run local server on port 4000',
    command: 'jekyll serve --livereload --drafts --config _config.yml,_config_production.yml'
  )
end
