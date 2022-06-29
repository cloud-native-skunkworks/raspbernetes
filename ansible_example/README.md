## Management with tailscale

<img src="../images/ts.png" width="400px" />

### Install K3s cluster

This is an example using my tailscale IP addresses as per the inventory. My local IP addresses are the following:
- 192.168.88.251 raspbernetes-0
- 192.168.88.246 raspbernetes-1
- 192.168.88.249 raspbernetes-2
- 192.168.88.250 raspbernetes-3

```
ssh ubuntu@100.75.189.33 "curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode 644"
ssh ubuntu@100.75.189.33 "sudo cat /var/lib/rancher/k3s/server/node-token"
```

_Use the above token_


_worker 0_
```
ssh ubuntu@100.119.138.43 "curl -sfL https://get.k3s.io | K3S_URL=https://192.168.88.251:6443 K3S_TOKEN=K105cd96af754b184f24c8853cf3285e2edb88341829622ccb4090b6ad463eb2980::server:4db20faec91585300d834ef9e5d52dce sh -"
```

_worker 1_
```
ssh ubuntu@100.70.58.39 "curl -sfL https://get.k3s.io | K3S_URL=https://192.168.88.251:6443 K3S_TOKEN=K105cd96af754b184f24c8853cf3285e2edb88341829622ccb4090b6ad463eb2980::server:4db20faec91585300d834ef9e5d52dce sh -"
```

_worker 2_
```
ssh ubuntu@100.119.154.86 "curl -sfL https://get.k3s.io | K3S_URL=https://192.168.88.251:6443 K3S_TOKEN=K105cd96af754b184f24c8853cf3285e2edb88341829622ccb4090b6ad463eb2980::server:4db20faec91585300d834ef9e5d52dce sh -"
```

_export kubeconfig_

```
ssh ubuntu@100.75.189.33 "sudo cat /etc/rancher/k3s/k3s.yaml"
```

Rename the kubeconfig to the tailscale ip

### Node management

```
ansible all -i inventory -m shell -a 'shutdown -r now'
```
```
ansible all -i inventory -m shell -a 'vcgencmd measure_temp'
```

### Switch management

_print poe status_
```
ansible leader -i inventory -m shell -a 'ssh -t admin@192.168.88.1 "interface ethernet poe print;quit"'
```
_poe off_
```
 ansible leader -i inventory -m shell -a 'ssh -t admin@192.168.88.1 "interface ethernet poe set ether5 poe-out=off;quit"'
```
_poe on_
```
ansible leader -i inventory -m shell -a 'ssh -t admin@192.168.88.1 "interface ethernet poe set ether5 poe-out=auto-on;quit"
```
