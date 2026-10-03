# Lab overview

Northline runs one management cluster for the region. Sites are downstream clusters. `site-co-01` is the central office. `site-ran-01` is the far-edge site.

| VM | Role | Start size, not a measured pin |
| --- | --- | --- |
| mgmt | Management cluster, image cache, Ironic. Gitea for the Fleet mirror lives here. | 8 vCPU, 16 GB |
| site-co | Host for `site-co-01`. Nested virt so lab 07 can start a guest. | 4 vCPU, 8 GB |
| site-ran | Host for `site-ran-01`. No KubeVirt. | 4 vCPU, 8 GB |

One subnet, one bridge, on every lab page: `192.168.125.0/24`, bridge `telco`. VM addresses are whatever the host assigned on that subnet. Read them with `virsh domifaddr`. There is no node named `eib`.

## What rodeo builds

`rodeo up` builds the platform and stops when the `BareMetalHost` objects are `available`. That is the management cluster plus two site hosts.

The management image is SUSE Linux Micro 6.2, RKE2 `v1.36.3+rke2r1`, Cilium, cert-manager, Metal3, Rancher Prime, `rancher-turtles-providers`, and SUSE Storage so Ironic has a volume. NeuVector is skipped. The image build uses Edge Image Builder and Kiwi at the 3.7 tags in `rodeo-plan.yaml`. Students do not wait on that build.

Rancher TLS is `rancher_tls.source: secret`, on a NodePort. The lab does not need a public listener on port 80 or 443.

## What you apply

`manifests/` is yours. rodeo does not apply it.

| File | Lab | Result |
| --- | --- | --- |
| `downstream-single.yaml` | 04 | `site-co-01` |
| `clusterclass.yaml` | 05 | The repeatable shape |
| `site-ran.yaml` | 05 | `site-ran-01` |
| `kubevirt-vm.yaml` | 07 | One guest on `site-co-01` |

Those four files are stubs in this pass. The tested YAML comes from telco-examples `release-3.7` (`e4ec0b0`) after a live apply, with SR-IOV removed.

## Hosts

AWS: `rodeo up --target aws` rents the EC2 instance and uses it as the KVM host. Metal3 never calls the EC2 API. The guests it provisions are libvirt VMs with a sushy-tools BMC.

Bare metal: the Linux machine you are on is already the KVM host. Same guests, same sushy-tools BMC.

Fleet files, if any, keep `lab.target: baremetal`. Do not set `lab.target: aws`.

## Tear-down

AWS: `rodeo destroy --cloud` or `rodeo fleet deprovision`. Credentials stay in `~/.aws/credentials` or the environment. They do not go in `rodeo-plan.yaml`.

Bare metal: `sudo rodeo clean --yes`.
