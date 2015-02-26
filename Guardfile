ignore /^MathJax/
ignore /^\.\#.*/
ignore /^\.#.*/
guard :shell do
  watch(/(.*)\.md$/) do |f| 
    puts `rake #{f[1] + '.pdf'}` 
  end
  watch(/(.*)\.slide\.md$/) do |f| 
    puts `rake #{f[1] + '.html'}` 
    puts `rake #{f[1] + '.pdf'}` 
  end
end

guard 'livereload' do
  watch(%r{.*\.(pdf|html)})
end
