# ansible-kubernetes

## Quickstart

    vagrant up
    
This will download vagrant box Ubuntu 16.04 and install a kubernetes cluster.
With 1 master and 2 nodes.

When done, you may want to use kubectl commands from your host. So copy the generated kubeconfig
to your home path :

    cp kubernetes-setup/kubeconfig ~/.kube/config


And for future node joining the cluster, the join command is available in `kubernetes-setup/join-command`

### Reverse Proxying with Traefik

Pre-requisites to use kubernetes module with ansible : 

    python >= 2.7
    openshift >= 0.6
    PyYAML >= 3.11

To install and configure Traefik v1.7.

    ansible-playbook --private-key=~/.vagrant.d/insecure_private_key -u vagrant -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory traefik-playbook.yml
