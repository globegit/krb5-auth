require 'rake'
require 'rake/testtask'
require 'rake/extensiontask'
require 'rake/clean'
require 'rbconfig'

Rake::ExtensionTask.new('krb5_auth')

desc 'Clean any build files and .gem files'
task :clean do
  Dir.chdir("ext/krb5_auth") do
    sh "make distclean" rescue nil
  end
  Dir['*.gem'].each{ |f| File.delete(f) }
  rm_rf('lib')
end

desc 'Create a tarball of the source'
task :archive do
  spec = eval(IO.read('krb5-auth.gemspec'))
  prefix = "krb5-auth-#{spec.version}/"
  Dir['*.tar*'].each{ |f| File.delete(f) }
  sh "git archive --prefix=#{prefix} --format=tar HEAD > krb5-auth-#{spec.version}.tar"
  sh "gzip krb5-auth-#{spec.version}.tar"
end

namespace :gem do
  desc 'Delete any existing gem files in the project.'
  task :clean do
    Dir['*.gem'].each{ |f| File.delete(f) } 
    rm_rf 'lib'
  end 

  desc 'Create the gem'
  task :create => [:clean] do
    spec = eval(IO.read('krb5-auth.gemspec'))
    Gem::Builder.new(spec).build
  end

  desc 'Install the gem'
  task :install => [:create] do
    file = Dir["*.gem"].first
    sh "gem install #{file}" 
  end

  desc 'Create a binary gem'
  task :binary => [:compile] do
    spec = eval(IO.read('krb5-auth.gemspec'))
    spec.platform = Gem::Platform::CURRENT
    spec.extensions = nil
    spec.files = spec.files.reject{ |f| f.include?('ext') }

    Gem::Builder.new(spec).build
  end
end

namespace 'test' do
  Rake::TestTask.new('all') do |t|
    task :all => :compile
    t.libs << 'ext' 
    t.warning = true
    t.verbose = true
  end

  Rake::TestTask.new('krb5') do |t|
    task :krb5 => :compile
    t.libs << 'ext' 
    t.test_files = FileList['test/test_krb5.rb']
    t.warning = true
    t.verbose = true
  end

  Rake::TestTask.new('keytab') do |t|
    task :keytab => :compile
    t.libs << 'ext' 
    t.test_files = FileList['test/test_krb5_keytab.rb']
    t.warning = true
    t.verbose = true
  end

  Rake::TestTask.new('principal') do |t|
    task :principal => :compile
    t.libs << 'ext' 
    t.test_files = FileList['test/test_principal.rb']
    t.warning = true
    t.verbose = true
  end

  Rake::TestTask.new('kadm5') do |t|
    task :kadm5 => :compile
    t.libs << 'ext' 
    t.test_files = FileList['test/test_kadm5.rb']
    t.warning = true
    t.verbose = true
  end
end
