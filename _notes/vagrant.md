--- title: vagrant ---

![vagrant](https://www.vagrantup.com/vagrant-public/img/logo-hashicorp.svg)

HashiCorp Vagrant provides the same, easy workflow regardless of your role as a developer, operator, or designer. It leverages a declarative configuration file which describes all your software requirements, packages, operating system configuration, users, and more.

https://www.vagrantup.com/
https://github.com/hashicorp/vagrant
https://www.vagrantup.com/docs
https://learn.hashicorp.com/collections/vagrant/getting-started

# Install Vagrant on MacOS
```bash 
brew install --cask vagrant
```

# Basic setup from Getting Started

## 1. Create a folder to use
```bash
mkdir vagrant-test
cd vagrant-test
```

## 2. Initialise vagrant to use that folder
You'll need to choose a "box" to use for the VM. 
These can be found here: https://app.vagrantup.com/boxes/search

All tutorials use: [hashicorp/bionic64](https://app.vagrantup.com/hashicorp/boxes/bionic64)

Hashicorp suggests using [Bento](https://app.vagrantup.com/bento) for alternatives. Unfortunately, their [bento/debian-11](https://app.vagrantup.com/bento/boxes/debian-11) box is super out of date.

The [Generic](https://app.vagrantup.com/generic) collection has an up-to-date [generic/debian11](https://app.vagrantup.com/generic/boxes/debian11) box that I used.

```bash
vagrant init generic/debian11
```

This will create a `Vagrantfile` in the current directory.

## 3. Configure `Vagrantfile`
The `Vagrantfile` specifies all the configs for the VM you want to run.

So. Many. Options

https://www.vagrantup.com/docs/vagrantfile

After some messing about, came up with this generic `Vagrantfile` which auto-configures [[docker]] & [[portainer]] agent.

```ruby
Vagrant.configure("2") do |config|

# specify the VM box to use
  config.vm.box = "generic/debian11"

# make sure the current dir is synced with the VM.
# This should happen by default but didn't seem to happen with the generic
# boxes for some reason.
  config.vm.synced_folder ".", "/vagrant"

# add a private network to the VM so the host can connect
  config.vm.network "private_network", type: "dhcp"

# do some shell command stuff
  config.vm.provision "shell" do |s|
# one 'inline' per command unless you do this '<<-SHELL ... SHELL' trick
      s.inline = <<-SHELL
        apt-get update
        apt-get --assume-yes upgrade
        apt-get --assume-yes install neofetch htop
        SHELL
  end

# install Docker
  config.vm.provision "docker" do |d|

# run the portainer_agent container
    d.run "portainer_agent",
      image: "portainer/agent:latest",
      args: "-p 9001:9001 \
            --restart=always \
            -v /var/run/docker.sock:/var/run/docker.sock \
            -v /var/lib/docker/volumes:/var/lib/docker/volumes"
    
  end

end
```

## 4. Create VM
```shell
vagrant up
```
You'll get a whole lot of info about how the build is going.

## Things you can do
It's a VM that you can see in Virtual Box. Some things you can do directly via vagrant

### SSH into VM
```shell
vagrant ssh
```
You'll be automatically logged in to the VM.

### Delete VM
```shell
vagrant destroy
```

### Re-apply the `Vagrantfile` config if you change something
```shell
vagrant provision
```

Things to try:
- Call as shell file as part of provisioning.
- Install and call an Ansible file