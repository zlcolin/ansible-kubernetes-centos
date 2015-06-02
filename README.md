# ansible-kubernetes-centos
Deployment automation of Kubernetes on CentOS 7

### Prerequisite
Suppose we have these nodes in the cluster:
```
centos1 (master, etcd) = 10.0.0.1
centos2 (minion) = 10.0.0.2
centos3 (minion) = 10.0.0.3
```

Create ssh public key on every node in the cluster (skip this step if you already have created the ssh public key):
```
   ssh-keygen
```

Copy master node ssh key to every single node within the cluster (including the master itself). Run:
```
for ip in 10.0.0.1 10.0.0.2 10.0.0.3; do ssh-copy-id $ip; done
```

Now, make sure the master can ssh to every single node (including the master itself) without password.

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
NAME                            LABELS        STATUS
10.0.0.2   Schedulable   name=10.0.0.2   Ready
10.0.0.3   Schedulable   name=10.0.0.3   Ready
```
