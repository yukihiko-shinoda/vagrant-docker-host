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
  config.vm.provider "virtualbox" do |vm|
    vm.memory = 2048
  end

  config.vm.network :forwarded_port, guest: 80, host: 80
  config.vm.network :forwarded_port, guest: 443, host: 443
  config.vm.network :forwarded_port, guest: 3306, host: 3306
  # config.vm.synced_folder "./", "/vagrant", type:"nfs"
  config.vm.synced_folder ".", "/vagrant", mount_options: ["dmode=775,fmode=664"]

  config.vm.provision "shell", inline: $set_environment_variables, run: "always"
end
