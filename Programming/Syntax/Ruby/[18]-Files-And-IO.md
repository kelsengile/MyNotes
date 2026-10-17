[Previous](./[17]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[19]-Gems-Bundler-And-The-Standard-Library.md)

*Working With Ruby*

# Lesson 18 - Files And IO

## 18.1 Reading Files

```ruby
content = File.read("notes.txt")            # whole file as a String
lines = File.readlines("notes.txt")         # array of lines (with "\n")
lines = File.readlines("notes.txt", chomp: true)

File.foreach("big.txt") do |line|           # one line at a time (memory friendly)
  puts line
end

File.open("notes.txt") do |f|               # block form closes the file for you
  f.each_line { |l| puts l }
  f.rewind
  first = f.gets
  f.read(10)                                # read up to 10 bytes/characters
end

File.exist?("notes.txt")   #=> true
File.file?("notes.txt")
File.directory?("src")
File.size("notes.txt")
File.zero?("empty.txt")
```

---

## 18.2 Writing Files

```ruby
File.write("out.txt", "Hello\n")             # overwrite, returns bytes written
File.write("out.txt", "More\n", mode: "a")   # append

File.open("out.txt", "w") do |f|
  f.puts "Line 1"
  f.print "no newline"
  f.write("raw", "data")
  f << "chained " << "output\n"
  f.printf("%03d\n", 7)
end
```

| Mode | Meaning |
| --- | --- |
| `"r"` | Read only (default); file must exist |
| `"w"` | Write; truncates or creates |
| `"a"` | Append; creates if needed |
| `"r+"` | Read and write |
| `"w+"` | Read and write; truncates |
| `"a+"` | Read and append |
| `"b"` suffix | Binary mode (e.g. `"rb"`, `"wb"`) |

Specify encodings when needed: `File.open("f.txt", "r:UTF-8")` or `File.read("f.txt", encoding: "UTF-8")`.

Writing to a temporary file and renaming it is a safe way to avoid half-written files:

```ruby
File.write("data.tmp", data)
File.rename("data.tmp", "data.json")
```

---

## 18.3 Paths, Directories, And FileUtils

```ruby
File.basename("/a/b/file.rb")          #=> "file.rb"
File.basename("/a/b/file.rb", ".rb")   #=> "file"
File.dirname("/a/b/file.rb")           #=> "/a/b"
File.extname("file.tar.gz")            #=> ".gz"
File.join("a", "b", "c.txt")           #=> "a/b/c.txt"
File.expand_path("~/notes")            # absolute path
File.expand_path("lib", __dir__)       # relative to this file's directory
File.absolute_path?("/tmp")            #=> true
```

Directories:

```ruby
Dir.pwd                          # current directory
Dir.chdir("/tmp") { ... }        # temporarily change directory
Dir.mkdir("new")
Dir.exist?("new")
Dir.entries(".")                 # includes "." and ".."
Dir.children(".")                # without them
Dir.glob("**/*.rb")              # recursive pattern search
Dir["*.txt"]                     # shortcut for glob
Dir.each_child(".") { |name| puts name }
```

`FileUtils` provides shell-like operations:

```ruby
require "fileutils"

FileUtils.mkdir_p("a/b/c")        # create nested directories
FileUtils.cp("a.txt", "b.txt")
FileUtils.cp_r("src", "backup")
FileUtils.mv("old.txt", "new.txt")
FileUtils.rm("tmp.txt")
FileUtils.rm_rf("build")          # careful!
FileUtils.touch("flag")
```

`Pathname` wraps paths in an object-oriented interface:

```ruby
require "pathname"

root = Pathname.new("/var/log")
log = root / "app" / "today.log"
log.exist?
log.basename.to_s
log.parent
log.read
root.children
root.glob("*.log")
```

---

## 18.4 File Metadata

```ruby
File.mtime("a.txt")          # modification time (a Time)
File.stat("a.txt").size
File.stat("a.txt").mode.to_s(8)
File.readable?("a.txt")
File.writable?("a.txt")
File.executable?("script")
File.symlink?("link")
File.chmod(0644, "a.txt")
File.delete("a.txt")         # raises if missing
File.rename("a", "b")
File.ftype("a.txt")          #=> "file"
```

---

## 18.5 Standard Streams And StringIO

```ruby
$stdout.puts "to stdout"
$stderr.puts "to stderr"
STDOUT.sync = true            # flush output immediately
input = $stdin.read           # everything from standard input
$stdin.each_line { |l| puts l.upcase }
ARGF.each_line { |l| puts l }  # reads files named in ARGV, or stdin
```

`StringIO` behaves like a file but lives in memory, which is great for tests:

```ruby
require "stringio"

io = StringIO.new
io.puts "captured"
io.string               #=> "captured\n"

input = StringIO.new("a\nb\n")
input.gets              #=> "a\n"

old = $stdout
$stdout = StringIO.new
puts "hidden"
captured = $stdout.string
$stdout = old
```

---

## 18.6 Temporary Files

```ruby
require "tempfile"
require "tmpdir"

Tempfile.create("report") do |f|   # deleted when the block ends
  f.write("data")
  f.flush
  puts f.path
end

Dir.mktmpdir do |dir|
  File.write(File.join(dir, "x.txt"), "hi")
end
```

---

## 18.7 Structured Data: JSON, YAML, And CSV

```ruby
require "json"

data = { name: "Ada", langs: ["Ruby", "C"] }
json = JSON.generate(data)            #=> '{"name":"Ada","langs":["Ruby","C"]}'
puts JSON.pretty_generate(data)
data.to_json
parsed = JSON.parse(json)             # string keys
JSON.parse(json, symbolize_names: true)
File.write("data.json", JSON.pretty_generate(data))
JSON.load_file("data.json")

require "yaml"
yaml = data.to_yaml
YAML.load(yaml)
YAML.safe_load(yaml, permitted_classes: [Symbol])   # safe for untrusted input
YAML.load_file("config.yml")

require "csv"       # a bundled gem since Ruby 3.4: add gem "csv" to your Gemfile
CSV.foreach("people.csv", headers: true) do |row|
  puts row["name"]
end
CSV.generate { |csv| csv << ["a", "b"]; csv << [1, 2] }
CSV.parse("a,b\n1,2", headers: true).map(&:to_h)
```

Never use `YAML.unsafe_load` or `Marshal.load` on untrusted data.

---

## 18.8 Running Other Programs

```ruby
system("ls", "-l")            # returns true/false/nil; output goes to terminal
output = `echo hello`         # backticks capture stdout
output = %x(date)
$?.success?                   # status of the last command
$?.exitstatus

require "open3"
stdout, stderr, status = Open3.capture3("grep", "-r", "TODO", ".")
stdout, status = Open3.capture2("sort", stdin_data: "b\na\n")

pid = spawn("sleep 1")
Process.wait(pid)
```

Pass the command and arguments separately (`system("ls", dir)`) rather than interpolating into a shell string to avoid injection bugs. Since Ruby 4.0, `Kernel#open` and `IO` methods no longer create processes from strings starting with `|`; use `IO.popen` or `Open3` explicitly instead.

---

[Previous](./[17]-Error-Handling.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[19]-Gems-Bundler-And-The-Standard-Library.md)
