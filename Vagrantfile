IMAGE_NAME = "ubuntu/bionic64"
N = 4

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 2
    end

    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "192.168.50.10"
        master.vm.hostname = "k8s-master"
        master.vm.provision :ansible do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.50.10",
                node_name: "k8s-master",
                ansible_python_interpreter:"/usr/bin/python3",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision :ansible do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{i + 10}",
                    node_name: "node-#{i}",
                    ansible_python_interpreter:"/usr/bin/python3",
                }
            end
        end
    end

    config.vm.synced_folder "~/.ssh", "/home/vagrant/.ssh", type: "rsync", rsync__exclude: ['authorized_keys', 'config.d/*', 'config'], privileged: true
end
