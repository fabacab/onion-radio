Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 8000, host: 8000
  config.vm.provision "ansible_local" do |a|
    a.playbook = "playbook.yaml"
    a.galaxy_role_file = "requirements.yaml"
    a.extra_vars = {
      ice_sourcepassword: "#{ENV['ice_sourcepassword']}",
      ice_relaypassword: "#{ENV['ice_relaypassword']}",
      ice_adminpassword: "#{ENV['ice_adminpassword']}"
    }
  end
end
