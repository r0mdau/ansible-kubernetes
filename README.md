# ansible-kubernetes

## Quickstart
```
vagrant up
```
This will download vagrant box Ubuntu 16.04 and install a kubernetes cluster.
With 1 master and 2 nodes.

When done, you may want to use kubectl commands from your host. So copy the generated kubeconfig
to your home path :
```
cp kubernetes-setup/kubeconfig ~/.kube/config
```

And for future node joining the cluster, the join command is available in `kubernetes-setup/join-command`
