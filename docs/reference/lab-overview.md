# Lab overview

Northline runs one management cluster for the region. Sites are downstream clusters. `site-co-01` is the central office. `site-ran-01` is the far-edge site.

The diagram of how it all nests, and the hardware it needs, are in [Architecture and hardware](architecture.md).

| VM | Role | Start size, not a measured pin |
| --- | --- | --- |
| mgmt | Management cluster, image cache, Ironic. Gitea for the Fleet mirror lives here. | 8 vCPU, 16 GB |
| site-co, site-co-2, site-co-3 | The three hosts of `site-co-01`. Nested virt so lab 07 can start a guest. | 4 vCPU, 8 GB each |
| site-ran | Host for `site-ran-01`. No KubeVirt. | 4 vCPU, 8 GB |

One subnet on every lab page: `192.168.122.0/24`, libvirt network `default`, bridge `virbr0`. Each VM has a fixed DHCP reservation: `mgmt` 192.168.122.10, `site-co` 192.168.122.21, `site-co-2` 192.168.122.23, `site-co-3` 192.168.122.24, `site-ran` 192.168.122.32. The cluster API addresses are MetalLB addresses: `site-co-01` on .20, `site-ran-01` on .22. Read them with `virsh domifaddr`. There is no node named `eib`.

## What rodeo builds

`rodeo up` builds the platform and stops when the `BareMetalHost` objects are `available`. That is the management cluster plus four site hosts. The VMs are declared in `definition.yaml` in this repo. Which of them become BareMetalHosts, and their labels, is rodeo-cli's built-in suse-telco layout. `telco.sites` in `rodeo-plan.yaml` can override it, and this repo does not need to.

The management VM is SUSE Linux Micro 6.2 (openSUSE Leap Micro 6.2 with `edition: opensource`, and community Rancher instead of Rancher Prime) with RKE2 `v1.36.3+rke2r1`, Cilium, cert-manager, Rancher Prime, Metal3 and `rancher-turtles-providers`. Ironic keeps its data on an `emptyDir`, the single-node default, so SUSE Storage is not installed there. NeuVector is skipped. The downstream image is built with Edge Image Builder at the 3.7 tag in `rodeo-plan.yaml` and served from `http://imagecache.local:8080` on `mgmt`. Students do not wait on that build.

Rancher TLS is `rancher_tls.source: rancher`, the self-signed certificate, on a NodePort. The lab does not need a public listener on port 80 or 443.

## What you apply

`manifests/` is yours. rodeo does not apply it.

| File | Lab | Result |
| --- | --- | --- |
| `site-co.yaml` | 04 | Downstream cluster `site-co-01`, three nodes, with SUSE Storage |
| `clusterclass.yaml` | 05 | ClusterClass `example-clusterclass` |
| `site-ran.yaml` | 05 | Downstream cluster `site-ran-01` |
| `kubevirt-vm.yaml` | 07 | VirtualMachine `site-co-guest` on `site-co-01` |

Those files are the 3.7 objects from telco-examples `release-3.7` (`e4ec0b0`), with SR-IOV removed. `example-clusterclass` and `site-ran-01` live in namespace `northline`. `site-co-01` and `site-co-guest` use `default`.

## Hosts

AWS: `rodeo up --target aws` rents the EC2 instance and uses it as the KVM host. Metal3 never calls the EC2 API. The guests it provisions are libvirt VMs with a sushy-tools BMC.

Bare metal: the Linux machine you are on is already the KVM host. Same guests, same sushy-tools BMC.

Fleet files, if any, keep `lab.target: baremetal`. Do not set `lab.target: aws`.

## Tear-down

AWS: `rodeo destroy --cloud` or `rodeo fleet deprovision`. Credentials stay in `~/.aws/credentials` or the environment. They do not go in `rodeo-plan.yaml`.

Bare metal: `sudo rodeo clean --yes`.
