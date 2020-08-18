Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "forwarded_port", guest: 8000, host: 8000
  config.vm.provision "ansible_local" do |a|
    a.playbook = "playbook.yaml"
    a.galaxy_role_file = "requirements.yaml"
    a.extra_vars = {
      icecast_admin_password: "#{ENV['icecast_admin_password']}",
      icecast_source_password: "#{ENV['icecast_source_password']}",
      icecast_relay_password: "#{ENV['icecast_relay_password']}",
      onion_radio_private_key_file: "#{ENV['onion_radio_private_key_file']}"
    }.reject {|k,v| v.empty?}
  end
end
