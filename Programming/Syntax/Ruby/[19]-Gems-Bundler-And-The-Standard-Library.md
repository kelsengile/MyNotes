[Previous](./[18]-Files-And-IO.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[20]-Testing-And-Debugging.md)

*Working With Ruby*

# Lesson 19 - Gems Bundler And The Standard Library

## 19.1 Loading Code: require, require_relative, load

```ruby
require "json"                    # load a library from the load path or an installed gem
require_relative "lib/helper"     # load a file relative to the current file
load "script.rb"                  # load every time it is called (re-executes the file)
```

- `require` loads a file **once** and returns `true` the first time, `false` afterwards.
- `require_relative` is the right choice for files in your own project.
- `$LOAD_PATH` (alias `$:`) lists directories searched by `require`; add one with `-I dir` or `$LOAD_PATH.unshift("lib")`.
- `autoload :Parser, "parser"` loads a constant only when first used.

A typical project layout:

```
my_app/
├── Gemfile
├── Gemfile.lock
├── lib/
│   ├── my_app.rb
│   └── my_app/
│       └── parser.rb
├── test/ (or spec/)
├── bin/
└── README.md
```

---

## 19.2 RubyGems

A **gem** is a packaged Ruby library. The `gem` command installs and manages them.

```bash
gem install rake              # install the latest version
gem install rails -v 8.0.0    # a specific version
gem list                      # installed gems
gem list --remote json        # search remote gems
gem info nokogiri
gem uninstall rake
gem update                    # update everything
gem outdated
gem env                       # show environment info (paths, sources)
```

Use a gem in code:

```ruby
require "colorize"
puts "ready".green
```

Most gems are hosted on **rubygems.org**. Always review a gem's maintenance status, downloads, and license before adding it.

---

## 19.3 Bundler And The Gemfile

Bundler manages the exact set of gems a project needs so that it behaves the same on every machine.

```ruby
# Gemfile
source "https://rubygems.org"

ruby "~> 4.0"

gem "sinatra", "~> 4.0"
gem "json"
gem "rake"

group :development, :test do
  gem "rspec"
  gem "rubocop", require: false
end

gem "my_lib", git: "https://github.com/me/my_lib.git"
gem "local_gem", path: "../local_gem"
```

```bash
bundle init                  # create a Gemfile
bundle install               # resolve and install; writes Gemfile.lock
bundle exec ruby app.rb      # run with the Gemfile's exact versions
bundle exec rake test
bundle update rack           # update one gem
bundle outdated
bundle add faker             # add a gem to the Gemfile
bundle config set --local path vendor/bundle
```

In scripts you can load everything with `require "bundler/setup"` or `Bundler.require`. Or use inline dependencies for a one-file script:

```ruby
require "bundler/inline"
gemfile do
  source "https://rubygems.org"
  gem "colorize"
end
```

**Version constraints:**

| Constraint | Meaning |
| --- | --- |
| `"1.2.3"` | Exactly 1.2.3 |
| `">= 1.2"` | At least 1.2 |
| `"~> 1.2"` | `>= 1.2` and `< 2.0` |
| `"~> 1.2.3"` | `>= 1.2.3` and `< 1.3` |

Commit `Gemfile.lock` for applications, so everyone gets the same versions.

---

## 19.4 Default Gems Versus Bundled Gems

Ruby ships with a standard library made of two kinds of gems:

- **Default gems** can be required directly (for example `json`, `set`, `time`, `optparse`, `fileutils`, `securerandom`, `forwardable`, `net/http`).
- **Bundled gems** are installed with Ruby but, in a Bundler project, must be listed in your `Gemfile` or `require` fails. Over recent releases several libraries moved here, including `csv`, `base64`, `bigdecimal`, `drb`, `mutex_m`, `observer`, `abbrev`, and `ostruct`.

If you see `LoadError: cannot load such file -- csv` under Bundler, add `gem "csv"` to the Gemfile. When you upgrade Ruby, run your tests with warnings enabled to catch these.

---

## 19.5 A Tour Of The Standard Library

