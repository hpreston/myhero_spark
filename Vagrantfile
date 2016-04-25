# -*- mode: ruby -*-
# vi: set ft=ruby :

# Specify Vagrant version and Vagrant API version
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

# Create and configure the Docker container(s)
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Disable synced folders for the Docker container
  # (prevents an NFS error on "vagrant up")
  #config.vm.synced_folder ".", "/vagrant", disabled: true

  config.ssh.insert_key = false

  # Configure the Docker provider for Vagrant
  config.vm.provider "docker" do |docker|

    # Define the location of the Vagrantfile for the host VM
    # Comment out this line to use default host VM that is
    # based on boot2docker
    docker.vagrant_vagrantfile = "Vagrantfile.host"

    # Specify the Docker image to use
    # or the location of the Dockerfile
    #docker.image = "nginx"
	docker.build_dir = "."

    # Specify port mappings
    # If omitted, no ports are mapped!
    docker.ports = ['5000:5000']

    # Environment Variables for Development
    docker.env = {
      "myhero_app_key" => "DevApp",
      "myhero_app_server" => "http://192.168.40.1:15001",
      "myhero_spark_bot_url" => "http://192.168.40.14:15003",
      "myhero_spark_bot_secret" => "DevBot",
    }

    # Specify a friendly name for the Docker container
    docker.name = 'myhero'
  end
end
