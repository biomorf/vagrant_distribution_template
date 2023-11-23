# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.require_version ">= 1.8"
VAGRANTFILE_API_VERSION = "2"

VAGRANT_BOX = "generic/ubuntu2004"
#VAGRANT_BOX = "ubuntu/focal64"

### privileged dind needs host's dockerd socket
#DOCKER_GID = 121
DOCKER_GID = `stat -c '%g' /var/run/docker.sock | tr -d '\n'`

DOCKER_HOST_NAME = "dockerhost"
DOCKER_HOST_VAGRANTFILE = "./hostVagrantfile"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define "squid", autostart: true do |c|
    #proxy.vm.box = VAGRANT_BOX
    c.vm.hostname = "squid"

    c.vm.provider  :docker do |d, override|
      d.vagrant_machine = "#{DOCKER_HOST_NAME}"
      d.vagrant_vagrantfile = "#{DOCKER_HOST_VAGRANTFILE}"
    ##### https://dev.to/mattdark/using-docker-as-provider-for-vagrant-10me
      # TODO test on Windows !!!
      override.vm.box = nil
      d.image = "docker.io/ubuntu/squid:4.10-20.04_beta"
      d.name = "squid"
      #d.build_dir = "./containers/squid/"
      #d.dockerfile = "Dockerfile.squid.4.10-20.04_beta"
      ### privileged dind needs host's dockerd socket
      d.build_args = ["--build-arg", "--tag", "squid_444", "DOCKER_GID=#{DOCKER_GID}"]
      d.remains_running = true
      d.force_host_vm = true
      #d.env = HASH
      #d.has_ssh = true
        ### https://github.com/nestybox/sysbox/blob/master/docs/user-guide/install-package.md#installing-sysbox
               #d.create_args = ["--runtime=sysbox-runc"]
      d.create_args = [
               "-e", "TZ=UTC",
               "-v", '/vagrant_etc/squid.conf:/etc/squid/squid.conf',
               "-v", '/vagrant_var/var/log/squid:/var/log/squid',
               "-v", '/vagrant_var/var/spool/squid:/var/spool/squid'
               ]
               #"-p", "3128:3128",
      d.ports = ["3128:3128"]
      #d.compose = true
      #d.cmd = ["vertx", "run", "vertx-examples/src/raw/java/httphelloworld/HelloWorldServer.java"]
      #d.volumes = ["/src/vertx/:/usr/local/src"]

      ### NOTE working
      ### !!! NOTE docker provider ignores ownership and permissions defined in vagrant
      ###          but uses host owner instead and respects file permissions has been setted on host
      #override.vm.synced_folder "./vagrant_var/var", "/vagrant_var/var",
      #  owner: "proxy", group: "proxy",
      #  mount_options: ["uid=13", "gid=13", "dmode=777", "fmode=777"]
    end


    ### NOTE systemd requires some extra time to start
    c.vm.boot_timeout = 600


    ###proxy.vm.synced_folder ".", "/vagrant_data", disabled: true

    ### Squid application port
    ##proxy.vm.network "forwarded_port", guest: 3128, host: 3128, guest_ip: "172.21.0.10"
    #c.vm.network "forwarded_port", guest: 3128, host: 3128

    ### OK because it is 'readonly' files
    #c.vm.synced_folder "./vagrant_etc/", "/vagrant_etc", owner: "root", group: "root", mount_options: ["uid=0", "gid=0"]

        #if Vagrant::proxy.vm.provider == "virtualbox" then
        #end
  end


  config.vm.synced_folder ".", "/vagrant_data"


  ### NOTE Vagrant does not support alpine's ash shell...
  ###if "#{config.vm.provider.dockerfile}" == "Dockerfile.alpine" then
  ###  config.ssh.shell = "sh -l"
  ###end
  #config.ssh.shell = "ash -l"

  # Install Docker and pull an image
  #config.vm.provision :docker do |d|
  #  d.pull_images "alpine:3.18"
  #end
end
