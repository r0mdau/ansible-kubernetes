# ansible-kubernetes

This project is a good starting point to learn how to install and configure a 
[Kubernetes](https://kubernetes.io) cluster.

- VM provisionning is fueled by [Vagrant](https://www.vagrantup.com/).
- Cluster configuration by [Ansible](https://www.ansible.com/)
- Kubernetes networking by [Calico](https://www.projectcalico.org/calico-networking-for-kubernetes/)
- Kubernetes automatic and dynamic routing by [Traefik](https://docs.traefik.io/)

## Quickstart
Install required softwares

    # add virtualbox repository
    sudo wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add - \
    && sudo echo "deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib" > /etc/apt/sources.list.d/virtualbox.list
    
    # add vagrant repository
    curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add - \
    && sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
    
    # install softwares
    sudo apt update \
    && sudo apt install -y virtualbox-6.1 ansible vagrant \
    && export VAGRANT_DEFAULT_PROVIDER=virtualbox \
    && vagrant plugin install vagrant-vbguest
    
    # install virtualbox extension pack
    wget https://download.virtualbox.org/virtualbox/6.1.26/Oracle_VM_VirtualBox_Extension_Pack-6.1.26.vbox-extpack \
    && sudo VBoxManage extpack install --replace Oracle_VM_VirtualBox_Extension_Pack-6.1.26.vbox-extpack


Before Vagrant uping, this Vagrantfile will set up **3 VM, using 2 vCPU and 2Gio vRAM each**, be sure you can
carry that.

    vagrant up
    
This will download vagrant box Ubuntu Focal and install a kubernetes cluster.
With 1 master and 2 nodes.

When done, you may want to use kubectl commands from your host. So copy the generated kubeconfig
to your home path :

    cp kubernetes-setup/kubeconfig ~/.kube/config
    kubectl get nodes
    # \o/ enjoy


And for future node joining the cluster, the join command is available in `kubernetes-setup/join-command`

### Reverse Proxying with Traefik

To install and configure Traefik v2.5 using helm:

    helm repo add traefik https://helm.traefik.io/traefik
    helm repo update
    helm install traefik traefik/traefik

#### Traefik dashboard

To install the dashboard:

    # add this line to /etc/hosts
    192.168.56.10 traefik.local
    # execute
    kubectl apply -f files/traefik/dashboard.yaml

Then browse to http://traefik.local:<port>/dashboard/

Find this auto-generated port in `kube-system` namespace looking at traefik service external port.

Without installing the dashboard, you can access it with port-forward:

    kubens kube-system
    kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000

Then browse to http://localhost:9000/dashboard/

### Example app

Everything should work, but running your custom docker image is a must have.
Use `kubectl apply` to launch yaml configurations files in the `files/app` folder. It will use a simple nodeJs docker
 image with 2 instances reverse proxyed by traefik \o/

Edit `/etc/hosts`:

    192.168.56.10 test.local traefik.local

Then browse test.local:<port>

Find this auto-generated port in `kube-system` namespace looking at traefik service external port.

### Dynamic provisionning volume

When it comes to StateFull apps. I like this feature provided 
by (Rancher team)[https://github.com/rancher/local-path-provisioner]

    kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml

Add `storageClassName: local-path` property to your PersistentVolumeClaims yaml file and let the automation work.
