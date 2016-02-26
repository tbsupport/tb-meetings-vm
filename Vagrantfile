# Use rbconfig to determine if we're on a windows host or not.
require 'rbconfig'

host = RbConfig::CONFIG['host_os']

Vagrant.configure("2") do |config|
  config.vm.box       = "ubuntu/trusty64"

  ## OS Tune

  # Give VM 1/4 system memory & access to all cpu cores on the host
  if host =~ /darwin/
    cpus = `sysctl -n hw.ncpu`.to_i
    # sysctl returns Bytes and we need to convert to MB
    mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
  elsif host =~ /linux/
    cpus = `nproc`.to_i
    # meminfo shows KB and we need to convert to MB
    mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
  else # sorry Windows folks, I can't help you
    cpus = 2
    mem = 1024
  end

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", mem]
    v.customize ["modifyvm", :id, "--cpus", cpus]
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "priv/ansible/dev.yml"
    ansible.inventory_path = "priv/ansible/local_hosts"
    ansible.tags = ENV["TAGS"]
    ansible.skip_tags = ENV["SKIP_TAGS"]
    ansible.verbose = 'v'
    ansible.limit = 'all'
  end

  config.vm.network :forwarded_port, host: 2202, guest: 22 

  # rails app
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  
  # erly app http
  config.vm.network "forwarded_port", guest: 8082, host: 8082
  
  # erly app rtmp
  config.vm.network "forwarded_port", guest: 1935, host: 1935
  
  # varnish
  config.vm.network "forwarded_port", guest: 80, host: 8081

  config.vm.network "private_network", ip: "192.168.50.113"

  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.synced_folder "src/", "/webapps",
    nfs: true,
    create: true,
    id: "vagrant-root"
end
