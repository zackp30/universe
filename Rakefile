task default: :pdf
book_regex = /(.*)-(\d*).*\.md/
book_regex2 = /(.*)-(\d*).*\.book/
pdfs = Dir.glob('**/*.md').map { |f| f.sub(/\.md$/, '.pdf') }
books = Dir.glob('**/*.md').map { |f| f if book_regex.match(f) }
books.compact!
pdfs -= books

pdfs = pdfs.sort
task pdf: pdfs

gpp_command = 'gpp -H -x -DTEX=1 --include gpp.gppb'

macros = Dir.glob('**/*.gpp').map { |f| f.sub(/\.gpp$/, '.gppb') }
task macro: macros

extensions = 'tex_math_single_backslash+footnotes+raw_tex+grid_tables+implicit_figures+fenced_code_blocks+definition_lists+pipe_tables'
filters = ['pandoc-citeproc', 'pandoc-plantuml-filter'].join ' --filter '

rule '.gppb' => '.gpp' do |t|
  # Remove all whitespace and newlines from macro files
  sh "tr -d '\n' < #{t.source} > #{t.name}"
  sh "sed -r 's/>\s+</></g' #{t.name} > #{t.name + '.tmp'}"
  sh "mv #{t.name + '.tmp'} #{t.name}"
end

sh 'mkdir -vp build/books' unless Dir.exist? 'build/books'

books.each { |f| cp f, "build/books/#{File.basename(f, '.md') + '.book'}" unless /^build\//.match f }
booksp = Dir.glob('build/**/*.book').map { |f| f.sub /\.book$/, '.bookp' }
         .sort_by { |i| /(.*)-(\d*).*\.bookp/.match(i)[2].to_i  }
task bookp: booksp
rule '.bookp' => '.book' do |t|
  r = book_regex2.match t.source
  sh "cat #{t.source} >> #{r[1]}.md"
  pdfs << "#{r[1]}.pdf" # processed later on
end

rule '.pdf' => '.md' do |t|
  sh "cat #{t.source} | #{gpp_command} | pandoc  --template latex.tex -S -f markdown+#{extensions} -o #{t.name} --filter #{filters}" unless book_regex.match t.source
  cp t.name, '.' unless book_regex.match t.source
  rm t.source if /^build\//.match t.source
end

rule '.pdf' => '.slide.md' do |t|
  sh "cat #{t.source} | #{gpp_command} | pandoc  -f markdown+#{extensions} -t beamer -o #{t.name} -S"
end
