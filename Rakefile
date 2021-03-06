namespace :docs do
  desc 'prepare build'
  task :prebuild do
    Dir.mkdir './images' unless Dir.exists? 'images'
    Dir.glob("./docs/*/images/*").each do |image|
      FileUtils.copy(image, "images/" + File.basename(image))
    end
    Dir.mkdir './files' unless Dir.exists? './files'
    Dir.glob("./docs/*/files/*").each do |file|
      FileUtils.copy(file, "./files/" + File.basename(file))
    end

    Dir.glob("./files/*.asc").each do |file|
      `asciidoctor #{file}`
    end
  end

  desc'build the documentation, specify basic or advanced'
  task :build, [:arg1] => :prebuild do |t, args|
    args.with_defaults(:arg1 => 'advanced')
    puts "Converting to HTML - #{args[:arg1]} docs"

   if args[:arg1] ==  'advanced'
      `asciidoctor certification-docbook.asc -a stylesheet=theme/style.css`
      puts " -- HTML output at certification-docbook.html"
    end

    if args[:arg1] == 'basic'
      `asciidoctor basic-certification-docbook.asc -a stylesheet=theme/style.css`
      puts " -- HTML output at basic-certification-docbook.html"
    end

    if args[:arg1] == 'contr'
      `asciidoctor contributing.asc -a stylesheet=theme/style.css`
      puts " -- HTML output at contributing.html"
    end

  end

  desc 'package the documents into the generated folder'
  task :package do
    Dir.mkdir './generated' unless Dir.exists? './generated'
    Dir.mkdir './generated/images' unless Dir.exists? './generated/images'
    Dir.glob("./images/*").each do |image|
      FileUtils.copy(image, "./generated/images/" + File.basename(image))
    end
    Dir.mkdir './generated/files' unless Dir.exists? './generated/files'
    Dir.glob("./files/*").each do |file|
      FileUtils.copy(file, "./generated/files/" + File.basename(file))
    end
    Dir.glob("./*.html").each do |file|
      FileUtils.copy(file, "./generated/index.html");
    end
  end

  desc 'unpackage the docs in the specified folder'
  task :unpackage, :to do |t, args|

    args.with_defaults(:to => '.')
    args[:to]

    Dir.mkdir args[:to] unless Dir.exists? args[:to]

    `rm -rf #{args[:to]}/images`
    `rm -rf #{args[:to]}/files`
    Dir.glob(args[:to]+"/*.html").each do |file|
      FileUtils.rm file
    end

    Dir.mkdir args[:to]+'/images' unless Dir.exists? args[:to]+'/images'
    Dir.glob("generated/images/*").each do |image|
      FileUtils.copy(image, "#{args[:to]}/images/" + File.basename(image))
    end
    Dir.mkdir args[:to]+'/files' unless Dir.exists? args[:to]+'/files'
    Dir.glob("generated/files/*").each do |file|
      FileUtils.copy(file, "#{args[:to]}/files/" + File.basename(file))
    end

    Dir.glob("generated/*.html").each do |file|
      FileUtils.copy(file, args[:to]+'/'+File.basename(file))
    end

  end

  desc 'push generated documents to the repository'
  task :upload, [:type] => [:package] do |t, args|
    args.with_defaults(:type => 'advanced')

    `git checkout gh-pages -f`
    if args[:type] ==  'advanced'
      puts "Uploading generated advanced documentation"
      Rake::Task["docs:unpackage"].invoke('advanced')
    end
    if args[:type] ==  'basic'
      puts "Uploading generated BASIC documentation"
      Rake::Task["docs:unpackage"].invoke('basic')
    end
    if args[:type] ==  'contr'
      puts "Uploading contributing file"
      Rake::Task["docs:unpackage"].invoke('contr')
    end
    `git add #{args[:type]}/ && git commit -m "Update documentation" `
    `git push origin gh-pages -f`
    `git checkout develop -f`
  end

  desc 'clean out generated formats'
  task :clean do
    `rm -rf generated`
    `rm -rf images`
    `rm -rf files`
    Dir.glob("./*.html").each do |file|
      FileUtils.rm file
    end
  end

end
