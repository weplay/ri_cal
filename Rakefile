require 'rubygems'
require 'rake'

begin
  require 'jeweler'
  Jeweler::Tasks.new do |gem|
    gem.name = "ri_cal"
    gem.summary = %Q{a new implementation of RFC2445 in Ruby}
    gem.description = %Q{A new Ruby implementation of RFC2445 iCalendar.

The existing Ruby iCalendar libraries (e.g. icalendar, vpim) provide for parsing and generating icalendar files,
but do not support important things like enumerating occurrences of repeating events.

This is a clean-slate implementation of RFC2445.

A Google group for discussion of this library has been set up http://groups.google.com/group/rical_gem
    }
    gem.email = "rick.denatale@gmail.com"
    gem.homepage = "http://github.com/rubyredrick/ri_cal"
    gem.authors = ["Rick DeNatale"]
    ['.gitignore', 'performance_data/*', 'sample_ical_files/*', 'website/*', 'config/website.yml'].each do |excl|
      gem.files.exclude excl
    end
    gem.extra_rdoc_files.include %w{History.txt copyrights.txt}
    # gem.add_development_dependency "thoughtbot-shoulda", ">= 0"
    # gem is a Gem::Specification... see http://www.rubygems.org/read/chapter/20 for additional settings
  end
  Jeweler::GemcutterTasks.new
rescue LoadError
  puts "Jeweler (or a dependency) not available. Install it with: gem install jeweler"
end

require 'rake/testtask'
Rake::TestTask.new(:test) do |test|
  test.libs << 'lib' << 'test'
  test.pattern = 'test/**/test_*.rb'
  test.verbose = true
end

begin
  require 'rcov/rcovtask'
  Rcov::RcovTask.new do |test|
    test.libs << 'test'
    test.pattern = 'test/**/test_*.rb'
    test.verbose = true
  end
rescue LoadError
  task :rcov do
    abort "RCov is not available. In order to run rcov, you must: sudo gem install spicycode-rcov"
  end
end

Dir['tasks/**/*.rake'].each { |t| load t }

task :default => [:"spec:with_tzinfo_gem", :"spec:with_active_support"]


require 'rake/rdoctask'
Rake::RDocTask.new do |rdoc|
  version = File.exist?('VERSION') ? File.read('VERSION') : ""

  rdoc.rdoc_dir = 'rdoc'
  rdoc.title = "ri_cal #{version}"
  rdoc.rdoc_files.include('README*')
  rdoc.rdoc_files.include('lib/**/*.rb')
end

begin
  require 'spec'
rescue LoadError
  require 'rubygems'
  require 'spec'
end
begin
  require 'spec/rake/spectask'
rescue LoadError
  puts <<-EOS
To use rspec for testing you must install rspec gem:
    gem install rspec
EOS
  exit(0)
end

desc "Run all specs"
Spec::Rake::SpecTask.new do |t|
  t.spec_opts = ['--options', "spec/spec.opts"]
  t.spec_files = FileList['spec/**/*_spec.rb']
  t.ruby_opts << "-rubygems"
end

namespace :spec do
  desc "Run all specs in the presence of ActiveSupport"
  Spec::Rake::SpecTask.new(:with_active_support) do |t|
    t.spec_opts = ['--options', "spec/spec.opts"]
    t.spec_files = FileList['spec/**/*_spec.rb']
    t.ruby_opts << "-r #{File.join(File.dirname(__FILE__), *%w[gem_loader load_active_support])}"
  end

  desc "Run all specs in the presence of the tzinfo gem"
  Spec::Rake::SpecTask.new(:with_tzinfo_gem) do |t|
    t.spec_opts = ['--options', "spec/spec.opts"]
    t.spec_files = FileList['spec/**/*_spec.rb']
    t.ruby_opts << "-r #{File.join(File.dirname(__FILE__), *%w[gem_loader load_tzinfo_gem])}"
  end
  multiruby_path = `which multiruby`.chomp
  if multiruby_path.length > 0 && Spec::Rake::SpecTask.instance_methods.include?("ruby_cmd")
    namespace :multi do
      desc "Run all specs with multiruby and ActiveSupport"
      Spec::Rake::SpecTask.new(:with_active_support) do |t|
        t.spec_opts = ['--options', "spec/spec.opts"]
        t.spec_files = FileList['spec/**/*_spec.rb']
        t.ruby_cmd = "#{multiruby_path}"
        t.verbose = true
        t.ruby_opts << "-r #{File.join(File.dirname(__FILE__), *%w[gem_loader load_active_support])}"
      end

      desc "Run all specs multiruby and the tzinfo gem"
      Spec::Rake::SpecTask.new(:with_tzinfo_gem) do |t|
        t.spec_opts = ['--options', "spec/spec.opts"]
        t.spec_files = FileList['spec/**/*_spec.rb']
        t.ruby_cmd = "#{multiruby_path}"
        t.verbose = true
        t.ruby_opts << "-r #{File.join(File.dirname(__FILE__), *%w[gem_loader load_tzinfo_gem])}"
      end
    end

    desc "run all specs under multiruby with ActiveSupport and also with the tzinfo gem"
    task :multi => [:"spec:multi:with_active_support", :"spec:multi:with_tzinfo_gem"]
  end
end

if RUBY_VERSION.match(/^1\.8\./)
  desc 'Run all specs with RCov'
  Spec::Rake::SpecTask.new(:rcov) do |t|
    t.spec_opts = ['--options', "spec/spec.opts"]
    t.spec_files = FileList['spec/**/*_spec.rb']
    t.rcov = true
    t.rcov_dir = "coverage"
    t.rcov_opts = ['--exclude', 'spec']
  end
end

namespace :performance do
  desc 'Run all benchmarks'
  task :benchmark do
    bench_script = File.join(File.dirname(__FILE__), '..', '/script', 'benchmark_subject')
    bench_file = File.join(File.dirname(__FILE__), '..', '/performance_data', 'benchmarks.out')
    cat = ">"
    FileList[File.join(File.dirname(__FILE__), '..', '/performance', '*')].each do |f|
      cmd = "#{bench_script} #{File.basename(f)} #{cat} #{bench_file}"
      puts cmd
      `#{cmd}`
      cat = '>>'
    end
  end

  desc 'Run all profiles'
  task :profile do
    bench_script = File.join(File.dirname(__FILE__), '..', '/script', 'profile_subject')
    FileList[File.join(File.dirname(__FILE__), '..', '/performance', '*')].each do |f|
      cmd = "#{bench_script} #{File.basename(f)}"
      puts cmd
      `#{cmd}`
    end
  end

end
 