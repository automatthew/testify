require "starter/tasks/npm"
require "starter/tasks/git"
require "starter/tasks/markdown"

task "build" => %w[bundle:example doc/chart.png]

task "bundle:example" do
  Dir.chdir "examples/browser" do
    sh "ark package < manifest.json > bundle.js"
  end
end

file "doc/chart.png" => %w[src/context.coffee doc/graph.coffee] do
  sh "coffee doc/graph.coffee"
end

task "readme"  do |t|
  File.open("README.md", "w") do |f|
    f.puts process_doc("doc/README.template.md")
  end
  #puts File.read("README.md")
end

#task "readme" do
  #puts process_doc("doc/README.template.md")
#end

def process_doc(path)
  regex = %r{^```([^\s#]+)(#L(\S+))?\s*```$}
  out = []
  base_path = File.dirname(path)
  string = File.open(path, "r") do |f|

    f.each_line do |line|

      if md = regex.match(line)
        _full, source_path, badline, line_spec = md.to_a
        if line_spec
          start, stop = line_spec.split("-").map { |s| s.to_i}
        else
          start = 1
        end

        source_path = File.expand_path("#{base_path}/#{source_path}").strip
        extension = File.extname(source_path)
        out << "```#{extension}\n\n"

        embed = []
        File.open(source_path, "r") do |source|
          source.each_line do |line|
            embed << line
          end
        end
        start -= 1
        if stop
          stop -=1
        else
          stop = embed.size - 1
        end
        out << embed.slice(start..stop).join()
        out << "```\n"
      else
        out << line
      end
    end

  end

  out.join()
end

task "build:usage" do
  examples = FileList["examples/*.coffee"]
  examples.each do |path|
    base = File.basename(path).chomp(File.extname(path))
    text_out = "#{base}.out.txt"
    cap_command("bin/testify -c #{path}", "./doc/#{base}.png")
    #system "bin/testify #{path} > ./doc/#{text_out}"
  end
end

task "smurf" do
  cap_command "bin/testify -c examples/basic_usage.coffee", "./doc/basic_usage.png"
end

def cap_command(command, file)
  system "clear"
  puts "$ #{command}"
  system command
  system "screencapture -l$(osascript -e 'tell app \"Terminal\" to id of window 1') -o #{file}"
end


