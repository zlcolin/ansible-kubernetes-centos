# ansible-kubernetes-centos
Deployment automation of Kubernetes on CentOS 7

### Prerequisite
##### SSH without password (optional, you can also use ansible-playbook flag to specify ssh credential)
Suppose we have these nodes in the cluster:
```
centos1 (master) = 192.168.228.70
centos2 (etcd)   = 192.168.228.69
centos3 (minion) = 192.168.228.68
centos4 (minion) = 192.168.228.67
centos5 (minion) = 192.168.228.66

```

Create ssh public key on master node (skip this step if you already have created the ssh public key):
```
   ssh-keygen
```

Copy master node ssh key to every single node within the cluster (including the master itself). Run:
```
for ip in 192.168.228.70 192.168.228.69 192.168.228.68 192.168.228.67 192.168.228.66; do ssh-copy-id $ip; done
```

Now, make sure the master can ssh to every single node (including the master itself) without password.

##### CA for private docker registry
Make sure you have your private docker registry ca and put it in `roles/docker-registry/templates/dockerCA.crt`.

### Run ansible-playbook
Before you really run ansible-playbook, you need to configure the `inventory` file in the root directory based on your own configuration. You should also configure the file `group_vars/all.yml` based on your own situation. After the customization, you can run this command on the master node:
```
ansible-playbook -i inventory integrated-deploy.yml --ask-become-pass
```

### Check deployment
Run the `kubectl` command to see if the nodes are ready:
```
kubectl get nodes
```

If the deployment is successful, you should see something like this:
```
NAME             LABELS                                  STATUS
192.168.228.66   kubernetes.io/hostname=192.168.228.66   Ready
192.168.228.67   kubernetes.io/hostname=192.168.228.67   Ready
192.168.228.68   kubernetes.io/hostname=192.168.228.68   Ready
```

### Configure internal DNS
Make a rolling update by running the following command:
```
ansible-playbook -i inventory kubelet-rolling-update-dns.yml --ask-become-pass
```
This will make the kubelet on each node to restart one by one with the dns configuration flag.
To make the add-on DNS to really work, you also need to run the SkyDNS controller and service yaml file.
