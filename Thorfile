require 'fileutils'

class Bakery < Thor
  include Thor::Actions

  desc 'prep', 'Prepare to bake an RPM'
  def prep
    # Sanity checks
    if not File.exists?('VERSION')
      raise 'VERSION file missing, cannot create rpm'
    end
    run 'bundle'

    FileUtils::mkdir_p('fakeroot/usr/bin')
    FileUtils::mkdir_p('fakeroot/usr/share/chef-valkyrie')
    FileUtils.cp('chef-valkyrie', 'fakeroot/usr/bin')
    FileUtils.cp('Readme.md', 'fakeroot/usr/share/chef-valkyrie')

  end

  desc 'rpm', 'create an RPM that installs chef-valkyrie'
  def rpm
    invoke :prep, []
    semver=File.open('VERSION') { |file| file.read }.chomp
    package_name = 'chef-valkyrie'
    iteration=`git rev-list HEAD --count`.chomp

    fpm_cmd = 'fpm -s python' + \
      ' -t rpm' + \
      " -n #{package_name}" + \
      " -v #{semver}" + \
      " --iteration #{iteration}" + \
      " --description 'chef-valkyrie is a tool to clean dead EC2 instances from your Chef Server'" + \
      ' --depends python' + \
      ' -s dir' + \
      ' -C fakeroot' + \
      ' usr'
    run(fpm_cmd)
  end

end
