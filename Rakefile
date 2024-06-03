require 'fileutils'
require 'httparty'
require 'json'
require 'pry'
require 'rubygems'
require 'zip'

RELEASES_URL = 'https://api.github.com/repos/godotengine/godot-builds/releases'
GODOT_VERSION = '4.2.1-stable'
EDITOR_PATH = '.editor/'
GODOT_PATH = ".editor/Godot_v#{GODOT_VERSION}_win64_console.exe"

task :download do
  github_data = JSON.parse HTTParty.get(RELEASES_URL).body
  version = github_data.find { |i| i['tag_name'] == GODOT_VERSION }

  exit 1 unless version

  asset_name = "Godot_v#{GODOT_VERSION}_win64.exe.zip"
  asset = version['assets'].find { |i| i['name'] == asset_name }

  dl_url = asset['browser_download_url']

  puts "Fetching #{dl_url}"
  FileUtils.rm_r EDITOR_PATH
  FileUtils.mkdir_p EDITOR_PATH

  open(EDITOR_PATH + 'godot.zip', 'wb') do |f|
    f.binmode

    print 'Starting download...'
    total = 0
    HTTParty.get(dl_url, stream_body: true, follow_redirects: true) do |chunk|
      total += chunk.size
      print "\r#{total} bytes downloaded"
      f.write(chunk)
    end
  end
end

task :extract do
  Zip::File.open("#{EDITOR_PATH}/godot.zip") do |zip|
    zip.each do |file|
      zip.extract(file, "#{EDITOR_PATH}/#{file.name}")
    end
  end

  FileUtils.rm "#{EDITOR_PATH}/godot.zip"
end

task install: %i[download extract]

task :test do
  `#{GODOT_PATH} --path .`
end

task :dev do
  `#{GODOT_PATH} --editor --path .`
end

task :clean do
  puts "Removing old build artifacts..."
  FileUtils.mkdir_p ".build"
  FileUtils.rm_r ".build"
end

task :export do
  presets = ['Web', 'Windows Desktop']
  presets.each do |preset|
    FileUtils.mkdir_p ".build/#{preset}"

    puts "Exporting project for #{preset}..."
    `#{GODOT_PATH} --headless --export-debug "#{preset}"`

    puts "Creating a ZIP archive..."
    Zip::File.open(".build/#{preset}.zip", create: true) do |zip|
      Dir[".build/#{preset}/*"].each do |filename|
        zip.add(filename.split('/')[-1], filename)
      end
    end
  end
end

task build: %w[clean export]
