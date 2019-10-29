# -*- mode: ruby -*-
# vi: set ft=ruby :

Dotenv.load

# @see https://github.com/hashicorp/vagrant/issues/7015#issuecomment-313850505
$set_environment_variables = <<SCRIPT
tee "/etc/profile.d/myvars.sh" > "/dev/null" <<EOF
export SLACK_WEBHOOK_URL=#{ENV['SLACK_WEBHOOK_URL']}
EOF
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "futureys/docker-host"
  config.vbguest.no_install = true
  config.vm.hostname = "#{ENV['HOST_NAME']}.#{ENV['DOMAIN_NAME']}"
  config.vm.provider "virtualbox" do |vm|
    vm.memory = 2048
  end

  config.vm.network :forwarded_port, guest: 80, host: 80      # HTTP  for web browser
  config.vm.network :forwarded_port, guest: 443, host: 443    # HTTPS for web browser
  config.vm.network :forwarded_port, guest: 2375, host: 2375  # Docker host for IDE
  config.vm.network :forwarded_port, guest: 3306, host: 3306  # MySQL for MySQL client
  config.vm.network :forwarded_port, guest: 5432, host: 5432  # PostgreSQL for PostgreSQL client
  # config.vm.synced_folder "./", "/vagrant", type:"nfs"
  # config.nfs.map_uid = ENV['MAC_CURRENT_USER_ID']
  # config.nfs.map_gid = ENV['MAC_CURRENT_USER_ID']
  config.vm.synced_folder ".", "/vagrant", group: "nobody", mount_options: ["dmode=775,fmode=664"]

  config.vm.provision "shell", inline: $set_environment_variables, run: "always"
  config.vm.provision "ansible_local" do |ansible|
    ansible.extra_vars     = {
        roles_common_domain_name: ENV['DOMAIN_NAME']
    }
    ansible.install        = false
    # ansible.inventory_path = "./inventories/box"
    ansible.playbook       = "playbook.yml"
    # ansible.skip_tags      = "cleaning"
    ansible.verbose        = "vvv"
  end
end
