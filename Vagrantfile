# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.define :imagr do |config|
    config.vm.box = "puppetlabs/ubuntu-14.04-64-nocm"
    config.vm.provision :shell, :path => "startup.sh"
    config.vm.network "public_network"
    config.vm.synced_folder '.', '/vagrant', disabled: true
    config.vm.synced_folder ".", "/usr/local/docker", {:mount_options => ['dmode=777','fmode=777']}
  end
end