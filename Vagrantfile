# -*- mode: ruby -*-
# vi: set ft=ruby :

# NOTE: This file is for testing the ansible provisioner after the box is built
# It is NOT included in the Vagrant box built by Packer
# Its purpose is to run the *exact same* provisioner that packer will run, i.e. the same ansible playbook
# NOTE: This is designed to run exactly one machine at a time. Destroy a machine before starting another


args = ARGV
args.delete('--provision')
action, type = args
#
box = Dir.glob('**/*.box').first
box_name = box.gsub('.box', '')
box_url = "file://#{Dir.pwd}/#{box}"
# STDOUT.puts box
# STDOUT.puts box_name
# STDOUT.puts box_url
#
# If action is 'up' or 'provision' then test for valid type, conflicting directories and box is available
vagrantfile_dir = Pathname(__FILE__).dirname.to_s
current_dir = Dir.pwd
if %w(up provision clean).include?(action)
  # vexit("Invalid machine type: '#{type}'. Available types are: #{valid_types.join(', ')}", 2) unless valid_types.include?(type)

  conflict_dirs = [
    "#{vagrantfile_dir}/.vagrant",
    "#{Dir.home}/.vagrant.d/boxes/#{box_name}",
    "#{Dir.home}/VirtualBox VMs/#{box_name}"
    # "#{Dir.home}/.vagrant.d/boxes/#{box_prefix}-VAGRANTSLASH-#{os_string}"
  ]

  # Prompt user to remove potentially conflicting directories
  conflict_dirs.each do |dir|
    next unless Dir.exists?(dir)
    if action.eql?('clean')
      FileUtils.rm_rf(dir)
    else
      STDOUT.puts "Potential conflicting directory '#{dir} exists. Remove? (y/n)"
      FileUtils.rm_rf(dir) if STDIN.gets.chomp.eql?('y')
    end
  end

  # STDOUT.puts "###\nMachine Type is '#{type}'\n###"
  exit if action.eql?('clean')
end


Vagrant.configure(2) do |config|
  # config.ssh.insert_key = false

  config.vm.provider :virtualbox do |v|
    v.customize ['modifyvm', :id, '--memory', '2048']
    v.customize ['modifyvm', :id, '--cpus', '2']
    v.customize ['modifyvm', :id, '--nictype1', 'virtio']
    v.name = box_name
  end

  config.vm.box = box_name
  config.vm.box_url = "#{box_url}"
  config.vm.box_check_update = false
  config.vm.network :private_network, ip: '192.168.21.12'

  # See:
  # https://www.vagrantup.com/docs/provisioning/ansible.html
  # https://www.vagrantup.com/docs/provisioning/ansible_common.html
  # http://docs.ansible.com/ansible/latest/guide_vagrant.html
  # config.vm.provision 'ansible' do |ansible|
  #   ansible.playbook = 'provision.yml'
  #   ansible.groups = { type => [vagrant_host_name] }
  # end
end
