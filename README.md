# rke2-ha-kubernetes

![image](https://github.com/alperen-selcuk/rke2-ha-kubernetes/assets/78741582/88b2cdd7-ee6b-4eb3-9d4a-f102c849aef3)


## pre-req

### nginx stream module

this nginx configuration need stream module on nginx so you can follow this instructions:

https://github.com/alperen-selcuk/nginx-stream-install


### all nodes configurations

```
systemctl disable apparmor.service
systemctl disable firewalld.service
systemctl stop apparmor.service
systemctl stop firewalld.service

systemctl disable swap.target
swapoff -a
```

### master nodes

```
mkdir -p /etc/rancher/rke2/
```

On the first node, you should set up the configuration file with your own pre-shared secret as the token. The token argument can be set on startup.
If you do not specify a pre-shared secret, RKE2 will generate one and place it at /var/lib/rancher/rke2/server/node-token. i will use my own token

#### first-master config :

```
cat<<EOF|tee /etc/rancher/rke2/config.yaml
token: rancher-token2024
tls-san:
  - YOUR-LOADBALANCER-IP
  - YOUR-KUBERNETES-DOMAIN
EOF
```

after that you must install rke2 and servis start

```
curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE=server sh -
systemctl enable rke2-server.service
systemctl start rke2-server.service
```

kubeconfig 
```
echo "export PATH=$PATH:/var/lib/rancher/rke2/bin" >> $HOME/.bashrc
echo "export KUBECONFIG=/etc/rancher/rke2/rke2.yaml"  >> $HOME/.bashrc 
```

#### other-masters config: (just add new line for master IP)

```
cat<<EOF|tee /etc/rancher/rke2/config.yaml
token: rancher-token2024
server: https://YOUR-FIRST-MASTER-IP:9345
tls-san:
  - YOUR-LOADBALANCER-IP
  - YOUR-KUBERNETES-DOMAIN
EOF
```

after again you must install rke2 and servis start

```
curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE=server sh -
systemctl enable rke2-server.service
systemctl start rke2-server.service
```

 you can find kubeconfig in rke directory. you can export kubeconfig or type with kubectl. both works.

```
kubectl --kubeconfig /etc/rancher/rke2/rke2.yaml get nodes

export KUBECONFIG=/etc/rancher/rke2/rke2.yaml
kubectl get nodes
```

#### worker nodes

```
cat<<EOF|tee /etc/rancher/rke/config.yaml
server: https://YOUR-FIRST-MASTER-IP:9345
token: rancher-token2024
```

after again you must install rke2 and servis start

```
curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE="agent" sh -
systemctl enable rke2-server.service
systemctl start rke2-server.service
```

