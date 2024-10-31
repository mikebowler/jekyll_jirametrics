task default: %w[server]

def run_command label:, command:
  puts label
  puts `#{command}`
  unless $?.success?
    puts "Failure during #{label.inspect} status=#{$?.inspect}" 
    exit 1
  end

end

task :deploy do
  run_command(
    label: 'Building the site',
    command: 'JEKYLL_ENV=production jekyll build'
  )
  run_command(
    label: 'Deploying to S3',
    command: 'aws s3 sync _site s3://okanaganagile.com'
  )
  run_command(
    label: 'Invalidating cloudfront cache',
    command: 'aws cloudfront create-invalidation --distribution-id EEQB5GCKN1V2P --paths "/*"'
  )
end

task :server do
  run_command(
    label: 'Run local server on port 4000',
    command: 'jekyll serve --livereload --drafts'
  )
end
