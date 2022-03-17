IMAGE_NAME = "rockylinux/8"
N = 3

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 1
    end

    config.vm.define "bastion" do |bastion|
        bastion.vm.box = IMAGE_NAME
        bastion.vm.network "private_network", ip: "10.10.1.3"
        bastion.vm.hostname = "bastion"
        bastion.vm.synced_folder "./ansible", "/ansible", type: "nfs"
        bastion.vm.provision "shell",
          inline: "yum install python39 -y"
        bastion.vm.provision "ansible" do |ansible|
            ansible.playbook = "bastion.yml"
            ansible.extra_vars = {
                node_ip: "10.10.1.3",
                ansible_python_interpreter: "/usr/bin/python3",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "10.10.1.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "shell",
              inline: "yum install python39 -y"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "node.yml"
                ansible.extra_vars = {
                    node_ip: "10.10.1.#{i + 10}",
                    ansible_python_interpreter: "/usr/bin/python3",
                }
            end
        end
    end
end
