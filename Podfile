# This podfile is intended for development and testing on Snowplow.
#
# If you are working on Snowplow, you do not need to have CocoaPods installed
# unless you want to install new development dependencies as the Pods directory
# is part of the source tree.

source 'https://github.com/CocoaPods/Specs.git'

abstract_target 'Base' do

  target 'Snowplow' do
    inherit! :search_paths
    platform :ios, '8.0'
    pod 'FMDB', '2.6.2'
    pod 'Reachability', '3.1.1'
  end

  target 'Snowplow-OSX' do
    pod 'FMDB', '2.6.2'
    platform :osx, '10.9'
  end
end

abstract_target 'BaseTests' do

  target 'SnowplowTests' do
    inherit! :search_paths
    platform :ios, '8.0'
    pod 'Nocilla'
    pod 'SnowplowIgluClient'
  end

  target 'Snowplow-OSXTests' do
    platform :osx, '10.9'
    pod 'Nocilla'
    pod 'SnowplowIgluClient'
  end
end

post_install do |installer|
  handle_sqlite3 installer
end

def handle_sqlite3 installer
  # We need to remove sqlite3 from the library
  # For details see: https://github.com/CocoaPods/CocoaPods/issues/830
  default_library = installer.aggregate_targets.detect { |i| i.target_definition.name == 'Snowplow' }
  [default_library.xcconfig_relative_path('Debug'), default_library.xcconfig_relative_path('Release')].each do |path|
    path = File.expand_path(File.join(File.dirname(__FILE__), path))
    File.open("config.tmp", "w") do |io|
      f = File.read(path)
      f.gsub!(/-l"sqlite3"/, '')
      io << f
    end
    FileUtils.mv("config.tmp", path)
  end

  # We need to add sqlite3 into the test suite
  default_library = installer.aggregate_targets.detect { |i| i.target_definition.name == 'SnowplowTests' }
  [default_library.xcconfig_relative_path('Debug'), default_library.xcconfig_relative_path('Release')].each do |path|
    path = File.expand_path(File.join(File.dirname(__FILE__), path))
    File.open("config.tmp", "w") do |io|
      f = File.read(path)
      f.gsub!(/(OTHER_LDFLAGS =)/, '\1 -l"sqlite3"')
      io << f
    end
    FileUtils.mv("config.tmp", path)
  end

  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['ONLY_ACTIVE_ARCH'] = 'NO'
    end
  end
end
