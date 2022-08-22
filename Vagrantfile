app = ENV['APP'] || 'orcid'

rails_env = ENV['RAILS_ENV'] || 'development'
wp_mysql_password = ENV['WP_MYSQL_PASSWORD'] || 'orcid_web'

Vagrant.configure("2") do |config|
  config.vm.hostname = "vagrant"
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.define "vagrant-#{app}"
  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM:
    vb.memory = "2048"
    vb.name = "vagrant-#{app}"
  end
  config.vm.network :private_network, ip: "192.168.168.168"
  config.vm.provision "ansible" do |ansible| 
    ansible.playbook="vagrant_provision.yml" 
    ansible.extra_vars = {
      rails_env: rails_env,
      mysql_host: "localhost",
      wp_mysql_password: wp_mysql_password
    } 
  end 

  if rails_env == 'development'
    config.vm.synced_folder "#{app}", "/vagrant/#{app}", mount_options: ["dmode=775,fmode=664"]
    config.vm.synced_folder "#{app}/bin", "/vagrant/#{app}/bin", mount_options: ["dmode=775,fmode=775"]
  end

end