```ruby
# Time and dates
Time.now
Time.now.strftime("%Y-%m-%d %H:%M:%S")
Time.at(0).utc
Time.new(2026, 10, 17, 12, 30)
Time.parse("2026-10-17 09:00")      # requires "time"
require "date"
Date.today
Date.new(2026, 12, 25) - Date.today #=> a Rational number of days
Date.parse("2026-10-17").strftime("%A")
Date.today.next_month

# Random and digests
require "securerandom"; SecureRandom.uuid
require "digest";       Digest::SHA256.hexdigest("hello")
require "base64";       Base64.strict_encode64("hi")   # bundled gem in recent Rubies

# Command-line parsing
require "optparse"
options = {}
OptionParser.new do |opts|
  opts.banner = "Usage: tool [options]"
  opts.on("-v", "--verbose", "Verbose output") { options[:verbose] = true }
  opts.on("-nNAME", "--name=NAME", String, "Name") { |v| options[:name] = v }
end.parse!

# Logging
require "logger"
log = Logger.new($stdout)
log.level = Logger::INFO
log.info("started")
log.error("failed")

# HTTP
require "net/http"
require "uri"
res = Net::HTTP.get_response(URI("https://example.com"))
puts res.code, res.body[0, 80]

# Timing
require "benchmark"
Benchmark.measure { 100_000.times { Math.sqrt(2) } }
Benchmark.realtime { sleep 0.1 }

# Templates
require "erb"
ERB.new("Hi <%= name %>!").result_with_hash(name: "Ada")

# Misc
require "singleton"      # class Config; include Singleton; end
require "forwardable"    # def_delegators
require "pp"             # pretty printing
require "ostruct"        # OpenStruct (convenient but slow; may need the gem)
require "timeout"        # Timeout.timeout(2) { ... }
require "uri"            # URI.parse("https://a.com/x?y=1").query
require "socket"         # TCPServer, TCPSocket
require "zlib"           # compression
require "open3"          # run subprocesses
require "tempfile"
require "monitor"        # reentrant locks
require "io/console"     # IO#getch, IO#winsize
```

---

## 19.6 Popular Gems And Frameworks

| Area | Gems |
| --- | --- |
| Web frameworks | Rails, Sinatra, Hanami, Roda |
| Web servers | Puma, Falcon, Unicorn |
| Testing | RSpec, Minitest, Capybara, FactoryBot, WebMock, VCR |
| Code quality | RuboCop, Reek, Brakeman (security), SimpleCov (coverage) |
| Data and ORM | ActiveRecord, Sequel, ROM |
| Background jobs | Sidekiq, Good Job, Solid Queue |
| HTTP clients | Faraday, HTTParty, Net::HTTP, httpx |
| Parsing | Nokogiri (HTML/XML), Oj (JSON), Kramdown, Redcarpet |
| CLI tools | Thor, TTY toolkit, GLI, Rake |
| Auth | Devise, OmniAuth |
| Debugging | debug, byebug, pry, irb |

---

## 19.7 Creating Your Own Gem

```bash
bundle gem my_tool
```

This generates a skeleton: a `gemspec`, `lib/` code, `Rakefile`, tests, and `bin/` scripts.

```ruby
# my_tool.gemspec (simplified)
Gem::Specification.new do |spec|
  spec.name = "my_tool"
  spec.version = MyTool::VERSION
  spec.authors = ["Ada Lovelace"]
  spec.summary = "Does one useful thing"
  spec.license = "MIT"
  spec.required_ruby_version = ">= 3.2"
  spec.files = Dir["lib/**/*", "README.md"]
  spec.add_dependency "json"
end
```

```bash
gem build my_tool.gemspec     # produces my_tool-0.1.0.gem
gem install ./my_tool-0.1.0.gem
gem push my_tool-0.1.0.gem    # publish to rubygems.org (needs an account)
```

Follow Semantic Versioning (MAJOR.MINOR.PATCH) so users can rely on `~>` constraints.

---

## 19.8 Rake: Task Automation

```ruby
# Rakefile
require "rake/testtask"

task default: :test

Rake::TestTask.new do |t|
  t.pattern = "test/**/*_test.rb"
end

desc "Say hello"
task :hello, [:name] do |_t, args|
  puts "Hello, #{args[:name] || 'world'}"
end

namespace :db do
  desc "Reset the database"
  task reset: [:drop, :create] do
    puts "reset"
  end
end
```

```bash
rake -T              # list documented tasks
rake hello[Ada]
rake db:reset
```

---

[Previous](./[18]-Files-And-IO.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[20]-Testing-And-Debugging.md)
