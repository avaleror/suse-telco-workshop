# Quick reference

## Names

| Item | Value |
| --- | --- |
| Operator | Northline |
| Management VM | `mgmt` |
| Site hosts | `site-co`, `site-ran` |
| Downstream clusters | `site-co-01`, `site-ran-01` |
| Bridge | `telco` |
| Subnet | `192.168.125.0/24` |
| DNS domain | `northline.telco` |
| Plan type | `suse-telco` |
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

kubectl apply -f manifests/downstream-single.yaml
kubectl apply -f manifests/clusterclass.yaml
kubectl apply -f manifests/site-ran.yaml
kubectl apply -f manifests/kubevirt-vm.yaml
```

The apply commands wait on real manifests. The files in this repo are stubs.

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
| rodeo-cli tag named in the README | `v0.19.0` (`cc12c84`) |
| SUSE Telco Cloud | 3.7.0 |
| RKE2 | `v1.36.3+rke2r1` |
| Rancher Prime | 2.15.1 |
| Turtles | `307.0.8+up0.27.0` |
| Metal3 chart | `307.0.31+up0.16.0` |
| KubeVirt / CDI | 1.8.3 / 1.65.0 |
| telco-examples | `e4ec0b0` on `release-3.7` |

Password: `~/.rodeo/secrets.yaml` on the KVM host. SCC code stays in that file too.
