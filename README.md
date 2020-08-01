# ansible-kubernetes

This project is a good starting point to learn how to install and configure a 
[Kubernetes](https://kubernetes.io) cluster.

- VM provisionning is fueled by [Vagrant](https://www.vagrantup.com/).
- Cluster configuration by [Ansible](https://www.ansible.com/)
- Kubernetes networking by [Calico](https://www.projectcalico.org/calico-networking-for-kubernetes/)
- Kubernetes automatic and dynamic routing by [Traefik](https://docs.traefik.io/)

## Quickstart
Before Vagrant uping, this Vagrantfile will set up 3 VM, using 2 vCPU and 2Gio vRAM each, be shure you can
carry that.


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

Or you can use kubectl apply.

### Example app

Everything should work, but running your custom docker image is a must have.
Use kubectl to launch the `r0mdau/node-hello-docker` image with 2 instances reverse proxyed
by traefik \o/