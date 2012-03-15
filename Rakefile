require 'rubygems'
require 'fileutils'

CONFIG = {
  :project_root => File.dirname(__FILE__),
  :build => "build",
  :mongodb => {
    :data_dir => "",
    :port     => 12345,
    :home     => File.dirname(__FILE__) + "/build/mongodb-osx-x86_64-2.0.3",
  },
  :urls => {
    :package => {
      :osx => {
        :url => "http://fastdl.mongodb.org/osx/mongodb-osx-x86_64-2.0.3.tgz",
        :file => "mongodb-osx-x86_64-2.0.3.tgz"
      }
    },
    :libs => {
      :java => {
        :url => "http://cloud.github.com/downloads/mongodb/mongo-java-driver/mongo-2.7.3.jar",
        :file => "mongo-2.7.3.jar"
      }
    }
  }
}

CONFIG[:build]              = CONFIG[:project_root] + "/build"
CONFIG[:mongodb][:data_dir] = CONFIG[:build]        + "/data"

def platform
  @platform ||= `uname`.split.first
  case @platform
  when "Darwin"
    :osx
  else
    :linux
  end
end

def ensure_dir p
  unless File.exist? p
    FileUtils.mkdir_p p
  end
end

def in_dir p
  ensure_dir p
  Dir.chdir(p) do |p|
    yield p
  end
end

def download_mongo url, local_file
  unless File.exist? local_file
    puts "Download: #{url} => #{local_file}"
  end
end

def untar f
  archive_dir = `tar tzf mongodb-osx-x86_64-2.0.3.tgz | head -n1`
  archive_dir = archive_dir.split('/').first
  puts "MOGODB: HOME: #{CONFIG[:mongodb][:home]}"
  unless File.exist? archive_dir
    system "tar", "xzf", f
  end
end

namespace :mongodb do
  desc "Install"
  task :install do
    in_dir(CONFIG[:build]) do |p|
      c = CONFIG[:urls][:package][platform]
      download_mongo c[:url], c[:file]
      untar c[:file]
    end
  end

  desc "Run the server (foreground)"
  task :mongod do
    data_dir = CONFIG[:mongodb][:data_dir]
    ensure_dir data_dir
    cmd = "#{CONFIG[:mongodb][:home]}/bin/mongod --dbpath #{data_dir}"
    puts cmd
    system cmd
  end
  
  desc "Run the mongodb shell"
  task :shell do
    cmd = "#{CONFIG[:mongodb][:home]}/bin/mongo"
    puts cmd
    system cmd
  end
end

# task :default => ["namespace::task_name"]
