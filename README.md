# rke2-ha-kubernetes

## pre-req

```
systemctl disable apparmor.service
systemctl disable firewalld.service
systemctl stop apparmor.service
systemctl stop firewalld.service

systemctl disable swap.target
swapoff -a
```

## master nodes

```
mkdir -p /etc/rancher/rke2/
```

On the first node, you should set up the configuration file with your own pre-shared secret as the token. The token argument can be set on startup.
If you do not specify a pre-shared secret, RKE2 will generate one and place it at /var/lib/rancher/rke2/server/node-token. i will use my own token

first-master config :
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

other-masters config: (just add new line for master IP)

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
