# -*- mode: ruby -*-
# vi: set ft=ruby :

# NOTE: The following variables must be set according your aws configuration:
# export AWS_ACCESS_KEY='????????????????????'                              
# export AWS_SECRET_KEY='????????????????????????????????????????'
# export AWS_PRIVATE_KEY_PATH='~/.ssh/vagrant.pem'
# export AWS_DEFAULT_REGION='us-east-1'
# export AWS_EC2_KEY_NAME='vagrant'
# export AWS_SECURITY_GROUP_NAME='vagrant'

# NOTE: Fill the following keys according your aws configuration:
HOSTS = {
  'gerrit' => {
    :amazon_image   => 'ami-fce3c696',
    :shh_username   => 'ubuntu',
    :instance_type  => 't2.micro',
    :disk_size_gb   => 10,
    :security_group => 'sg-4c72a434',
    :elastic_ip     => '50.16.175.221',
    :private_ip     => '172.31.0.20',
    :subnet_id      => 'subnet-4168ec37'    
  }
}

HOSTS.each do | name, info |
  Vagrant.configure(2) do |config|

    config.vm.box = 'aws'
    config.vm.synced_folder '.', '/vagrant', disabled: true

    config.vm.define name do |vm_name|
      vm_name.vm.provider :aws do |aws, override|
        aws.access_key_id        = ENV['AWS_ACCESS_KEY']
        aws.secret_access_key    = ENV['AWS_SECRET_KEY']
        aws.region               = ENV['AWS_DEFAULT_REGION']
        aws.availability_zone    = ENV['AWS_DEFAULT_REGION'] + 'a'
        aws.keypair_name         = ENV['AWS_EC2_KEY_NAME']
        aws.ami                  = info[:amazon_image]
        aws.instance_type        = info[:instance_type]
        aws.security_groups      = info[:security_group]
        aws.elastic_ip           = info[:elastic_ip]
        aws.private_ip_address   = info[:private_ip]
        aws.subnet_id            = info[:subnet_id]
        aws.block_device_mapping = [{ 'DeviceName' => '/dev/sda1', 'Ebs.VolumeSize' => info[:disk_size_gb] }]
        aws.tags = {
          'Name'        => name,
          'Environment' => 'vagrant-sandbox'
          }  
        # Credentials to login to EC2 Instance
        override.ssh.username         = info[:shh_username]
        override.ssh.private_key_path = ENV['AWS_PRIVATE_KEY_PATH']
      end

      # Configuration for Ansible as Provisioner
      vm_name.vm.provision :ansible do |ansible|
        ansible.playbook          = 'playbook.yml'
        ansible.verbose           = 'v'
        ansible.host_key_checking = false
        ansible.limit             = 'all'
      end

    end
  end
end