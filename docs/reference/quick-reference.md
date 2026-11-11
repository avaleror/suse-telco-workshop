# Quick reference

## Names

| Item | Value |
| --- | --- |
| Operator | Northline |
| Management VM | `mgmt` |
| Site hosts | `site-co`, `site-co-2`, `site-co-3`, `site-ran` |
| Downstream clusters | `site-co-01`, `site-ran-01` |
| ClusterClass | `example-clusterclass` in namespace `northline` |
| Guest VM | `site-co-guest` on `site-co-01` |
| Network | libvirt `default`, bridge `virbr0` |
| Subnet | `192.168.122.0/24` |
| Host addresses | `mgmt` .10, `site-co` .21, `site-co-2` .23, `site-co-3` .24, `site-ran` .32 |
| Cluster endpoints | `site-co-01` .20, `site-ran-01` .22 (MetalLB) |
| DNS domain | `northline.telco` |
| Plan type | `suse-telco` |
| Edition | `product` (default) or `opensource` |
| Default target | `baremetal` |
| Console NodePort | `30080` |
| Fleet labels | `demo=true`, `site-type=downstream` |
| Auto-import label | `cluster-api.cattle.io/rancher-auto-import: "true"` |
| BMC method | `redfish-virtualmedia` |

## Commands

```bash
virsh list --all
virsh domifaddr mgmt

rodeo doctor
rodeo up
rodeo up --target aws

kubectl apply -f manifests/site-co.yaml
kubectl apply -f manifests/clusterclass.yaml
kubectl apply -f manifests/site-ran.yaml
kubectl apply -f manifests/kubevirt-vm.yaml
```

Apply the first three files on the management cluster. Apply `kubevirt-vm.yaml` on `site-co-01`.

## Tear-down

```bash
# AWS
rodeo destroy --cloud
# or: rodeo fleet deprovision

# Bare metal
sudo rodeo clean --yes
```

## Pins

| Piece | Pin |
| --- | --- |
| rodeo-cli tag named in the README | `v0.28.0` (`db583de`) |
| SUSE Telco Cloud | 3.7.0 |
| RKE2 | `v1.36.3+rke2r1` |
| Rancher Prime | 2.15.1 |
| Turtles providers chart | `307.0.8+up0.27.0` |
| Metal3 chart | `307.0.31+up0.16.0` |
| KubeVirt / CDI | 1.8.3 / 1.65.0 |
| telco-examples | `e4ec0b0` on `release-3.7` |

Password: `~/.rodeo/secrets.yaml` on the KVM host. No SCC code is needed; an optional one (product edition) goes in that file too.
