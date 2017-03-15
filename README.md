## Ruby Skeleton Project Script

Name this script whatever you want. The script takes one parameter which is the name of your project, which will also be used to name the directory and various other files, etc. For an executable script make sure you do chmod +x scriptname, then it is just a case of ./scriptname project. Not the best Ruby code around, but it does what I want and saves me some time.

To test, type the following, I named my script create_project:
```
codinguy@debian:~/Projects$ ./create_project testskel
codinguy@debian:~/Projects$ cd taskskel
codinguy@debian:~/Projects/testskel$ rake test

/usr/bin/ruby2.1 -I”lib:tests” -I”/usr/lib/ruby/vendor_ruby” “/usr/lib/ruby/vendor_ruby/rake/rake_test_loader.rb” “tests/test_testskel.rb”

Run options:

# Running tests:

Finished tests in 0.006407s, 156.0830 tests/s, 156.0830 assertions/s.
1 tests, 1 assertions, 0 failures, 0 errors, 0 skips

ruby -v: ruby 2.1.5p273 (2014-11-13) [x86_64-linux-gnu]
```
You should see something similar to the above output from executing rake test, this proves the skeleton project has been setup correctly.
```ruby	
#!/usr/bin/ruby -w
 
# script to create a project skeleton directory layout,
# taken from Learn Ruby the Hard Way documentation
#
# project/
#   /bin
#   /data
#   /doc
#   /ext
#   /lib/
#     /project/
#     project.rb
#   /tests/
#     test_project.rb
#   Rakefile
#   project.gemspec
 
# create project directory and related sub directories
#
# @params prj [String] project name as passed in via command line
# @return exit_status [Integer] status of shell operation
def create_dirs(prj)
  exit_status = 0
 
  dirs = [
    "mkdir #{prj}",
    "mkdir #{prj}/bin",
    "mkdir #{prj}/data",
    "mkdir #{prj}/doc",
    "mkdir #{prj}/ext",
    "mkdir #{prj}/lib",
    "mkdir #{prj}/lib/#{prj}",
    "mkdir #{prj}/tests"
  ]
   
  dirs.each do |d| 
    Open3.popen3("#{d}") { |i,o,e,t|
      exit_status = t.value
    }
  end
   
  return exit_status
end
 
# create empty files in preparation for filling
# with relevant data
#
# @params prj [String] project name as passed in via command line
# @return exit_status [Integer] status of shell operation
def create_empty_files(prj)
  exit_status = 0
 
  touch_files = [
    "touch #{prj}/Rakefile",
    "touch #{prj}/#{prj}.gemspec",
    "touch #{prj}/bin/#{prj}",
    "touch #{prj}/lib/#{prj}.rb",
    "touch #{prj}/tests/test_#{prj}.rb"
  ]
 
  touch_files.each do |f|
    Open3.popen3("#{f}") { |i,o,e,t|
      exit_status = t.value
    }
  end
 
  return exit_status
end
 
# write initial data into Rakefile
#
# @params prj [String] project name as passed in via command line
def write_rakefile(prj)
  rakefile = [
    "require 'rake/testtask'",
    "",
    "Rake::TestTask.new do |t|",
    "  t.libs << "tests"",
    "  t.test_files = FileList['tests/test*.rb']",
    "  t.verbose = true",
    "end"
  ]
 
  File.open("#{prj}/Rakefile", "w+") do |r|
    r.puts(rakefile)
  end
end
 
# write initial gemspec data into gemspec file
# change data as appropriate here
#
# @params prj [String] project name as passed in via command line
def write_gemspec(prj)
  gemspec = [
    "# coding: utf-8",
    "lib = File.expand_path('../lib', __FILE__)",
    "$LOAD_PATH.unshift(lib) unless #LOAD_PATH.include?(lib)",
    "",
    "Gem::Specification.new do |spec|",
    "  spec.name          = "#{prj}"",
    "  spec.version       = '1.0'",
    "  spec.authors       = ["Your Name"]",
    "  spec.email         = ["your@email"]",
    "  spec.summary       = %q{Short summary of #{prj} here}",
    "  spec.description   = q%{Longer description of #{prj}}",
    "  spec.homepage      = "http://homepage/"",
    "  spec.license       = "BSD"",
    "",
    "  spec.files         = ['lib/#{prj}.rb']",
    "  spec.executables   = ['bin/#{prj}']",
    "  spec.test_files    = ['tests/test_#{prj}.rb']",
    "  spec.require_paths = ["lib"]",
    "end"
  ]
 
  File.open("#{prj}/#{prj}.gemspec","w+") do |g|
    g.puts(gemspec)
  end
end
 
# write a dummy unit test file with test case
#
# @params prj [String] project name as passed in via command line
def write_dummy_test(prj)
  test = [
    "require './lib/#{prj}.rb'",
    "require "test/unit"",
    "",
    "class Test#{prj} < Test::Unit::TestCase",
    "",
    "  def test_sample",
    "    assert_equal(4, 2+2)",
    "  end",
    "",
    "end"
  ]
 
  File.open("#{prj}/tests/test_#{prj}.rb","w+") do |t|
    t.puts(test)
  end
end
 
# main
require "open3"
 
project_name = ARGV.first
 
create_dirs(project_name)
create_empty_files(project_name)
 
write_rakefile(project_name)
write_gemspec(project_name)
write_dummy_test(project_name)
```
