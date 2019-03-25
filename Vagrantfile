# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
require 'getoptlong'
require 'dotenv'

# Load the BackEnd Environment File for database setup
def load_env(vagrant_config)
  Dotenv.load! File.expand_path("#{vagrant_config['code']['main_path']}/.env", __FILE__)
rescue LoadError
  $stderr.puts 'Please install dotenv plugin with "vagrant plugin install dotenv"'
  exit
rescue Errno::ENOENT
  $stderr.puts 'Please create .env file, e.g. from .env.example'
  exit
end

# Clear old log files
def clear_log_files(vagrant_config)
  FileUtils.rm("#{vagrant_config['code']['logs_path']}/nginx/*.log", :force => true)
  FileUtils.rm("#{vagrant_config['code']['logs_path']}/php-fpm/*.log", :force => true)
end

# Default Settings
VAGRANTFILE_API_VERSION = "2"

# Check if we have specified a PHP Version
opts = GetoptLong.new(
  [ '--provision', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '-f', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '--do', GetoptLong::OPTIONAL_ARGUMENT],
  [ '--no-cleanup', GetoptLong::OPTIONAL_ARGUMENT],
  [ '--no-cache', GetoptLong::OPTIONAL_ARGUMENT],
  [ '--force-recreate', GetoptLong::OPTIONAL_ARGUMENT],
  [ '--with-redis', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '--machine-readable', GetoptLong::OPTIONAL_ARGUMENT ],
  [ '--no-color', GetoptLong::OPTIONAL_ARGUMENT ],
)

# Docker Compose Options
docker_compose_yml = ['/vagrant/docker-compose/01-core.yml']
docker_compose_build = '--force-rm'
docker_compose_up = '-d'
docker_clear_node_volume = false

# Check for any command line options
opts.each do |opt, arg|
  case opt
    when '--no-cache'
      docker_compose_build = docker_compose_build + ' --no-cache'
      docker_clear_node_volume = true
    when '--force-recreate'
      docker_compose_up = docker_compose_up + ' --force-recreate'
    when '--with-redis'
      docker_compose_yml.push('/vagrant/docker-compose/02-redis.yml')
  end
end

# Load the config file for custom environment
current_dir = File.dirname(File.expand_path(__FILE__))
vagrant_config = YAML.load_file("#{current_dir}/config.yml")

Vagrant.configure(2) do |config|

  load_env(vagrant_config)

  config.trigger.after :halt do |trigger|
    clear_log_files(vagrant_config)
  end
  
  config.vm.box = "geerlingguy/centos7"
  config.vm.hostname = "vagrant-docker"
  config.vm.synced_folder ".", "/vagrant"

  config.vm.provider "virtualbox" do |v|
    v.memory = "#{vagrant_config['vm']['memory']}"
    v.cpus = "#{vagrant_config['vm']['cpus']}"
    v.name = "Docker"
    v.customize [ "guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 1000 ]
  end
  
  config.vm.network "private_network",
    ip: "192.168.10.100"

  config.vm.network "forwarded_port",
    guest: 80,
    host: 8080

  config.vm.network "forwarded_port",
    guest: 6379,
    host: 6379

  config.vm.provision "shell",
    inline: "sudo yum install -y yum-utils device-mapper-persistent-data lvm2"
  config.vm.provision "shell",
    inline: "sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo"
  config.vm.provision "shell",
    inline: "sudo yum install -y docker-ce"
  config.vm.provision "shell",
    inline: "sudo yum install -y git"

  config.vm.provision "shell",
    inline: "sudo usermod -a -G docker $USER"
  config.vm.provision "shell",
    inline: "sudo usermod -a -G docker vagrant"
  config.vm.provision "shell",
    inline: "sudo systemctl start docker"
  config.vm.provision "shell",
    inline: "sudo systemctl enable docker.service"

  # Sync the tmp directory for logs and cache to not fill the VM
  config.vm.provision "shell", inline: "sudo mkdir -p /var/log/nginx"
  config.vm.provision "shell", inline: "sudo mkdir -p /var/log/php-fpm"
  config.vm.synced_folder "#{vagrant_config['code']['logs_path']}/nginx", "/var/log/nginx", create: true
  config.vm.synced_folder "#{vagrant_config['code']['logs_path']}/php-fpm", "/var/log/php-fpm", create: true

  # Main
  config.vm.provision "shell", inline: "sudo mkdir -p /var/log/main"
  config.vm.synced_folder "#{vagrant_config['code']['logs_path']}/main", "/var/log/main", create: true
  config.vm.synced_folder "#{vagrant_config['code']['main_path']}", "/var/www/main"

  # Database
  config.vm.provision "shell", inline: "sudo mkdir -p /var/lib/mysql"
  config.vm.provision "shell", inline: "sudo mkdir -p /opt/mysql/init && sudo cp /vagrant/db/schema.sql /opt/mysql/init"

  # Add some handy shortcuts for docker
  config.vm.provision "shell",
    inline: "echo \"alias main='docker exec -ti main bash'\" >> /home/vagrant/.bashrc"
  config.vm.provision "shell",
    inline: "echo \"alias db='docker exec -ti db bash'\" >> /home/vagrant/.bashrc"
  config.vm.provision "shell",
    inline: "echo \"alias cron='docker exec -ti cron bash'\" >> /home/vagrant/.bashrc"
  config.vm.provision "shell",
    inline: "echo \"alias nginx='docker exec -ti nginx bash'\" >> /home/vagrant/.bashrc"

  config.vm.provision :docker_compose,
    env: {
      :MYSQL_ROOT_PASSWORD  => ENV['DB_HTTP_WRITE_PASSWORD'],
      :MYSQL_DATABASE       => ENV['DB_HTTP_WRITE_NAME'],
      :MYSQL_USER           => ENV['DB_HTTP_WRITE_USER'],
      :MYSQL_PASSWORD       => ENV['DB_HTTP_WRITE_PASSWORD']
    },
    yml: docker_compose_yml,
    rebuild: true,
    run: "always",
    command_options: {
      rm: "",
      build: "#{docker_compose_build}",
      up: "#{docker_compose_up}"
    }

end
